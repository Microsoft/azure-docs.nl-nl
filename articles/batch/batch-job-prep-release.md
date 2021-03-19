---
title: Taken maken om & complete taken op reken knooppunten voor te bereiden
description: Met voorbereidings taken op taak niveau kunt u de gegevens overdracht naar Azure Batch Compute-knoop punten en release taken voor het opruimen van het knoop punt bij het volt ooien van de taak
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919451"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Taak voorbereiding en taak release taken uitvoeren op batch Compute-knoop punten

 Een Azure Batch taak vereist vaak een bepaalde vorm van Setup voordat de taken worden uitgevoerd, en het onderhoud na de taak wanneer de taken zijn voltooid. U moet mogelijk algemene invoer gegevens voor de taak downloaden naar uw reken knooppunten, of taak uitvoer gegevens uploaden naar Azure Storage nadat de taak is voltooid. U kunt **taak voorbereiding** en **taak release** taken gebruiken om deze bewerkingen uit te voeren.

## <a name="what-are-job-preparation-and-release-tasks"></a>Wat zijn taak voorbereiding en release taken?
Voordat de taken van een taak worden uitgevoerd, wordt de taak voorbereiding uitgevoerd op alle reken knooppunten die zijn gepland om ten minste één taak uit te voeren. Zodra de taak is voltooid, wordt de taak vrijgave uitgevoerd op elk knoop punt in de pool dat ten minste één taak heeft uitgevoerd. Net als bij normale batch taken kunt u een opdracht regel opgeven die moet worden aangeroepen wanneer er een taak voorbereidings-of vrijgave taak wordt uitgevoerd.

Taak voorbereiding en release taken bieden vertrouwde batch-taak functies, zoals het downloaden van bestanden ([bron bestanden][net_job_prep_resourcefiles]), verhoogde uitvoering, aangepaste omgevings variabelen, maximale uitvoerings duur, aantal nieuwe pogingen en bewaar tijd voor bestanden.

In de volgende gedeelten leert u hoe u de klassen [JobPreparationTask][net_job_prep] en [JobReleaseTask][net_job_release] kunt gebruiken die worden gevonden in de [batch .net][api_net] -bibliotheek.

> [!TIP]
> Taak voorbereiding en release taken zijn vooral nuttig in omgevingen met gedeelde groepen, waarin een pool van reken knooppunten persistent is tussen taak uitvoeringen en wordt gebruikt door veel taken.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Wanneer u taak voorbereiding en release taken wilt gebruiken
Taak voorbereiding en taak release taken zijn geschikt voor de volgende situaties:

**Algemene taak gegevens downloaden**

Batch-taken vereisen vaak een gemeen schappelijke set gegevens als invoer voor de taken van de taak. In de berekening van de dagelijkse risico analyse zijn de markt gegevens bijvoorbeeld specifiek voor het project, maar gemeen schappelijk voor alle taken in de taak. Deze markt gegevens, vaak meerdere gigabytes grootten, moeten naar elk reken knooppunt worden gedownload, zodat elke taak die op het knoop punt wordt uitgevoerd, kan worden gebruikt. Een taak **voorbereidings taak** gebruiken om deze gegevens te downloaden naar elk knoop punt voordat de andere taken van de taak worden uitgevoerd.

**Taak-en taak uitvoer verwijderen**

In een omgeving met gedeelde groep waarbij de reken knooppunten van een pool niet buiten gebruik worden gesteld door taken, is het mogelijk dat u taak gegevens tussen uitvoeringen moet verwijderen. Mogelijk moet u schijf ruimte op de knoop punten besparen of voldoen aan het beveiligings beleid van uw organisatie. Gebruik een taak **release** om gegevens te verwijderen die zijn gedownload door een taak voorbereidings taak of die tijdens het uitvoeren van de taak zijn gegenereerd.

**Logboekbehoud**

Mogelijk wilt u een kopie van de logboek bestanden die door uw taken worden gegenereerd, of eventueel crash dump bestanden die door toepassingen die kunnen worden gegenereerd, opslaan. Een taak **release taak** in dergelijke gevallen gebruiken om deze gegevens te comprimeren en te uploaden naar een [Azure Storage][azure_storage] -account.

> [!TIP]
> Een andere manier om logboeken en andere taak-en taak uitvoer gegevens te behouden, is door de [Azure batch bestands conventies](batch-task-output.md) bibliotheek te gebruiken.
>
>

## <a name="job-preparation-task"></a>Taak voor het voorbereiden van taken


Voordat de taken van een taak worden uitgevoerd, voert batch de taak voorbereidings taken uit op elk reken knooppunt dat is gepland om een taak uit te voeren. Batch wacht op het volt ooien van de taak voorbereidings taak voordat de taken worden uitgevoerd die zijn gepland om te worden uitgevoerd op het knoop punt. U kunt de service echter zo configureren dat deze niet wordt gewacht. Als het knoop punt opnieuw wordt opgestart, wordt de taak voor het voorbereiden van de taak opnieuw uitgevoerd. U kunt dit gedrag ook uitschakelen. Als u een taak hebt met een taak voorbereidings taak en taak beheer taak geconfigureerd, wordt de taak voor het voorbereiden van de taak vóór de taak beheer taak uitgevoerd, net zoals voor alle andere taken. De taak voor het voorbereiden van taken wordt altijd eerst uitgevoerd.

De taak voor het voorbereiden van taken wordt alleen uitgevoerd op knoop punten waarop een taak is gepland. Hiermee wordt voor komen dat een voorbereidings taak onnodig wordt uitgevoerd als er geen taak aan een knoop punt is toegewezen. Dit kan gebeuren wanneer het aantal taken voor een taak kleiner is dan het aantal knoop punten in een pool. Dit geldt ook wanneer [gelijktijdige taak uitvoering](batch-parallel-node-tasks.md) is ingeschakeld, waardoor enkele knoop punten niet-actief blijven als het aantal taken lager is dan het totale aantal mogelijke gelijktijdige taken. Door de taak voorbereidings taak niet uit te voeren op niet-actieve knoop punten, kunt u minder geld best Eden aan de kosten voor gegevens overdracht.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] wijkt af van [CloudPool. StartTask][pool_starttask] in dat JobPreparationTask aan het begin van elke taak wordt uitgevoerd, terwijl StartTask alleen wordt uitgevoerd wanneer een reken knooppunt eerst aan een groep wordt toegevoegd of opnieuw wordt opgestart.
>


>## <a name="job-release-task"></a>Taak voor taak release

Zodra een taak is gemarkeerd als voltooid, wordt de taak vrijgave uitgevoerd op elk knoop punt in de pool dat ten minste één taak heeft uitgevoerd. U markeert een taak als voltooid door een Terminate-aanvraag uit te geven. De batch-service stelt vervolgens de taak status in op *beëindigen*, beëindigt actieve of actieve taken die zijn gekoppeld aan de taak en voert de taak vrijgave uit. De taak wordt vervolgens verplaatst naar de status *voltooid* .

> [!NOTE]
> Bij het verwijderen van de taak wordt ook de taak release uitgevoerd. Als een taak echter al is beëindigd, wordt de release taak niet een tweede keer uitgevoerd als de taak later wordt verwijderd.

Taak release taken kunnen Maxi maal 15 minuten worden uitgevoerd voordat de batch-service wordt beëindigd. Zie de [documentatie over rest API](/rest/api/batchservice/job/add#jobreleasetask)voor meer informatie.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Taak voorbereiding en release taken met batch .NET
Wijs een [JobPreparationTask][net_job_prep] -object toe aan de eigenschap [eigenschap cloudjob. JobPreparationTask][net_job_prep_cloudjob] van uw taak om een taak voorbereidings taak te gebruiken. U kunt ook een [JobReleaseTask][net_job_release] initialiseren en toewijzen aan de eigenschap [eigenschap cloudjob. JobReleaseTask][net_job_prep_cloudjob] van uw taak om de release taak van de taak in te stellen.

In dit code fragment `myBatchClient` is een instantie van [BatchClient][net_batch_client]en `myPool` is een bestaande pool binnen het batch-account.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Zoals eerder vermeld, wordt de release taak uitgevoerd wanneer een taak wordt beëindigd of verwijderd. Een taak beëindigen met [JobOperations. TerminateJobAsync][net_job_terminate]. Een taak verwijderen met [JobOperations. DeleteJobAsync][net_job_delete]. Gewoonlijk beëindigt of verwijdert u een taak wanneer de taken zijn voltooid of wanneer er een time-out is bereikt die u hebt gedefinieerd.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Code voorbeeld op GitHub
Bekijk het [JobPrepRelease][job_prep_release_sample] -voorbeeld project op github om de taak voorbereiding en release taken in actie weer te geven. Deze console toepassing doet het volgende:

1. Hiermee maakt u een pool met twee knoop punten.
2. Hiermee maakt u een taak met taak voorbereiding, release en standaard taken.
3. Voert de taak voor het voorbereiden van taken uit, die eerst de knoop punt-ID schrijft naar een tekst bestand in de map ' gedeelde ' van een knoop punt.
4. Voert een taak uit op elk knoop punt dat de taak-ID naar hetzelfde tekst bestand schrijft.
5. Wanneer alle taken zijn voltooid (of als de time-out is bereikt), wordt de inhoud van het tekst bestand van elk knoop punt afgedrukt naar de-console.
6. Wanneer de taak is voltooid, voert de taak release uit om het bestand uit het knoop punt te verwijderen.
7. Hiermee worden de afsluit codes van de taak voorbereidings-en release taken afgedrukt voor elk knoop punt waarop het wordt uitgevoerd.
8. Onderbreekt de uitvoering om bevestiging van het verwijderen van taken en/of groepen toe te staan.

De uitvoer van de voorbeeld toepassing ziet er ongeveer als volgt uit:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Als gevolg van het maken van de variabele en de start tijd van knoop punten in een nieuwe pool (sommige knoop punten zijn gereed voor taken vóór andere), ziet u mogelijk andere uitvoer. In het bijzonder, omdat de taken snel worden voltooid, kan een van de knoop punten van de groep alle taken van de taak uitvoeren. Als dit het geval is, zult u merken dat de taak voorbereidings-en release taken niet bestaan voor het knoop punt waarop geen taken worden uitgevoerd.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Taak voorbereiding en release taken in het Azure Portal controleren
Wanneer u de voorbeeld toepassing uitvoert, kunt u de [Azure Portal][portal] gebruiken om de eigenschappen van de taak en de bijbehorende taken weer te geven, of het gedeelde tekst bestand te downloaden dat door de taken van de taak is gewijzigd.

In de onderstaande scherm afbeelding ziet u de **Blade voorbereidings taken** in de Azure Portal na het uitvoeren van de voorbeeld toepassing. Ga naar de *JobPrepReleaseSampleJob* -eigenschappen nadat uw taken zijn voltooid (maar voordat u uw taak en groep hebt verwijderd) en klik op **voorbereidings taken** of **release taken** om de eigenschappen ervan weer te geven.

![Eigenschappen voor het voorbereiden van taken in Azure Portal][1]

## <a name="next-steps"></a>Volgende stappen
### <a name="application-packages"></a>Toepassingspakketten
Naast de taak voor het voorbereiden van taken, kunt u ook de functie [toepassings pakketten](batch-application-packages.md) van batch gebruiken om reken knooppunten voor te bereiden voor het uitvoeren van taken. Deze functie is vooral nuttig voor het implementeren van toepassingen waarvoor geen installatie programma wordt uitgevoerd, toepassingen die veel (100) bestanden bevatten of toepassingen waarvoor strikt versie beheer is vereist.

### <a name="installing-applications-and-staging-data"></a>Toepassingen en faserings gegevens installeren
Deze MSDN-forum post bevat een overzicht van verschillende methoden voor het voorbereiden van uw knoop punten voor het uitvoeren van taken:

[Toepassingen en faserings gegevens op batch Compute-knoop punten installeren][forum_post]

Dit wordt geschreven door een van de Azure Batch-team leden, maar er zijn verschillende technieken beschreven die u kunt gebruiken om toepassingen en gegevens te implementeren op reken knooppunten.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

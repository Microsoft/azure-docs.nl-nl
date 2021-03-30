---
title: Uitvoer gegevens persistent maken voor Azure Storage met .NET file conventies Library
description: Meer informatie over het gebruik van Azure Batch bestands conventies bibliotheek voor .NET om batch taken te behouden & taak uitvoer naar Azure Storage en de uitvoer in de Azure Portal te bekijken.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936959"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Taak-en taak gegevens persistent maken om te Azure Storage met de conventies bibliotheek voor batch bestanden voor .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Een manier om taak gegevens te behouden, is de [Azure batch bestands conventies bibliotheek voor .net][nuget_package]te gebruiken. De bestands conventies bibliotheek vereenvoudigt het proces van het opslaan van uitvoer gegevens van taken naar Azure Storage en het ophalen ervan. U kunt de bestands conventies bibliotheek in zowel de taak-als de client code &mdash; in de taak code gebruiken voor het persistent maken van bestanden en in de client code om deze weer te geven en op te halen. De taak code kan ook gebruikmaken van de bibliotheek om de uitvoer van upstream-taken op te halen, zoals in een scenario met [taak afhankelijkheden](batch-task-dependencies.md) .

Als u uitvoer bestanden wilt ophalen met behulp van de bestands conventies bibliotheek, kunt u de bestanden voor een bepaalde taak of taak vinden door deze te vermelden op ID en doel. U hoeft de namen of locaties van de bestanden niet te weten. U kunt bijvoorbeeld de bestands conventies bibliotheek gebruiken om alle tussenliggende bestanden voor een bepaalde taak weer te geven of een voorbeeld bestand voor een bepaalde taak te verkrijgen.

> [!TIP]
> Vanaf versie 2017-05-01 ondersteunt de batch-Service-API permanente uitvoer gegevens tot Azure Storage voor taken en taak beheer taken die worden uitgevoerd op Pools die zijn gemaakt met de configuratie van de virtuele machine. De batch-Service-API biedt een eenvoudige manier om uitvoer op te slaan vanuit de code die een taak maakt en fungeert als alternatief voor de bestands conventies bibliotheek. U kunt uw batch-client toepassingen aanpassen om de uitvoer te behouden, zonder dat u de toepassing hoeft bij te werken waarop de taak wordt uitgevoerd. Zie [taak gegevens persistent maken voor Azure Storage met de API van de batch-service](batch-task-output-files.md)voor meer informatie.

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Wanneer moet ik de bestands conventies bibliotheek gebruiken om de uitvoer van de taak te behouden?

Azure Batch biedt meer dan één manier om taak uitvoer te behouden. De bestands conventies zijn het meest geschikt voor deze scenario's:

- U kunt eenvoudig de code wijzigen voor de toepassing die door de taak wordt uitgevoerd om bestanden te behouden met behulp van de bestands conventies bibliotheek.
- U wilt gegevens streamen naar Azure Storage terwijl de taak nog actief is.
- U wilt gegevens persistent maken van groepen die zijn gemaakt met de Cloud service configuratie of de configuratie van de virtuele machine.
- Uw client toepassing of andere taken in de taak moeten uitvoer bestanden op basis van de ID of het doel van de taak zoeken en downloaden.
- U wilt de taak uitvoer weer geven in de Azure Portal.

Als uw scenario verschilt van wat hierboven wordt vermeld, moet u mogelijk een andere benadering overwegen. Zie voor meer informatie over andere opties voor het persistent maken van taak uitvoer de optie [persistente taak en taak uitvoer naar Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Wat zijn de conventies van het batch-bestand standaard?

De [conventies Standard van batch file](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) bevat een naamgevings schema voor de doel containers en BLOB-paden waarnaar uw uitvoer bestanden worden geschreven. Bestanden die zijn opgeslagen in Azure Storage die voldoen aan de bestands conventies standaard, zijn automatisch beschikbaar voor weer gave in de Azure Portal. De portal is op de hoogte van de naamgevings Conventie en kan daarom bestanden weer geven die eraan voldoen.

In de bestands conventies bibliotheek voor .NET worden de opslag containers en de uitvoer bestanden van de taak automatisch ingedeeld volgens de bestands conventies standaard. De bestands conventies bibliotheek bevat ook methoden voor het opvragen van uitvoer bestanden in Azure Storage volgens taak-ID, taak-ID of doel.

Als u een andere taal dan .NET ontwikkelt, kunt u de bestands conventies zelf in uw toepassing implementeren. Zie [de conventies van het batch-bestand implementeren](batch-task-output.md#implement-the-batch-file-conventions-standard)voor meer informatie.

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Een Azure Storage-account koppelen aan uw batch-account

Als u uitvoer gegevens wilt behouden voor Azure Storage met behulp van de bestands conventies bibliotheek, moet u eerst een Azure Storage account koppelen aan uw batch-account. Als u dit nog niet hebt gedaan, koppelt u een opslag account aan uw batch-account met behulp van de [Azure Portal](https://portal.azure.com):

1. Ga in Azure Portal naar uw Batch-account.
1. Selecteer **opslag account** onder **instellingen**.
1. Als u nog geen opslag account hebt gekoppeld aan uw batch-account, klikt u op **opslag account (geen)**.
1. Selecteer een opslag account in de lijst voor uw abonnement. Gebruik voor de beste prestaties een Azure Storage-account dat zich in dezelfde regio bevindt als het batch-account waarin uw taken worden uitgevoerd.

## <a name="persist-output-data"></a>Uitvoer gegevens behouden

Als u taak-en taak uitvoer gegevens wilt behouden met de bestands conventies bibliotheek, maakt u een container in Azure Storage en slaat u de uitvoer op in de container. Gebruik de [Azure Storage-client bibliotheek voor .net](https://www.nuget.org/packages/WindowsAzure.Storage) in uw taak code om de uitvoer van de taak naar de container te uploaden.

Zie [aan de slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor meer informatie over het werken met containers en blobs in azure Storage.

> [!WARNING]
> Alle taak-en taak uitvoer die persistent is gemaakt met de bestands conventies bibliotheek worden opgeslagen in dezelfde container. Als voor een groot aantal taken tegelijkertijd bestanden proberen te worden bewaard, kunnen Azure Storage beperkings limieten worden afgedwongen. Zie voor meer informatie over beperkings limieten de [controle lijst voor prestaties en schaal baarheid voor Blob Storage](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Opslagcontainer maken

Als u de taak uitvoer naar Azure Storage wilt behouden, moet u eerst een container maken door [eigenschap cloudjob][net_cloudjob]aan te roepen. [PrepareOutputStorageAsync][net_prepareoutputasync]. Deze extensie methode gebruikt een [Cloud Storage account][net_cloudstorageaccount] -object als para meter. Er wordt een container gemaakt met de naam volgens de bestands conventies standaard, zodat de inhoud detecteerbaar is voor de Azure Portal en de methoden voor het ophalen die verderop in het artikel worden beschreven.

Normaal gesp roken plaatst u de code voor het maken van een container in uw client toepassing &mdash; de toepassing die uw Pools, Jobs en taken maakt.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Taak uitvoer opslaan

Nu u een container in Azure Storage hebt voor bereid, kunnen taken uitvoer naar de container opslaan met behulp van de klasse [TaskOutputStorage][net_taskoutputstorage] die wordt gevonden in de bestands conventies bibliotheek.

In uw taak code maakt u eerst een [TaskOutputStorage][net_taskoutputstorage] -object. Vervolgens roept u de [TaskOutputStorage][net_taskoutputstorage]aan wanneer de taak is voltooid. Methode [SaveAsync][net_saveasync] om de uitvoer op te slaan in azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

De `kind` para meter van de [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ ](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) De methode SaveAsync categoriseert de persistente bestanden. Er zijn vier vooraf gedefinieerde [TaskOutputKind][net_taskoutputkind] typen: `TaskOutput` , `TaskPreview` , `TaskLog` , en `TaskIntermediate.` u kunt ook aangepaste uitvoer Categorieën definiëren.

Met deze uitvoer typen kunt u opgeven welk type uitvoer moet worden weer gegeven wanneer u later een query uitvoert op batch voor de persistente uitvoer van een bepaalde taak. Met andere woorden, als u de uitvoer van een taak vermeldt, kunt u de lijst filteren op een van de uitvoer typen. Bijvoorbeeld ' Geef mij de *Preview* -uitvoer voor taak *109*'. Meer informatie over het opnemen en ophalen van uitvoer wordt weer gegeven in de uitvoer ophalen verderop in het artikel.

> [!TIP]
> Het uitvoer type bepaalt ook waar in de Azure Portal een bepaald bestand wordt weer gegeven: *TaskOutput* gecategoriseerde bestanden worden weer gegeven onder taak **uitvoer bestanden** en *tasklog* -bestanden worden weer gegeven onder **taak logboeken**.

### <a name="store-job-outputs"></a>Taak uitvoer opslaan

Naast het opslaan van taak uitvoer kunt u de uitvoer van een volledige taak opslaan. In de samenvoeg taak van een film rendering-taak kunt u bijvoorbeeld de volledig gerenderde film persistent maken als taak uitvoer. Als uw taak is voltooid, kan uw client toepassing de uitvoer van de taak weer geven en ophalen en hoeft u niet de afzonderlijke taken te doorzoeken.

Sla de taak uitvoer op door de [JobOutputStorage][net_joboutputstorage]aan te roepen. Methode [SaveAsync][net_joboutputstorage_saveasync] en geef de [JobOutputKind][net_joboutputkind] en bestands naam op:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Net als bij het **TaskOutputKind** -type voor taak uitvoer, gebruikt u het type [JobOutputKind][net_joboutputkind] om de persistente bestanden van een taak te categoriseren. Met deze para meter kunt u later een specifiek type uitvoer opvragen (lijst). Het **JobOutputKind** -type bevat zowel uitvoer-als preview-categorieën, en biedt ondersteuning voor het maken van aangepaste categorieën.

### <a name="store-task-logs"></a>Taak logboeken opslaan

Naast het persistent maken van een bestand naar een duurzame opslag wanneer een taak of taak is voltooid, moet u mogelijk bestanden behouden die worden bijgewerkt tijdens de uitvoering van een taak &mdash; logboek bestand of `stdout.txt` `stderr.txt` , bijvoorbeeld. Voor dit doel biedt de Azure Batch bestands conventies bibliotheek de [TaskOutputStorage][net_taskoutputstorage]. Methode [SaveTrackedAsync][net_savetrackedasync] . Met [SaveTrackedAsync][net_savetrackedasync]kunt u updates bijhouden voor een bestand op het knoop punt (met een interval dat u opgeeft) en de updates persistent maken voor Azure Storage.

In het volgende code fragment gebruiken we [SaveTrackedAsync][net_savetrackedasync] om `stdout.txt` de 15 seconden een update Azure Storage uit te voeren tijdens de uitvoering van de taak:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

De sectie `Code to process data and produce output file(s)` met opmerkingen is een tijdelijke aanduiding voor de code die door de taak normaal zou worden uitgevoerd. U kunt bijvoorbeeld code hebben waarmee gegevens worden gedownload van Azure Storage en er trans formatie of berekening op wordt uitgevoerd. Het belang rijk onderdeel van dit fragment laat zien hoe u deze code in een blok kunt verpakken `using` om periodiek een bestand bij te werken met [SaveTrackedAsync][net_savetrackedasync].

De knooppunt agent is een programma dat wordt uitgevoerd op elk knoop punt in de pool en biedt de opdracht-en besturings interface tussen het knoop punt en de batch-service. De `Task.Delay` aanroep is aan het einde van dit `using` blok vereist om ervoor te zorgen dat de knooppunt agent tijd heeft om de inhoud van standaard uit te legen naar het stdout.txt-bestand op het knoop punt. Zonder deze vertraging is het mogelijk de laatste paar seconden van uitvoer te missen. Deze vertraging is mogelijk niet vereist voor alle bestanden.

> [!NOTE]
> Wanneer u bestands tracering inschakelt met **SaveTrackedAsync**, worden alleen *toegevoegde items toegevoegd* aan het bijgehouden bestand Azure Storage. Gebruik deze methode alleen voor het volgen van niet-roterende logboek bestanden of andere bestanden die zijn geschreven naar met toevoeg bewerkingen naar het einde van het bestand.

## <a name="retrieve-output-data"></a>Uitvoer gegevens ophalen

Wanneer u de persistente uitvoer ophaalt met behulp van de bestands conventies bibliotheek van Azure Batch, doet u dit op taak-en taak gerichte manier. U kunt de uitvoer voor de opgegeven taak of taak aanvragen zonder dat u het pad in Azure Storage of zelfs de bestands naam ervan hoeft te weten. In plaats daarvan kunt u uitvoer bestanden aanvragen op taak-of taak-ID.

In het volgende code fragment worden de taken van een taak herhaald, wordt er informatie over de uitvoer bestanden voor de taak afgedrukt en worden de bestanden vervolgens uit de opslag gedownload.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Uitvoer bestanden weer geven in de Azure Portal

Met de Azure Portal worden de uitvoer bestanden en-logboeken van de taak weer gegeven die zijn opgeslagen in een gekoppeld Azure Storage account met behulp van de [conventies standaard voor batch-bestanden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). U kunt deze conventies zelf implementeren in de taal van uw keuze, of u kunt de bestands conventies bibliotheek in uw .NET-toepassingen gebruiken.

Als u de weer gave van uw uitvoer bestanden in de portal wilt inschakelen, moet u voldoen aan de volgende vereisten:

1. Een Azure Storage-account koppelen aan uw batch-account.
1. Voldoen aan de vooraf gedefinieerde naamgevings conventies voor opslag containers en bestanden bij het persistent maken van uitvoer. U kunt de definitie van deze conventies vinden in het [Leesmij][github_file_conventions_readme]-bestand voor de bestands conventies bibliotheek. Als u de [Azure batch bestands conventies][nuget_package] bibliotheek gebruikt om uw uitvoer persistent te maken, worden uw bestanden bewaard volgens de standaard bestands conventies.

Als u de taak uitvoer bestanden en logboeken wilt weer geven in de Azure Portal, navigeert u naar de taak waarvan u de gewenste uitvoer wilt doen en klikt u vervolgens op **opgeslagen uitvoer bestanden** of **opgeslagen logboeken**. In deze afbeelding worden de **opgeslagen uitvoer bestanden** voor de taak met de id ' 007 ' weer gegeven:

![De Blade taak uitvoer in het Azure Portal][2]

## <a name="code-sample"></a>Codevoorbeeld

Het [PersistOutputs][github_persistoutputs] -voorbeeld project is een van de [Azure batch code voorbeelden][github_samples] op github. Deze Visual Studio-oplossing laat zien hoe u de Azure Batch bestands conventies bibliotheek kunt gebruiken om de uitvoer van een taak naar duurzame opslag te behouden. Voer de volgende stappen uit om het voor beeld uit te voeren:

1. Open het project in **Visual Studio 2019**.
2. Voeg uw batch-en opslag **account referenties** toe aan **AccountSettings. settings** in het Microsoft.Azure.Batch. samples. common project.
3. **Maak** de oplossing (maar voer deze niet uit). Herstel eventuele NuGet-pakketten als u hierom wordt gevraagd.
4. Gebruik de Azure Portal om een [toepassings pakket](batch-application-packages.md) te uploaden voor **PersistOutputsTask**. Neem de `PersistOutputsTask.exe` en de afhankelijke assembly's op in het zip-pakket, stel de toepassings-id in op ' PersistOutputsTask ' en de versie van het toepassings pakket op ' 1,0 '.
5. Het **PersistOutputs** -project **starten** (uitvoeren).
6. Wanneer u wordt gevraagd de persistentie technologie te kiezen die moet worden gebruikt voor het uitvoeren van het voor beeld, voert u **1** in om het voor beeld uit te voeren met behulp van de bestands conventies bibliotheek om de taak 

## <a name="next-steps"></a>Volgende stappen

### <a name="get-the-batch-file-conventions-library-for-net"></a>De conventies bibliotheek voor batch bestanden voor .NET ophalen

De conventies bibliotheek voor het batch-bestand voor .NET is beschikbaar op [NuGet][nuget_package]. De tape wisselaar breidt de klassen [eigenschap cloudjob][net_cloudjob] en [CloudTask][net_cloudtask] uit met nieuwe methoden. Zie ook de [referentie documentatie](/dotnet/api/microsoft.azure.batch.conventions.files) voor de bestands conventies bibliotheek.

De [bron code][github_file_conventions] voor de bestands conventies bibliotheek is beschikbaar op github in de Microsoft Azure SDK voor .net-opslag plaats. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Andere benaderingen verkennen voor het persistent maken van uitvoer gegevens

- Zie [persistente taak en taak uitvoer voor Azure Storage](batch-task-output.md) voor een overzicht van permanente taak-en taak gegevens.
- Zie [taak gegevens persistent maken voor Azure Storage met de batch-Service-API](batch-task-output-files.md) voor meer informatie over het gebruik van de API voor batch-service om uitvoer gegevens te behouden.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Opgeslagen uitvoer bestanden en opgeslagen logboeken-selectors in Portal"
[2]: ./media/batch-task-output/task-output-02.png "De Blade taak uitvoer in het Azure Portal"

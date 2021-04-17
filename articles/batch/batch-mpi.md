---
title: Taken met meerdere instanties gebruiken om MPI-toepassingen uit te voeren
description: Meer informatie over het uitvoeren Message Passing Interface (MPI) met behulp van het taaktype voor meerdere exemplaren in Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389397"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Taken met meerdere exemplaren gebruiken om Message Passing Interface (MPI)-toepassingen in Batch uit te voeren

Met taken met meerdere instanties kunt u een Azure Batch uitvoeren op meerdere rekenknooppunten tegelijk. Deze taken maken high performance computing-scenario's Message Passing Interface (MPI)-toepassingen in Batch mogelijk. In dit artikel leert u hoe u taken met meerdere exemplaren kunt uitvoeren met behulp van de [Batch .NET-bibliotheek.](/dotnet/api/microsoft.azure.batch)

> [!NOTE]
> Hoewel de voorbeelden in dit artikel gericht zijn op Batch .NET, MS-MPI en Windows-rekenknooppunten, zijn de hier besproken taakconcepten voor meerdere instanties van toepassing op andere platforms en technologieën (bijvoorbeeld Python en Intel MPI op Linux-knooppunten).

## <a name="multi-instance-task-overview"></a>Overzicht van taken met meerdere exemplaren

In Batch wordt elke taak normaal gesproken uitgevoerd op één reken knooppunt. U kunt meerdere taken naar een job verzenden en de Batch-service plant elke taak voor uitvoering op een knooppunt. Door echter de instellingen voor meerdere exemplaren van een taak te configureren, laat u Batch in plaats daarvan één primaire taak en verschillende subtaken maken die vervolgens op meerdere knooppunten worden uitgevoerd.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagram met een overzicht van instellingen voor meerdere exemplaren.":::

Wanneer u een taak met instellingen voor meerdere exemplaren naar een job indient, voert Batch verschillende stappen uit die uniek zijn voor taken met meerdere exemplaren:

1. De Batch-service maakt één **primaire** en verschillende **subtaken op** basis van de instellingen voor meerdere exemplaren. Het totale aantal taken (primair plus alle subtaken) komt overeen met het aantal instanties  (rekenknooppunten) dat u opgeeft in de instellingen voor meerdere instanties.
2. Batch wijst een van de rekenknooppunten aan als **de hoofdknooppunten** en plant de primaire taak die op de master moet worden uitgevoerd. De subtaken worden gepland om uit te voeren op de rest van de rekenknooppunten die zijn toegewezen aan de taak met meerdere instanties, één subtaak per knooppunt.
3. De primaire en alle subtaken downloaden alle **algemene resourcebestanden die** u opgeeft in de instellingen voor meerdere exemplaren.
4. Nadat de algemene bronbestanden zijn gedownload, voeren de  primaire en subtaken de coördinatieopdracht uit die u opgeeft in de instellingen voor meerdere exemplaren. De coördinatieopdracht wordt doorgaans gebruikt om knooppunten voor te bereiden voor het uitvoeren van de taak. Dit kan bestaan uit het starten van achtergrondservices (zoals die van [Microsoft MPI)](/message-passing-interface/microsoft-mpi) en het controleren of de knooppunten gereed zijn voor het verwerken van berichten tussen `smpd.exe` knooppunten.
5. De primaire taak voert de toepassingsopdracht  uit op het hoofd-knooppunt nadat de coördinatieopdracht is voltooid door de primaire en alle subtaken.  De toepassingsopdracht is de opdrachtregel van de taak met meerdere exemplaren zelf en wordt alleen uitgevoerd door de primaire taak. In een oplossing op basis [van MS-MPI](/message-passing-interface/microsoft-mpi) voert u hier uw MPI-toepassing uit met behulp van `mpiexec.exe` .

> [!NOTE]
> Hoewel deze functioneel uniek is, is de 'taak met meerdere exemplaren' geen uniek taaktype, zoals [starttask](/dotnet/api/microsoft.azure.batch.starttask) of [JobPreparationTask.](/dotnet/api/microsoft.azure.batch.jobpreparationtask) De taak met meerdere exemplaren is gewoon een standaard Batch-taak[(CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) in Batch .NET) waarvan de instellingen voor meerdere exemplaren zijn geconfigureerd. In dit artikel verwijzen we hier naar als de **taak met meerdere exemplaren.**

## <a name="requirements-for-multi-instance-tasks"></a>Vereisten voor taken met meerdere instanties

Voor taken met meerdere exemplaren is een pool vereist **waarvoor communicatie tussen knooppunts is** ingeschakeld en waarvoor **gelijktijdige taakuitvoering is uitgeschakeld.** Als u gelijktijdige taakuitvoering wilt uitschakelen, stelt u de eigenschap [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) in op 1.

> [!NOTE]
> Batch [beperkt](batch-quota-limit.md#pool-size-limits) de grootte van een pool met communicatie tussen knooppunt ingeschakeld.

Dit codefragment laat zien hoe u een pool maakt voor taken met meerdere exemplaren met behulp van de Batch .NET-bibliotheek.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Als u probeert een taak met meerdere exemplaren uit te voeren in een pool met communicatie tussen knooppunt uitgeschakeld of als de waarde *taskSlotsPerNode* groter is dan 1, wordt de taak nooit gepland. Deze blijft voor onbepaalde tijd in de status Actief.

### <a name="use-a-starttask-to-install-mpi"></a>Een StartTask gebruiken om MPI te installeren

Als u MPI-toepassingen wilt uitvoeren met een taak met meerdere exemplaren, moet u eerst een MPI-implementatie (bijvoorbeeld MS-MPI of Intel MPI) installeren op de rekenknooppunten in de pool. Dit is een goed moment om een [StartTask](/dotnet/api/microsoft.azure.batch.starttask)te gebruiken, die wordt uitgevoerd wanneer een knooppunt lid wordt van een pool of opnieuw wordt gestart. Met dit codefragment wordt een StartTask gemaakt die het MS-MPI-installatiepakket op specificeert als [een bronbestand.](/dotnet/api/microsoft.azure.batch.resourcefile) De opdrachtregel van de begintaak wordt uitgevoerd nadat het resourcebestand naar het knooppunt is gedownload. In dit geval voert de opdrachtregel een installatie zonder toezicht van MS-MPI uit.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remote Direct Memory Access (RDMA)

Wanneer u een [voor RDMA](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) geschikte grootte kiest, zoals A9 voor de rekenknooppunten in uw Batch-pool, kan uw MPI-toepassing profiteren van het RDMA-netwerk (Remote Direct Memory Access) met hoge prestaties en lage latentie van Azure.

Zoek naar de grootten die zijn opgegeven als 'geschikt voor RDMA' in Grootten voor virtuele [machines in Azure](../virtual-machines/sizes.md) (voor VirtualMachineConfiguration-pools) of Grootten voor [Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (voor CloudServicesConfiguration-pools).

> [!NOTE]
> Als u wilt profiteren van RDMA op [Linux-rekenknooppunten,](batch-linux-nodes.md)moet u **Intel MPI op** de knooppunten gebruiken.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Een taak met meerdere exemplaren maken met Batch .NET

Nu we de poolvereisten en de installatie van het MPI-pakket hebben behandeld, gaan we de taak met meerdere instanties maken. In dit fragment maken we een standaard [CloudTask en](/dotnet/api/microsoft.azure.batch.cloudtask)configureren we vervolgens de [eigenschap MultiInstanceSettings.](/dotnet/api/microsoft.azure.batch.cloudtask) Zoals eerder vermeld, is de taak met meerdere exemplaren geen afzonderlijk taaktype, maar een standaard Batch-taak die is geconfigureerd met instellingen voor meerdere exemplaren.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primaire taak en subtaken

Wanneer u de instellingen voor meerdere exemplaren voor een taak maakt, geeft u het aantal rekenknooppunten op dat de taak moet uitvoeren. Wanneer u de taak naar een job  verstuurt, maakt de Batch-service één primaire taak en voldoende **subtaken** die samen overeenkomen met het aantal knooppunten dat u hebt opgegeven.

Aan deze taken wordt een geheel getal-id toegewezen in het bereik van 0 *tot numberOfInstances* - 1. De taak met id 0 is de primaire taak en alle andere id's zijn subtaken. Als u bijvoorbeeld de volgende instellingen voor meerdere exemplaren voor een taak maakt, heeft de primaire taak de id 0 en de subtaken id's van 1 tot en met 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hoofd-knooppunt

Wanneer u een taak met meerdere exemplaren indient, wijst de Batch-service een van de rekenknooppunten aan als het hoofdknooppunt en wordt de primaire taak gepland voor uitvoering op het hoofdknooppunt. De subtaken worden gepland om uit te voeren op de rest van de knooppunten die zijn toegewezen aan de taak met meerdere instanties.

## <a name="coordination-command"></a>Coördinatieopdracht

De **coördinatieopdracht** wordt uitgevoerd door zowel de primaire als de subtaken.

De aanroep van de coördinatieopdracht blokkeert. Batch voert de toepassingsopdracht pas uit als de coördinatieopdracht voor alle subtaken is geretourneerd. De coördinatieopdracht moet daarom alle vereiste achtergrondservices starten, controleren of ze gereed zijn voor gebruik en vervolgens afsluiten. Met deze coördinatieopdracht voor een oplossing met MS-MPI versie 7 wordt bijvoorbeeld de SMPD-service op het knooppunt gestart en wordt vervolgens afgesloten:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Let op het gebruik `start` van in deze coördinatieopdracht. Dit is vereist omdat de `smpd.exe` toepassing niet direct na de uitvoering terugkeert. Zonder het gebruik van de startopdracht zou deze coördinatieopdracht niet worden uitgevoerd en wordt de toepassingsopdracht daarom geblokkeerd.

## <a name="application-command"></a>Toepassingsopdracht

Zodra de primaire taak en alle subtaken zijn voltooid met het uitvoeren van de coördinatieopdracht, wordt de opdrachtregel van de taak met meerdere exemplaren alleen uitgevoerd door de *primaire taak*. We noemen dit de **toepassingsopdracht om** deze te onderscheiden van de coördinatieopdracht.

Gebruik voor MS-MPI-toepassingen de toepassingsopdracht om uw MPI-toepassing uit te voeren met `mpiexec.exe` . Hier is bijvoorbeeld een toepassingsopdracht voor een oplossing met MS-MPI-versie 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Omdat ms-MPI de variabele standaard gebruikt `mpiexec.exe` `CCP_NODES` (zie [Omgevingsvariabelen),](#environment-variables)sluit de bovenstaande opdrachtregel van de voorbeeldtoepassing deze uit.

## <a name="environment-variables"></a>Omgevingsvariabelen

Batch maakt verschillende [omgevingsvariabelen die](batch-compute-node-environment-variables.md) specifiek zijn voor taken met meerdere instanties op de rekenknooppunten die zijn toegewezen aan een taak met meerdere instanties. Uw coördinatie- en toepassingsopdrachtregels kunnen verwijzen naar deze omgevingsvariabelen, evenals de scripts en programma's die ze uitvoeren.

De volgende omgevingsvariabelen worden gemaakt door de Batch-service voor gebruik door taken met meerdere instanties:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Zie Omgevingsvariabelen voor reken knooppunt voor volledige informatie over deze omgevingsvariabelen en de andere omgevingsvariabelen voor Batch-reken-knooppunt, inclusief de inhoud en [zichtbaarheid.](batch-compute-node-environment-variables.md)

> [!TIP]
> Het [Batch Linux MPI-codevoorbeeld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) bevat een voorbeeld van hoe verschillende van deze omgevingsvariabelen kunnen worden gebruikt.

## <a name="resource-files"></a>Resourcebestanden

Er zijn twee sets resourcebestanden waarmee u rekening moet  houden voor taken met meerdere exemplaren: algemene **resourcebestanden** die door alle taken  worden gedownload (zowel primaire als subtaken) en de **resourcebestanden** die zijn opgegeven voor de taak met meerdere exemplaren zelf, die alleen door de primaire taak wordt gedownload.

U kunt een of meer algemene **resourcebestanden opgeven** in de instellingen voor meerdere exemplaren voor een taak. Deze algemene bronbestanden worden gedownload van [Azure Storage](../storage/common/storage-introduction.md) door  de primaire en alle subtaken naar de gedeelde map van de taak van elk knooppunt. U kunt toegang krijgen tot de gedeelde map van de taak vanuit toepassings- en coördinatieopdrachtregels met behulp van de `AZ_BATCH_TASK_SHARED_DIR` omgevingsvariabele. Het pad is identiek op elk knooppunt dat is toegewezen aan de taak met meerdere exemplaren. U kunt dus één coördinatieopdracht delen tussen de primaire en alle `AZ_BATCH_TASK_SHARED_DIR` subtaken. Batch 'deelt' de map niet in het oogpunt van externe toegang, maar u kunt deze gebruiken als een bevestigings- of sharepunt zoals eerder vermeld in de tip over omgevingsvariabelen.

Resourcebestanden die u voor de taak met meerdere exemplaren zelf opgeeft, worden standaard gedownload naar de werkmap van `AZ_BATCH_TASK_WORKING_DIR` de taak, , . Zoals vermeld, downloadt alleen de primaire taak resourcebestanden die zijn opgegeven voor de taak met meerdere exemplaren zelf, in tegenstelling tot algemene resourcebestanden.

> [!IMPORTANT]
> Gebruik altijd de omgevingsvariabelen `AZ_BATCH_TASK_SHARED_DIR` en om te verwijzen naar deze `AZ_BATCH_TASK_WORKING_DIR` directories in de opdrachtregels. Probeer de paden niet handmatig te maken.

## <a name="task-lifetime"></a>Levensduur van taken

De levensduur van de primaire taak bepaalt de levensduur van de volledige taak met meerdere exemplaren. Wanneer de primaire wordt afgesloten, worden alle subtaken beëindigd. De afsluitende code van de primaire is de afsluitende code van de taak en wordt daarom gebruikt om te bepalen of de taak is geslaagd of mislukt voor nieuwe doeleinden.

Als een van de subtaken mislukt, wordt afgesloten met een retourcode die niet nul is, bijvoorbeeld de volledige taak met meerdere exemplaren mislukt. De taak met meerdere exemplaren wordt vervolgens beëindigd en opnieuw proberen, tot aan de limiet voor opnieuw proberen.

Wanneer u een taak met meerdere exemplaren verwijdert, worden de primaire taak en alle subtaken ook verwijderd door de Batch-service. Alle subtaken mappen en hun bestanden worden verwijderd uit de rekenknooppunten, net als voor een standaardtaak.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) voor een taak met meerdere exemplaren, zoals de eigenschappen [MaxTaskRetryCount,](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)en [RetentionTime,](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) worden gehonoreerd als ze voor een standaardtaak zijn en worden toegepast op de primaire en alle subtaken. Als u echter de eigenschapRetentionTime wijzigt nadat u de taak met meerdere exemplaren aan de taak hebt toegevoegd, wordt deze wijziging alleen toegepast op de primaire taak en blijven alle subtaken de oorspronkelijke RetentionTime gebruiken.

De recente takenlijst van een reken knooppunt weerspiegelt de id van een subtaak als de recente taak deel uitmaakte van een taak met meerdere exemplaren.

## <a name="obtain-information-about-subtasks"></a>Informatie over subtaken verkrijgen

Als u informatie over subtaken wilt verkrijgen met behulp van de Batch .NET-bibliotheek, roept u de [methode CloudTask.ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) aan. Deze methode retourneert informatie over alle subtaken en informatie over het reken-knooppunt dat de taken heeft uitgevoerd. Op basis van deze informatie kunt u de hoofdmap van elke subtaken, de pool-id, de huidige status, de afsluitende code en meer bepalen. U kunt deze informatie gebruiken in combinatie met de [methode PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) om de bestanden van de subtaken te verkrijgen. Houd er rekening mee dat deze methode geen informatie voor de primaire taak (id 0) retourneren.

> [!NOTE]
> Tenzij anders vermeld, zijn Batch .NET-methoden die worden toegepast op de [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) met meerdere exemplaren zelf alleen *van* toepassing op de primaire taak. Wanneer u bijvoorbeeld de methode [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) aanroept voor een taak met meerdere exemplaren, worden alleen de bestanden van de primaire taak geretourneerd.

In het volgende codefragment ziet u hoe u subtaken kunt verkrijgen en bestandsinhoud kunt aanvragen bij de knooppunten waarop ze hebben uitgevoerd.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Codevoorbeeld

In [het codevoorbeeld MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) op GitHub wordt gedemonstreerd hoe u een taak met meerdere exemplaren gebruikt om een [MS-MPI-toepassing](/message-passing-interface/microsoft-mpi) uit te voeren op Batch-rekenknooppunten. Volg de onderstaande stappen om het voorbeeld uit te voeren.

### <a name="preparation"></a>Voorbereiding

1. Download de [ms-MPI SDK- en Redist-installatieprogramma's](/message-passing-interface/microsoft-mpi) en installeer deze. Na de installatie kunt u controleren of de MS-MPI-omgevingsvariabelen zijn ingesteld.
1. Bouw een *releaseversie* van het MPIHelloWorld-voorbeeld-MPI-programma. [](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) Dit is het programma dat wordt uitgevoerd op rekenknooppunten door de taak met meerdere instanties.
1. Maak een ZIP-bestand met (dat u in stap 2 hebt gemaakt) en `MPIHelloWorld.exe` `MSMpiSetup.exe` (dat u in stap 1 hebt gedownload). In de volgende stap uploadt u dit zip-bestand als een toepassingspakket.
1. Gebruik de [Azure Portal](https://portal.azure.com) om een [Batch-toepassing](batch-application-packages.md) met de naam 'MPIHelloWorld' te maken en geef het zip-bestand dat u in de vorige stap hebt gemaakt op als versie 1.0 van het toepassingspakket. Zie [Toepassingen uploaden en beheren](batch-application-packages.md#upload-and-manage-applications) voor meer informatie.

> [!TIP]
> Het bouwen *van* een releaseversie van zorgt ervoor dat u geen extra afhankelijkheden (bijvoorbeeld of ) in uw `MPIHelloWorld.exe` `msvcp140d.dll` toepassingspakket hoeft op te `vcruntime140d.dll` nemen.

### <a name="execution"></a>Uitvoering

1. Download het [ZIP-bestand azure-batch-samples](https://github.com/Azure/azure-batch-samples/archive/master.zip) van GitHub.
1. Open de oplossing MultiInstanceTasks  in Visual Studio 2019. Het `MultiInstanceTasks.sln` oplossingsbestand bevindt zich in:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Voer de referenties voor uw Batch- en Storage-account `AccountSettings.settings` in het **Microsoft.Azure.Batch.Samples.Common-project** in.
1. **Bouw de oplossing** MultiInstanceTasks en voer deze uit om de MPI-voorbeeldtoepassing uit te voeren op rekenknooppunten in een Batch-pool.
1. *Optioneel:* gebruik de [Azure Portal](https://portal.azure.com) of [Batch Explorer](https://azure.github.io/BatchExplorer/) om de voorbeeldpool, taak en taak ('MultiInstanceSamplePool', 'MultiInstanceSampleJob', 'MultiInstanceSampleTask') te onderzoeken voordat u de resources verwijdert.

> [!TIP]
> U kunt [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) gratis downloaden als u nog geen Visual Studio.

De uitvoer `MultiInstanceTasks.exe` van is vergelijkbaar met het volgende:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [MPI-ondersteuning voor Linux op Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Meer informatie over het [maken van pools met Linux-rekenknooppunten](batch-linux-nodes.md) voor gebruik in uw Azure Batch MPI-oplossingen.

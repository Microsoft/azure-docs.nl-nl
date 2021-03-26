---
title: Taken met meerdere instanties gebruiken voor het uitvoeren van MPI-toepassingen
description: Meer informatie over het uitvoeren van MPI-toepassingen (Message Passing Interface) met het taak type meerdere instanties in Azure Batch.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 51fc580e0bb31e0e975c53b44887a5889a784eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605668"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Taken met meerdere instanties gebruiken voor het uitvoeren van MPI-toepassingen (Message Passing Interface) in batch

Met taken met meerdere instanties kunt u een Azure Batch taak op meerdere reken knooppunten tegelijk uitvoeren. Met deze taken worden High Performance Computing scenario's ingeschakeld, zoals MPI-toepassingen (Message Passing Interface) in batch. In dit artikel leert u hoe u taken met meerdere instanties kunt uitvoeren met behulp van de [batch .net](/dotnet/api/microsoft.azure.batch) -bibliotheek.

> [!NOTE]
> Hoewel de voor beelden in dit artikel gericht zijn op batch .NET-, MS-MPI-en Windows Compute-knoop punten, zijn de taak concepten voor meerdere instanties die hier worden besproken, van toepassing op andere platforms en technologieën (python en Intel MPI op Linux-knoop punten).

## <a name="multi-instance-task-overview"></a>Overzicht van taken met meerdere instanties

In batch wordt elke taak normaal gesp roken uitgevoerd op één Compute-knoop punt: u verzendt meerdere taken naar een taak en de batch-service plant elke taak voor uitvoering op een knoop punt. Door de **instellingen voor meerdere instanties** van een taak te configureren, vertelt u batch in plaats daarvan een primaire taak en verschillende subtaken die vervolgens op meerdere knoop punten worden uitgevoerd.

:::image type="content" source="media/batch-mpi/batch_mpi_01.png" alt-text="Diagram met een overzicht van instellingen voor meerdere instanties.":::

Wanneer u een taak met instellingen voor meerdere instanties naar een taak verzendt, voert batch verschillende stappen uit die uniek zijn voor taken met meerdere instanties:

1. Met de batch-service worden één **primaire** en verschillende **subtaken** gemaakt op basis van de instellingen voor meerdere instanties. Het totale aantal taken (primaire plus alle subtaken) komt overeen met het aantal **exemplaren** (reken knooppunten) dat u opgeeft in de instellingen voor meerdere instanties.
2. Met batch wordt een van de reken knooppunten aangeduid als de **Master** en wordt de primaire taak gepland om te worden uitgevoerd op de Master. De subtaken worden gepland om te worden uitgevoerd op de rest van de reken knooppunten die aan de taak met meerdere instanties zijn toegewezen, één subtaak per knoop punt.
3. De primaire en alle subtaken downloaden alle **algemene bron bestanden** die u opgeeft in de instellingen voor meerdere instanties.
4. Nadat de gemeen schappelijke bron bestanden zijn gedownload, voeren de primaire en subtaken de **coördinatie opdracht** uit die u in de instellingen voor meerdere instanties opgeeft. De coördinatie opdracht wordt doorgaans gebruikt om knoop punten voor het uitvoeren van de taak voor te bereiden. Dit kan onder andere het starten van de achtergrond Services zijn (zoals [micro soft mpi](/message-passing-interface/microsoft-mpi) `smpd.exe` ) en te controleren of de knoop punten gereed zijn voor het verwerken van berichten tussen knoop punten.
5. De primaire taak voert de **toepassing opdracht** uit op het hoofd knooppunt *nadat* de coördinatie opdracht met succes is voltooid door de primaire en alle subtaken. De opdracht toepassing is de opdracht regel van de taak voor meerdere exemplaren zelf en wordt alleen uitgevoerd door de primaire taak. In een op [MS-mpi](/message-passing-interface/microsoft-mpi) gebaseerde oplossing kunt u uw mpi-toepassing uitvoeren met `mpiexec.exe` .

> [!NOTE]
> Hoewel de taak ' meerdere instanties ' is, is dit geen uniek taak type zoals [StartTask](/dotnet/api/microsoft.azure.batch.starttask) of [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). De taak met meerdere instanties is gewoon een standaard batch taak ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) in batch .net) waarvan de instellingen voor meerdere instanties zijn geconfigureerd. In dit artikel verwijzen we naar deze **taak met meerdere exemplaren**.

## <a name="requirements-for-multi-instance-tasks"></a>Vereisten voor taken met meerdere instanties

Voor taken met meerdere instanties is een pool vereist waarvoor **communicatie tussen knoop punten is ingeschakeld** en waarvoor **gelijktijdige taak uitvoering is uitgeschakeld**. Als u het uitvoeren van gelijktijdige taken wilt uitschakelen, stelt u de eigenschap [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) in op 1.

> [!NOTE]
> Batch [beperkt](batch-quota-limit.md#pool-size-limits) de grootte van een pool waarvoor communicatie tussen knoop punten is ingeschakeld.

Dit code fragment laat zien hoe u een pool voor taken met meerdere instanties maakt met behulp van de batch .NET-bibliotheek.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Als u probeert een taak met meerdere instanties uit te voeren in een groep waarvoor communicatie tussen knoop punten is uitgeschakeld, of met een *taskSlotsPerNode* -waarde die groter is dan 1, wordt de taak nooit gepland, omdat deze voor onbepaalde tijd in de status actief blijft.

### <a name="use-a-starttask-to-install-mpi"></a>Een StartTask gebruiken om MPI te installeren

Als u MPI-toepassingen met een taak met meerdere exemplaren wilt uitvoeren, moet u eerst een MPI-implementatie (MS-MPI of Intel MPI) installeren op de reken knooppunten in de pool. Dit is een goed moment om een [StartTask](/dotnet/api/microsoft.azure.batch.starttask)te gebruiken, die wordt uitgevoerd wanneer een knoop punt wordt toegevoegd aan een pool of opnieuw wordt opgestart. Met dit code fragment maakt u een StartTask die het MS-MPI-installatie pakket opgeeft als een [resource bestand](/dotnet/api/microsoft.azure.batch.resourcefile). De opdracht regel van de begin taak wordt uitgevoerd nadat het bron bestand naar het knoop punt is gedownload. In dit geval voert de opdracht regel een installatie van MS-MPI zonder toezicht uit.

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

Wanneer u kiest voor een [grootte van RDMA-functionaliteit](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) , zoals A9 voor de reken knooppunten in uw batch-pool, kan uw mpi-toepassing profiteren van het snelle RDMA-netwerk met hoge prestaties van Azure met lage latentie.

Zoek naar de grootten die zijn opgegeven als ' RDMA capable ' in [grootten voor virtuele machines in azure](../virtual-machines/sizes.md) (voor VirtualMachineConfiguration-groepen) of [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (voor CloudServicesConfiguration-groepen).

> [!NOTE]
> Als u wilt profiteren van RDMA op [Linux-reken knooppunten](batch-linux-nodes.md), moet u **Intel mpi** gebruiken op de knoop punten.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Een taak met meerdere instanties maken met batch .NET

Nu we de groeps vereisten en de MPI-pakket installatie hebben behandeld, gaan we de taak voor meerdere instanties maken. In dit code fragment maakt u een standaard [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)en configureert u vervolgens de eigenschap [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) . Zoals eerder vermeld, is de taak met meerdere instanties geen verschillend taak type, maar een standaard batch taak die is geconfigureerd met instellingen voor meerdere instanties.

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

Wanneer u de instellingen voor meerdere instanties voor een taak maakt, geeft u het aantal reken knooppunten op waarmee de taak moet worden uitgevoerd. Wanneer u de taak bij een taak indient, maakt de batch-service één **primaire** taak en voldoende **subtaken** die samen overeenkomen met het aantal knoop punten dat u hebt opgegeven.

Aan deze taken wordt een geheel getal-ID toegewezen in het bereik van 0 tot *numberOfInstances* -1. De taak met ID 0 is de primaire taak en alle andere Id's zijn subtaken. Als u bijvoorbeeld de volgende instellingen voor meerdere instanties voor een taak maakt, is de primaire taak een ID van 0 en de subtaken hebben de Id's 1 tot en met 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Hoofd knooppunt

Wanneer u een taak met meerdere instanties verzendt, wijst de batch-service een van de reken knooppunten aan als het ' hoofd knooppunt ' en wordt de primaire taak gepland voor uitvoering op het hoofd knooppunt. De subtaken worden gepland om te worden uitgevoerd op de rest van de knoop punten die aan de taak met meerdere instanties zijn toegewezen.

## <a name="coordination-command"></a>Coördinatie opdracht

De **coördinatie opdracht** wordt uitgevoerd door de primaire en subtaken.

De aanroep van de coördinatie opdracht wordt geblokkeerd-batch voert de opdracht van de toepassing niet uit totdat de coördinatie opdracht voor alle subtaken geslaagd is. De coördinatie opdracht moet daarom de vereiste achtergrond Services starten, controleren of ze gereed zijn voor gebruik en vervolgens afsluiten. Met deze coördinatie opdracht voor een oplossing die gebruikmaakt van MS-MPI versie 7, wordt bijvoorbeeld de SMPD-service op het knoop punt gestart. vervolgens wordt het volgende afgesloten:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Let op het gebruik van `start` in deze coördinatie opdracht. Dit is vereist omdat de `smpd.exe` toepassing niet direct na de uitvoering wordt geretourneerd. Zonder het gebruik van de start opdracht, wordt deze coördinatie opdracht niet geretourneerd en wordt de toepassings opdracht daarom geblokkeerd.

## <a name="application-command"></a>Toepassings opdracht

Zodra de primaire taak en alle subtaken klaar zijn met het uitvoeren van de coördinatie opdracht, wordt de opdracht regel van de multi-instance taak *alleen* uitgevoerd door de primaire taak. We noemen deze opdracht om de **toepassing** te onderscheiden van de coördinatie opdracht.

Voor MS-MPI-toepassingen gebruikt u de opdracht toepassing om uw MPI-toepassing uit te voeren met `mpiexec.exe` . Dit is bijvoorbeeld een toepassings opdracht voor een oplossing met behulp van MS-MPI versie 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Omdat MS-MPI `mpiexec.exe` `CCP_NODES` standaard de variabele gebruikt (Zie [omgevings variabelen](#environment-variables)), sluit de voorbeeld toepassing opdracht regel hierboven uit.

## <a name="environment-variables"></a>Omgevingsvariabelen

Batch maakt verschillende [omgevings variabelen](batch-compute-node-environment-variables.md) die specifiek zijn voor taken met meerdere instanties op de reken knooppunten die zijn toegewezen aan een taak met meerdere exemplaren. Uw coördinatie-en toepassings opdracht regels kunnen verwijzen naar deze omgevings variabelen, evenals de scripts en Program ma's die ze uitvoeren.

De volgende omgevings variabelen worden gemaakt door de batch-service voor gebruik door taken met meerdere instanties:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Zie [omgevings variabelen voor reken knooppunten](batch-compute-node-environment-variables.md)voor volledige informatie over deze en de andere batch compute node-omgevings variabelen, inclusief de inhoud en zicht baarheid.

> [!TIP]
> Het [batch Linux mpi code sample](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) bevat een voor beeld van hoe u een aantal van deze omgevings variabelen kunt gebruiken.

## <a name="resource-files"></a>Bron bestanden

Er zijn twee sets bron bestanden voor het uitvoeren van taken met meerdere instanties: **algemene bron bestanden** die door *alle* taken worden gedownload (zowel primaire als subtaken), en de **bron bestanden** die zijn opgegeven voor de taak voor meerdere exemplaren zelf, waarbij *alleen de primaire* taak wordt gedownload.

U kunt een of meer **algemene bron bestanden** opgeven in de instellingen voor meerdere instanties voor een taak. Deze algemene bron bestanden worden gedownload van [Azure Storage](../storage/common/storage-introduction.md) naar de **gedeelde map** van elk knoop punt op basis van de primaire en alle subtaken. U kunt met behulp van de omgevings variabele toegang krijgen tot de gedeelde map van de toepassing en de coördinatie opdracht regels `AZ_BATCH_TASK_SHARED_DIR` . Het `AZ_BATCH_TASK_SHARED_DIR` pad is identiek op elk knoop punt dat aan de taak met meerdere instanties is toegewezen, zodat u één coördinatie opdracht tussen de primaire en alle subtaken kunt delen. Batch heeft geen ' deel ' van de map in een RAS-Sense, maar u kunt deze gebruiken als een koppelings-of share punt zoals eerder is vermeld in de tip op omgevings variabelen.

Bron bestanden die u opgeeft voor de taak voor meerdere exemplaren zelf, worden standaard gedownload naar de werkmap van de taak `AZ_BATCH_TASK_WORKING_DIR` . Zoals vermeld, in tegens telling tot algemene bron bestanden, worden met alleen de primaire taak bron bestanden gedownload die zijn opgegeven voor de taak met meerdere instanties zelf.

> [!IMPORTANT]
> Gebruik altijd de omgevings variabelen `AZ_BATCH_TASK_SHARED_DIR` en `AZ_BATCH_TASK_WORKING_DIR` Raadpleeg deze mappen in de opdracht regels. Probeer de paden niet hand matig te maken.

## <a name="task-lifetime"></a>Levens duur van taak

De levens duur van de primaire taak bepaalt de levens duur van de hele taak met meerdere exemplaren. Wanneer de primaire taak wordt afgesloten, worden alle subtaken beëindigd. De afsluit code van de primaire is de afsluit code van de taak en wordt daarom gebruikt om het slagen of mislukken van de taak te bepalen voor nieuwe pogingen.

Als een van de subtaken mislukt, wordt afgesloten met een retour code die niet gelijk is aan nul, bijvoorbeeld als de hele taak met meerdere exemplaren mislukt. De taak met meerdere exemplaren wordt vervolgens afgesloten en opnieuw geprobeerd, tot de limiet voor nieuwe pogingen is bereikt.

Wanneer u een taak met meerdere instanties verwijdert, worden ook de primaire en alle subtaken verwijderd door de batch-service. Alle subtaak mappen en de bijbehorende bestanden worden verwijderd uit de reken knooppunten, net als bij een standaard taak.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) voor een taak met meerdere instanties, zoals de eigenschappen [MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)en [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) , worden gehonoreerd als voor een standaard taak en gelden voor de primaire en alle subtaken. Als u echter de eigenschap theRetentionTime wijzigt nadat u de taak voor meerdere instanties hebt toegevoegd aan de taak, wordt deze wijziging alleen toegepast op de primaire taak en blijven alle subtaken de oorspronkelijke RetentionTime gebruiken.

De lijst met recente taken van een compute-knoop punt weerspiegelt de ID van een subtaak als de recente taak deel uitmaakt van een taak met meerdere exemplaren.

## <a name="obtain-information-about-subtasks"></a>Informatie over subtaken ophalen

Als u informatie over subtaken wilt ophalen met behulp van de batch .NET-bibliotheek, roept u de methode [CloudTask. ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) aan. Deze methode retourneert informatie over alle subtaken en informatie over het reken knooppunt waarmee de taken worden uitgevoerd. Vanuit deze informatie kunt u de hoofdmap van elke subtaak bepalen, de groeps-ID, de huidige status, afsluit code en meer. U kunt deze informatie in combi natie met de methode [pool Operations. GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) gebruiken om de bestanden van de subtaak op te halen. Houd er rekening mee dat met deze methode geen informatie wordt geretourneerd voor de primaire taak (ID 0).

> [!NOTE]
> Tenzij anders vermeld, zijn de batch .NET-methoden die worden toegepast op de multi-instance [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) zelf *alleen* van toepassing op de primaire taak. Wanneer u bijvoorbeeld de methode [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) aanroept voor een taak met meerdere instanties, worden alleen de bestanden van de primaire taak geretourneerd.

Het volgende code fragment laat zien hoe u informatie over subtaken kunt ophalen, evenals de inhoud van een aanvraag bestand van de knoop punten waarop ze zijn uitgevoerd.

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

Het [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) -code voorbeeld op github demonstreert hoe u een taak met meerdere instanties gebruikt om een [MS-mpi-](/message-passing-interface/microsoft-mpi) toepassing uit te voeren op batch Compute-knoop punten. Volg de onderstaande stappen om het voor beeld uit te voeren.

### <a name="preparation"></a>Voorbereiding

1. Down load de [MS-mpi SDK en redist installatie Programma's](/message-passing-interface/microsoft-mpi) en installeer deze. Na de installatie kunt u controleren of de variabelen van de MS-MPI-omgeving zijn ingesteld.
1. Bouw een *release* versie van het [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) -voor beeld MPI-programma. Dit is het programma dat wordt uitgevoerd op reken knooppunten door de taak met meerdere exemplaren.
1. Maak een zip-bestand met `MPIHelloWorld.exe` (dat u hebt gemaakt in stap 2) en `MSMpiSetup.exe` (die u hebt gedownload in stap 1). In de volgende stap uploadt u dit zip-bestand als een toepassings pakket.
1. Gebruik de [Azure Portal](https://portal.azure.com) om een batch- [toepassing](batch-application-packages.md) te maken met de naam ' MPIHelloWorld ' en geef het zip-bestand op dat u in de vorige stap hebt gemaakt als versie ' 1,0 ' van het toepassings pakket. Zie [toepassingen uploaden en beheren](batch-application-packages.md#upload-and-manage-applications) voor meer informatie.

> [!TIP]
> Het bouwen van een *release* versie van `MPIHelloWorld.exe` zorgt ervoor dat u geen aanvullende afhankelijkheden (bijvoorbeeld `msvcp140d.dll` of `vcruntime140d.dll` ) in uw toepassings pakket hoeft op te geven.

### <a name="execution"></a>Uitvoering

1. Down load het [zip-bestand voor Azure-batch-samples](https://github.com/Azure/azure-batch-samples/archive/master.zip) van github.
1. Open de MultiInstanceTasks- **oplossing** in Visual Studio 2019. Het `MultiInstanceTasks.sln` oplossings bestand bevindt zich in:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Voer uw batch-en Storage-account referenties in `AccountSettings.settings` in het **Microsoft.Azure.Batch. samples. common** project.
1. **Bouw en voer** de MultiInstanceTasks-oplossing uit om de voorbeeld toepassing mpi uit te voeren op reken knooppunten in een batch-pool.
1. *Optioneel*: gebruik de [Azure Portal](https://portal.azure.com) of [batch Explorer](https://azure.github.io/BatchExplorer/) om de voorbeeld groep, taak en taak ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") te controleren voordat u de resources verwijdert.

> [!TIP]
> Als u Visual Studio nog niet hebt, kunt u de [Visual Studio-Community](https://visualstudio.microsoft.com/vs/community/) gratis downloaden.

Uitvoer van `MultiInstanceTasks.exe` is vergelijkbaar met het volgende:

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

- Lees meer over [MPI-ondersteuning voor Linux op Azure batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Meer informatie over het [maken van Pools van Linux-reken knooppunten](batch-linux-nodes.md) voor gebruik in uw Azure batch mpi-oplossingen.

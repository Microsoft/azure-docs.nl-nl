---
title: Een parallelle workload uitvoeren met behulp van de .NET API
description: 'Zelfstudie: Mediabestanden parallel transcoderen met ffmpeg in Azure Batch met behulp van de clientbibliotheek Batch .NET'
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 396d0f6a2ef9a8c24fc92b641c889ef9e1a7df49
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578294"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Zelfstudie: een parallelle workload uitvoeren met Azure Batch met behulp van de .NET API

Met Azure Batch kunt u grootschalige parallelle en HPC-batchrekentaken (High Performance Computing) efficiënt uitvoeren in Azure. Deze zelfstudie leidt u door een C#-voorbeeld van het uitvoeren van een parallelle workload met behulp van Batch. U leert een gangbare Batch-toepassingswerkstroom en leert hoe u via programmacode werkt met Batch- en Storage-bronnen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een toepassingspakket toevoegen aan uw Batch-account
> * Verifiëren met Batch- en Storage-accounts
> * Invoerbestanden uploaden naar Storage
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Een job en taken maken om invoerbestanden te verwerken
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

In deze zelfstudie zet u MP4-mediabestanden parallel om in de MP3-indeling met behulp van het open-source hulpprogramma [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2017 of later](https://www.visualstudio.com/vs) of [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1) voor Linux, macOS of Windows.

* Een Batch-account en een gekoppeld Azure Storage-account. Raadpleeg de Batch-quickstarts met behulp van [Azure Portal](quick-create-portal.md) of [Azure CLI](quick-create-cli.md) voor instructies voor het maken van deze accounts.

* [64-bits Windows-versie van ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) (.zip). Download het zipbestand naar uw lokale computer. Voor deze zelfstudie hebt u alleen het zip-bestand nodig. U hoeft het bestand niet uit te pakken of lokaal te installeren.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="add-an-application-package"></a>Een toepassingspakket toevoegen

Gebruik de Azure Portal om ffmpeg als een [toepassingspakket](batch-application-packages.md) toe te voegen aan uw Batch-account. Toepassingspakketten helpen u bij het beheer van taaktoepassingen en de implementatie ervan in de rekenknooppunten in uw pool. 

1. Klik in de Azure Portal op **Meer services** > **Batch-accounts** en klik op de naam van uw Batch-account.
3. Klik op **Toepassingen** > **Toevoegen**.
4. Voer *ffmpeg* in voor **Toepassings-id** met een pakketversie van *4.3.1*. Selecteer het zip-bestand voor ffmpeg dat u eerder hebt gedownload en klik vervolgens op **OK**. Het toepassingspakket voor ffmpeg wordt toegevoegd aan uw Batch-account.

![Toepassingspakket toevoegen](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Het voorbeeld downloaden en uitvoeren

### <a name="download-the-sample"></a>Het voorbeeld downloaden

[Download of kloon de voorbeeld-app](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) vanuit GitHub. Als u de opslagplaats van de voorbeeld-app wilt klonen met een Git-client, gebruikt u de volgende opdracht:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Ga naar de map met het Visual Studio-oplossingsbestand `BatchDotNetFfmpegTutorial.sln`.

Open het oplossingsbestand in Visual Studio en werk de referentietekenreeksen in `Program.cs` bij met de waarden die u hebt verkregen voor uw accounts. Bijvoorbeeld:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Zorg er ook voor dat de verwijzing naar het toepassingspakket voor ffmpeg in de oplossing overeenkomt met de id en versie van het ffmpeg-pakket dat u hebt geüpload naar uw Batch-account.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>Het voorbeeldproject compileren en uitvoeren

Bouw de toepassing in Visual Studio en voer deze uit. U kunt ook de opdrachtregel gebruiken met de opdrachten `dotnet build` en `dotnet run`. Nadat de toepassing is uitgevoerd, bekijkt u de code voor meer informatie over wat elk onderdeel van de toepassing doet. Dit kan bijvoorbeeld als volgt in Visual Studio:

* Klik met de rechtermuisknop op de oplossing in Solution Explorer en klik op **Build Solution**. 

* Bevestig het herstel van alle NuGet-pakketten als dit wordt gevraagd. Als u ontbrekende pakketten wilt downloaden, zorgt u ervoor dat [NuGet-pakketbeheer](https://docs.nuget.org/consume/installing-nuget) is geïnstalleerd.

Voer dit pakketbeheer vervolgens uit. Wanneer u de voorbeeldtoepassing uitvoert, ziet de uitvoer van de console er ongeveer als volgt uit. Tijdens de uitvoering wordt bij `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` gewacht terwijl de rekenknooppunten van de pool worden gestart. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Ga naar uw Batch-account in de Azure Portal om de pool, rekenknooppunten, job en taken te controleren. Als u bijvoorbeeld een heatmap van de rekenknooppunten in uw pool wilt zien, klikt u op **Pools** > *WinFFmpegPool*.

Wanneer taken worden uitgevoerd, ziet de heatmap er bijvoorbeeld als volgt uit:

![Heatmap van pool](./media/tutorial-parallel-dotnet/pool.png)

Wanneer u de toepassing uitvoert in de standaardconfiguratie, bedraagt de uitvoeringstijd doorgaans ongeveer **10 minuten**. Het maken van de pool kost de meeste tijd.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>De code bekijken

In de volgende secties wordt de voorbeeldtoepassing uitgesplitst in de stappen die ermee worden uitgevoerd om een workload in de Batch-service te verwerken. Raadpleeg het bestand `Program.cs` in de oplossing terwijl u de rest van dit artikel leest, aangezien niet elke regel met code in het voorbeeld wordt besproken.

### <a name="authenticate-blob-and-batch-clients"></a>Blob- en Batch-clients verifiëren

Om te kunnen werken met het gekoppelde opslagaccount, gebruikt de app de Azure Storage-clientbibliotheek voor .NET. Er wordt een verwijzing naar het account gemaakt met [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount), waarbij verificatie met gedeelde sleutels wordt gebruikt. Vervolgens wordt een [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) gemaakt.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

De app maakt een [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)-object om pools, jobs en taken in de Batch-service te maken en te beheren. De Batch-client in het voorbeeld gebruikt verificatie op basis van gedeelde sleutels. Batch ondersteunt ook verificatie via [Azure Active Directory](batch-aad-auth.md) om afzonderlijke gebruikers of een toepassing zonder toezicht te verifiëren.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Invoerbestanden uploaden

De app geeft het object `blobClient` door aan de methode `CreateContainerIfNotExistAsync` om een opslagcontainer voor de invoerbestanden (MP4-indeling) en een container voor de taakuitvoer te maken.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Vervolgens worden bestanden geüpload naar de invoercontainer vanuit de lokale map `InputFiles`. De bestanden in de opslag zijn gedefinieerd als Batch [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile)-objecten die Batch later kan downloaden naar rekenknooppunten. 

Twee methoden in `Program.cs` worden gebruikt bij het uploaden van de bestanden:

* `UploadFilesToContainerAsync`: deze methode retourneert een verzameling ResourceFile-objecten en roept intern `UploadResourceFileToContainerAsync` aan om elk bestand te uploaden dat in de parameter `inputFilePaths` wordt doorgegeven.
* `UploadResourceFileToContainerAsync`: deze methode uploadt elk bestand als een blob naar de invoercontainer. Nadat het bestand is geüpload, haalt deze methode een Shared Access Signature (SAS) voor de blob op en retourneert de methode een ResourceFile-object dat het bestand vertegenwoordigt.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Voor meer informatie over het uploaden van bestanden als blobs naar een opslagaccount met .NET raadpleegt u [Blobs uploaden, downloaden en weergeven met behulp van .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Een pool van rekenknooppunten maken

Daarna wordt in het voorbeeld met behulp van een aanroep naar `CreatePoolIfNotExistAsync` een pool van rekenknooppunten in het Batch-account gemaakt. Deze gedefinieerde methode gebruikt de methode [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) om het aantal knooppunten, de VM-grootte en de poolconfiguratie in te stellen. Hier geeft het object [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) een [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) op naar een Windows Server-installatiekopie die is gepubliceerd in de Azure Marketplace. Batch ondersteunt diverse VM-installatiekopieën in de Azure Marketplace, evenals aangepaste VM-installatiekopieën.

Het aantal knooppunten en de VM-grootte worden ingesteld met behulp van gedefinieerde constanten. Batch ondersteunt toegewezen knooppunten en [knooppunten met een lage prioriteit](batch-low-pri-vms.md) en u kunt een of beide gebruiken in uw pools. Toegewezen rekenknooppunten zijn gereserveerd voor uw pool. Knooppunten met een lage prioriteit worden aangeboden tegen een lagere prijs en worden gehaald uit het overschot van de VM-capaciteit in Azure. Knooppunten met een lage prioriteit zijn niet beschikbaar als Azure onvoldoende capaciteit heeft. In het voorbeeld wordt standaard een pool met slechts 5 knooppunten met lage prioriteit gemaakt met de grootte *Standard_A1_v2*.

>[!Note]
>Controleer de quota van uw knooppunten. Zie [Quota en limieten voor Batch-service](batch-quota-limit.md#increase-a-quota) voor instructies over het maken van een quotumaanvraag.

De toepassing ffmpeg wordt geïmplementeerd in de rekenknooppunten door een [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) toe te voegen aan de poolconfiguratie.

Met de [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)-methode wordt de pool naar de Batch-service verzonden.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Een taak maken

Een Batch-taak (job) geeft een pool op die taken moet uitvoeren en optionele instellingen, zoals een prioriteit en planning voor het werk. In het voorbeeld wordt een job gemaakt met een aanroep naar `CreateJobAsync`. Deze gedefinieerde methode gebruikt de methode [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) om een taak te maken in uw pool.

Met de [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync)-methode wordt de taak naar de Batch-service verzonden. De Batch-taak heeft in eerste instantie geen taken.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Taken maken

In het voorbeeld worden taken in de job gemaakt met een aanroep van de methode `AddTasksAsync`, die een lijst met [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)-objecten maakt. Elke `CloudTask` voert ffmpeg uit om een `ResourceFile`-invoerobject met de eigenschap [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline) te verwerken. ffmpeg is eerder op elk knooppunt geïnstalleerd toen de pool werd gemaakt. Hier voert de opdrachtregel ffmpeg uit om elk MP4-invoerbestand (video) te converteren naar een MP3-bestand (audio).

Het voorbeeld maakt een [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile)-object voor het MP3-bestand nadat de opdrachtregel is uitgevoerd. De uitvoerbestanden van elke taak (één in dit geval) worden geüpload naar een container in het gekoppelde opslagaccount met behulp van de eigenschap [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) van de taak. In het codevoorbeeld werd voorheen een Shared Access Signature-URL (`outputContainerSasUrl`) verkregen om schrijftoegang te verlenen aan de uitvoercontainer. Schrijf de voorwaarden op die voor het `outputFile`-object zijn ingesteld. Een uitvoerbestand van een taak wordt alleen naar de container geüpload nadat de taak is voltooid (`OutputFileUploadCondition.TaskSuccess`). Zie het volledige [codevoorbeeld](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) op GitHub voor nadere implementatiedetails.

Vervolgens voegt het voorbeeld taken toe aan de taak met de methode [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync), die ze in de wachtrij plaatst voor uitvoering op de rekenknooppunten.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Taken controleren

Wanneer Batch taken toevoegt aan een job, plaatst de service ze automatisch in de wachtrij en plant de uitvoering ervan op rekenknooppunten in de gekoppelde pool. Op basis van de instellingen die u opgeeft, zal Batch alle taken in de wachtrij plaatsen, plannen en opnieuw proberen uit te voeren, en ook andere taakbeheertaken uitvoeren.

Er zijn veel manieren om de uitvoering van taken te controleren. In dit voorbeeld wordt de methode `MonitorTasks` gedefinieerd om alleen te rapporteren bij voltooiing en wanneer de taak de status mislukt of geslaagd heeft. De code `MonitorTasks` geeft een [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) op om efficiënt alleen minimale informatie over de taken te selecteren. Vervolgens wordt een [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) gemaakt die hulpprogramma's voor het controleren van taakstatussen biedt. In `MonitorTasks` wacht het voorbeeld totdat alle taken `TaskState.Completed` bereiken binnen een tijdslimiet. Vervolgens wordt de job beëindigd en worden taken gerapporteerd die zijn voltooid, maar waarbij een fout is opgetreden, zoals een afsluitcode die niet nul is.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat de taken zijn uitgevoerd, verwijdert de app automatisch de gemaakte invoeropslagcontainer en biedt u de mogelijkheid de Batch-pool en -taak te verwijderen. De klassen [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) en [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) van BatchClient hebben overeenkomstige verwijderingsmethoden, die worden aangeroepen wanneer u de verwijdering bevestigt. Hoewel jobs en taken zelf niet in rekening worden gebracht, worden rekenknooppunten wel in rekening gebracht. Daarom is het raadzaam om pools alleen toe te wijzen als dat nodig is. Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd. De uitvoerbestanden blijven echter aanwezig in het opslagaccount.

Verwijder de resourcegroep, het Batch-account en het opslagaccount wanneer u deze niet meer nodig hebt. Hiervoor selecteert u in Azure Portal de resourcegroep voor het Batch-account en klikt u op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een toepassingspakket toevoegen aan uw Batch-account
> * Verifiëren met Batch- en Storage-accounts
> * Invoerbestanden uploaden naar Storage
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Een job en taken maken om invoerbestanden te verwerken
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

Voor meer voorbeelden van het gebruik van de .NET API om Batch-workloads te plannen en te verwerken raadpleegt u de voorbeelden op GitHub.

> [!div class="nextstepaction"]
> [C#-voorbeelden voor Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)


Door de exemplaarvariabelen in te stellen op LowPriorityNodeCount = 0 en DedicatedNodeCount=5 is het probleem opgelost en kon de taak worden voltooid.

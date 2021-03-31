---
title: Uitvoer gegevens persistent maken voor Azure Storage met de API voor batch-service
description: Meer informatie over het gebruik van de batch-Service-API voor het persistent maken van batch taak-en taak uitvoer gegevens naar Azure Storage.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936925"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Taak gegevens persistent maken voor Azure Storage met de API voor de batch-service

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

De batch-Service-API ondersteunt het persistent maken van uitvoer gegevens naar Azure Storage voor taken en taak beheer taken die worden uitgevoerd op Pools met de configuratie van de virtuele machine. Wanneer u een taak toevoegt, kunt u een container opgeven in Azure Storage als doel voor de uitvoer van de taak. De batch-service schrijft vervolgens uitvoer gegevens naar die container wanneer de taak is voltooid.

Een voor deel van het gebruik van de batch-Service-API om taak uitvoer te behouden, is dat u de toepassing die de taak uitvoert niet hoeft te wijzigen. In plaats daarvan kunt u met een paar wijzigingen in uw client toepassing de uitvoer van de taak behouden vanuit dezelfde code die de taak maakt.

> [!IMPORTANT]
> Het persistent maken van taak gegevens naar Azure Storage met de API van de batch-service werkt niet met groepen die zijn gemaakt vóór [1 februari 2018](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204).

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Wanneer moet ik de batch-Service-API gebruiken om taak uitvoer te behouden?

Azure Batch biedt meer dan één manier om taak uitvoer te behouden. Het gebruik van de batch-Service-API is een handige benadering die het meest geschikt is voor deze scenario's:

- U wilt code schrijven om de taak uitvoer te behouden vanuit uw client toepassing, zonder de toepassing te wijzigen die door de taak wordt uitgevoerd.
- U wilt de uitvoer behouden van batch taken en taak beheer taken in Pools die zijn gemaakt met de configuratie van de virtuele machine.
- U wilt de uitvoer persistent maken naar een Azure Storage container met een wille keurige naam.
- U wilt uitvoer persistent maken naar een Azure Storage-container met de naam volgens de [conventies van het batch-bestand Standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files).

Als uw scenario verschilt van wat hierboven wordt vermeld, moet u mogelijk een andere benadering overwegen. De batch-Service-API biedt momenteel geen ondersteuning voor streaming-uitvoer naar Azure Storage terwijl de taak wordt uitgevoerd. Voor het streamen van uitvoer kunt u de conventies bibliotheek voor batch bestanden gebruiken die beschikbaar is voor .NET. Voor andere talen moet u uw eigen oplossing implementeren. Zie voor meer informatie over andere opties voor het persistent maken van taak uitvoer de optie [persistente taak en taak uitvoer naar Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Een container maken in Azure Storage

Als u de taak uitvoer naar Azure Storage wilt behouden, moet u een container maken die fungeert als bestemming voor uw uitvoer bestanden. Maak de container voordat u de taak uitvoert, bij voor keur voordat u uw taak verzendt. Als u de container wilt maken, gebruikt u de juiste Azure Storage-client bibliotheek of-SDK. Zie de [Azure Storage-documentatie](../storage/index.yml)voor meer informatie over Azure Storage-api's.

Als u bijvoorbeeld uw toepassing in C# schrijft, gebruikt u de Azure Storage- [client bibliotheek voor .net](https://www.nuget.org/packages/WindowsAzure.Storage/). In het volgende voor beeld ziet u hoe u een container maakt:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Een Shared Access Signature voor de container ophalen

Nadat u de container hebt gemaakt, kunt u een Shared Access Signature (SAS) met schrijf toegang tot de container ophalen. Een SAS biedt gedelegeerde toegang tot de container. De SAS verleent toegang met een opgegeven set machtigingen en over een opgegeven tijds interval. De batch-service heeft een SAS met schrijf machtigingen nodig om de taak uitvoer naar de container te schrijven. Zie [Shared Access signatures \( sa's gebruiken \) in azure Storage](../storage/common/storage-sas-overview.md)voor meer informatie over SAS.

Wanneer u een SAS krijgt met behulp van de Azure Storage Api's, retourneert de API een SAS-token teken reeks. Deze token teken reeks bevat alle para meters van de SA'S, met inbegrip van de machtigingen en het interval waarmee de SA'S geldig zijn. Als u de SAS wilt gebruiken om toegang te krijgen tot een container in Azure Storage, moet u de SAS-token teken reeks toevoegen aan de resource-URI. De bron-URI, samen met het toegevoegde SAS-token, biedt geverifieerde toegang tot Azure Storage.

In het volgende voor beeld ziet u hoe u een SAS-token teken reeks voor alleen-schrijven kunt ophalen voor de container. vervolgens voegt u de SAS toe aan de container-URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Uitvoer bestanden opgeven voor taak uitvoer

Als u uitvoer bestanden voor een taak wilt opgeven, maakt u een verzameling [uitvoer](/dotnet/api/microsoft.azure.batch.outputfile) bare objecten en wijst u deze toe aan de eigenschap [CloudTask. OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) wanneer u de taak maakt.

In het volgende voor beeld van C#-code wordt een taak gemaakt die wille keurige getallen schrijft naar een bestand met de naam `output.txt` . In het voor beeld wordt een uitvoer bestand gemaakt `output.txt` dat naar de container wordt geschreven. In het voor beeld worden ook uitvoer bestanden gemaakt voor alle logboek bestanden die overeenkomen met het bestands patroon `std*.txt` (_bijvoorbeeld_, `stdout.txt` en `stderr.txt` ). De container-URL vereist de SA'S die eerder voor de container zijn gemaakt. De batch-service gebruikt de SAS om toegang tot de container te verifiëren:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Als u dit voor beeld gebruikt met Linux, moet u ervoor zorgen dat u de backslashes wijzigt in slashes.

### <a name="specify-a-file-pattern-for-matching"></a>Een bestands patroon opgeven voor het zoeken

Wanneer u een uitvoer bestand opgeeft, kunt u de eigenschap [output file. FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) gebruiken om een bestands patroon op te geven voor de overeenkomst. Het bestands patroon kan overeenkomen met nul bestanden, één bestand of een set bestanden die door de taak worden gemaakt.

De eigenschap **FilePattern** ondersteunt standaard bestandssysteem joker tekens, zoals `*` (voor niet-recursieve overeenkomsten) en `**` (voor recursieve overeenkomsten). Het bovenstaande code voorbeeld geeft bijvoorbeeld aan dat het bestand patroon `std*.txt` niet-recursief moet overeenkomen:

`filePattern: @"..\std*.txt"`

Als u één bestand wilt uploaden, geeft u een bestands patroon zonder joker tekens op. In het bovenstaande code voorbeeld wordt bijvoorbeeld het bestands patroon aangegeven dat moet overeenkomen `output.txt` :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Een upload voorwaarde opgeven

De eigenschap [OutputFileUploadOptions. UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) staat het voorwaardelijk uploaden van uitvoer bestanden toe. Een veelvoorkomend scenario is het uploaden van één set bestanden als de taak slaagt en een andere set bestanden als deze mislukt. Het is bijvoorbeeld mogelijk dat u alleen uitgebreide logboek bestanden wilt uploaden wanneer de taak mislukt en wordt afgesloten met een afsluit code die niet gelijk is aan nul. Het is ook mogelijk dat u alleen resultaat bestanden wilt uploaden als de taak is voltooid, omdat deze bestanden ontbreken of onvolledig zijn als de taak mislukt.

In het bovenstaande code voorbeeld wordt de eigenschap **UploadCondition** ingesteld op **TaskCompletion**. Met deze instelling geeft u op dat het bestand moet worden geüpload nadat de taken zijn voltooid, ongeacht de waarde van de afsluit code.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Zie [OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) Enum voor andere instellingen.

### <a name="disambiguate-files-with-the-same-name"></a>Dubbel zinnigheid-bestanden met dezelfde naam

De taken in een taak kunnen bestanden met dezelfde naam opleveren. `stdout.txt`En `stderr.txt` worden bijvoorbeeld gemaakt voor elke taak die in een taak wordt uitgevoerd. Omdat elke taak in een eigen context wordt uitgevoerd, conflicteren deze bestanden niet met het bestands systeem van het knoop punt. Wanneer u echter bestanden van meerdere taken naar een gedeelde container uploadt, moet u bestanden met dezelfde naam dubbel zinnigheid.

De eigenschap [OutputFileBlobContainerDestination. Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) specificeert de doel-BLOB of de virtuele map voor uitvoer bestanden. U kunt de eigenschap **Path** gebruiken om de BLOB of virtuele map zo een naam te bieden dat uitvoer bestanden met dezelfde naam uniek zijn genoemd in azure Storage. Het gebruik van de taak-ID in het pad is een goede manier om unieke namen te garanderen en bestanden eenvoudig te identificeren.

Als de eigenschap **FilePattern** is ingesteld op een Joker teken expressie, worden alle bestanden die overeenkomen met het patroon geüpload naar de virtuele map die is opgegeven door de eigenschap **Path** . Als de container bijvoorbeeld de `mycontainer` taak-id is, `mytask` en het bestands patroon is `..\std*.txt` , zijn de absolute uri's van de uitvoer bestanden in azure Storage vergelijkbaar met:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Als de eigenschap **FilePattern** is ingesteld op een enkele bestands naam, wat betekent dat deze geen joker tekens bevat, wordt met de waarde van de eigenschap **Path** de volledige naam van de BLOB opgegeven. Als u verwacht dat de naam in conflict is met één bestand uit meerdere taken, neemt u de namen van de virtuele map op als onderdeel van de bestands naam om die bestanden te dubbel zinnigheid. Stel bijvoorbeeld de eigenschap **Path** in op bevat de taak-id, het scheidings teken (meestal een slash) en de bestands naam:

`path: taskId + @"/output.txt"`

De absolute Uri's van de uitvoer bestanden voor een reeks taken zijn vergelijkbaar met:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Zie [de blobs in een container](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)weer geven voor meer informatie over virtuele mappen in azure Storage.

## <a name="diagnose-file-upload-errors"></a>Fouten bij het uploaden van bestanden vaststellen

Als het uploaden van uitvoer bestanden naar Azure Storage mislukt, wordt de taak verplaatst naar de status **voltooid** en wordt de eigenschap [TaskExecutionInformation. FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) ingesteld. Controleer de eigenschap **FailureInformation** om te bepalen welke fout is opgetreden. Dit is bijvoorbeeld een fout die optreedt bij het uploaden van bestanden als de container niet kan worden gevonden:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Bij elke upload van het bestand schrijft batch twee logboek bestanden naar het reken knooppunt `fileuploadout.txt` en `fileuploaderr.txt` . U kunt deze logboek bestanden controleren om meer te weten te komen over een specifieke fout. In gevallen waarin de upload van het bestand nooit is geslaagd, bijvoorbeeld omdat de taak zelf niet kan worden uitgevoerd, zijn deze logboek bestanden niet aanwezig.

## <a name="diagnose-file-upload-performance"></a>Prestaties van het uploaden van bestanden vaststellen

De `fileuploadout.txt` voortgang van het uploaden van bestands Logboeken. U kunt dit bestand bekijken voor meer informatie over hoe lang het uploaden van uw bestand tot gevolg heeft. Houd er rekening mee dat er veel factoren zijn bij het uploaden van de prestaties, zoals de grootte van het knoop punt, andere activiteiten op het knoop punt op het moment van de upload, of de doel container zich in dezelfde regio bevindt als de batch-pool, hoeveel knoop punten er tegelijkertijd worden geüpload naar het opslag account, enzovoort.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>De batch-Service-API gebruiken met de conventies van het batch-bestand Standard

Wanneer u de taak uitvoer persistent maakt met de API van de batch-service, kunt u uw doel container en blobs naar wens benoemen. U kunt er ook voor kiezen om deze een naam te geven op basis van de [conventies standaard voor batch-bestanden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). De file conventies Standard bepaalt de namen van de doel container en Blob in Azure Storage voor een bepaald uitvoer bestand op basis van de namen van de taak en de taak. Als u de bestands conventies standaard gebruikt voor de naamgeving van uitvoer bestanden, zijn uw uitvoer bestanden beschikbaar voor weer gave in de [Azure Portal](https://portal.azure.com).

Als u in C# ontwikkelt, kunt u de methoden gebruiken die zijn ingebouwd in de [Conventie bibliotheek voor batch bestanden voor .net](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Deze bibliotheek maakt de juiste namen voor containers en blobs voor u. U kunt bijvoorbeeld de API aanroepen om de juiste naam voor de container op te halen, op basis van de taak naam:

```csharp
string containerName = job.OutputStorageContainerName();
```

U kunt de methode [CloudJobExtensions. GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) gebruiken om een SAS-URL (Shared Access Signature) te retour neren die wordt gebruikt om naar de container te schrijven. U kunt deze SAS vervolgens door geven aan de [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) -constructor.

Als u in een andere taal dan C# ontwikkelt, moet u de bestands conventies standaard zelf implementeren.

## <a name="code-sample"></a>Codevoorbeeld

Het [PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) -voorbeeld project is een van de [Azure batch code voorbeelden](https://github.com/Azure/azure-batch-samples) op github. In deze Visual Studio-oplossing ziet u hoe u de batch-client bibliotheek voor .NET gebruikt om de uitvoer van taken naar duurzame opslag te behouden. Voer de volgende stappen uit om het voor beeld uit te voeren:

1. Open het project in **Visual Studio 2019**.
2. Voeg uw batch-en opslag **account referenties** toe aan **AccountSettings. settings** in het Microsoft.Azure.Batch. samples. common project.
3. **Maak** de oplossing (maar voer deze niet uit). Herstel eventuele NuGet-pakketten als u hierom wordt gevraagd.
4. Gebruik de Azure Portal om een [toepassings pakket](batch-application-packages.md) te uploaden voor **PersistOutputsTask**. Neem de `PersistOutputsTask.exe` en de afhankelijke assembly's op in het zip-pakket, stel de toepassings-id in op ' PersistOutputsTask ' en de versie van het toepassings pakket op ' 1,0 '.
5. Het **PersistOutputs** -project **starten** (uitvoeren).
6. Wanneer u wordt gevraagd de persistentie technologie te kiezen die moet worden gebruikt voor het uitvoeren van het voor beeld, voert u **2** in om het voor beeld uit te voeren met de API van de batch-service om de taak uitvoer
7. Voer, indien gewenst, het voor beeld opnieuw uit, geef **3** op voor persistente uitvoer met de batch-Service-API en geef ook de naam van de doel container en het BLOB op volgens de bestands conventies standaard.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over het persistent maken van taak uitvoer met de bestands conventies bibliotheek voor .NET raadpleegt [u taak-en taak gegevens persistent maken voor Azure Storage met de conventies bibliotheek voor batch bestanden voor .net](batch-task-output-file-conventions.md).
- Zie voor meer informatie over andere benaderingen voor het persistent maken van uitvoer gegevens in Azure Batch [persistente taak-en taak uitvoer naar Azure Storage](batch-task-output.md).

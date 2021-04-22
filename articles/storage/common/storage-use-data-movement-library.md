---
title: Gegevens overdragen met de bibliotheek voor gegevensoverdracht voor .NET
titleSuffix: Azure Storage
description: Gebruik de bibliotheek voor gegevensverkeer om gegevens te verplaatsen naar of te kopiëren van blob- en bestandsinhoud. Kopieer gegevens naar Azure Storage lokale bestanden of kopieer gegevens binnen of tussen opslagaccounts. Migreert uw gegevens eenvoudig naar Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875931"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Gegevens overdragen met de bibliotheek voor gegevensverplaatsing

De Azure Storage Data Movement-bibliotheek is een platformoverschrijdende open source-bibliotheek die is ontworpen voor het uploaden, downloaden en kopiëren van blobs en bestanden met hoge prestaties. De bibliotheek voor gegevensverkeer biedt handige methoden die niet beschikbaar zijn in de Azure Storage-clientbibliotheek voor .NET. Deze methoden bieden de mogelijkheid om het aantal parallelle bewerkingen in te stellen, de voortgang van de overdracht bij te houden, eenvoudig een geannuleerde overdracht te hervatten en nog veel meer.

Deze bibliotheek maakt ook gebruik van .NET Core, wat betekent dat u deze kunt gebruiken bij het bouwen van .NET-apps voor Windows, Linux en macOS. Raadpleeg de .NET Core-documentatie voor meer informatie over [.NET Core.](https://dotnet.github.io/) Deze bibliotheek werkt ook voor traditionele .NET Framework apps voor Windows.

In dit document wordt gedemonstreerd hoe u een .NET Core-consoletoepassing maakt die wordt uitgevoerd op Windows, Linux en macOS en de volgende scenario's uitvoert:

- Upload bestanden en mappen naar Blob Storage.
- Definieer het aantal parallelle bewerkingen bij het overdragen van gegevens.
- De voortgang van de gegevensoverdracht bijhouden.
- Hervat geannuleerde gegevensoverdracht.
- Kopieer het bestand van de URL naar Blob Storage.
- Kopieer van Blob Storage naar Blob Storage.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- Een [Azure-opslagaccount](storage-account-create.md)

## <a name="setup"></a>Instellen

1. Ga naar [de .NET Core-installatiehandleiding](https://dotnet.microsoft.com/download) om de installatie van de .NET Core SDK. Wanneer u uw omgeving selecteert, kiest u de opdrachtregeloptie.
2. Maak vanaf de opdrachtregel een map voor uw project. Navigeer naar deze map en typ om `dotnet new console -o <sample-project-name>` een C#-consoleproject te maken.
3. Open deze map in Visual Studio Code. Deze stap kan snel worden uitgevoerd via de opdrachtregel door `code .` in Windows te typen.
4. Installeer de [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) vanuit de Visual Studio Code Marketplace. Start Visual Studio Code opnieuw.
5. Op dit moment ziet u twee prompts. Een is voor het toevoegen van 'vereiste assets om te bouwen en fouten op te sporen'. Klik op Ja. Een andere prompt is om niet-opgeloste afhankelijkheden te herstellen. Klik op Herstellen.
6. Wijzig `launch.json` onder om een externe terminal als console te `.vscode` gebruiken. Deze instelling moet worden gelezen als `"console": "externalTerminal"`
7. Visual Studio Code kunt u fouten opsporen in .NET Core-toepassingen. Druk `F5` op om uw toepassing uit te voeren en te controleren of uw installatie werkt. U ziet 'Hallo wereld!' wordt afgedrukt naar de console.

## <a name="add-the-data-movement-library-to-your-project"></a>De bibliotheek voor gegevensverkeer toevoegen aan uw project

1. Voeg de nieuwste versie van de bibliotheek voor gegevensverkeer toe aan de `dependencies` sectie van uw `<project-name>.csproj` bestand. Op het moment van schrijven zou deze versie `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Er wordt een prompt weergegeven om uw project te herstellen. Klik op de knop Herstellen. U kunt uw project ook herstellen vanaf de opdrachtregel door de opdracht in de hoofdmap van uw `dotnet restore` projectmap te typen.

`<project-name>.csproj`Wijzigen:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Het geraamte van uw toepassing instellen

Het eerste wat we doen, is de 'skeleton'-code van onze toepassing instellen. Deze code vraagt ons om een opslagaccountnaam en accountsleutel en gebruikt deze referenties om een -object te `CloudStorageAccount` maken. Dit object wordt gebruikt voor interactie met ons opslagaccount in alle overdrachtsscenario's. De code vraagt ons ook om het type overdrachtsbewerking te kiezen dat we willen uitvoeren.

`Program.cs`Wijzigen:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Een lokaal bestand uploaden naar een blob

Voeg de methoden `GetSourcePath` en `GetBlob` toe aan `Program.cs` :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Wijzig de `TransferLocalFileToAzureBlob` methode:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Deze code vraagt ons om het pad naar een lokaal bestand, de naam van een nieuwe of bestaande container en de naam van een nieuwe blob. De `TransferManager.UploadAsync` methode voert het uploaden uit met behulp van deze informatie.

Druk op `F5` om uw toepassing uit te voeren. U kunt controleren of het uploaden heeft plaatsgevonden door uw opslagaccount weer te [Microsoft Azure Storage Explorer.](https://storageexplorer.com/)

## <a name="set-the-number-of-parallel-operations"></a>Het aantal parallelle bewerkingen instellen

Een van de functies van de bibliotheek voor gegevensverplaatsing is de mogelijkheid om het aantal parallelle bewerkingen in te stellen om de doorvoer van gegevensoverdracht te verhogen. De bibliotheek voor gegevensverkeer stelt standaard het aantal parallelle bewerkingen in op 8 * het aantal kernen op uw computer.

Houd er rekening mee dat veel parallelle bewerkingen in een omgeving met lage bandbreedte de netwerkverbinding kunnen overbelasten en dat bewerkingen niet volledig kunnen worden uitgevoerd. U moet experimenteren met deze instelling om te bepalen wat het beste werkt op basis van uw beschikbare netwerkbandbreedte.

Laten we code toevoegen waarmee we het aantal parallelle bewerkingen kunnen instellen. We gaan ook code toevoegen die tijd in beslag neemt voordat de overdracht is voltooid.

Voeg een `SetNumberOfParallelOperations` methode toe aan `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Wijzig de `ExecuteChoice` methode om te `SetNumberOfParallelOperations` gebruiken:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Wijzig de `TransferLocalFileToAzureBlob` methode om een timer te gebruiken:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Voortgang van overdracht bijhouden

Het is handig om te weten hoe lang het duurde voordat de gegevens zijn overdragen. Het zou echter nog beter zijn om de voortgang van de *overdracht* te zien tijdens de overdrachtsbewerking. Voor dit scenario moeten we een `TransferContext` -object maken. Het `TransferContext` object is er in twee vormen: en `SingleTransferContext` `DirectoryTransferContext` . De eerste is voor het overdragen van één bestand en het tweede voor het overdragen van een map met bestanden.

Voeg de methoden `GetSingleTransferContext` en `GetDirectoryTransferContext` toe aan `Program.cs` :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Wijzig de `TransferLocalFileToAzureBlob` methode om te `GetSingleTransferContext` gebruiken:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Een geannuleerde overdracht hervatten

Een andere handige functie van de bibliotheek voor gegevensverkeer is de mogelijkheid om een geannuleerde overdracht te hervatten. We gaan code toevoegen waarmee we de overdracht tijdelijk kunnen annuleren door te typen en de overdracht 3 seconden `c` later te hervatten.

`TransferLocalFileToAzureBlob`Wijzigen:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Tot nu toe is onze `checkpoint` waarde altijd ingesteld op `null` . Als we nu de overdracht annuleren, halen we het laatste controlepunt van de overdracht op en gebruiken we dit nieuwe controlepunt in onze overdrachtscontext.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Een lokale map overdragen naar Blob Storage

Het zou erg zijn als de bibliotheek voor gegevensoverdracht slechts één bestand tegelijk kan overdragen. Gelukkig is dit niet het geval. De bibliotheek voor gegevensverplaatsing biedt de mogelijkheid om een map met bestanden en alle subdirectory's ervan over te dragen. Laten we code toevoegen waarmee we dat kunnen doen.

Voeg eerst de methode toe `GetBlobDirectory` aan `Program.cs` :

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Wijzig vervolgens `TransferLocalDirectoryToAzureBlobDirectory` :

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Er zijn enkele verschillen tussen deze methode en de methode voor het uploaden van één bestand. We gebruiken nu en `TransferManager.UploadDirectoryAsync` de methode die we eerder hebben `getDirectoryTransferContext` gemaakt. Daarnaast geven we nu een waarde op voor de uploadbewerking, waarmee we kunnen aangeven dat `options` we subdirectorieën in onze upload willen opnemen.

## <a name="copy-a-file-from-url-to-a-blob"></a>Een bestand kopiëren van de URL naar een blob

Nu gaan we code toevoegen waarmee we een bestand van een URL naar een Azure Blob kunnen kopiëren.

`TransferUrlToAzureBlob`Wijzigen:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Een belangrijk gebruiksgeval voor deze functie is wanneer u gegevens van een andere cloudservice (bijvoorbeeld AWS) naar Azure moet verplaatsen. Zolang u een URL hebt die u toegang geeft tot de resource, kunt u die resource eenvoudig verplaatsen naar Azure Blobs met behulp van de `TransferManager.CopyAsync` methode . Deze methode introduceert ook een nieuwe Booleaanse parameter. Als u deze parameter instelt op , geeft dit aan dat `true` we een asynchrone kopie aan de serverzijde willen maken. Als u deze parameter instelt op , wordt een synchrone kopie aangegeven. Dit betekent dat de resource eerst naar de lokale computer wordt gedownload en vervolgens wordt geüpload `false` naar Azure Blob. Synchrone kopie is momenteel echter alleen beschikbaar voor het kopiëren van de ene Azure Storage resource naar de andere.

## <a name="copy-a-blob"></a>Een blob kopiëren

Een andere functie die uniek wordt geboden door de bibliotheek voor gegevensverkeer, is de mogelijkheid om te kopiëren van de ene Azure Storage resource naar de andere.

`TransferAzureBlobToAzureBlob`Wijzigen:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

In dit voorbeeld stellen we de Booleaanse parameter in in om aan te `TransferManager.CopyAsync` geven dat we een `false` synchrone kopie willen maken. Dit betekent dat de resource eerst wordt gedownload naar onze lokale computer en vervolgens wordt geüpload naar Azure Blob. De synchrone kopieeroptie is een uitstekende manier om ervoor te zorgen dat uw kopieerbewerking een consistente snelheid heeft. De snelheid van een asynchrone kopie aan de serverzijde is daarentegen afhankelijk van de beschikbare netwerkbandbreedte op de server, die kan variëren. Synchrone kopieën kunnen echter extra kosten voor het verkeer genereren in vergelijking met asynchrone kopieën. De aanbevolen aanpak is om synchrone kopieën te gebruiken in een Azure-VM die zich in dezelfde regio als uw bronopslagaccount belandt om kosten voor het uitvoeren van gegevens te voorkomen.

De toepassing voor gegevensverkeer is nu voltooid. [Het volledige codevoorbeeld is beschikbaar op GitHub.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>Volgende stappen

[Azure Storage naslagdocumentatie voor de bibliotheek voor gegevensverkeer.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

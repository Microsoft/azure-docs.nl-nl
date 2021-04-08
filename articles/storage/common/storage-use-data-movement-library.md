---
title: Gegevens overdragen met de bibliotheek voor gegevens verplaatsing voor .NET
titleSuffix: Azure Storage
description: Gebruik de gegevens verplaatsings bibliotheek om gegevens naar of van BLOB-en bestands inhoud te verplaatsen of kopiëren. Gegevens kopiëren naar Azure Storage van lokale bestanden of gegevens kopiëren binnen of tussen opslag accounts. Migreer uw gegevens eenvoudig naar Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023411"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Gegevens overdragen met de bibliotheek voor gegevensverplaatsing

De Azure Storage-bibliotheek voor gegevens verplaatsing is een platformoverschrijdende open-bron bibliotheek die is ontworpen voor hoge prestaties uploaden, downloaden en kopiëren van blobs en bestanden. De bibliotheek voor gegevens verplaatsing biedt handige methoden die niet beschikbaar zijn in de Azure Storage-client bibliotheek voor .NET. Deze methoden bieden de mogelijkheid om het aantal parallelle bewerkingen in te stellen, de voortgang van de overdracht op te sporen, een geannuleerde overdracht eenvoudig te hervatten en nog veel meer.

In deze bibliotheek wordt ook gebruikgemaakt van .NET core. Dit betekent dat u deze kunt gebruiken bij het maken van .NET-Apps voor Windows, Linux en macOS. Raadpleeg de [documentatie van .net core](https://dotnet.github.io/)voor meer informatie over .net core. Deze bibliotheek werkt ook voor traditionele .NET Framework-apps voor Windows.

Dit document laat zien hoe u een .NET core-console toepassing maakt die wordt uitgevoerd op Windows, Linux en macOS en de volgende scenario's uitvoert:

- Upload bestanden en mappen naar Blob Storage.
- Definieer het aantal parallelle bewerkingen bij het overbrengen van gegevens.
- Voortgang van gegevens overdracht bijhouden.
- Geannuleerde gegevens overdracht hervatten.
- Kopieer het bestand van de URL naar de Blob Storage.
- Kopieer van Blob Storage naar Blob Storage.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- Een [Azure-opslag account](storage-account-create.md)

## <a name="setup"></a>Instellen

1. Ga naar de [.net Core-installatie handleiding](https://www.microsoft.com/net/core) om .net core te installeren. Wanneer u uw omgeving selecteert, kiest u de opdracht regel optie.
2. Maak een map voor uw project vanaf de opdracht regel. Navigeer naar deze map en typ `dotnet new console -o <sample-project-name>` om een C#-console project te maken.
3. Open deze map in Visual Studio code. Deze stap kan snel worden uitgevoerd via de opdracht regel door `code .` in Windows te typen.
4. Installeer de [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) vanuit de Visual Studio code Marketplace. Start Visual Studio code opnieuw.
5. Op dit moment ziet u twee prompts. Een is voor het toevoegen van ' vereiste assets voor het maken en debuggen '. Klik op Ja. Er is nog een prompt voor het herstellen van niet-opgeloste afhankelijkheden. Klik op herstellen.
6. Wijzig `launch.json` onder `.vscode` om externe terminal te gebruiken als een-console. Deze instelling moet worden gelezen als `"console": "externalTerminal"`
7. Met Visual Studio code kunt u fouten opsporen in .NET core-toepassingen. `F5`Zorg ervoor dat uw toepassing wordt uitgevoerd en controleer of uw installatie werkt. U ziet "Hallo wereld!" afgedrukt op de-console.

## <a name="add-the-data-movement-library-to-your-project"></a>De bibliotheek voor gegevens verplaatsing toevoegen aan uw project

1. Voeg de nieuwste versie van de bibliotheek voor gegevens verplaatsing toe aan de `dependencies` sectie van uw `<project-name>.csproj` bestand. Op het moment van schrijven zou deze versie worden `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Er moet een prompt worden weer gegeven om het project te herstellen. Klik op de knop herstellen. U kunt het project ook herstellen vanaf de opdracht regel door de opdracht `dotnet restore` in de hoofdmap van de projectmap te typen.

Wijzigen `<project-name>.csproj` :

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

## <a name="set-up-the-skeleton-of-your-application"></a>Het skelet van uw toepassing instellen

Het eerste wat we doen, is de ' skelet code ' van onze toepassing instellen. Deze code vraagt ons om een naam van een opslag account en de account sleutel en gebruikt deze referenties voor het maken van een `CloudStorageAccount` object. Dit object wordt gebruikt om te communiceren met het opslag account in alle overdrachts scenario's. De code vraagt ons ook om het type overdrachts bewerking te kiezen dat we willen uitvoeren.

Wijzigen `Program.cs` :

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

## <a name="upload-a-local-file-to-a-blob"></a>Een lokaal bestand uploaden naar een BLOB

Voeg de methoden toe `GetSourcePath` en `GetBlob` toe aan `Program.cs` :

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

Deze code vraagt ons naar het pad naar een lokaal bestand, de naam van een nieuwe of bestaande container en de naam van een nieuwe blob. De `TransferManager.UploadAsync` methode voert de upload uit met behulp van deze gegevens.

Druk `F5` op het uitvoeren van uw toepassing. U kunt controleren of het uploaden is uitgevoerd door uw opslag account te bekijken met de [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Het aantal parallelle bewerkingen instellen

Een functie die wordt aangeboden door de bibliotheek voor gegevens verplaatsing is de mogelijkheid om het aantal parallelle bewerkingen in te stellen om de door Voer van gegevens overdracht te verhogen. De gegevens verplaatsings bibliotheek stelt standaard het aantal parallelle bewerkingen in op 8 * het aantal kern geheugens op uw machine.

Houd er rekening mee dat veel parallelle bewerkingen in een omgeving met weinig band breedte de netwerk verbinding kunnen overbelasten en de bewerkingen in feite voor komen. U moet experimenteren met deze instelling om te bepalen wat het beste werkt op basis van de beschik bare netwerk bandbreedte.

Laten we code toevoegen waarmee we het aantal parallelle bewerkingen kunnen instellen. Laten we ook code toevoegen die laat zien hoe lang het duurt voordat de overdracht is voltooid.

Een `SetNumberOfParallelOperations` methode toevoegen aan `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Wijzig de `ExecuteChoice` methode die moet worden gebruikt `SetNumberOfParallelOperations` :

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

## <a name="track-transfer-progress"></a>Voortgang van de overdracht volgen

Hoe lang het duurt voordat de gegevens zijn overgedragen, is nuttig. De voortgang van de overdracht *tijdens* de overdracht kan echter nog beter worden weer geven. Voor dit scenario moet een object worden gemaakt `TransferContext` . Het `TransferContext` object is beschikbaar in twee vormen: `SingleTransferContext` en `DirectoryTransferContext` . De eerste is voor het overzetten van één bestand en de laatste is voor het overdragen van een map bestanden.

Voeg de methoden toe `GetSingleTransferContext` en `GetDirectoryTransferContext` toe aan `Program.cs` :

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

Wijzig de `TransferLocalFileToAzureBlob` methode die moet worden gebruikt `GetSingleTransferContext` :

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

Een andere handige functie die wordt aangeboden door de bibliotheek voor gegevens verplaatsing is de mogelijkheid om een geannuleerde overdracht te hervatten. We voegen code toe waarmee we de overdracht tijdelijk kunnen annuleren door te typen `c` en de overdracht 3 seconden later te hervatten.

Wijzigen `TransferLocalFileToAzureBlob` :

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

Tot nu toe is onze `checkpoint` waarde altijd ingesteld op `null` . Als we de overdracht annuleren, halen we het laatste controle punt van de overdracht op en gebruiken we dit nieuwe controle punt in onze overdrachts context.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Een lokale directory overdragen naar Blob Storage

Disappointing als de bibliotheek voor gegevens verplaatsing slechts één bestand tegelijk kan overdragen. Gelukkig is dit niet het geval. De bibliotheek voor gegevens verplaatsing biedt de mogelijkheid om een map met bestanden en alle bijbehorende submappen over te dragen. Laten we een code toevoegen waarmee we er alleen kunnen doen.

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

Er zijn enkele verschillen tussen deze methode en de methode voor het uploaden van één bestand. We gebruiken nu `TransferManager.UploadDirectoryAsync` en de `getDirectoryTransferContext` methode die u eerder hebt gemaakt. Daarnaast bieden we nu een `options` waarde voor onze upload bewerking, waarmee we kunnen aangeven dat we in onze upload submappen willen opnemen.

## <a name="copy-a-file-from-url-to-a-blob"></a>Een bestand kopiëren van een URL naar een BLOB

Nu gaan we code toevoegen waarmee we een bestand kunnen kopiëren van een URL naar een Azure-Blob.

Wijzigen `TransferUrlToAzureBlob` :

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

Een belang rijke use case voor deze functie is wanneer u gegevens van een andere Cloud service (bijvoorbeeld AWS) naar Azure moet verplaatsen. Als u een URL hebt waarmee u toegang krijgt tot de resource, kunt u deze resource eenvoudig verplaatsen naar Azure-blobs met behulp van de- `TransferManager.CopyAsync` methode. Met deze methode wordt ook een nieuwe Boole-para meter geïntroduceerd. Als deze para meter `true` wordt ingesteld, wordt aangegeven dat er een asynchrone kopie op de server moet worden uitgevoerd. Als u deze para meter instelt op `false` een synchrone kopie, wat betekent dat de resource eerst naar onze lokale machine wordt gedownload en vervolgens naar Azure Blob is geüpload. Synchrone kopiëren is momenteel echter alleen beschikbaar voor het kopiëren van de ene Azure Storage Resource naar een andere.

## <a name="copy-a-blob"></a>Een BLOB kopiëren

Een andere functie die uniek is in de bibliotheek voor gegevens verplaatsing is de mogelijkheid om van de ene Azure Storage Resource naar een andere te kopiëren.

Wijzigen `TransferAzureBlobToAzureBlob` :

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

In dit voor beeld wordt de Boole-para meter in ingesteld op om aan `TransferManager.CopyAsync` `false` te geven dat we een synchrone kopie willen uitvoeren. Dit betekent dat de resource eerst naar de lokale machine wordt gedownload en vervolgens naar Azure Blob is geüpload. De optie voor synchroon kopiëren is een uitstekende manier om ervoor te zorgen dat uw Kopieer bewerking een consistente snelheid heeft. Daarentegen is de snelheid van een asynchrone kopie aan de server zijde afhankelijk van de beschik bare netwerk bandbreedte op de server, die kan schommelen. Synchrone kopiëren kan echter extra uitvoer kosten genereren in vergelijking met asynchrone kopieën. De aanbevolen aanpak is het gebruik van synchrone kopieën in een Azure-VM die zich in dezelfde regio bevindt als uw bron opslag account om te voor komen dat er geen kosten in rekening worden brengt.

De toepassing voor gegevens verplaatsing is nu voltooid. [Het volledige code voorbeeld is beschikbaar op github](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Volgende stappen

[Referentie documentatie voor de gegevens verplaatsings bibliotheek Azure Storage](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

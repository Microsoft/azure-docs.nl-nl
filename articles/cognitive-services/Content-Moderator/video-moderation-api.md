---
title: Video-inhoud analyseren voor aanstootgevend materiaal in C#-Content Moderator
titleSuffix: Azure Cognitive Services
description: Video-inhoud analyseren voor verschillende aanstootgevende materialen met behulp van de Content Moderator SDK voor .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f98eac4305333ec7225c90da2777b7e02f050a0
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853529"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Video-inhoud voor aanstootgevend materiaal in C analyseren #

In dit artikel vindt u informatie en code voorbeelden waarmee u aan de slag kunt met de [Content moderator SDK voor .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) voor het scannen van video-inhoud voor inhoud voor volwassenen of ongepaste.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Azure-resources instellen

De functie voor video toezicht van de Content Moderator is beschikbaar als een gratis open bare preview- **Media processor** in azure Media Services (AMS). Azure Media Services is een gespecialiseerde Azure-service voor het opslaan en streamen van video-inhoud. 

### <a name="create-an-azure-media-services-account"></a>Een Azure Media Services-account maken

Volg de instructies in [Create a Azure Media Services account](../../media-services/previous/media-services-portal-create-account.md) om u te abonneren op AMS en een gekoppeld Azure Storage-account te maken. Maak in dat opslag account een nieuwe Blob Storage-container.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Ga naar het nieuwe AMS-abonnement in de Azure Portal en selecteer **API-toegang** in het menu aan de zijkant. Selecteer **verbinding maken met Azure Media Services met Service-Principal**. Let op de waarde in het veld **rest API eindpunt** . u hebt dit later nodig.

Selecteer in het gedeelte **Azure AD-App** de optie **nieuwe maken** en geef uw nieuwe Azure AD-toepassings registratie een naam (bijvoorbeeld ' VideoModADApp '). Klik op **Opslaan** en wacht enkele minuten totdat de toepassing is geconfigureerd. Vervolgens ziet u de nieuwe app-registratie in het gedeelte **Azure AD-App** van de pagina.

Selecteer de registratie van uw app en klik op de knop **toepassing beheren** hieronder. Noteer de waarde in het veld **toepassings-id** . u hebt dit later nodig. Selecteer **instellingen**  >  **sleutels** en voer een beschrijving in voor een nieuwe sleutel (bijvoorbeeld ' VideoModKey '). Klik op **Opslaan** en noteer de nieuwe sleutel waarde. Kopieer deze teken reeks en sla deze op een veilige plek op.

Zie [aan de slag met Azure AD-verificatie](../../media-services/previous/media-services-portal-get-started-with-aad.md)voor een gedetailleerder overzicht van het bovenstaande proces.

Zodra u dit hebt gedaan, kunt u de media processor voor video toezicht op twee verschillende manieren gebruiken.

## <a name="use-azure-media-services-explorer"></a>Azure Media Services Explorer gebruiken

De Azure Media Services Explorer is een gebruikers vriendelijke frontend voor AMS. Gebruik het om door uw AMS-account te bladeren, Video's te uploaden en inhoud te scannen met de Content Moderator-media processor. Down load en installeer deze via [github](https://github.com/Azure/Azure-Media-Services-Explorer/releases)of Raadpleeg het [blog bericht van Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) voor meer informatie.

![Azure Media Services Explorer met Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

1. Maak in Visual Studio een nieuw **console-app-project (.NET Framework)** en noem het **VideoModeration**. 
1. Als uw oplossing nog meer projecten bevat, selecteert u deze als het enkele opstartproject.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens de volgende pakketten:
    - windowsazure. Media Services
    - windowsazure. Media Services. Extensions

## <a name="add-video-moderation-code"></a>Code voor video toezicht toevoegen

Vervolgens kopieert en plakt u de code uit deze handleiding in uw project om een eenvoudig scenario voor inhoudstoezicht te implementeren.

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Voeg aan het begin van het bestand _Program.cs_ de volgende `using`-instructies toe.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Resource verwijzingen instellen

Voeg de volgende statische velden toe aan de klasse **Program** in _Program.cs_. Deze velden bevatten de gegevens die nodig zijn om verbinding te maken met uw AMS-abonnement. Vul deze in met de waarden die u in de bovenstaande stappen hebt ontvangen. Houd er rekening mee dat `CLIENT_ID` de waarde van de **toepassings-id** van uw Azure AD-app is en de `CLIENT_SECRET` waarde is van de ' VideoModKey ' die u hebt gemaakt voor die app.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Als u een lokaal video bestand (het eenvoudigste geval) wilt gebruiken, voegt u het toe aan het project en voert u het pad in als de `INPUT_FILE` waarde (relatieve paden zijn relatief ten opzichte van de uitvoerings Directory).

U moet ook de _preset.jsvoor_ het bestand in de huidige map maken en gebruiken om een versie nummer op te geven. Bijvoorbeeld:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>De invoer video (s) laden

Met de methode **Main** van de klasse **Program** wordt een Azure-media context gemaakt en vervolgens een Azure Storage context (als uw Video's zich in Blob-opslag bevinden). De resterende code scant een video van een lokale map, Blob of meerdere blobs in een Azure storage-container. U kunt alle opties proberen door de andere code regels te controleren.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Een Azure-media context maken

Voeg de volgende methode toe aan de klasse **Program**. Dit maakt gebruik van uw AMS-referenties om communicatie met AMS toe te staan.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>De code toevoegen om een Azure Storage context te maken

Voeg de volgende methode toe aan de klasse **Program**. U gebruikt de opslag context, die u hebt gemaakt op basis van uw opslag referenties, om toegang te krijgen tot de Blob-opslag.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Voeg de code toe om Azure-media-assets te maken op basis van het lokale bestand en de BLOB

De Content Moderator media processor voert taken uit op **activa** binnen het Azure Media Services platform.
Met deze methoden maakt u de assets van een lokaal bestand of een gekoppelde blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>De code toevoegen voor het scannen van een verzameling Video's (als blobs) binnen een container

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>De methode voor het uitvoeren van de Content Moderator-taak toevoegen

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Helperfuncties toevoegen

Met deze methoden wordt het Content Moderator-uitvoer bestand (JSON) gedownload uit de Azure Media Services-Asset en kan de status van de taak voor toezicht worden bijgehouden, zodat het programma de status actief kan registreren in de-console.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Het programma uitvoeren en de uitvoer controleren

Analyseer het JSON-antwoord nadat de taak voor inhouds toezicht is voltooid. Deze bestaat uit de volgende elementen:

- Samen vatting van video gegevens
- **Opnamen** als '**fragmenten**'
- **Key-frames** als "**Events**" met de vlag **reviewRecommended "(= True of false)** op basis van de scores voor **volwassenen** en **ongepaste**
- **Start**, **duration**, **totalDuration** en **Time Stamp** bevinden zich in ' Ticks '. Delen door **tijd schaal** om het getal in seconden op te halen.
 
> [!NOTE]
> - `adultScore` vertegenwoordigt de potentiële aanwezigheids-en voorspellende Score van inhoud die in bepaalde situaties als seksueel expliciet of volwassen kan worden beschouwd.
> - `racyScore` vertegenwoordigt de potentiële aanwezigheids-en voorspellende Score van inhoud die in bepaalde situaties als seksueel voor stel of rijp kan worden beschouwd.
> - `adultScore` en `racyScore` tussen 0 en 1 liggen. Hoe hoger de score, hoe hoger het model is om te voors pellen dat de categorie van toepassing kan zijn. Dit voor beeld is afhankelijk van een statistisch model in plaats van hand matige gecodeerde resultaten. We raden u aan om te testen met uw eigen inhoud om te bepalen hoe elke categorie wordt uitgelijnd op uw vereisten.
> - `reviewRecommended` is waar of onwaar, afhankelijk van de drempel waarden van de interne Score. Klanten moeten beoordelen of u deze waarde moet gebruiken of besluiten over aangepaste drempel waarden op basis van hun inhouds beleid.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het genereren van [video beoordelingen](video-reviews-quickstart-dotnet.md) uit uw toezicht uitvoer.

[Down load de Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere content moderator Quick starts voor .net.
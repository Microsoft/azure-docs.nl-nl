---
title: Azure Functions ontwikkelen met Media Services v3
description: In dit onderwerp wordt beschreven hoe u Azure Functions kunt ontwikkelen met Media Services V3 met behulp van de Azure Portal.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 389ad34bb856675dfabd761507ed07cc722c032a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961673"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Azure Functions ontwikkelen met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u aan de slag gaat met het maken van Azure Functions die gebruikmaken van Media Services. Met de Azure-functie die in dit artikel is gedefinieerd, wordt een opslag account container met de naam **input** voor nieuwe MP4-bestanden bewaakt. Zodra een bestand in de opslag container wordt neergezet, voert de BLOB-trigger de functie uit. Zie  [overzicht](../../azure-functions/functions-overview.md) en andere onderwerpen in de sectie **Azure functions** om Azure functions te controleren.

Als u bestaande Azure Functions wilt verkennen en implementeren die gebruikmaken van Azure Media Services, raadpleegt u [Media Services Azure functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Deze opslag plaats bevat voor beelden die Media Services gebruiken om werk stromen weer te geven die zijn gerelateerd aan het opnemen van inhoud rechtstreeks vanuit Blob Storage, versleuteling en het schrijven van inhoud naar Blob Storage.

## <a name="prerequisites"></a>Vereisten

- Voordat u uw eerste functie kunt maken, moet u een actief Azure-account hebben. Als u nog geen Azure-account hebt, zijn er [gratis accounts beschikbaar](https://azure.microsoft.com/free/).
- Als u Azure Functions wilt maken die acties uitvoeren op uw Azure Media Services-account (AMS) of als u wilt Luis teren naar gebeurtenissen die door Media Services worden verzonden, moet u een AMS-account maken, zoals [hier](account-create-how-to.md)wordt beschreven.

## <a name="create-a-function-app"></a>Een functie-app maken

1. Ga naar de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Maak een functie-app, zoals [hier](../../azure-functions/functions-create-function-app-portal.md)wordt beschreven.

>[!NOTE]
> Een opslag account dat u opgeeft, moet zich in dezelfde regio bevinden als uw app.

## <a name="configure-function-app-settings"></a>Instellingen voor de functie-app configureren

Wanneer u Media Services functies ontwikkelt, is het handig om omgevings variabelen toe te voegen die tijdens uw functies worden gebruikt. Als u de app-instellingen wilt configureren, klikt u op de koppeling app-instellingen configureren. Zie  [How to configure Azure function app Settings](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)(Engelstalig) voor meer informatie.

## <a name="create-a-function"></a>Een functie maken

Als uw functie-app is geïmplementeerd, kunt u deze vinden in **App Services** Azure functions.

1. Selecteer de functie-app en klik op **nieuwe functie**.
1. Kies de **C#** -taal en het scenario voor **gegevens verwerking** .
1. Kies **sjabloon blobtrigger** -sjabloon. Deze functie wordt geactiveerd wanneer een BLOB wordt geüpload naar de **invoer** container. De naam van de **invoer** wordt in de volgende stap in het **pad** opgegeven.
1. Wanneer u **sjabloon blobtrigger** selecteert, worden er nog meer besturings elementen op de pagina weer gegeven.
1. Klik op **Create**.

## <a name="files"></a>Bestanden

Uw Azure-functie is gekoppeld aan code bestanden en andere bestanden die in deze sectie worden beschreven. Wanneer u de Azure Portal gebruikt om een functie te maken, **function.js** voor u gemaakt en **uitgevoerd. CSX** . U moet eenproject.jstoevoegen of uploaden **voor** het bestand. In de rest van deze sectie vindt u een korte uitleg van elk bestand en worden de bijbehorende definities weer gegeven.

### <a name="functionjson"></a>function.json

De function.jsin het bestand definieert de functie bindingen en andere configuratie-instellingen. De runtime gebruikt dit bestand om te bepalen welke gebeurtenissen moeten worden bewaakt en hoe gegevens moeten worden door gegeven en hoe de uitvoering van de functie kan worden geretourneerd. Zie [Azure Functions HTTP and webhook bindings](../../azure-functions/functions-reference.md#function-code) (Azure Functions-HTTP- en webhookbindingen) voor meer informatie.

>[!NOTE]
>Stel de eigenschap **disabled** in op **True** om te voor komen dat de functie wordt uitgevoerd.

Vervang de inhoud van de bestaande function.jsin het bestand door de volgende code:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.jsop

De project.jsin het bestand bevat afhankelijkheden. Hier volgt een voor beeld van **project.jsin** een bestand dat de vereiste .net Azure Media Services-pakketten bevat van NuGet. Houd er rekening mee dat de versie nummers worden gewijzigd met de meest recente updates voor de pakketten. Daarom moet u de meest recente versies bevestigen.

Voeg de volgende definitie toe aan project.jsop.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```

### <a name="runcsx"></a>run. CSX

Dit is de C#-code voor uw functie.  Met de functie die hieronder is gedefinieerd, wordt een opslag account container met de naam **input** (die is opgegeven in het pad) voor nieuwe MP4-bestanden gecontroleerd. Zodra een bestand in de opslag container wordt neergezet, voert de BLOB-trigger de functie uit.

In het voor beeld dat in deze sectie is gedefinieerd, ziet u het volgende:

1. Een Asset opnemen in een Media Services-account (door een BLOB te omgaan in een AMS-Asset)
2. Een coderings taak verzenden die gebruikmaakt van de vooraf ingestelde ' adaptieve streaming ' van Media Encoder Standard

Vervang de inhoud van het bestaande run. CSX-bestand door de volgende code: wanneer u klaar bent met het definiëren van de functie, klikt u op **opslaan en uitvoeren**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Uw functie testen

Als u uw functie wilt testen, moet u een MP4-bestand uploaden naar de **invoer** container van het opslag account dat u in de Connection String hebt opgegeven.  

1. Selecteer het opslag account dat u hebt opgegeven.
2. Klik op **blobs**.
3. Klik op **+ Container**. Geef de container **invoer** een naam.
4. Druk op **uploaden** en blader naar een. MP4-bestand dat u wilt uploaden.

>[!NOTE]
> Wanneer u een BLOB-trigger in een verbruiks plan gebruikt, kan er een vertraging van 10 minuten zijn bij het verwerken van nieuwe blobs nadat een functie-app niet actief is geweest. Nadat de functie-app is uitgevoerd, worden de blobs onmiddellijk verwerkt. Zie [Blob Storage-triggers en-bindingen](../../azure-functions/functions-bindings-storage-blob.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om te beginnen met het ontwikkelen van een Media Services-toepassing.

Zie de [Azure Functions Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration) voor meer informatie over de voor beelden/oplossingen van het gebruik van Azure Functions en Logic Apps met Azure Media Services voor het maken van aangepaste werk stromen voor het maken van inhoud.

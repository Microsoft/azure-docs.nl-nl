---
title: Bestanden uploaden naar een Media Services-account met behulp van .NET | Microsoft Docs
description: Meer informatie over het verkrijgen van media-inhoud in Media Services door assets te maken en te uploaden met behulp van .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013702"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Bestanden uploaden naar een Media Services-account met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In Media Services moet u uw digitale bestanden uploaden naar (of opnemen in) een asset. De **Asset** -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten.  Zodra de bestanden zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming.

De bestanden in de asset worden **assetbestanden** genoemd. Het **AssetFile** -exemplaar en het daad werkelijke media bestand zijn twee verschillende objecten. Het AssetFile-exemplaar bevat meta gegevens over het Media bestand, terwijl het Media bestand de daad werkelijke media-inhoud bevat.

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:
 
 * Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van Url's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}. Origin. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Daarom is het percentage code ring niet toegestaan. De waarde van de eigenschap **name** mag niet de volgende tekens voor [percentage versleuteling](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)bevatten:! * ' ();: @ &= + $,/?% # [] '. Daarnaast kan er slechts één '. ' zijn voor de bestandsnaam extensie.
* De lengte van de naam mag niet langer zijn dan 260 tekens.
* Er is een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. Raadpleeg [dit](media-services-quotas-and-limitations.md) artikel voor meer informatie over de maximale bestandsgrootte.
* Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

Wanneer u assets maakt, kunt u de volgende versleutelings opties opgeven:

* **Geen**: er wordt geen versleuteling gebruikt. Dit is de standaardwaarde. Wanneer u deze optie gebruikt, wordt uw inhoud niet door Voer of op rest in de opslag beveiligd.
  Als u van plan bent een MP4 te leveren met behulp van progressief downloaden, gebruikt u deze optie: 
* **CommonEncryption** : gebruik deze optie als u inhoud uploadt die al is versleuteld en beveiligd met common Encryption of playready DRM (bijvoorbeeld Smooth streaming beveiligd met PlayReady DRM).
* **EnvelopeEncrypted** : gebruik deze optie als u HLS versleuteld uploadt met AES. Houd er rekening mee dat de bestanden moeten zijn gecodeerd en versleuteld door Transform Manager.
* **StorageEncrypted** : Hiermee versleutelt u uw ongebruikte inhoud lokaal met AES-256-bits versleuteling en uploadt u deze naar Azure Storage waar deze wordt opgeslagen op rest. De versleuteling van assets die zijn beveiligd met Storage Encryption, wordt automatisch ongedaan gemaakt en de assets worden automatisch in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. Eventueel kunnen ze opnieuw worden versleuteld voordat ze opnieuw worden geüpload als een nieuwe uitvoerasset. Storage Encryption wordt voornamelijk gebruikt om uw invoerbestanden met media van hoge kwaliteit die zijn opgeslagen op de schijf, te beveiligen met een sterke versleuteling.
  
    Media Services biedt opslag versleuteling op schijf voor uw assets, niet via de kabel, zoals DRM (Digital Rights Manager).
  
    Als de opslag van uw asset is versleuteld, moet u een beleid voor assetlevering configureren. Zie voor meer informatie beleid voor het [leveren van assets configureren](media-services-dotnet-configure-asset-delivery-policy.md).

Als u opgeeft dat uw activum moet worden versleuteld met een **CommonEncrypted** -optie of een **EnvelopeEncrypted** -optie, moet u uw asset koppelen aan een **ContentKey**. Zie [How to Create a ContentKey](media-services-dotnet-create-contentkey.md)(Engelstalig) voor meer informatie. 

Als u opgeeft dat uw activa moeten worden versleuteld met een **StorageEncrypted** -optie, maakt de Media Services SDK voor .net een **StorageEncrypted** **ContentKey** voor uw asset.

In dit artikel wordt beschreven hoe u Media Services .NET SDK en Media Services .NET SDK-extensies gebruikt om bestanden te uploaden naar een Media Services-Asset.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Een enkel bestand uploaden met Media Services .NET SDK

De volgende code gebruikt .NET om één bestand te uploaden. De AccessPolicy en de Locator worden gemaakt en vernietigd door de upload functie. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Meerdere bestanden uploaden met Media Services .NET SDK
De volgende code laat zien hoe u een Asset maakt en meerdere bestanden uploadt.

De code doet het volgende:

* Hiermee maakt u een leeg activum met behulp van de methode CreateEmptyAsset die in de vorige stap is gedefinieerd.
* Hiermee maakt u een **AccessPolicy** -exemplaar dat de machtigingen en de duur van toegang tot de Asset definieert.
* Hiermee maakt u een **Locator** -exemplaar dat toegang biedt tot de Asset.
* Hiermee maakt u een **BlobTransferClient** -exemplaar. Dit type vertegenwoordigt een client die actief is op de Azure-blobs. In dit voor beeld bewaakt de client de voortgang van het uploaden. 
* Inventariseert bestanden in de opgegeven map en maakt een **AssetFile** -exemplaar voor elk bestand.
* De bestanden worden geüpload naar Media Services met behulp van de methode **UploadAsync** . 

> [!NOTE]
> Gebruik de methode UploadAsync om ervoor te zorgen dat de aanroepen niet worden geblokkeerd en dat de bestanden parallel worden geüpload.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Houd bij het uploaden van een groot aantal assets rekening met het volgende:

* Maak een nieuw **cloudmediacontext maakt** -object per thread. De **cloudmediacontext maakt** -klasse is niet thread-veilig.
* Verhoog NumberOfConcurrentTransfers van de standaard waarde van 2 naar een hogere waarde, zoals 5. Het instellen van deze eigenschap is van invloed op alle exemplaren van **cloudmediacontext maakt**. 
* Behoud ParallelTransferThreadCount met de standaard waarde van 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Activa bulksgewijs opnemen met behulp van Media Services .NET SDK
Het uploaden van grote activa bestanden kan een knel punt zijn tijdens het maken van activa. Als u activa wilt opnemen in bulk of bulk opname, moet u het maken van activa vanuit het upload proces ontkoppelen. Als u een aanpak met massale opname wilt gebruiken, maakt u een manifest (IngestManifest) waarin de Asset en de bijbehorende bestanden worden beschreven. Gebruik vervolgens de upload methode van uw keuze om de bijbehorende bestanden te uploaden naar de BLOB-container van het manifest. Microsoft Azure Media Services controleert de BLOB-container die is gekoppeld aan het manifest. Zodra een bestand is geüpload naar de BLOB-container, Microsoft Azure Media Services het maken van het activum volt ooien op basis van de configuratie van de asset in het manifest (IngestManifestAsset).

Als u een nieuwe IngestManifest wilt maken, roept u de methode Create aan die wordt weer gegeven door de IngestManifests-verzameling op de Cloudmediacontext maakt. Met deze methode maakt u een nieuwe IngestManifest met de naam van het manifest dat u opgeeft.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Maak de activa die zijn gekoppeld aan de bulk-IngestManifest. Configureer de gewenste versleutelings opties voor de Asset voor bulk opname.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Een IngestManifestAsset koppelt een Asset met een bulk IngestManifest voor bulk opname. Het koppelt ook de AssetFiles die elk activum opstelt. Als u een IngestManifestAsset wilt maken, gebruikt u de methode Create in de context van de server.

In het volgende voor beeld ziet u het toevoegen van twee nieuwe IngestManifestAssets die de twee assets koppelen die eerder zijn gemaakt in het bulk opname-manifest. Elke IngestManifestAsset koppelt ook een set bestanden die voor elke Asset worden geüpload tijdens bulk opname.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

U kunt elke client toepassing met snelle snelheid gebruiken voor het uploaden van de Asset-bestanden naar de BLOB storage container URI die is verschaft door de eigenschap **IIngestManifest. BlobStorageUriForUpload** van de IngestManifest. 

De volgende code laat zien hoe u .NET SDK gebruikt om de assets bestanden te uploaden.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

De code voor het uploaden van de Asset-bestanden voor het voor beeld dat in dit artikel wordt gebruikt, wordt in het volgende code voorbeeld weer gegeven:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

U kunt de voortgang van het bulk opname proces voor alle assets die zijn gekoppeld aan een **IngestManifest** bepalen door de statistieken-eigenschap van de **IngestManifest** te pollen. Als u de voortgangs gegevens wilt bijwerken, moet u een nieuwe **cloudmediacontext maakt** gebruiken telkens wanneer u de eigenschap Statistics navraagt.

In het volgende voor beeld ziet u hoe u een IngestManifest op basis van de **id** navraagt.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Bestanden uploaden met behulp van .NET SDK-extensies
In het volgende voor beeld ziet u hoe u één bestand uploadt met behulp van .NET SDK-extensies. In dit geval wordt de methode **CreateFromFile** gebruikt, maar de asynchrone versie is ook beschikbaar (**CreateFromFileAsync**). Met de methode **CreateFromFile** kunt u de bestands naam, de versleutelings optie en een call back opgeven om de upload voortgang van het bestand te rapporteren.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

In het volgende voor beeld wordt de functie Upload File aangeroepen en wordt opslag versleuteling opgegeven als de optie voor het maken van activa.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.

U kunt ook Azure Functions gebruiken om een coderingstaak te activeren op basis van een bestand dat binnenkomt in de geconfigureerde container. Zie [dit voorbeeld](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Volgende stap
Nu u een Asset naar Media Services hebt geüpload, gaat u naar het artikel [een media processor ophalen][How to Get a Media Processor] .

[How to Get a Media Processor]: media-services-get-media-processor.md

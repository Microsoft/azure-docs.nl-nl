---
title: Aan de slag met het leveren van inhoud on demand met .NET | Microsoft Docs
description: In deze zelfstudie leert u een eenvoudige toepassing voor de levering van on demand inhoud te implementeren met Azure Media Services door gebruik te maken van .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: bbbf924ea950c5ba281be430190370ae77734b7a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184348"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Aan de slag met het leveren van inhoud on demand met .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

In deze zelfstudie wordt u begeleid bij het implementeren van een basisservice voor levering van VoD-inhoud (Video-on-Demand) met de AMS-toepassing (Azure Media Services) via de Azure Media Services .NET SDK.

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Een Azure-account. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Een Media Services-account. Zie [Een Media Services-account maken](media-services-portal-create-account.md) voor meer informatie over het maken van een Media Services-account.
* .NET framework 4.0 of hoger.
* Visual Studio.

Deze zelfstudie bevat de volgende taken:

1. Streaming-eindpunt starten (vanuit Azure Portal).
2. Een Visual Studio-project maken en configureren.
3. Verbinding maken met het Azure Media Services-account.
2. Een videobestand uploaden.
3. Het bronbestand coderen in een set Adaptive Bitrate MP4-bestanden.
4. De asset publiceren en URL's voor streamen en progressief downloaden ophalen.  
5. Uw inhoud afspelen.

## <a name="overview"></a>Overzicht
In deze zelfstudie leert u een eenvoudige toepassing voor de levering van VoD-inhoud (Video-on-Demand) te implementeren met Azure Media Services door gebruik te maken van de Azure Media Services (AMS) SDK voor .NET.

In deze zelfstudie maakt u kennis met de algemene werkstroom voor Media Services en de meest algemene programmeerobjecten en -taken die zijn vereist voor het ontwikkelen van Media Services. Wanneer u de zelfstudie hebt voltooid, kunt u een voorbeeldmediabestand streamen of progressief downloaden dat u hebt eerder hebt geüpload, gecodeerd of gedownload.

### <a name="ams-model"></a>AMS-model

In de volgende afbeelding ziet u een aantal van de meest gebruikte objecten bij het ontwikkelen van VoD-toepassingen in het Media Services OData-model.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

[![Diagram met een aantal van de meest gebruikte objecten in het object gegevens model van Azure Media Services voor het ontwikkelen van video op aanvraag toepassingen.](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

U kunt [hier](https://media.windows.net/API/$metadata?api-version=2.15) het hele model bekijken.  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Streaming-eindpunten starten met behulp van Azure Portal

Bij het werken met Azure Media Services wordt video meestal via Adaptive Bitrate Streaming geleverd. Media Services biedt dynamische pakketten waarmee u uw Adaptive Bitrate MP4-inhoud 'just in time' kunt leveren in de streaming-indelingen die door Media Services worden ondersteund (MPEG DASH, HLS, Smooth Streaming), zonder dat u vooraf verpakte versies van elk van deze streaming-indelingen hoeft op te slaan.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

U start het streaming-eindpunt als volgt:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Klik in het venster Instellingen op Streaming-eindpunten.
3. Klik op het standaardstreaming-eindpunt.

    Het venster DETAILS VAN STANDAARDSTREAMING-EINDPUNT wordt weergegeven.

4. Klik op het pictogram Start.
5. Klik op de knop Opslaan om uw wijzigingen op te slaan.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 
2. Maak een nieuwe map (deze kan overal op uw lokaal station zijn opgeslagen) en kopieer een MP4-bestand dat u wilt coderen en streamen of progressief wilt downloaden. In dit voorbeeld wordt het pad C:\VideoFiles gebruikt.

## <a name="connect-to-the-media-services-account"></a>Verbinding met het Azure Media Services-account maken

Als u Media Services gebruikt met .NET, moet u voor de meeste Media Services-programmeertaken de klasse **CloudMediaContext** gebruiken: verbinding maken met het Media Services-account; maken, bijwerken, gebruiken en verwijderen van de volgende objecten: assets, assetbestanden, taken, toegangsbeleid, locators enzovoort.

Overschrijf de standaardprogrammaklasse met de volgende code: de code laat zien u hoe de verbindingswaarden in het bestand App.config kunt lezen en hoe u het object **CloudMediaContext** maakt om verbinding met Media Services te maken. Zie [Verbinding maken met de Media Services-API](media-services-use-aad-auth-to-access-ams-api.md) voor meer informatie.

Zorg ervoor dat de bestandsnaam en het pad waar u het media-bestand hebt opgeslagen, zijn bijgewerkt.

Met de functie **Main** worden methoden aangeroepen die later in deze sectie verder worden gedefinieerd.

> [!NOTE]
> Er worden compilatiefouten geretourneerd totdat u definities hebt toegevoegd voor alle functies die later in dit artikel worden gedefinieerd.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Een nieuwe asset maken en een videobestand uploaden

In Media Services moet u uw digitale bestanden uploaden naar (of opnemen in) een asset. De **Asset** -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten.  Zodra de bestanden zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming. De bestanden in de asset worden **assetbestanden** genoemd.

Met de methode **UploadFile**, zoals hieronder gedefinieerd, wordt **CreateFromFile** (gedefinieerd in .NET SDK Extensions) aangeroepen. Met **CreateFromFile** wordt een nieuwe asset gemaakt waarnaar het opgegeven bestand wordt geüpload.

De methode **CreateFromFile** maakt **AssetCreationOptions**, waarmee u een van de volgende opties voor het maken van activa kunt opgeven:

* **Geen**: er wordt geen versleuteling gebruikt. Dit is de standaardwaarde. Houd er rekening mee dat bij gebruik van deze optie de inhoud tijdens de overdracht of in de opslag niet is beveiligd.
  Als u een MP4-bestand wilt leveren via progressief downloaden, gebruikt u deze optie.
* **StorageEncrypted**: gebruik deze optie om uw niet-versleutelde inhoud lokaal te versleutelen met Advanced Encryption Standard (AES) 256-bitsversleuteling, waarna de inhoud wordt geüpload naar en versleuteld wordt bewaard in Azure Storage. De versleuteling van assets die zijn beveiligd met Storage Encryption, wordt automatisch ongedaan gemaakt en de assets worden automatisch in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. Eventueel kunnen ze opnieuw worden versleuteld voordat ze opnieuw worden geüpload als een nieuwe uitvoerasset. Storage Encryption wordt voornamelijk gebruikt om uw invoerbestanden met media van hoge kwaliteit die zijn opgeslagen op de schijf, te beveiligen met een sterke versleuteling.
* **CommonEncryptionProtected**: gebruik deze optie als u inhoud uploadt die al is versleuteld en beveiligd met Common Encryption of PlayReady DRM (bijvoorbeeld Smooth Streaming beveiligd met PlayReady DRM).
* **EnvelopeEncryptionProtected**: gebruik deze optie als u een HLS-stream uploadt die is versleuteld met AES. Houd er rekening mee dat de bestanden moeten zijn gecodeerd en versleuteld door Transform Manager.

Met de methode **CreateFromFile** kunt u ook een retouraanroep opgeven om de uploadvoortgang van het bestand te rapporteren.

In het volgende voorbeeld is voor de assetopties de waarde **Geen** opgegeven.

Voeg de volgende methode toe aan de klasse Program.

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

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Het bronbestand coderen in een set Adaptive Bitrate MP4-bestanden
Nadat assets zijn opgenomen in Media Services, kan de media worden gecodeerd, transmuxed, van een watermerk worden voorzien enzovoort, voordat deze aan clients wordt geleverd. Deze activiteiten worden gepland en uitgevoerd op meerdere achtergrondrolinstanties om hoge prestaties en een hoge beschikbaarheid te garanderen. Deze activiteiten worden taken genoemd. Elke taak bestaat uit atomische taken die daadwerkelijk werken op het assetbestand.

Zoals eerder al is aangegeven, wordt bij het werken met Azure Media Services meestal Adaptive Bitrate Streaming aan de clients geleverd. Met Media Services kunt u een dynamisch pakket met een van de volgende indelingen van MP4-bestanden met een adaptieve bitsnelheid maken: HTTP Live Streaming (HLS), Smooth Streaming en MPEG DASH.

Als u gebruik wilt maken van dynamische pakketten, moet u uw tussentijds (bron)bestand (trans)coderen naar een set MP4-bestanden met een adaptieve bitsnelheid of naar Smooth Streaming-bestanden met een adaptieve bitsnelheid.  

De volgende code toont u hoe u een codeertaak verzendt. De taak bevat één taak die aangeeft dat het tussentijdse bestand met **Media Encoder Standard** moet worden getranscodeerd in een set MP4-bestanden met een adaptieve bitsnelheid. De code verzendt de taak en wacht totdat de taak is voltooid.

Zodra de taak is voltooid, kunt u uw asset streamen of MP4-bestanden die zijn gemaakt naar aanleiding van een transcodering progressief downloaden.

Voeg de volgende methode toe aan de klasse Program.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>De asset publiceren en URL's ophalen voor streamen en progressief downloaden

Als u een asset wilt streamen of downloaden, moet u deze eerste publiceren door een locator te maken. Locators bieden toegang tot bestanden in de asset. Media Services ondersteunt twee typen locators: OnDemandOrigin-locators, voor het streamen van media (bijvoorbeeld MPEG DASH, HLS, of Smooth Streaming) en SAS-locators (Shared Access Signature), voor het downloaden van media-bestanden.

### <a name="some-details-about-url-formats"></a>Details over URL-indelingen

Nadat u de locators hebt gemaakt, kunt u de URL's maken die worden gebruikt om uw bestanden te streamen of te downloaden. In het voorbeeld in deze zelfstudie worden URL's geretourneerd die u in de juiste browsers kunt plakken. Deze sectie bevat korte voorbeelden van hoe verschillende indelingen er uitzien.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Een streaming-URL voor MPEG DASH heeft de volgende indeling:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Een streaming-URL voor HLS heeft de volgende indeling:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Een streaming-URL voor Smooth Streaming heeft de volgende indeling:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Een SAS-URL die wordt gebruikt om bestanden te downloaden, heeft de volgende indeling:

{blobcontainernaam}/{assetname}/{bestandsnaam}/{SAS-handtekening}

Media Services .NET SDK Extensions bieden handige Help-methoden die ingedeelde URL's voor de gepubliceerde asset retourneren.

De volgende code gebruikt .NET SDK Extensions om locators te maken en URL's op te halen die u kunt gebruiken om te streamen of progressief te downloaden. De code toont u ook hoe u bestanden downloadt naar een lokale map.

Voeg de volgende methode toe aan de klasse Program.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testen door uw inhoud af te spelen

Zodra u het programma uitvoert dat in de vorige sectie is gedefinieerd, worden er URL's in het consolevenster weergegeven die vergelijkbaar zijn met de volgende URL's.

URL's voor adaptief streamen:

Smooth Streaming

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

URL's voor progressief downloaden (audio en video).

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


Plak de URL in het URL-tekstvak in de [Azure Media Services-speler](https://aka.ms/azuremediaplayer) om de video te streamen.

Als u het progressief downloaden wilt testen, plakt u een URL in een browser (bijvoorbeeld Internet Explorer, Chrome of Safari).

Zie de volgende onderwerpen voor meer informatie:

- [Uw inhoud afspelen op bestaande spelers](media-services-playback-content-with-existing-players.md)
- [Een adaptieve MPEG-DASH-videostream insluiten in een HTML5-toepassing met DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Voorbeeld downloaden
Het volgende voorbeeld bevat de code die u hebt gemaakt in deze zelfstudie: [voorbeeld](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/

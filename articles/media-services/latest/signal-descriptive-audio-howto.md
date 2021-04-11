---
title: Beschrijvende audio sporen met Media Services v3
description: Volg de stappen in deze zelf studie voor het uploaden van een bestand, het coderen van de video, het toevoegen van beschrijvende geluids sporen en het streamen van uw inhoud met Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00a3fa397bf88520fa4923b6fbe7495c0aa0b8a2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277399"
---
# <a name="signal-descriptive-audio-tracks"></a>Beschrijvende geluids sporen voor signalen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

U kunt een commentaar track toevoegen aan uw video om visueel gehandicapten te helpen bij het volgen van de video-opname door naar de gesp roken tekst te Luis teren. In Media Services v3 signaleert u beschrijvende audio tracks door aantekeningen te maken op het audio spoor in het manifest bestand.

In dit artikel wordt beschreven hoe u een video codeert, een alleen-audio-MP4-bestand (AAC-codec) met een beschrijvende audio in het uitvoer activum uploadt en het ISM-bestand bewerkt om de beschrijvende audio toe te voegen.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](./account-create-how-to.md).
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.
- Bekijk [dynamische verpakkingen](encode-dynamic-packaging-concept.md).
- Bekijk de zelf studie [Video's uploaden, coderen en streamen](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Een invoerasset maken en er een lokaal bestand in uploaden 

Met de functie **CreateInputAsset** wordt een nieuwe [invoerasset](/rest/api/media/assets) gemaakt en het opgegeven lokale videobestand wordt hierin geladen. Deze **Asset** wordt gebruikt als invoer voor uw coderings taak. In Media Services v3 kan de invoer van een **taak** een **Asset** zijn of inhoud die u beschikbaar maakt voor uw Media Services-account via https-url's. 

Zie [dit artikel](job-input-from-http-how-to.md) voor meer informatie over het coderen van een HTTPS-URL.  

In Media Services v3 kunt u Azure Storage-API's gebruiken om bestanden te uploaden. Het volgende .NET-fragment laat zien hoe.

De volgende functie voert deze acties uit:

* Hiermee maakt u een **Asset** 
* Hiermee wordt een Beschrijf bare [SAS-URL](../../storage/common/storage-sas-overview.md) naar de container van het activum [in de opslag](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) opgehaald
* Uploadt het bestand naar de container in opslag met de SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Als u de naam van het gemaakte invoer element wilt door geven aan andere methoden, moet u ervoor zorgen dat u de eigenschap gebruikt voor `Name` het activa object dat wordt geretourneerd `CreateInputAssetAsync` , bijvoorbeeld inputAsset.name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Een uitvoer activum maken om het resultaat van de coderings taak op te slaan

In de [uitvoerasset](/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. De volgende functie laat zien hoe u een uitvoer activum maakt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Als u de naam van het gemaakte uitvoer activum wilt door geven aan andere methoden, moet u ervoor zorgen dat u de eigenschap gebruikt voor `Name` het activa object dat wordt geretourneerd `CreateIOutputAssetAsync` , bijvoorbeeld outputAsset.name. 

In het geval van dit artikel geeft u de `outputAsset.Name` waarde door aan `SubmitJobAsync` de `UploadAudioIntoOutputAsset` functies en.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Een trans formatie en een taak maken die het geüploade bestand codeert

Bij het coderen of verwerken van inhoud in Media Services is het een gangbaar patroon om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u dat recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt aangeroepen als een **transformatie**. Zie [Transformaties en taken](./transform-jobs-concept.md) voor meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten. 

In het volgende voor beeld wordt een Transform gemaakt (als deze niet bestaat).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Met de volgende functie wordt een taak verzonden.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. U kunt het beste Event Grid gebruiken om te wachten tot de taak is voltooid.

De taak doorloopt doorgaans de volgende statussen: **gepland**, **in de wachtrij geplaatst**, **verwerkt**, **voltooid** (de eind status). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de melding **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

Zie [Handling Event grid Events](monitoring/reacting-to-media-services-events.md)(Engelstalig) voor meer informatie.

## <a name="upload-the-audio-only-mp4-file"></a>Het MP4-bestand met alleen audio uploaden

Upload het extra MP4-bestand met alleen audio (AAC-codec) met een beschrijvende audio in het uitvoer element.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Hier volgt een voor beeld van een aanroep van de `UpoadAudioIntoOutputAsset` functie:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Het ISM-bestand bewerken

Wanneer uw coderings taak is voltooid, bevat de uitvoer-Asset de bestanden die door de coderings taak zijn gegenereerd. 

1. Navigeer in het Azure Portal naar het opslag account dat is gekoppeld aan uw Media Services-account. 
1. Zoek de container met de naam van uw uitvoer activum. 
1. Zoek in de container het. ISM-bestand en klik op **BLOB bewerken** (in het rechter venster). 
1. Bewerk het ISM-bestand door de informatie over het geüploade MP4-bestand (AAC-codec) met de beschrijvende audio toe te voegen en op **Opslaan** te drukken wanneer u klaar bent.

    U moet de para meters Accessibility en Role toevoegen aan het. ISM-bestand om de beschrijvende audio tracks te Signa leren. Het is uw verantwoordelijkheid om deze para meters op de juiste wijze in te stellen om een audio track als audio beschrijving te Signa leren. Voeg bijvoorbeeld `<param name="accessibility" value="description" />` en `<param name="role" value="alternate" />` toe aan het. ISM-bestand voor een specifiek audio spoor, zoals wordt weer gegeven in het volgende voor beeld.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Een streaming-Locator ophalen

Nadat de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U kunt dit doen in twee stappen: maak eerst een [streaming-locator](/rest/api/media/streaminglocators) en bouw vervolgens de streaming-URL's die clients kunnen gebruiken. 

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-Locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt uitgevoerd en de laatste keer totdat deze is verwijderd, tenzij u de optionele begin-en eind tijden configureert. 

Bij het maken van een [StreamingLocator](/rest/api/media/streaminglocators) moet u de gewenste **StreamingPolicyName** opgeven. In dit voor beeld moet u in-the-Clear (of niet-versleutelde inhoud) streamen, zodat het vooraf gedefinieerde beleid voor Clear streaming (**PredefinedStreamingPolicy. ClearStreamingOnly**) wordt gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal streaming-beleidsvermeldingen. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

Bij de volgende code wordt ervan uitgegaan dat u de functie met een unieke locatorName aanroept.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Terwijl in het voorbeeld in dit onderwerp streaming wordt behandeld, kunt u dezelfde aanroep gebruiken om een streaming-locator te maken voor het leveren van video via progressief downloaden.

### <a name="get-streaming-urls"></a>Streaming-URL's ophalen

Nu de [streaming-locator](/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen, zoals weergegeven in **GetStreamingURLs**. Als u een URL wilt samenstellen, moet u de hostnaam van het [streaming-eindpunt](/rest/api/media/streamingendpoints) en het pad van de **streaming-locator** samenvoegen. In dit voorbeeld wordt het *standaard***streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard***streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

> [!NOTE]
> In deze methode hebt u de locatorName nodig die is gebruikt bij het maken van de **streaming-locator** voor de uitvoerasset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testen met Azure Media Player

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL:** een van de waarden van de streaming-URL die u hebt ontvangen van uw toepassing. 
 
     U kunt de URL plakken in de HLS-, Dash-, of Smooth-indeling. Azure Media Player schakelt over op naar een geschikt streaming-protocol zodat de stream automatisch op uw apparaat wordt afgespeeld.
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="next-steps"></a>Volgende stappen

[Video's analyseren](analyze-videos-tutorial.md)

---
title: Veelgestelde vragen over Azure Media Services
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013377"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Veelgestelde vragen over Media Services v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit artikel worden veelgestelde vragen behandeld die worden gegenereerd door de gebruikers community van Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Veelgestelde vragen over algemene AMS

V: hoe streamt u naar Apple iOS-apparaten?

A: Voeg ' (Format = M3U8-AAPL) ' toe aan het gedeelte '/manifest ' van de URL om de oorsprong van de streaming-server te laten weten HLS-inhoud te retour neren voor het gebruik van Apple iOS systeem eigen apparaten (Zie voor meer informatie [afleveren van inhoud](media-services-deliver-content-overview.md)),

V: hoe schaal ik indexeren?

A: de gereserveerde eenheden zijn hetzelfde voor het coderen en indexeren van taken. Volg de instructies voor [het schalen van gereserveerde encoding-eenheden](media-services-scale-media-processing-overview.md). **Houd er rekening mee** dat de prestaties van de Indexeer functie niet worden beïnvloed door het gereserveerde eenheids type.

V: Ik heb een video geüpload, gecodeerd en gepubliceerd. Wat is de reden waarom de video niet wordt afgespeeld wanneer ik deze probeer te streamen?

A: een van de meest voorkomende oorzaken is dat u het streaming-eind punt niet hebt van waaruit u wilt afspelen in de **actieve** status.  

V: kan ik samen stellen op een live stream?

A: het samen stellen op live streams wordt momenteel niet aangeboden in Azure Media Services. u moet de computer dus vooraf op de computers opstellen.

V: kan ik Azure CDN gebruiken met live streamen?

A: Media Services ondersteunt de integratie met Azure CDN (Zie [streaming-eind punten beheren in een Media Services-account](media-services-portal-manage-streaming-endpoints.md)) voor meer informatie.  U kunt live streamen met CDN gebruiken. Azure Media Services biedt Smooth Streaming, HLS en MPEG-DASH-uitvoer. Al deze indelingen gebruiken HTTP voor het overdragen van gegevens en profiteren van de voor delen van HTTP-caching. De werkelijke video-en audio gegevens van live streamen zijn verdeeld over fragmenten en deze afzonderlijke fragmenten worden in het cache geheugen in CDN opgehaald. Alleen gegevens moeten worden vernieuwd, zijn de gegevens in het manifest. Met CDN worden de manifest gegevens regel matig vernieuwd.

V: biedt Azure Media Services ondersteuning voor het opslaan van installatie kopieën?

A: als u alleen JPEG-of PNG-afbeeldingen wilt opslaan, moet u deze in Azure Blob Storage bewaren. U hoeft ze niet in uw Media Services-account te plaatsen, tenzij u ze wilt blijven gebruiken voor uw video-of audio-assets. Of als u de installatie kopieën wilt gebruiken als overlays in de video encoder. Media Encoder Standard ondersteunt het bedekken van afbeeldingen aan de bovenkant van Video's en dat is wat JPEG en PNG als ondersteunde invoer indelingen bevat. Zie [overlays maken](media-services-advanced-encoding-with-mes.md#overlay)voor meer informatie.

V: hoe kan ik assets van het ene Media Services account naar het andere kopiëren?

A: als u assets van een Media Services account wilt kopiëren met behulp van .NET, gebruikt u [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) extension methode die beschikbaar is in de [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) -opslag plaats. Zie [deze](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum-thread voor meer informatie.

V: wat zijn de ondersteunde tekens voor het benoemen van bestanden bij het werken met AMS?

A: Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van Url's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}. Origin. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Daarom is het percentage code ring niet toegestaan. De waarde van de eigenschap **name** mag niet de volgende tekens voor [percentage versleuteling](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)bevatten:! * ' ();: @ &= + $,/?% # [] '. Daarnaast kan er slechts één '. ' zijn voor de bestandsnaam extensie.

V: verbinding maken met behulp van REST?

A: Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

V: hoe kan ik een video draaien tijdens het coderings proces?

A: de [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) biedt ondersteuning voor rotaties op basis van de hoek van 90/180/270. Het standaard gedrag is ' auto ', waarbij wordt geprobeerd om de meta gegevens van de draai bewerking in het bestand binnenkomend MP4/MOV te detecteren en te compenseren. Neem het volgende **bron** element op in een van de [vooraf gedefinieerde JSON](media-services-mes-presets-overview.md)-voor instellingen:

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

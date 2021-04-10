---
title: Voorbeelden van Media Services v3
description: Dit artikel bevat een lijst met alle voor beelden die beschikbaar zijn voor Media Services v3, geordend op methode en SDK.  Voor beelden zijn .NET, Node.JS, python en Java, evenals REST met postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: bfe39020da0be245f47d0c954de7598914d6534d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105051335"
---
# <a name="media-services-v3-samples"></a>Voorbeelden van Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dit artikel bevat een lijst met alle voor beelden die beschikbaar zijn voor Media Services, geordend op methode en SDK.  Voor beelden zijn .NET, Node.JS, python en Java, evenals REST met postman.

## <a name="rest-postman-collection"></a>REST postman-verzameling

De [rest postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) -voor beelden bevat een postman-omgeving en-verzameling die u kunt importeren in de Postman-client.

## <a name="samples-by-sdk"></a>Voor beelden per SDK

U vindt hier een beschrijving en koppelingen naar de voor beelden die u op elk van de tabbladen kunt vinden.

## <a name="net"></a>[.NET](#tab/net/)

| Map | Description |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Een taak verzenden met behulp van een ingebouwde voor instelling en een HTTP-URL-invoer, een uitvoer activum voor streaming publiceren en resultaten voor verificatie downloaden.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Een taak verzenden met een aangepaste voor instelling van H. 264-code ring en een HTTP-URL-invoer, een uitvoer activum publiceren voor streaming en de resultaten voor verificatie downloaden.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Een taak verzenden met behulp van een aangepaste HEVC-voor instelling voor versleuteling en een HTTP-URL-invoer, een uitvoer activum publiceren voor streaming en de resultaten voor verificatie downloaden.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Een taak verzenden met behulp van de JobInputSequence om twee of meer assets samen te voegen die kunnen worden geknipt door de begin-of eind tijd. Het resulterende gecodeerde bestand is één video waarbij alle activa samen worden gecombineerd.  In het voor beeld wordt ook output Asset gepubliceerd voor streaming en download resultaten voor verificatie.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Een taak verzenden met behulp van een aangepaste voor instelling met een miniatuur Sprite en een HTTP-URL-invoer, een uitvoer activum voor streaming publiceren en resultaten voor verificatie downloaden.|
| [Live-LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Een LiveEvent maken met een volledig archief van Maxi maal 25 uur en een filter op de Asset met 5 minuten DVR-venster. Een filter gebruiken om een Locator voor streaming te maken.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Een video Analyzer-trans formatie maken, een video bestand uploaden naar een invoer-Asset, een taak bij de trans formatie verzenden en de resultaten voor verificatie downloaden.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Een Audio Analyzer-trans formatie maken, een media bestand uploaden naar een invoer-Asset, een taak bij de trans formatie verzenden en de resultaten voor verificatie downloaden.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Een trans formatie maken met een ingebouwde AdaptiveStreaming-voor instelling, een taak verzenden, een ContentKeyPolicy maken met behulp van een geheime sleutel, de ContentKeyPolicy koppelen aan StreamingLocator, een Token ophalen en een URL afdrukken voor afspelen in Azure Media Player. Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES-128 en Azure Media Player gebruikt het token om te ontsleutelen.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Een trans formatie maken met een ingebouwde AdaptiveStreaming-voor instelling, een taak indienen, een ContentKeyPolicy met Widevine-configuratie maken met behulp van een geheime sleutel, de ContentKeyPolicy koppelen aan StreamingLocator, een Token ophalen en een URL afdrukken voor afspelen in een Widevine-speler. Wanneer een gebruiker Widevine beveiligde inhoud aanvraagt, vraagt de Player-toepassing een licentie aan bij de Media Services License-Service. Als de spelertoepassing is geautoriseerd, ontvangt de speler een licentie van de licentieservice van Media Services. Een Widevine-licentie bevat de ontsleutelings sleutel die door de client speler kan worden gebruikt om de inhoud te ontsleutelen en te streamen.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Een trans formatie maken met een ingebouwde AdaptiveStreaming-voor instelling, een taak indienen, een ContentKeyPolicy met PlayReady-configuratie maken met behulp van een geheime sleutel, de ContentKeyPolicy koppelen aan StreamingLocator, een Token ophalen en een URL afdrukken voor afspelen in een Azure Media Player. Wanneer een gebruiker met PlayReady beveiligde inhoud aanvraagt, vraagt de Player-toepassing een licentie aan bij de Media Services License-Service. Als de spelertoepassing is geautoriseerd, ontvangt de speler een licentie van de licentieservice van Media Services. Een PlayReady-licentie bevat de ontsleutelings sleutel die door de client speler kan worden gebruikt om de inhoud te ontsleutelen en te streamen.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Uw inhoud dynamisch versleutelen met PlayReady en Widevine DRM en de inhoud afspelen zonder een licentie van de licentie service aan te vragen. U ziet hoe u een trans formatie maakt met ingebouwde AdaptiveStreaming-voor instelling, een taak verzendt, een ContentKeyPolicy met open beperking en PlayReady/Widevine permanente configuratie maakt, de ContentKeyPolicy koppelt aan een StreamingLocator en een URL afdrukt voor afspelen.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Een trans formatie maken met een ingebouwde AdaptiveStreaming-voor instelling, een taak verzenden, een Asset-filter en een account filter maken, de filters koppelen aan streaming-Locators en afdruk-url's voor afspelen.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Een trans formatie maken met een ingebouwde AdaptiveStreaming-voor instelling, een taak verzenden, uitvoer activum publiceren voor HLS en STREEPJES streaming.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Richt lijnen en aanbevolen procedures voor een productie systeem met behulp van on-demand encoding of Analytics. Lezers moeten beginnen met het aanvullende artikel [hoge Beschik baarheid met Media Services en VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding). Er is een apart oplossings bestand beschikbaar voor het [HighAvailabilityEncodingStreaming](/HighAvailabilityEncodingStreaming/Readme.md) -voor beeld. |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Map|Description|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Verbinding maken en assets opnemen |
|[Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Eenvoudige live streamen. **Waarschuwing**, Controleer of alle resources zijn opgeruimd en dat deze niet meer worden gefactureerd in de Portal wanneer u gebruikmaakt van Live.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Het uploaden van een lokaal bestand of code ring vanuit een bron-URL, het gebruik van Storage SDK voor het downloaden van inhoud en het streamen van gegevens naar een speler. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Coderen en streamen met behulp van Widevine en PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Het gebruik van de voor instellingen video en Audio Analyzer voor het genereren van meta gegevens en inzichten uit een video-of audio bestand. |

## <a name="python"></a>[Python](#tab/python)

Op dit moment is er één python- [voor beeld met een basis codering met python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Map|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Het analyseren van audio in een media bestand. |
|Inhoudsbeveiliging|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Uw inhoud dynamisch versleutelen met AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Hoe u uw inhoud dynamisch versleutelt met PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Uw inhoud dynamisch versleutelen met Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Hoe u uw inhoud dynamisch kunt versleutelen met FairPlay DRM en de inhoud kunt afspelen zonder een licentie van de licentie service aan te vragen.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Uw inhoud dynamisch versleutelen met PlayReady en Widevine DRM en de inhoud afspelen zonder een licentie van de licentie service aan te vragen.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Inhoud filteren met behulp van Asset-en account filters.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Hoe u VOD-inhoud dynamisch verpakt in HLS/DASH voor streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Het maken van een live-gebeurtenis met een volledig archief van Maxi maal 25 uur en een filter op de Asset met 5 minuten DVR-venster en het maken van een Locator voor streaming en het filter gebruiken.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Het maken van een live-gebeurtenis met een volledig archief van Maxi maal 25 uur en een filter op de Asset met 5 minuten DVR-venster en het maken van een Locator voor streaming en het filter gebruiken.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Een aangepaste coderings transformatie maken met behulp van de StandardEncoderPreset-instellingen.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Een taak verzenden met behulp van een ingebouwde voor instelling en een HTTP-URL-invoer, een uitvoer activum voor streaming publiceren en resultaten voor verificatie downloaden.|

---

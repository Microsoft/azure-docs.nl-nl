---
title: Release opmerkingen bij Azure Media Services v3
description: Om up-to-date te blijven met de meest recente ontwikkelingen, biedt dit artikel u de meest recente updates op Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: fc48c9b8a0a7510dd8792c959c1f63a0340f89ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011201"
---
# <a name="azure-media-services-v3-release-notes"></a>Release opmerkingen bij Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="known-issues"></a>Bekende problemen

## <a name="february-2021"></a>Februari 2021

### <a name="hevc-encoding-support-in-standard-encoder"></a>Ondersteuning voor HEVC-code ring in de standaard encoder

De standaard encoder ondersteunt nu ondersteuning voor het coderen van 8-bits HEVC (H. 265). HEVC-inhoud kan worden geleverd en verpakt via de dynamische Pakketer met behulp van de hev1-indeling.  

Er is een nieuwe, aangepaste .NET-code ring met HEVC-voor beeld beschikbaar in de [opslag plaats Media-Services-v3-DotNet Git hub](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC).
Naast aangepaste code ring zijn de volgende nieuwe ingebouwde HEVC-coderings definities nu beschikbaar:

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

Klanten die voorheen HEVC gebruiken in de Premium encoder van de v2-API, moeten migreren om de nieuwe ondersteuning voor HEVC-code ring te gebruiken in de standaard encoder.

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Aankondiging van Azure Media Services v2 API en Sdk's-afschaffing

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Werk uw Azure Media Services REST API en Sdk's bij naar v3 op 29 februari 2024

Omdat versie 3 van Azure Media Services REST API en client-Sdk's voor .NET en Java meer mogelijkheden hebben dan versie 2, wordt versie 2 van de Azure Media Services REST API en de client-Sdk's voor .NET en Java buiten gebruik gesteld.

We raden u aan de switch sneller te laten profiteren van de uitgebreide voor delen van versie 3 van Azure Media Services REST API en de client-Sdk's voor .NET en Java.
Versie 3 biedt:
 
- 24x7 ondersteuning voor Live-gebeurtenissen
- ARM REST Api's, client-Sdk's voor .NET core, Node.js, Python, Java, Go en Ruby.
- Door de klant beheerde sleutels, integratie van vertrouwde opslag, ondersteuning voor persoonlijke koppelingen en [meer](https://review.docs.microsoft.com/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Actie vereist

Als u de onderbreking van uw werk belastingen tot een minimum wilt beperken, raadpleegt u de [migratie handleiding](https://go.microsoft.com/fwlink/?linkid=2149150&clcid=0x409) om uw code over te stappen van versie 2 API en sdk's naar versie 3 API en SDK vóór 29 februari 2024.
**Na 29 februari 2024** wordt door Azure Media Services geen verkeer meer geaccepteerd op de rest API van versie 2, de arm Account Management API-versie 2015-10-01 of van de sdk's van versie 2 .net client. Dit geldt ook voor open source client-SDK'S van derden die de API van versie 2 kunnen aanroepen.  

Zie de officiële [aankondiging van Azure-updates](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

### <a name="standard-encoder-support-for-v2-api-features"></a>Standard encoder-ondersteuning voor v2 API-functies

Naast de nieuwe, toegevoegde ondersteuning voor HEVC (H. 265)-code ring zijn de volgende functies nu beschikbaar in de 2020-05-01-versie van de coderings-API.

- Meerdere invoer bestanden worden nu ondersteund met behulp van de nieuwe **JobInputClip** -ondersteuning.
    - Er is een voor beeld beschikbaar voor .NET waarin wordt getoond hoe [twee assets samen worden gecombineerd](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets).
- Met de optie voor het bijhouden van audio nummers kunnen klanten de inkomende audio sporen selecteren en toewijzen en deze naar de uitvoer voor code ring routeren
    - Zie de [rest API OpenAPI voor meer informatie](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385) over **AudioTrackDescriptor** en het bijhouden van de selectie
- De selectie voor code ring bijhouden: Hiermee kunnen klanten nummers kiezen van een ABR-bron bestand of een live-archief met meerdere bitrate sporen. Zeer nuttig voor het genereren van Mp4's uit de bestanden van de live-gebeurtenis archivering.
    - Zie [VideoTrackDescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562)
- Mogelijkheden voor redactie (vervaging) die zijn toegevoegd aan FaceDetector
    - Zie de modi [redactie](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) en [gecombineerd](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) van de voor instelling FaceDetector

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>Nieuwe client SDK-releases voor 2020-05-01 versie van de Azure Media Services-API

Nieuwe client SDK-versies voor alle beschik bare talen zijn nu beschikbaar met de bovenstaande functies.
Werk de nieuwste client-Sdk's in uw code bases bij met behulp van uw pakket beheer.

- [.NET SDK-pakket 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js type script-versie 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python Azure-verbeheerder-Media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-Beta. 2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>Nieuwe beveiligings functies die beschikbaar zijn in de 2020-05-01-versie van de Azure Media Services-API

- Door de **[klant beheerde sleutels](concept-use-customer-managed-keys-byok.md)**: inhouds sleutels en andere gegevens die zijn opgeslagen in accounts die zijn gemaakt met de versie-API 2020-05-01, worden versleuteld met een account sleutel. Klanten kunnen een sleutel opgeven om de account sleutel te versleutelen.

- **[Vertrouwde opslag](concept-trusted-storage.md)**: Media Services kunnen worden geconfigureerd voor toegang tot Azure Storage met behulp van een beheerde identiteit die is gekoppeld aan het Media Services-account. Wanneer opslag accounts worden geopend met behulp van een beheerde identiteit, kunnen klanten meer beperkende netwerk-Acl's configureren voor het opslag account zonder dat Media Services scenario's worden geblokkeerd.

- **[Beheerde identiteiten](concept-managed-identities.md)**: klanten kunnen een door het systeem toegewezen beheerde identiteit inschakelen voor een Media Services account om toegang te bieden tot sleutel kluizen (voor door de klant beheerde sleutels) en opslag accounts (voor vertrouwde opslag).


### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>Bijgewerkte type script-Node.js-voor beelden met isomorphic SDK voor Java script

De Node.js-voor beelden zijn bijgewerkt voor gebruik van de meest recente isomorphic-SDK. In de voor beelden wordt nu het gebruik van type script weer gegeven. Daarnaast is er een nieuw live streaming-voor beeld toegevoegd voor Node.js/typescript.

Zie de meest recente voor beelden in de **[Media-Services-v3-node-zelf studies](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** Git hub opslag plaats.

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>Nieuwe stand-by-modus om sneller opstarten vanuit een warme status te ondersteunen

Live-gebeurtenissen bieden nu ondersteuning voor een goedkope facturerings modus voor "stand-by". Hierdoor kunnen klanten live-gebeurtenissen vooraf toewijzen tegen lagere kosten voor het maken van ' hot Pools '. Klanten kunnen vervolgens de stand-by-Live-gebeurtenissen gebruiken om sneller over te stappen op de actieve status dan bij het maken van koud.  Dit vermindert de tijd om het kanaal aanzienlijk te starten en maakt snelle toewijzing van dynamische groepen mogelijk van machines die in een lagere prijs modus worden uitgevoerd.
Bekijk [hier](https://azure.microsoft.com/pricing/details/media-services)de meest recente prijs informatie.
Zie het artikel- [live gebeurtenis Staten en facturering](https://docs.microsoft.com/azure/media-services/latest/live-event-states-billing) voor meer informatie over de stand-by status en de andere statussen van Live Events.

## <a name="december-2020"></a>December 2020

### <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Media Services is nu beschikbaar in de regio Noor wegen Oost in de Azure Portal.  Er bevindt zich geen restV2 in deze regio.

## <a name="october-2020"></a>Oktober 2020

### <a name="basic-audio-analysis"></a>Basis analyse van audio

De vooraf ingestelde audio analyse bevat nu een prijs categorie voor de Basic-modus. De nieuwe Basic Audio Analyzer-modus biedt een goedkope optie voor het extra heren van spraak-transcriptie en het opmaken van uitvoer bijschriften en ondertiteling. Deze modus voert spraak-naar-tekst transcriptie en genereert een ondertitelings bestand van VTT. De uitvoer van deze modus bevat een Insights-JSON-bestand met alleen de tref woorden, transcriptie en timing gegevens. Automatische taal detectie en diarization van de spreker zijn niet opgenomen in deze modus. Zie de lijst met [ondersteunde talen.](analyzing-video-audio-files-concept.md#built-in-presets)

Klanten die Indexeer functie v1 en Indexeer functie v2 gebruiken, moeten worden gemigreerd naar de standaard instelling voor de analyse van audio.

Zie [video-en audio bestanden analyseren](analyzing-video-audio-files-concept.md)voor meer informatie over de Basic Audio Analyzer-modus.  Zie [een eenvoudige audio transformatie maken](how-to-create-basic-audio-transform.md)voor meer informatie over het gebruik van de Basic Audio Analyzer-modus met de rest API.

### <a name="live-events"></a>Livegebeurtenissen

Updates van de meeste eigenschappen zijn nu toegestaan wanneer Live-gebeurtenissen worden gestopt. Daarnaast mogen gebruikers een voor voegsel opgeven voor de statische hostnaam voor de invoer en preview-Url's van de live-gebeurtenis. VanityUrl wordt nu aangeroepen `useStaticHostName` om het doel van de eigenschap beter weer te geven.

Live-gebeurtenissen hebben nu de status stand-by.  Bekijk [Live Events en live outputs in Media Services](./live-events-outputs-concept.md).

Een live-gebeurtenis ondersteunt het ontvangen van verschillende invoer hoogte-breedte verhoudingen. Met de stretch-modus kunnen klanten het stretch gedrag voor de uitvoer opgeven.

Met Live Encoding wordt nu de mogelijkheid toegevoegd voor het uitvoeren van vaste-sleutel frame-interval fragmenten tussen 0,5 en 20 seconden.

### <a name="accounts"></a>Accounts

> [!WARNING]
> Als u een Media Services-account maakt met de 2020-05-01 API-versie, werkt dit niet met RESTv2 

## <a name="august-2020"></a>Augustus 2020

### <a name="dynamic-encryption"></a>Dynamische versleuteling

Ondersteuning voor de verouderde (PIFF 1,1) versleuteling van de oudere PlayReady-bestands indeling is nu beschikbaar in de dynamische Pakketset. Dit biedt ondersteuning voor verouderde slimme TV-sets van Samsung en LG waarmee de vroege concepten van de Common Encryption Standard (CENC) die door micro soft zijn gepubliceerd, zijn geïmplementeerd.  De PIFF 1,1-indeling wordt ook wel bekend als de versleutelings indeling die eerder werd ondersteund door de Silverlight-client bibliotheek. Het enige use-case scenario voor deze versleutelings indeling is het doel van de verouderde Smart TV-markt als er een niet-gelastig aantal Smart Tv's in sommige regio's wordt ondersteund dat alleen ondersteuning biedt voor Smooth Streaming met PIFF 1,1-versleuteling.

Als u de nieuwe ondersteuning voor PIFF 1,1-versleuteling wilt gebruiken, wijzigt u de versleutelings waarde in ' piff ' in het URL-pad van de streaming-Locator. Zie [Content Protection-overzicht](content-protection-overview.md) voor meer informatie.
Bijvoorbeeld: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Ondersteuning voor PIFF 1,1 wordt geboden als een achterwaarts compatibele oplossing voor slimme TV (Samsung, LG) waarmee de vroege versie van Silverlight van Common Encryption is geïmplementeerd. U wordt aangeraden alleen de PIFF-indeling te gebruiken wanneer dit nodig is voor de ondersteuning van oudere Samsung-en LG Smart Tv's die worden verzonden tussen 2009-2015 en die de PIFF 1,1-versie van PlayReady-versleuteling ondersteunen. 

## <a name="july-2020"></a>Juli 2020

### <a name="live-transcriptions"></a>Live-transcripties

Live transcripties ondersteunt nu 19 talen en 8 regio's.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Uw inhoud beveiligen met Media Services en Azure AD

We hebben een zelf studie gepubliceerd met de naam [end-to-end inhouds beveiliging met behulp van Azure AD](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Hoge beschikbaarheid

We hebben een hoge Beschik baarheid gepubliceerd met het [overzicht](./media-services-high-availability-encoding.md) van Media Services en video on demand (VOD) en voor [beeld](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="june-2020"></a>Juni 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Preview-versie van live video-analyses op IoT Edge

De preview-versie van live video Analytics op IoT Edge openbaar geworden. Zie [release opmerkingen](../live-video-analytics-edge/release-notes.md)voor meer informatie.

Live video Analytics op IoT Edge is een uitbrei ding van de media service familie. Zo kunt u live video analyseren met AI-modellen van uw keuze op uw eigen edge-apparaten en deze video optioneel vastleggen en opnemen. U kunt nu apps bouwen met realtime video analyses aan de rand zonder dat u zich zorgen hoeft te maken over de complexiteit van het bouwen en gebruiken van een live video pijplijn.

## <a name="may-2020"></a>Mei 2020

Azure Media Services is nu algemeen beschikbaar in de volgende regio's: "Duitsland-noord", "Duitsland-west-centraal", "Zwitserland-noord" en "Zwitserland-west". Klanten kunnen Media Services naar deze regio's implementeren met behulp van de Azure Portal.

## <a name="april-2020"></a>April 2020

### <a name="improvements-in-documentation"></a>Verbeteringen in de documentatie

Azure Media Player documenten zijn gemigreerd naar de [Azure-documentatie](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Januari 2020

### <a name="improvements-in-media-processors"></a>Verbeteringen in Media-processors

- Verbeterde ondersteuning voor geïnterlinieerde bronnen in video analyse: dergelijke inhoud wordt nu niet-geïnterlinieerd voordat ze worden verzonden naar interferentie-engines.
- Wanneer u miniaturen genereert met de ' beste ' modus, zoekt het coderings programma nu meer dan 30 seconden om een frame te selecteren dat niet Monochromatic is.

### <a name="azure-government-cloud-updates"></a>Cloud updates Azure Government

Media Services GA'ed in de volgende Azure Government regio's: *USGov Arizona* en *USGov Texas*.

## <a name="december-2019"></a>December 2019

CDN-ondersteuning is toegevoegd voor de *oorsprong: prefetch-* headers voor Live en video on-demand streaming; beschikbaar voor klanten die een direct-contract met Akamai CDN hebben. Origin-Assist CDN-Prefetch functie omvat de volgende HTTP-header-uitwisselingen tussen Akamai CDN en Azure Media Services Origin:

|HTTP-header|Waarden|Afzender|Ontvanger|Doel|
| ---- | ---- | ---- | ---- | ----- |
|CDN-oorsprong-assistentie-prefetch-ingeschakeld | 1 (standaard) of 0 |CDN|Oorsprong|Om aan te geven dat CDN prefetch is ingeschakeld|
|CDN-oorsprong-assisteren-prefetch-pad| Voorbeeld: <br/>Fragmenten (video = 1400000000, Format = mpd-time-CMAF)|Oorsprong|CDN|Het prefetch-pad naar CDN opgeven|
|CDN-oorsprong-assistentie-prefetch-aanvraag|1 (prefetch-aanvraag) of 0 (normale aanvraag)|CDN|Oorsprong|Om aan te geven dat de aanvraag van CDN een prefetch is|

Als u een deel van de koptekst uitwisseling in actie wilt zien, kunt u de volgende stappen uitvoeren:

1. Gebruik postman of krul om een aanvraag voor het Media Services van de oorsprong van een audio-of video segment of fragment uit te geven. Zorg ervoor dat u de koptekst CDN-oorsprong-assistentie-prefetch-ingeschakeld: 1 in de aanvraag toevoegt.
2. In het antwoord ziet u de header CDN-Origin-Assist-prefetch-pad met een relatief pad als waarde.

## <a name="november-2019"></a>November 2019

### <a name="live-transcription-preview"></a>Live transcriptie preview

Live transcriptie is nu beschikbaar in de open bare preview-versie en kan worden gebruikt in de regio vs-West 2.

Live transcriptie is ontworpen om samen te werken met Live-gebeurtenissen als een invoeg toepassing.  Het wordt ondersteund op zowel Pass-through-als standaard-of Premium-code ring van Live-gebeurtenissen.  Als deze functie is ingeschakeld, gebruikt de service de functie voor [spraak-naar-tekst](../../cognitive-services/speech-service/speech-to-text.md) van Cognitive Services om de gesp roken woorden in de binnenkomende audio naar tekst te transcriberen. Deze tekst wordt vervolgens beschikbaar gesteld voor levering en video en audio in MPEG-DASH-en HLS-protocollen. De facturering is gebaseerd op een nieuwe add-on meter die extra kosten voor de live-gebeurtenis is wanneer de status wordt uitgevoerd.  Zie [Live transcriptie](live-transcription.md) voor meer informatie over Live transcriptie en facturering

> [!NOTE]
> Live transcriptie is momenteel alleen beschikbaar als preview-functie in de regio vs-West 2. Het biedt alleen ondersteuning voor transcriptie van gesp roken woorden in het Engels (en-US).

### <a name="content-protection"></a>Inhoudsbeveiliging

De functie voor het voor *komen van tokens* voor het opnieuw afspelen in een beperkt aantal regio's in september is nu beschikbaar in alle regio's.
Media Services klanten kunnen nu een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of licentie aan te vragen. Zie [token replay voor komen](content-protection-overview.md#token-replay-prevention)voor meer informatie.

### <a name="new-recommended-live-encoder-partners"></a>Nieuwe aanbevolen partners voor Live encoders

Er is ondersteuning toegevoegd voor de volgende nieuwe aanbevolen partner encoders voor RTMP live streamen:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 en Max. actie camera's](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Verbeteringen voor bestands codering

- Er is nu een nieuwe voor instelling voor het coderen van inhoud beschikbaar. Er wordt een set GOP terug-uitgelijnde Mp4's gemaakt met behulp van inhoud-bewuste code ring. Op basis van de invoer inhoud voert de service een eerste licht gewicht analyse van de invoer inhoud uit. Deze resultaten worden gebruikt om het optimale aantal lagen, de juiste bitsnelheid en resolutie-instellingen voor levering door adaptieve streaming te bepalen. Deze standaard instelling is met name van toepassing op Video's met een lage complexiteit en middel grote complexiteit, waarbij de uitvoer bestanden een lagere bitsnelheid hebben, maar met een kwaliteit die nog steeds een goede ervaring voor kijkers biedt. De uitvoer bevat MP4-bestanden met Interleaved video-en audio-indeling. Zie de [open API-specificaties](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)voor meer informatie.
- Verbeterde prestaties en multithreading voor de de in de standaard encoder. Onder specifieke voor waarden moet de klant een prestatie verbetering van 5-40% VOD-code ring zien. Inhoud met weinig complexiteit die is gecodeerd in meerdere bitsnelheden, ziet de hoogste prestatie verhoging. 
- Standaard codering onderhoudt nu een reguliere GOP terug-uitgebracht voor de VFR-inhoud (variable frame rate) tijdens VOD-code ring wanneer u de GOP terug-instelling op basis van tijd gebruikt.  Dit betekent dat de klant de prijs informatie voor gemengde frames indient die varieert tussen 15-30 fps, bijvoorbeeld de normale GOP terug-afstanden die worden berekend voor de uitvoer naar Adaptive Bitrate Streaming MP4-bestanden. Dit verbetert de mogelijkheid om naadloos tussen sporen te scha kelen bij het leveren van HLS of een streepje. 
-  Verbeterde AV-synchronisatie voor variabele frame frequentie (VFR)-bron inhoud

### <a name="video-indexer-video-analytics"></a>Video Indexer, video analyse

- Keyframes die zijn geëxtraheerd met behulp van de VideoAnalyzer-voor instelling zijn nu in de oorspronkelijke resolutie van de video in plaats van het formaat te wijzigen. Met een hoge resolutie voor keyframes beschikt u over originele installatie kopieën en kunt u gebruikmaken van de op afbeeldingen gebaseerde kunst matige intelligentie modellen die worden verstrekt door de micro soft-Computer Vision en Custom Vision-Services om nog meer inzicht te krijgen in uw video.

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Live lineaire code ring van Live Events

Media Services v3 kondigt de preview van 24 uur x 365 dagen aan live lineaire code ring van Live Events aan.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van *Azure media indexer* en *Azure media indexer 2 Preview* aangekondigd. Zie het artikel  [verouderde onderdelen](../previous/legacy-components.md) voor de pensioen datums. [Azure Media Services video indexer](../video-indexer/index.yml) vervangt deze verouderde media processors.

Zie [Migrate from Azure media indexer en Azure media indexer 2 to Azure Media Services video indexer](../previous/migrate-indexer-v1-v2.md)voor meer informatie.

## <a name="august-2019"></a>Augustus 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Zuid-Afrika regionale paar is geopend voor Media Services 

Media Services is nu beschikbaar in Zuid-Afrika-noord en Zuid-Afrika-west regio's.

Zie [Clouds en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. deze worden buiten gebruik gesteld. Voor de pensioen datums raadpleegt u dit artikel over [verouderde onderdelen](../previous/legacy-components.md) .

Zie [WAME migreren naar Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) en [aam migreren naar Media Encoder Standard](../previous/migrate-azure-media-encoder.md)voor meer informatie.
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Inhoudsbeveiliging

Wanneer inhoud die wordt beveiligd met token beperking wordt gestreamd, moeten eind gebruikers een token verkrijgen dat wordt verzonden als onderdeel van de aanvraag voor de sleutel levering. Met de functie voor het voor *komen van tokens* kunnen Media Services klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of licentie aan te vragen. Zie [token replay voor komen](content-protection-overview.md#token-replay-prevention)voor meer informatie.

Vanaf juli was de preview-functie alleen beschikbaar in VS Centraal en VS-West-centraal.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Video subknipsel

U kunt nu een video knippen of subfragmenten wanneer u deze codeert met behulp van een [taak](/rest/api/media/jobs). 

Deze functionaliteit werkt met elke [trans formatie](/rest/api/media/transforms) die is gebouwd met behulp van de [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -voor instellingen of de [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -voor waarden. 

Zie voor beelden:

* [Een video met .NET knippen](subclip-video-dotnet-howto.md)
* [Een video met REST samenknippen](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Mei 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor ondersteuning voor Media Services Diagnostische logboeken en metrische gegevens

U kunt nu Azure Monitor gebruiken om telemetriegegevens weer te geven die zijn verzonden door Media Services.

* Gebruik de diagnostische logboeken van Azure Monitor om aanvragen te bewaken die worden verzonden door het Media Services key delivery-eind punt. 
* Bewaak de metrische gegevens die worden verzonden door Media Services [streaming-eind punten](streaming-endpoint-concept.md).   

Zie [Media Services metrische gegevens en Diagnostische logboeken bewaken](media-services-metrics-diagnostic-logs.md)voor meer informatie.

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Ondersteuning voor meerdere audio-tracks in dynamische pakketten 

Bij het streamen van assets met meerdere audio sporen met meerdere codecs en talen, ondersteunt [dynamische verpakking](dynamic-packaging-overview.md) nu meerdere audio sporen voor de HLS-uitvoer (versie 4 of hoger).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Het regionale paar van Korea is geopend voor Media Services 

Media Services is nu beschikbaar in Korea-centraal en Korea-zuid regio's. 

Zie [Clouds en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

### <a name="performance-improvements"></a>Prestatieverbeteringen

Er zijn updates toegevoegd die Media Services prestatie verbeteringen bevatten.

* De maximale bestands grootte die wordt ondersteund voor de verwerking, is bijgewerkt. Zie, [quota en limieten](limits-quotas-constraints.md).
* De [versleutelings snelheid wordt verbeterd](concept-media-reserved-units.md).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nieuwe voor instellingen

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) is toegevoegd aan de ingebouwde analyse-voor instellingen.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) is toegevoegd aan de ingebouwde coderings definities. Zie voor meer informatie [code ring met inhoud](content-aware-encoding.md). 

## <a name="march-2019"></a>Maart 2019

Dynamische verpakking ondersteunt nu Dolby Atmos. Zie [Audio codecs die worden ondersteund door dynamische pakketten](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)voor meer informatie.

U kunt nu een lijst met Asset-of account filters opgeven die van toepassing zijn op uw streaming-Locator. Zie [filters koppelen aan streaming-Locator](filters-concept.md#associating-filters-with-streaming-locator)voor meer informatie.

## <a name="february-2019"></a>Februari 2019

Media Services V3 wordt nu ondersteund in azure National Clouds. Niet alle functies zijn nog in alle Clouds beschikbaar. Zie [Clouds en regio's waarin Azure Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie.

De gebeurtenis [micro soft. media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) is toegevoegd aan de Azure Event grid-schema's voor Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-en MPI-bestanden 

Bij het coderen met Media Encoder Standard voor het maken van MP4-bestand (en), wordt er een nieuw. mpi-bestand gegenereerd en toegevoegd aan de uitvoer Asset. Dit MPI-bestand is bedoeld om de prestaties te verbeteren voor [dynamische pakket](dynamic-packaging-overview.md) -en streaming-scenario's.

U moet het MPI-bestand niet wijzigen of verwijderen of afhankelijk van de aanwezigheid (of niet) van een dergelijk bestand afnemen in uw service.

## <a name="december-2018"></a>December 2018

Updates van de GA-versie van de V3 API zijn onder andere:
       
* De **PresentationTimeRange** -eigenschappen zijn niet meer vereist voor **Asset filters** en **account filters**. 
* De query opties $top en $skip voor **taken** en **trans formaties** zijn verwijderd en $OrderBy is toegevoegd. Als onderdeel van het toevoegen van de nieuwe ordenings functionaliteit werd ontdekt dat de opties voor $top en $skip per ongeluk werden weer gegeven, zelfs als ze niet zijn geïmplementeerd.
* De Enumeration-uitbreid baarheid is opnieuw ingeschakeld. Deze functie is ingeschakeld in de Preview-versies van de SDK en is per ongeluk uitgeschakeld in de GA-versie.
* Er zijn twee vooraf gedefinieerde stroomsgewijze beleids regels gewijzigd. **SecureStreaming** is nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** is nu **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

De CLI 2,0-module is nu beschikbaar voor [Azure Media Services v3 ga](/cli/azure/ams) – v-2.0.50.

### <a name="new-commands"></a>Nieuwe opdrachten

- [AZ AMS account](/cli/azure/ams/account)
- [AZ AMS account-filter](/cli/azure/ams/account-filter)
- [AZ AMS Asset](/cli/azure/ams/asset)
- [AZ AMS Asset-filter](/cli/azure/ams/asset-filter)
- [AZ AMS content-Key-policy](/cli/azure/ams/content-key-policy)
- [AZ AMS job](/cli/azure/ams/job)
- [AZ AMS Live-Event](/cli/azure/ams/live-event)
- [AZ AMS live-output](/cli/azure/ams/live-output)
- [AZ AMS streaming-Endpoint](/cli/azure/ams/streaming-endpoint)
- [AZ AMS streaming-Locator](/cli/azure/ams/streaming-locator)
- [AZ AMS account MRU](/cli/azure/ams/account/mru) -Hiermee kunt u gereserveerde media-eenheden beheren. Zie [gereserveerde media-eenheden schalen](media-reserved-units-cli-how-to.md)voor meer informatie.

### <a name="new-features-and-breaking-changes"></a>Nieuwe functies en belang rijke wijzigingen

#### <a name="asset-commands"></a>Asset-opdrachten

- ```--storage-account``` en ```--container``` argumenten zijn toegevoegd.
- Standaard waarden voor verloop tijd (nu + 23h) en machtigingen (lezen) in de ```az ams asset get-sas-url``` opdracht toegevoegd.

#### <a name="job-commands"></a>Opdracht opdrachten

- ```--correlation-data``` en ```--label``` argumenten toegevoegd
- ```--output-asset-names``` de naam is gewijzigd in ```--output-assets``` . Nu accepteert het een lijst met door spaties gescheiden activa in de indeling ' assets = label '. Activa zonder label kunnen als volgt worden verzonden: ' assets = '.

#### <a name="streaming-locator-commands"></a>Opdrachten voor streaming-Locator

- ```az ams streaming locator``` de basis opdracht is vervangen door ```az ams streaming-locator``` .
- ```--streaming-locator-id``` en ```--alternative-media-id support``` argumenten zijn toegevoegd.
- ```--content-keys argument``` het argument is bijgewerkt.
- ```--content-policy-name``` de naam is gewijzigd in ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Streaming-beleids opdrachten

- ```az ams streaming policy``` de basis opdracht is vervangen door ```az ams streaming-policy``` .
- De ondersteuning voor versleutelings parameters is ```az ams streaming-policy create``` toegevoegd.

#### <a name="transform-commands"></a>Opdrachten transformeren

- ```--preset-names``` het argument is vervangen door ```--preset``` . Nu kunt u slechts één uitvoer/voor instelling per keer instellen (om meer informatie toe te voegen om uit te voeren ```az ams transform output add``` ). U kunt ook aangepaste StandardEncoderPreset instellen door het pad naar uw aangepaste JSON door te geven.
- ```az ams transform output remove``` kan worden uitgevoerd door de uitvoer index door te geven die moet worden verwijderd.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenten die zijn toegevoegd in ```az ams transform create``` en ```az ams transform output add``` opdrachten.

## <a name="october-2018---ga"></a>Oktober 2018-GA

In deze sectie worden Azure Media Services updates (AMS) van oktober beschreven.

### <a name="rest-v3-ga-release"></a>REST v3 GA release

De [rest v3 ga-release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) bevat meer Api's voor Live, account/Asset level-manifest filters en DRM-ondersteuning.

#### <a name="azure-resource-management"></a>Azure-resource beheer 

Ondersteuning voor Azure Resource Management maakt Unified management en Operations API mogelijk (nu alles op één plek).

Vanaf deze release kunt u Resource Manager-sjablonen gebruiken om live-gebeurtenissen te maken.

#### <a name="improvement-of-asset-operations"></a>Verbetering van activa bewerkingen 

De volgende verbeteringen zijn geïntroduceerd:

- Opname van HTTP (s) Url's of Azure Blob Storage SAS-Url's.
- Geef de naam van de container voor assets op. 
- Eenvoudigere uitvoer ondersteuning voor het maken van aangepaste werk stromen met Azure Functions.

#### <a name="new-transform-object"></a>Nieuw Transform-object

Het nieuwe object **transform** vereenvoudigt het coderings model. Met het nieuwe object kunt u eenvoudig sjabloon en voor instellingen voor het maken en delen van coderings bronnen beheren. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory-verificatie en Azure RBAC

Azure AD-verificatie en Azure op rollen gebaseerd toegangs beheer (Azure RBAC) bieden veilige trans formaties, LiveEvents, beleids regels voor inhouds sleutels of activa op rol of gebruikers in azure AD.

#### <a name="client-sdks"></a>Client-SDK 's  

Talen die worden ondersteund in Media Services V3: .NET core, Java, Node.js, Ruby, type script, python en go.

#### <a name="live-encoding-updates"></a>Live encoding-updates

De volgende Live encoding-updates worden geïntroduceerd:

- Nieuwe modus voor de lage latentie voor Live (10 seconden end-to-end).
- Verbeterde RTMP-ondersteuning (verbeterde stabiliteit en meer ondersteuning voor broncode encoders).
- RTMP beveiligde opname.

    Wanneer u een live gebeurtenis maakt, krijgt u nu vier opname-Url's. De 4 opname-Url's zijn bijna identiek, hebben hetzelfde streaming token (AppId), alleen het poort nummer onderdeel is anders. Twee van de Url's zijn primaire en back-ups voor RTMP. 
- ondersteuning voor trans codering met 24 uur. 
- Verbeterde ondersteuning voor AD-Signa lering in RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Verbeterde ondersteuning voor Event Grid

U kunt de volgende Event Grid verbeteringen voor de ondersteuning bekijken:

- Azure Event Grid integratie voor eenvoudiger ontwikkeling met Logic Apps en Azure Functions. 
- Abonneer u op gebeurtenissen op code ring, Live kanalen en meer.

### <a name="cmaf-support"></a>CMAF-ondersteuning

Ondersteuning voor CMAF en ' cbcs-versleuteling voor Apple HLS (iOS 11 +) en MPEG-DASH-spelers die CMAF ondersteunen.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA release is in augustus aangekondigd. Zie [Wat is video indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json)voor nieuwe informatie over momenteel ondersteunde functies. 

### <a name="plans-for-changes"></a>Plannen voor wijzigingen

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
De Azure CLI 2,0-module die bewerkingen bevat voor alle functies (waaronder Live, beleids regels voor inhouds sleutels, accounts/Asset-filters, streaming-beleid) is binnenkort beschikbaar. 

### <a name="known-issues"></a>Bekende problemen

De volgende problemen zijn alleen van invloed op klanten die de preview-API voor activa of AccountFilters hebben gebruikt.

Als u activa of account filters hebt gemaakt tussen 09/28 en 10/12 met Media Services v3 CLI of Api's, moet u alle asset-en AccountFilters verwijderen en opnieuw maken vanwege een versie conflict. 

## <a name="may-2018---preview"></a>Mei 2018-preview

### <a name="net-sdk"></a>.NET SDK

De volgende functies zijn aanwezig in de .NET SDK:

* **Trans formaties** en **taken** voor het coderen of analyseren van media-inhoud. Zie voor voor beelden [stroom bestanden](stream-files-tutorial-with-api.md) en [analyseren](analyze-videos-tutorial-with-api.md).
* **Streaming-Locators** voor het publiceren en streamen van inhoud op apparaten van eind gebruikers
* **Stroomsgewijze beleids regels** en **beleids regels voor inhouds sleutels** voor het configureren van key delivery and Content Protection (DRM) bij het leveren van inhoud.
* **Live Events** en **Live outputs** voor het configureren van de opname en het archiveren van inhoud voor live streamen.
* **Activa** voor het opslaan en publiceren van media-inhoud in azure Storage. 
* **Streaming-eind punten** voor het configureren en schalen van dynamische pakketten, versleuteling en streaming voor zowel live als on-demand media-inhoud.

### <a name="known-issues"></a>Bekende problemen

* Bij het verzenden van een taak kunt u opgeven dat u uw bron video wilt opnemen met behulp van HTTPS-Url's, SAS-Url's of paden naar bestanden die zich in Azure Blob Storage bevinden. Media Services V3 biedt momenteel geen ondersteuning voor gesegmenteerde overdrachts codering via HTTPS-Url's.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="see-also"></a>Zie ook

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](migrate-v-2-v-3-migration-introduction.md).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](media-services-overview.md)
- [Release opmerkingen bij Media Services v2](../previous/media-services-release-notes.md)
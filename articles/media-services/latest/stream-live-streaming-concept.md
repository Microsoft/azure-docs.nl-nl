---
title: Overzicht van live streamen
description: Dit artikel geeft een overzicht van live streamen met behulp van Azure Media Services v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: f480f7821ea3443c76c551f45ac74d136f417060
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277297"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Live streamen met Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u live gebeurtenissen aan uw klanten leveren via de Azure-Cloud. Als u uw Live-gebeurtenissen wilt streamen met Media Services, hebt u het volgende nodig:  

- Een camera die wordt gebruikt om de live-gebeurtenis vast te leggen.<br/>Bekijk voor het instellen van ideeën de [eenvoudige en draag bare video versnellings configuratie voor Video's]( https://link.medium.com/KNTtiN6IeT).

    Als u geen toegang hebt tot een camera, kunnen hulpprogram ma's zoals [Wirecast](https://www.telestream.net/wirecast/overview.htm) worden gebruikt voor het genereren van een live-feed vanuit een video bestand.
- Een live video encoder dat signalen van een camera (of een ander apparaat, zoals een laptop) converteert naar een bijdrage toevoer die naar Media Services wordt verzonden. De bijdrage feed kan signalen bevatten die gerelateerd zijn aan advertenties, zoals SCTE-35 markeringen.<br/>Zie voor een lijst met de aanbevolen code ring voor live [streaming.](encode-recommended-on-premises-live-encoders.md) Bekijk ook deze blog: [productie van live streams met IB](https://link.medium.com/ttuwHpaJeT).
- Onderdelen in Media Services waarmee u de live-gebeurtenis kunt opnemen, bekijken, versleutelen, coderen en verzenden naar uw klanten, of naar een CDN voor verdere distributie.

Voor klanten die inhoud willen leveren aan grote Internet doelgroepen, raden we u aan CDN op het streaming- [eind punt](stream-streaming-endpoint-concept.md)in te scha kelen.

In dit artikel vindt u een overzicht en richt lijnen voor live streamen met Media Services en koppelingen naar andere relevante artikelen.
 
> [!NOTE]
> U kunt de [Azure Portal](https://portal.azure.com/) gebruiken om v3 [Live-gebeurtenissen](live-event-outputs-concept.md)te beheren, v3- [assets](assets-concept.md)weer te geven, informatie over het openen van api's op te halen. Gebruik voor alle andere beheer taken (bijvoorbeeld trans formaties en taken) de [rest API](/rest/api/media/), [cli](/cli/azure/ams)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging-and-delivery"></a>Dynamische verpakking en levering

Met Media Services kunt u gebruikmaken van [dynamische pakketten](encode-dynamic-packaging-concept.md), waarmee u uw live streams kunt bekijken en uitzenden in [MPEG Dash-, HLS-en Smooth streaming-indelingen](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) van de contributie feed die naar de service wordt verzonden. Uw viewers kunnen de Live Stream afspelen met een HLS, DASH of Smooth Streaming compatibele spelers. U kunt [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) in uw web-of mobiele toepassingen gebruiken om uw stream in een van deze protocollen te leveren.

## <a name="dynamic-encryption"></a>Dynamische versleuteling

Dynamische versleuteling biedt u de mogelijkheid om uw Live of on-demand inhoud dynamisch te versleutelen met AES-128 of een van de drie belangrijkste digital rights management (DRM)-systemen: micro soft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde klanten. Zie [dynamische versleuteling](drm-content-protection-concept.md) voor meer informatie.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="dynamic-filtering"></a>Dynamisch filteren

Dynamische filtering wordt gebruikt voor het beheren van het aantal sporen, indelingen, bitrates en presentatie tijd Vensters die worden verzonden naar de spelers. Zie [filters en dynamische manifesten](filters-dynamic-manifest-concept.md)voor meer informatie.

## <a name="live-event-types"></a>Live gebeurtenis typen

[Live-gebeurtenissen](/rest/api/media/liveevents) zijn verantwoordelijk voor het opnemen en verwerken van live video feeds. Een livegebeurtenis kan worden ingesteld op een *pass-through* (een on-premises live-encoder verzendt een stroom met meerdere bitsnelheden) of *live-codering* (een on-premises live-encoder verzendt een stream met één bitsnelheid). Zie [Live Events en live outputs](live-event-outputs-concept.md)voor meer informatie over live streamen in Media Services v3.

### <a name="pass-through"></a>Pass-through

![Diagram waarin wordt getoond hoe de video-en audio-feeds van een Pass-through live-gebeurtenis worden opgenomen en verwerkt.](./media/live-streaming/pass-through.svg)

Wanneer u de gebeurtenis Pass-Through **Live** gebruikt, vertrouwt u met uw on-premises Live coderings programma om een video stroom met meerdere bitrates te genereren en deze te verzenden als de bijdrage aan de live gebeurtenis (met RTMP of gefragmenteerd-MP4 invoer Protocol). De live-gebeurtenis voert vervolgens via de binnenkomende video stromen naar het dynamische pakket (streaming-eind punt) zonder verdere trans codering. Een dergelijke door gang werkende gebeurtenis is geoptimaliseerd voor langlopende Live-gebeurtenissen of 24x365e lineaire live-streaming. 

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Wanneer u Cloud codering met Media Services gebruikt, configureert u uw on-premises Live coderings programma voor het verzenden van een video met één bitsnelheid als de contributie invoer (Maxi maal 32Mbps aggregatie) naar de live gebeurtenis (met RTMP of gefragmenteerd-MP4 invoer Protocol). De live-gebeurtenis transcodeert de binnenkomende single-bitrate stream in [meerdere bitrate video streams](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) met verschillende resoluties om de levering te verbeteren en maakt deze beschikbaar voor levering aan het afspelen van apparaten via industrie standaard protocollen zoals MPEG-Dash, Apple http live streaming (HLS) en micro soft Smooth streaming. 

### <a name="live-transcription-preview"></a>Live transcriptie (preview-versie)

Live transcriptie is een functie die u kunt gebruiken met Live-gebeurtenissen die door Pass-Through of Live-code ring zijn. Zie [Live transcriptie](live-event-live-transcription-how-to.md)voor meer informatie. Als deze functie is ingeschakeld, gebruikt de service de functie voor [spraak-naar-tekst](../../cognitive-services/speech-service/speech-to-text.md) van Cognitive Services om de gesp roken woorden in de binnenkomende audio naar tekst te transcriberen. Deze tekst wordt vervolgens beschikbaar gesteld voor levering en video en audio in MPEG-DASH-en HLS-protocollen.

> [!NOTE]
> Live transcriptie is momenteel beschikbaar als preview-functie in VS-West 2.

## <a name="live-streaming-workflow"></a>Werkstroom voor live streamen

Als u inzicht wilt krijgen in de werk stroom voor live streamen in Media Services v3, moet u eerst de volgende concepten bekijken en begrijpen: 

- [Streaming-eindpunten](stream-streaming-endpoint-concept.md)
- [Livegebeurtenissen en live-uitvoer](live-event-outputs-concept.md)
- [Streaming-locators](stream-streaming-locators-concept.md)

### <a name="general-steps"></a>Algemene stappen

1. Controleer in uw Media Services-account of het **streaming-eind punt** (origin) wordt uitgevoerd. 
2. Maak een [livegebeurtenis](live-event-outputs-concept.md). <br/>Bij het maken van de gebeurtenis kunt u opgeven dat deze automatisch wordt gestart. U kunt ook de gebeurtenis starten wanneer u klaar bent om te streamen.<br/> Wanneer auto start is ingesteld op True, wordt de live-gebeurtenis direct na het maken gestart. De facturering begint zodra de live-gebeurtenis wordt gestart. U moet stoppen op de resource van de live-gebeurtenis expliciet aanroepen om verdere facturering te stoppen. Zie [Live Event States and billing](live-event-states-billing-concept.md)(Engelstalig) voor meer informatie.
3. Haal de opname-URL ('s) op en configureer uw on-premises Encoder voor het gebruik van de URL om de contributie feed te verzenden.<br/>Bekijk [Aanbevolen Live coderings](encode-recommended-on-premises-live-encoders.md)Programma's.
4. Haal de Preview-URL op en gebruik deze om te controleren of de invoer van het coderings programma daad werkelijk wordt ontvangen.
5. Maak een nieuw **Asset** -object. 

    Elke live uitvoer is gekoppeld aan een Asset, die wordt gebruikt om de video op te nemen in de gekoppelde Azure Blob Storage-container. 
6. Een **Live uitvoer** maken en de Asset-naam gebruiken die u hebt gemaakt, zodat de stroom kan worden gearchiveerd in de Asset.

    Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Wanneer u de live uitvoer verwijdert, worden de onderliggende activa en inhoud in de Asset niet verwijderd.
7. Maak een **streaming-Locator** met de [ingebouwde streaming-beleids typen](stream-streaming-policy-concept.md).

    Als u de live-uitvoer wilt publiceren, moet u een streaming-Locator voor de gekoppelde Asset maken. 
8. Geef een lijst van de paden in de **streaming-Locator** op om terug te gaan naar de url's die moeten worden gebruikt (dit zijn deterministisch).
9. Haal de hostnaam op voor het **streaming-eind punt** (oorsprong) waaruit u gegevens wilt streamen.
10. Combi neer de URL uit stap 8 met de hostnaam in stap 9 om de volledige URL op te halen.
11. Als u uw **live-gebeurtenis** niet meer zichtbaar wilt maken, moet u het streamen van de gebeurtenis stoppen en de **streaming-Locator** verwijderen.
12. Als u klaar bent met het streamen van gebeurtenissen en de resources wilt opruimen die eerder zijn ingericht, volgt u de volgende procedure.

    * Stop het pushen van de stream vanuit het coderingsprogramma.
    * Stop de live-gebeurtenis. Zodra de live-gebeurtenis is gestopt, worden er geen kosten in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
    * U kunt het streaming-eind punt stoppen, tenzij u wilt door gaan met het archiveren van uw live-gebeurtenis als een stroom op aanvraag. Als de live-gebeurtenis is gestopt, worden er geen kosten in rekening gebracht.

De Asset waarnaar de live uitvoer wordt gearchiveerd, wordt automatisch een Asset op aanvraag wanneer de live uitvoer wordt verwijderd. U moet alle Live outputs verwijderen voordat een live gebeurtenis kan worden gestopt. U kunt een optionele vlag [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) gebruiken om live-uitvoer automatisch te verwijderen bij het stoppen. 

> [!TIP]
> Zie [zelf studie over live streamen](stream-live-tutorial-with-api.md): in dit artikel wordt de code onderzocht waarmee de hierboven beschreven stappen worden geïmplementeerd.

## <a name="other-important-articles"></a>Andere belang rijke artikelen

- [Aanbevolen live-encoders](encode-recommended-on-premises-live-encoders.md)
- [Een cloud-DVR gebruiken](live-event-cloud-dvr-time-how-to.md)
- [Functie vergelijking van live gebeurtenis typen](live-event-types-comparison-reference.md)
- [Statussen en facturering](live-event-states-billing-concept.md)
- [Latentie](live-event-latency-reference.md)

## <a name="live-streaming-questions"></a>Vragen over live streamen

Zie het artikel [over vragen over live streamen](questions-collection.md#live-streaming) .

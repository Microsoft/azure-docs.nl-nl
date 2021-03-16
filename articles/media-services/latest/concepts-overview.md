---
title: Terminologie en concepten Media Services
description: Meer informatie over terminologie en concepten voor Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 2d388cf6c776e6d30a487ce9e14ba1601dce1d23
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491602"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologie en concepten Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit onderwerp vindt u een kort overzicht van Azure Media Services terminologie en concepten. Het artikel bevat ook koppelingen naar artikelen met uitgebreide uitleg over Media Services v3-concepten en-functionaliteit.

De basis concepten die in deze onderwerpen worden beschreven, moeten worden gecontroleerd voordat de ontwikkeling wordt gestart.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3-terminologie

|Termijn|Beschrijving|
|---|---|
|Live-gebeurtenis|Een **live-gebeurtenis** vertegenwoordigt een pijp lijn voor opname, code ring (optioneel) en het verpakken van live streams van video, audio en realtime meta gegevens.<br/><br/>Voor klanten die migreren van Media Services v2 Api's, vervangt de **live-gebeurtenis** de **kanaal** entiteit in v2. Zie voor meer informatie [migreren van v2 naar v3](migrate-v-2-v-3-migration-introduction.md).|
|Streaming-eind punt/verpakking/oorsprong|Een **streaming-eind punt** vertegenwoordigt een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren. Er wordt een van de algemene protocollen voor streaming media (HLS of DASH) gebruikt. Daarnaast biedt het **streaming-eind punt** dynamische code ring (just-in-time) voor toonaangevende Digital Rights Management systemen (DRMs).<br/><br/>In de media streaming-industrie wordt deze service meestal een **packager** of **Origin** genoemd.  Andere veelvoorkomende termen in de branche voor deze mogelijkheid zijn JITP (just-in-time-packager) of JITE (just-in-time-Encryption).

## <a name="media-services-v3-concepts"></a>Media Services v3-concepten

|Concepten|Beschrijving|Koppelingen|
|---|---|---|
|Activa en uploaden van inhoud|Als u media-inhoud in azure wilt beheren, versleutelen, coderen, analyseren en streamen, moet u een Media Services account maken en uw digitale bestanden uploaden naar **assets**.|[Uploaden naar en opslaan in de cloud](storage-account-concept.md)<br/><br/>[Het concept van assets](assets-concept.md)|
|Inhoud versleutelen|Wanneer u uw digitale media bestanden van hoge kwaliteit uploadt naar assets, kunt u ze coderen in indelingen die kunnen worden afgespeeld op een groot aantal verschillende browsers en apparaten. <br/><br/>Als u wilt coderen met Media Services v3, moet u **trans formaties** en **taken** maken.|[Trans formaties en taken](transforms-jobs-concept.md)<br/><br/>[Code ring met Media Services](encoding-concept.md)|
|Inhoud wordt geanalyseerd (Video Indexer)|Met Media Services v3 kunt u inzichten uit uw video-en audio bestanden ophalen met behulp van Media Services v3-voor waarden. Als u uw inhoud wilt analyseren met Media Services v3-voor instellingen, moet u **trans formaties** en **taken** maken.<br/><br/>Als u gedetailleerdere inzichten wilt, gebruikt u [Video Indexer](../video-indexer/index.yml) rechtstreeks.|[Video-en audio bestanden analyseren](analyzing-video-audio-files-concept.md)|
|Verpakking en levering|Als uw inhoud eenmaal is gecodeerd, kunt u gebruikmaken van **dynamische pakketten**. In Media Services is een **streaming-eind punt** de dynamische verpakkings service die wordt gebruikt voor het leveren van media-inhoud aan client spelers. Als u Video's in het uitvoer bare activum beschikbaar wilt maken voor het afspelen van clients, moet u een **streaming-Locator** maken en vervolgens streaming-url's bouwen. <br/><br/>Bij het maken van de **streaming-Locator**, naast de naam van het activum, moet u het **streaming-beleid** opgeven. Met **stroomsgewijze beleids regels** kunt u streaming-protocollen en versleutelings opties definiëren (indien van toepassing) voor uw **streaming-Locators**. Dynamische pakketten worden gebruikt, ongeacht of u uw inhoud live of on-demand streamt. <br/><br/>U kunt Media Services **dynamische manifesten** gebruiken om alleen een specifieke vertoning of subfragmenten van uw video te streamen.|[Dynamische verpakking](dynamic-packaging-overview.md)<br/><br/>[Streaming-eind punten](streaming-endpoint-concept.md)<br/><br/>[Streaming-Locators](streaming-locators-concept.md)<br/><br/>[Streaming-beleid](streaming-policy-concept.md)<br/><br/>[Dynamische manifesten](filters-dynamic-manifest-overview.md)<br/><br/>[Filters](filters-concept.md)|
|Inhoudsbeveiliging|Met Media Services kunt u uw Live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of/en een van de drie belangrijkste DRM-systemen: micro soft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde klanten. <br/><br/>Als u versleutelings opties voor uw stroom opgeeft, maakt u het **beleid voor inhouds sleutels** en koppelt u dit aan uw **streaming-Locator**. Met het **beleid voor inhouds sleutels** kunt u configureren hoe de inhouds sleutel wordt bezorgd bij de eind clients.<br/><br/> Probeer het beleid opnieuw te gebruiken wanneer dezelfde opties nodig zijn.| [Beleid voor inhouds sleutels](content-key-policy-concept.md)<br/><br/>[Inhouds beveiliging](content-protection-overview.md)|
|Live streamen|Met Media Services kunt u live gebeurtenissen aan uw klanten leveren via de Azure-Cloud. **Livegebeurtenissen** zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een **live gebeurtenis** maakt, wordt er een invoer eindpunt gemaakt dat u kunt gebruiken om een live signaal van een extern coderings programma te verzenden. Zodra u de stroom naar de **live gebeurtenis** hebt gestroomd, kunt u de streaming-gebeurtenis starten door een **Asset**, **Live output** en **streaming-Locator** te maken. **Live output** archiveert de stroom naar de **Asset** en maakt deze beschikbaar voor gebruikers via het **streaming-eind punt**. Een livegebeurtenis kan worden ingesteld op een *pass-through* (een on-premises live-encoder verzendt een stroom met meerdere bitsnelheden) of *live-codering* (een on-premises live-encoder verzendt een stream met één bitsnelheid). |[Overzicht van live streamen](live-streaming-overview.md)<br/><br/>[Livegebeurtenissen en live-uitvoer](live-events-outputs-concept.md)|
|Controleren met Event Grid|Als u de voortgang van de taak wilt zien, gebruikt u **Event grid**. Media Services worden ook de live-gebeurtenis typen meeverzonden. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. |[Event Grid-gebeurtenissen verwerken](reacting-to-media-services-events.md)<br/><br/>[Schema's](media-services-event-schemas.md)|
|Controleren met Azure Monitor|Bewaak metrische gegevens en Diagnostische logboeken die u helpen inzicht te krijgen in de prestaties van uw apps met Azure Monitor.|[Metrische gegevens en diagnostische logboeken](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Schema's van diagnostische logboeken](monitoring/monitor-media-services-data-reference.md)|
|Afspeel-clients|U kunt Azure Media Player gebruiken om media-inhoud af te spelen die door Media Services wordt gestreamd op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden zoals HTML5, MSE (Media Source Extensions) en EME (Encrypted Media Extensions) om een geavanceerde adaptieve streamingervaring te bieden. |[Overzicht van Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Externe bestanden coderen en video streamen - REST](stream-files-tutorial-with-rest.md)
* [Geüploade bestanden coderen en video streamen - .NET](stream-files-tutorial-with-api.md)
* [Live streamen - .NET](stream-live-tutorial-with-api.md)
* [Uw video analyseren - .NET](analyze-videos-tutorial-with-api.md)
* [Dynamische AES-128-versleuteling - .NET](protect-with-aes128.md)
* [Dynamisch versleutelen met multi-DRM - .NET](protect-with-drm.md)
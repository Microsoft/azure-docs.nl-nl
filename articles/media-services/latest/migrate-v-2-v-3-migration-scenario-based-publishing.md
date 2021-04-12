---
title: Richt lijnen voor de migratie van verpakking en levering
description: In dit artikel vindt u richt lijnen op basis van een scenario voor verpakking en levering die u helpt bij het migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279014"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van pakketten en levering

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u op scenario's gebaseerde richt lijnen voor verpakking en levering die u helpen bij het migreren van Azure Media Services v2 naar v3.

Belang rijke wijzigingen in de manier waarop inhoud wordt gepubliceerd in v3 API. Het nieuwe publicatie model is vereenvoudigd en maakt gebruik van minder entiteiten om een streaming-Locator te maken. De API werd gereduceerd tot slechts twee entiteiten versus de vier entiteiten die eerder zijn vereist. Beleids regels voor inhouds sleutels en streaming-Locators vervangen nu de nood zaak voor `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` , en `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Verpakking en levering in v3

1. [Beleids regels voor inhouds sleutels](drm-content-key-policy-concept.md)maken.
1. [Streaming-Locators](stream-streaming-locators-concept.md)maken.
1. De [streaming-paden](create-streaming-locator-build-url.md) ophalen 
    1. Configureer deze voor een [Dash](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) -of [HLS](encode-dynamic-packaging-concept.md#hls-protocol) -speler.

Raadpleeg publicatie concepten, zelf studies en de onderstaande hand leidingen voor specifieke stappen.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publicatie concepten, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Dynamische pakketten in Media Services v3](encode-dynamic-packaging-concept.md)
- [Filters](filters-concept.md)
- [Uw manifesten filteren met behulp van dynamische pakket](filters-dynamic-manifest-concept.md)
- [Streaming-eind punten (oorsprong) in Azure Media Services](stream-streaming-endpoint-concept.md)
- [Inhoud streamen met CDN-integratie](stream-scale-streaming-cdn-concept.md)
- [Streaming-Locators](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Handleidingen

- [Streaming-eind punten beheren met Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [CLI-voorbeeld: Een asset publiceren](cli-publish-asset.md)
- [Een streaming-locator maken en URL's bouwen](create-streaming-locator-build-url.md)
- [De resultaten van een taak downloaden](job-download-results-how-to.md)
- [Beschrijvende geluids sporen voor signalen](signal-descriptive-audio-howto.md)
- [Volledige installatie Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [De Video.js Player gebruiken met Azure Media Services](player-how-to-video-js-player.md)
- [De Shake-speler gebruiken met Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

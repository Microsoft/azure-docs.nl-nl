---
title: Algemene scenario's Microsoft Azure Media Services | Microsoft Docs
description: Dit artikel geeft een overzicht van Microsoft Azure Media Services scenario's.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: d195ad6715c47b9b4c14dc2e65ba1d07ebf79ce8
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696258"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Algemene scenario's Microsoft Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. Bekijk de nieuwste versie [Media Services v3](../latest/media-services-overview.md). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Met Microsoft Azure Media Services (AMS) kunt u veilig video- of audio-inhoud uploaden, opslaan, coderen en verpakken, zowel voor levering on-demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

In dit artikel vindt u algemene scenario's voor het leveren van uw inhoud live of on-demand.

## <a name="overview"></a>Overzicht

### <a name="prerequisites"></a>Vereisten

* Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com) voor meer informatie.
* Een Azure Media Services-account. Zie [Een account maken](media-services-portal-create-account.md) voor meer informatie.
* Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben.

    Wanneer uw AMS-account wordt gemaakt, wordt er een **standaard** streaming-eind punt toegevoegd aan uw account met de status **gestopt** . Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt de status **Wordt uitgevoerd** hebben.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Veelgebruikte objecten bij ontwikkelen op basis van het AMS OData-model

In de volgende afbeelding ziet u een aantal van de meest gebruikte objecten bij het ontwikkelen in het Media Services OData-model.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

[![Diagram met enkele van de meest gebruikte objecten bij het ontwikkelen op basis van het object gegevens model van Azure Media Services.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

U kunt [hier](https://media.windows.net/API/$metadata?api-version=2.15) het hele model bekijken.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Inhoud in de opslag beveiligen en niet-versleutelde streamingmedia leveren

![VoD-werkstroom](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Upload een mediabestand van hoge kwaliteit naar een asset.

    Het Toep assen van de optie voor opslag versleuteling op uw asset om uw inhoud te beschermen tijdens het uploaden en tijdens de opslag wordt aanbevolen.

1. Codeer de assets als een set Adaptive Bitrate MP4-bestanden.

    Het is raadzaam de optie voor opslag versleuteling toe te passen op de uitvoer Asset om uw inhoud op rest te beveiligen.

1. Configureer het beleid voor de levering van assets (gebruikt voor dynamische pakketten).

    Als de opslag van uw asset is versleuteld, **moet** u een beleid voor assetlevering configureren.
1. Publiceer de asset door een OnDemand-locator te maken.
1. Stream de gepubliceerde inhoud.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>De inhoud in de opslag beveiligen, dynamisch versleutelde streamingmedia leveren

![Beveiligen met PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Upload een mediabestand van hoge kwaliteit naar een asset. Pas de optie voor opslagversleuteling toe op de asset.
1. Codeer de assets als een set Adaptive Bitrate MP4-bestanden. Pas de optie voor opslagversleuteling toe op de uitvoerasset.
1. Maak een inhoudssleutel voor de versleuteling van de asset die tijdens het afspelen dynamisch moet worden versleuteld.
1. Configureer het autorisatiebeleid voor de inhoudssleutel.
1. Configureer het beleid voor de levering van assets (gebruikt door dynamische pakketten en dynamische versleuteling).
1. Publiceer de asset door een OnDemand-locator te maken.
1. Stream de gepubliceerde inhoud.

## <a name="deliver-progressive-download"></a>Een progressieve download leveren

1. Upload een mediabestand van hoge kwaliteit naar een asset.
1. Codeer het bestand naar één MP4-bestand.
1. Publiceer de asset door een OnDemand- of SAS-locator te maken. Als SAS-locator wordt gebruikt, wordt de inhoud gedownload vanaf Azure Blob Storage. U hoeft geen streaming-eind punten in de status gestart te hebben.
1. Download de inhoud op progressieve wijze.

## <a name="delivering-live-streaming-events"></a>Evenementen live streamen

1. Live-inhoud met verschillende protocollen voor live streamen opnemen (bijvoorbeeld RTMP of Smooth Streaming).
1. (Optioneel) Uw stream coderen in een stream met een adaptieve bitsnelheid.
1. Een voorbeeld van uw livestream bekijken.
1. Bezorgt de inhoud via:
    1. Algemene streaming-protocollen (bijvoorbeeld MPEG DASH, Smooth, HLS) rechtstreeks aan uw klanten,
    1. Naar een Content Delivery Network (CDN) voor verdere distributie, of
    1. Noteer de opgenomen inhoud en bewaar deze op een later tijdstip (video-on-demand).

Als u live streamt, kunt u één van de volgende routes kiezen:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Werken met kanalen die een multi-bitrate livestream van on-premises encoders ontvangen (pass-through)

Het volgende diagram toont de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij de **passthrough**-werkstroom.

![Diagram met de belangrijkste onderdelen van het A M S-platform dat is betrokken bij de "Pass-Through"-werk stroom.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Zie [Werken met kanalen die een multi-bitrate livestream van on-premises coderingsprogramma’s ontvangen](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie.

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services

In het volgende diagram ziet u de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij een live streaming-werk stroom waarbij een kanaal is ingeschakeld voor Live encoding met Media Services.

![Live-werkstroom](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

## <a name="consuming-content"></a>Inhoud gebruiken

Azure Media Services biedt de hulpprogramma's die u nodig hebt om geavanceerde, dynamische clientspelertoepassingen te maken voor de meeste platforms, waaronder: iOS-apparaten, Android-apparaten, Windows, Windows Phone, Xbox en settopboxen.

## <a name="enabling-azure-cdn"></a>Azure CDN inschakelen

Media Services ondersteunt de integratie met Azure CDN. Zie [Streaming-eindpunten in een Media Services-account beheren](media-services-portal-manage-streaming-endpoints.md) voor meer informatie over het inschakelen van Azure CDN.

## <a name="scaling-a-media-services-account"></a>Een Media Services-account schalen

AMS-klanten kunnen streaming-eindpunten, de mediaverwerking en de opslag in hun AMS-accounts schalen.

* Media Services-klanten kunnen een **Standard**-streaming-eindpunt of een Premium-**streaming**-eindpunt kiezen. **Standard**-streaming-eindpunten zijn geschikt voor de meeste streaming-workloads. Deze hebben dezelfde kenmerken als **Premium**-streaming-eindpunten en de uitgaande bandbreedte wordt hiermee automatisch geschaald.

    **Premium**-streaming-eindpunten zijn geschikt voor geavanceerde workloads omdat er gebruik wordt gemaakt van toegewezen, schaalbare bandbreedtecapaciteit. Klanten met een **Premium**-streaming-eindpunt krijgen standaard één streaming-eenheid (SU). Het streaming-eindpunt kan worden geschaald door SU's toe te voegen. Elke SU biedt extra bandbreedtecapaciteit voor de toepassing. Zie het onderwerp [Streaming-eindpunten schalen](media-services-portal-scale-streaming-endpoints.md) voor meer informatie over het schalen van **Premium**-streaming-eindpunten.

* Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**).

    Naast het opgeven van het type gereserveerde eenheid kunt u opgeven om uw account in te richten met **gereserveerde eenheden** (RUs). Op basis van het aantal ingerichte RU's wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account.

    > [!NOTE]
    > RU's werken voor het parallel verwerken van alle media, waaronder indexeringstaken via Azure Media Indexer. Indexeringstaken worden echter niet sneller verwerkt met snellere gereserveerde eenheden, terwijl dit bij coderen wel het geval is.

    Zie [Media verwerking schalen](media-services-portal-scale-media-processing.md)voor meer informatie.

* U kunt uw Media Services-account schalen door opslagaccounts toe te voegen. Elk opslagaccount is beperkt tot 500 TB. Zie [Opslagaccounts beheren](./media-services-managing-multiple-storage-accounts.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Migreren naar Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
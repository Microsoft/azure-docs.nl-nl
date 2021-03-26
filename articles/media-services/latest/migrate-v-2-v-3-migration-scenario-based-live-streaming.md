---
title: Media Services voor migratie van live streaming
description: In dit artikel vindt u informatie over het scenario op basis van live streamen, die u de minimale migratie van Azure Media Services versie v2 naar v3 helpt.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 322a599d5ad02ddcf19c61261d968d098b6c9871
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563123"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van live streaming

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

De Azure Portal ondersteunt nu het instellen en beheren van Live-gebeurtenissen.  U wordt geadviseerd om het uit te proberen tijdens het testen van de migratie van v2 naar v3.

## <a name="test-the-v3-live-event-workflow"></a>De V3 Live Event werk stroom testen

> [!NOTE]
> Kanalen en Program Ma's die zijn gemaakt met v2 (die zijn toegewezen aan Live-gebeurtenissen en live-uitvoer in v3), worden beheerd met de V3 API. De richt lijnen zijn om te scha kelen naar v3 Live-gebeurtenissen en live-uitvoer op een geschikt tijdstip wanneer u uw bestaande v2-kanaal kunt stoppen. Er is momenteel geen ondersteuning voor het naadloos migreren van een voortdurend uitgevoerde 24x7 live-kanaal naar de nieuwe v3 Live-gebeurtenissen. De downtime van onderhoud moet dus worden gecoördineerd op het beste gemak voor uw publiek en bedrijf.

Test de nieuwe manier om live-gebeurtenissen te leveren met Media Services voordat u uw inhoud verplaatst van v2 naar v3. Hier vindt u de V3-functies waarmee u kunt samen werken met de migratie.

- Maak een nieuwe v3 [live-gebeurtenis](live-events-outputs-concept.md#live-events) voor code ring. U kunt [1080p-en 720p-coderings instellingen](live-event-types-comparison.md#system-presets)inschakelen.
- De entiteit [Live uitvoer](live-events-outputs-concept.md#live-outputs) gebruiken in plaats van Program ma's
- [Streaming-Locators](streaming-locators-concept.md)maken.
- Denk na over uw behoefte aan [HLS-en dash](dynamic-packaging-overview.md) -live-streaming.
- Als u snel aan de slag wilt met Live Events, bekijkt u de nieuwe functies van de [modus stand-by](live-events-outputs-concept.md#standby-mode) .
- Als u uw live-evenement wilt transcriberen terwijl dit gebeurt, kunt u de nieuwe functie van [Live transcriptie](live-transcription.md) verkennen.
- Maak zakelijke (24x7x365 Live-gebeurtenissen in v3 als u een langere streaming-duur nodig hebt.
- Gebruik [Event grid](monitoring/monitor-events-portal-how-to.md) om uw Live-gebeurtenissen te bewaken.

Zie concepten van Live Events, zelf studies en de onderstaande hand leidingen voor specifieke stappen.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Concepten van Live Events, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Live streamen met Azure Media Services v3](live-streaming-overview.md)
- [Livegebeurtenissen en live-uitvoer in Media Services](live-events-outputs-concept.md)
- [Geverifieerde on-premises live streaming encoders](recommended-on-premises-live-encoders.md)
- [Gebruik tijd verschuivingen en live uitvoer om video weergave op aanvraag te maken](live-event-cloud-dvr.md)
- [Live-transcriptie (preview-versie)](live-transcription.md)
- [Vergelijking van live gebeurtenis typen](live-event-types-comparison.md)
- [Live gebeurtenis statussen en facturering](live-event-states-billing.md)
- [Instellingen voor lage latentie van Live Event](live-event-latency.md)
- [Fout codes voor Live-gebeurtenissen Media Services](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Zelf studies en Quick starts

- [Zelfstudie: Live streamen met Media Services](stream-live-tutorial-with-api.md)
- [Een Azure Media Services-livestream maken met OBS](live-events-obs-quickstart.md)
- [Quickstart: Inhoud uploaden, coderen en streamen met de portal](manage-assets-quickstart.md)
- [Quick Start: een Azure Media Services live-stream maken met Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

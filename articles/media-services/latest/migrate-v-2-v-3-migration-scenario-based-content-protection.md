---
title: Richt lijnen voor migratie op basis van inhouds beveiliging | Microsoft Docs
description: In dit artikel vindt u richt lijnen voor het beveiligen van inhouds beveiliging, die u helpen min te migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a546120a93f311be29083d5f23d4716316bf64f4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690353"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van inhouds beveiligings scenario's

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u richt lijnen voor het beveiligen van inhouds beveiliging, die u helpen min te migreren van Azure Media Services v2 naar v3.

## <a name="protect-content-in-v3-api"></a>Inhoud in v3 API beveiligen

Gebruik de ondersteuning voor functies met [meerdere sleutels](design-multi-drm-system-with-access-control.md) in de nieuwe v3 API.

Zie concepten voor inhouds beveiliging, zelf studies en hand leidingen hieronder voor specifieke stappen.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepten voor inhouds beveiliging, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Uw inhoud beveiligen met Media Services dynamische versleuteling](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)
- [Media Services V3 met PlayReady-licentie sjabloon](playready-license-template-overview.md)
- [Overzicht van Media Services V3 met Widevine-licentie sjabloon](widevine-license-template-overview.md)
- [Vereisten voor en configuratie van Apple FairPlay-licenties](fairplay-license-overview.md)
- [Streaming-beleid](streaming-policy-concept.md)
- [Beleid voor inhouds sleutels](content-key-policy-concept.md)

### <a name="tutorials"></a>Zelfstudies

[Quickstart: De portal gebruiken om inhoud te versleutelen](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Handleidingen

- [Een ondertekeningssleutel ophalen uit het bestaand beleid](get-content-key-policy-dotnet-howto.md)
- [Offline FairPlay streaming voor iOS met Media Services v3](offline-fairplay-for-ios.md)
- [Offline Widevine streaming voor Android met Media Services v3](offline-widevine-for-android.md)
- [Offline PlayReady streaming voor Windows 10 met Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
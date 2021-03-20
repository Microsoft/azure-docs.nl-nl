---
title: Migreren van Media Services v2 naar v3-Inleiding
description: Dit artikel is een inleiding op de migratie van Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953288"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migreren van Media Services v2 naar v3-Inleiding

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

De Media Services-migratie handleiding helpt u bij het migreren van Media Services v2 Api's naar v3-Api's op basis van een migratie die gebruikmaakt van de nieuwe functies en functies die nu beschikbaar zijn. U moet uw beste arrest gebruiken en bepalen wat het beste past bij uw scenario.

## <a name="how-to-use-this-guide"></a>Het gebruik van deze handleiding

### <a name="navigating"></a>Navigeren

In de hand leiding wordt de volgende afbeelding weer gegeven.

![migratie stappen](./media/migration-guide/steps.svg)<br/>

De stap die u aanmeldt, wordt aangeduid met een kleur wijziging in het nummer van de stap, bijvoorbeeld:

![migratie stappen 2](./media/migration-guide/steps-2.svg)<br/>

Aan het einde van elke pagina ziet u koppelingen naar de rest van de migratie documenten die u onder de **volgende stappen** kop kunt lezen.

### <a name="guidance"></a>Hulp

De richt lijnen die hier worden beschreven, zijn *Algemeen*. Het bevat inhoud om uw bewustzijn te verbeteren van wat nu beschikbaar is in v3 en wat is gewijzigd in de Media Services werk stromen.

Raadpleeg voor meer gedetailleerde richt lijnen, zoals scherm afbeeldingen en conceptuele afbeeldingen, koppelingen naar concepten, zelf studies, Quick starts, voor beelden en API-verwijzingen in elk scenario op basis van scenario's. We hebben ook voor beelden weer gegeven die u helpen de v2 API te vergelijken met de V3 API.

## <a name="migration-guidance-overview"></a>Overzicht van migratie richtlijnen

Er zijn vier algemene stappen die moeten worden gevolgd tijdens de migratie:

## <a name="step-1-benefits"></a>Stap 1 voor delen

<hr color="#5ea0ef" size="10">

Meer [informatie over de voor delen](migrate-v-2-v-3-migration-benefits.md) van migreren naar Media Services API v3.

## <a name="step-2-differences"></a>Stap 2 verschillen

<hr color="#5ea0ef" size="10">

Inzicht in de verschillen tussen de Media Services v2 API en de V3 API.

- [API-toegang](migrate-v-2-v-3-differences-api-access.md)
- [Functie hiaten](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminologie en entiteits wijzigingen](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Stap 3 SDK instellen

<hr color="#5ea0ef" size="10">

Meer informatie over de SDK-verschillen en de [configuratie voor het migreren naar de V3-rest API of client-SDK](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Stap 4: richt lijnen op basis van Scenario's

<hr color="#5ea0ef" size="10">

Uw toepassing van Media Services v2 kan uniek zijn. Daarom hebben we op scenario's gebaseerde richt lijnen gegeven op basis van de manier waarop u Media Services in het verleden *hebt* gebruikt en de stappen voor elke functie van de service, zoals:

- [Codering](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Live streamen](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Verpakking en levering](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Inhouds beveiliging](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Gereserveerde media-eenheden (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

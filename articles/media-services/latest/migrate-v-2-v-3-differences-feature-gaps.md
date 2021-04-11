---
title: Hiaten tussen onderdelen tussen Azure Media Services v2 en v3
description: In dit artikel worden de hiaten tussen Azure Media Services v2 en V3 beschreven.
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
ms.openlocfilehash: 564f3127fc6901695890daa520152a7aa1a2337f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962965"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Hiaten tussen onderdelen tussen Azure Media Services v2 en v3

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-2.svg)

In dit deel van de migratie richtlijnen vindt u gedetailleerde informatie over de verschillen tussen de v2-en V3-Api's.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Functie hiaten tussen v2 en V3 Api's

De V3 API heeft de volgende functie hiaten met de v2 API. Een aantal geavanceerde functies van de Media Encoder Standard in v2 Api's zijn momenteel niet beschikbaar in v3:

- Het invoegen van een Silent audio-track wanneer de invoer geen audio heeft, omdat deze niet langer nodig is voor de Azure Media Player.

- Er wordt een video track ingevoegd wanneer de invoer geen video bevat.

- Live-gebeurtenissen met transcode ring bieden momenteel geen ondersteuning voor de toevoeging van een mid-Stream en het invoegen van AD-markeringen via API-aanroepen.

- De Azure media Premium Encoder wordt niet meer ondersteund in v2. Als u het gebruikt voor 8-bits HEVC-code ring, gebruikt u de nieuwe ondersteuning voor HEVC in de standaard encoder. 
    - Er is ondersteuning toegevoegd voor audio kanaal toewijzing aan de standaard encoder.  Zie [Audio in de Media Services code ring Swagger-documentatie](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Als u geavanceerde functies of uitvoer indelingen van het gelicentieerde product van derden gebruikt, zoals MXF of ProRes, gebruikt u de Azure-partner oplossing van Telestream, die transactioneel is op het moment dat de v2 buiten gebruik wordt gesteld. U kunt er ook Voorst Ellen communicatie of [Bitmovin](http://bitmovin.com)gebruiken.

- De eigenschap Availability set van het streaming-eind punt in v2 wordt niet meer ondersteund. Bekijk het voorbeeld project en richt lijnen voor VOD-levering met [hoge Beschik baarheid](./architecture-high-availability-encoding-concept.md) in de V3 API.

- In Media Services v3 kan FairPlay IV niet worden opgegeven. Hoewel dit geen invloed heeft op klanten die Media Services gebruiken voor zowel verpakking als licentie levering, kan het een probleem zijn bij het gebruik van een DRM-systeem van derden voor het leveren van de FairPlay-licenties (hybride modus).

- Opslag versleuteling aan de client zijde voor de beveiliging van assets op rest is verwijderd in de V3 API en vervangen door de opslag service versleuteling voor Data-at-rest. De V3-Api's blijven werken met bestaande opslag versleutelde assets, maar kunnen geen nieuwe worden gemaakt.

## <a name="terminology-and-entity-changes"></a>Terminologie en entiteits wijzigingen

Zie [terminologie en entiteits](migrate-v-2-v-3-differences-terminology.md) wijzigingen voor aanvullende wijzigingen in de API.

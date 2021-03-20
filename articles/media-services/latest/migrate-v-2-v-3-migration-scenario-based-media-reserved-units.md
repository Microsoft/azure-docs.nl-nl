---
title: Richt lijnen voor migratie van gereserveerde media-eenheden (MRUs)
description: In dit artikel vindt u informatie over MRU-scenario's die u helpen bij het migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 5ca01bcea348b866c0f40f82ebe90dc31d032739
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927080"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Richt lijnen voor migratie van MRUs-scenario's (Media gereserveerde eenheden)

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u informatie over MRU-scenario's die u helpen bij het migreren van Azure Media Services v2 naar v3.

- Voor nieuwe v3-accounts die zijn gemaakt in de Azure Portal, of met de 2020-05-01-versie van de V3 API, is het niet meer nodig om gereserveerde media-eenheden (MRUs) in te stellen. Het systeem wordt nu automatisch omhoog en omlaag geschaald op basis van de belasting.
- Als u een v3-of v2-account hebt dat is gemaakt vóór de 2020-05-01-versie van de API, kunt u nog steeds de gelijktijdigheid en prestaties van uw taken beheren met gereserveerde media-eenheden. Zie Mediaverwerking schalen voor meer informatie. U kunt de MRUs beheren met CLI 2,0 voor Media Services v3 of met behulp van de Azure Portal.  
- Als u de optie voor het beheren van MRUs in het Azure Portal niet ziet, voert u een account uit dat is gemaakt met de 2020-05-01-API of hoger.
- Als u bekend bent met het instellen van uw MRU-type voor S3, worden uw prestaties verbeterd of blijven ze hetzelfde.
- Als u een bestaande v2-klant bent, moet u een nieuw v2-account maken ter ondersteuning van uw bestaande toepassing voordat de migratie is voltooid. 
- Indexeer functie v1 of andere media processors die nog niet volledig zijn afgeschaft, moeten mogelijk opnieuw worden ingeschakeld. 

Zie [gereserveerde media-eenheden](concept-media-reserved-units.md) en [het schalen van gereserveerde media-eenheden](media-reserved-units-cli-how-to.md)voor meer informatie over MRUs.

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU-concepten, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

[Gereserveerde media-eenheden](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Handleidingen

[Gereserveerde media-eenheden schalen](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

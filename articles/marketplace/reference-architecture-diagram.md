---
title: Diagram voor referentie architectuur | Azure Marketplace
description: Een referentie architectuur diagram maken voor een aanbieding in de micro soft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 0331bdd8f928b52e56a709fdadbc95892d437cf4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744671"
---
# <a name="reference-architecture-diagram"></a>Diagram van referentie architectuur

Het referentie architectuur diagram is een model dat staat voor de infra structuur waarvan uw aanbieding afhankelijk is. Voor Azure IP-oplossingen moet het diagram ook laten zien hoe uw aanbieding gebruikmaakt van de Cloud Services van micro soft volgens de technische vereisten van het samen verkopen van IP-adressen. Het is niet ontworpen om de kwaliteit van de architectuur te beoordelen. Het is ontworpen om te laten zien hoe uw oplossing gebruikmaakt van micro soft-Services.

Het referentie architectuur diagram kan worden gemaakt via meerdere hulpprogram ma's. Micro soft Visio wordt aangeraden, omdat het meerdere stencils bevat die Azure-architectuur modellen weer gegeven.

Een handig uitgangs punt voor het bouwen van referentie architectuur diagrammen is het gebruik van de [Azure Architecture-modellen](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Typische onderdelen van een referentie architectuur diagram

- Cloud Services die uw aanbieding hosten en ermee werken, met inbegrip van de Azure-resources
- Gegevens verbindingen, gegevens lagen en gegevens services die worden gebruikt door uw aanbieding
- Cloud Services die worden gebruikt voor het beheren van de beveiliging, authenticatie en gebruikers van de aanbieding
- Gebruikers interfaces en andere services die de aanbieding beschikbaar maken voor gebruikers
- Hybride of on-premises connectiviteit en integraties of een combi natie van beide

Deze scherm afbeelding toont een voor beeld van een referentie architectuur diagram.

[![Deze afbeelding is een voor beeld van een architectuur diagram voor co-verkoop.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Dit voor beeld van een referentie architectuur diagram is een verticale chatbot die kan worden geïntegreerd met intranet sites om te helpen bij het oplossen van prognose vraag scenario's via een machine learning-algoritme. Deze oplossing maakt gebruik van toeleverings keten en productie plannings gegevens van verschillende ERP-systemen. De bot is ontworpen om vragen te beantwoorden over wanneer een verkoper op mogelijke leverings datums voor een bestelling kan door voeren.

## <a name="next-steps"></a>Volgende stappen

- [Co-sell configureren voor een commerciële Marketplace-aanbieding](commercial-marketplace-co-sell.md)

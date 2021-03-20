---
title: Azure-netwerk architectuur
description: Dit artikel bevat een algemene beschrijving van het Microsoft Azure infrastructuur netwerk.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567861"
---
# <a name="azure-network-architecture"></a>Azure-netwerk architectuur
De Azure-netwerk architectuur biedt connectiviteit van Internet naar de Azure-data centers. Elke werk belasting die is geïmplementeerd (IaaS, PaaS en SaaS) in azure, maakt gebruik van het Azure Data Center-netwerk.

## <a name="network-topology"></a>Netwerktopologie
De netwerk architectuur van een Azure-Data Center bestaat uit de volgende onderdelen:

- Edge-netwerk
- Wide Area Network
- Netwerk van regionale gateways
- Data Center-netwerk

![Diagram van Azure-netwerk](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Netwerkonderdelen
Een korte beschrijving van de netwerk onderdelen.

- Edge-netwerk

   - Scheidings punt tussen micro soft-netwerken en andere netwerken (bijvoorbeeld Internet, bedrijfs netwerk)
   - Biedt Internet-en [ExpressRoute](../../expressroute/expressroute-introduction.md) -peering in azure

- Wide Area Network

   - Micro soft intelligent backbone-netwerk voor de hele wereld
   - Biedt connectiviteit tussen [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/)

- Regionale gateway

   - Point of aggregatie voor alle data centers in een Azure-regio
   - Biedt een enorme connectiviteit tussen data centers binnen een Azure-regio (bijvoorbeeld multi honderd terabits per Data Center)

- Data Center-netwerk

   - Biedt connectiviteit tussen servers in het Data Center met een laag overschreven band breedte

De bovenstaande netwerk onderdelen zijn ontworpen om maximale Beschik baarheid te bieden voor de ondersteuning van always on, altijd beschik bare Cloud bedrijven. De redundantie is ontworpen en gebouwd in het netwerk van het fysieke aspect, tot aan het beheer protocol.

## <a name="datacenter-network-resiliency"></a>Tolerantie van datacenter netwerk
Laten we het ontwerp principe voor tolerantie illustreren met behulp van het Data Center-netwerk.

Het Data Center Network is een gewijzigde versie van een [Clos-netwerk](https://en.wikipedia.org/wiki/Clos_network)dat hoge band breedte voor de bi-afdeling biedt voor schaal bare Cloud verkeer. Het netwerk is gemaakt met behulp van een groot aantal basisproduct apparaten om de impact die wordt veroorzaakt door individuele hardwarestoringen te verminderen. Deze apparaten bevinden zich strategisch op verschillende fysieke locaties met een afzonderlijk energie-en koele domein om de impact van een omgevings gebeurtenis te verminderen.  Op het besturings vlak worden alle netwerk apparaten uitgevoerd als OSI model Layer 3-routerings modus, waardoor het historische probleem van de verkeers vertraging wordt voor komen. Alle paden tussen de verschillende lagen zijn actief om hoge redundantie en band breedte te bieden met behulp van Equal-Cost Multi-Path-route ring (ECMP).

In het volgende diagram ziet u dat het datacenter netwerk is gemaakt door verschillende lagen van netwerk apparaten. De balken in het diagram vertegenwoordigen groepen netwerk apparaten die de connectiviteit van redundantie en hoge band breedte bieden.

![Data Center-netwerk](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)

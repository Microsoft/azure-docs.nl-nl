---
title: 'Scenario: any-to-any'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: any-to-any"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 3b5002873160490dfb7b8d3ad9790f9c6f1e8ae6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525785"
---
# <a name="scenario-any-to-any"></a>Scenario: any-to-any

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een wille keurig scenario kan elke spoke een andere spoke bereiken. Als er meerdere hubs bestaan, is hub-naar-hub-route ring (ook wel inter-hub genoemd) standaard ingeschakeld in standaard virtueel WAN. U kunt deze configuratie maken met verschillende methoden, zoals de Azure Portal, of een [Azure Quick](quickstart-any-to-any-template.md)start-sjabloon. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring. 

## <a name="design"></a><a name="design"></a>Ontwerp

Om erachter te komen hoeveel route tabellen nodig zijn in een virtueel WAN-scenario, kunt u een verbindings matrix maken waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom).

| Van |   Tot |  *VNets* | *Vertakkingen* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;| Direct | Direct |
| Vertakkingen   | &#8594;| Direct  | Direct |

Elk van de cellen in de vorige tabel beschrijft of een virtuele WAN-verbinding (de ' aan '-zijde van de stroom, de rijkoppen) communiceert met een voor voegsel van de bestemming (de ' aan ' kant van de stroom, de kolom koppen cursief). In dit scenario zijn er geen firewalls of virtuele netwerk apparaten, waardoor communicatie rechtstreeks via Virtual WAN (dus het woord ' direct ' in de tabel) wordt uitgevoerd.

Aangezien alle verbindingen van zowel VNets als filialen (VPN, ExpressRoute en gebruikers VPN) dezelfde connectiviteits vereisten hebben, is er één route tabel vereist. Als gevolg hiervan worden alle verbindingen gekoppeld en door gegeven aan dezelfde route tabel, de standaard route tabel:

* Virtuele netwerken:
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

In **afbeelding 1** kunnen alle VNets en branches (VPN, EXPRESSROUTE, P2S) elkaar bereiken. In een virtuele hub werken verbindingen als volgt:

* Een VPN-verbinding verbindt een VPN-site met een VPN-gateway.
* Een virtuele netwerk verbinding verbindt een virtueel netwerk met een virtuele hub. De router van de virtuele hub biedt de doorvoer functionaliteit tussen VNets.
* Een ExpressRoute-verbinding verbindt een ExpressRoute-circuit met een ExpressRoute-gateway.

Deze verbindingen (standaard bij het maken) zijn gekoppeld aan de standaard route tabel, tenzij u de routerings configuratie van de verbinding instelt op **geen** of een aangepaste route tabel. Deze verbindingen geven routes ook standaard door aan de standaard route tabel. Dit biedt een wille keurig scenario waarbij elke spoke (VNet, VPN, er, P2S) elkaar kan bereiken.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="afbeelding 1":::

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Dit scenario is standaard ingeschakeld voor virtuele WAN. Als de instellingen voor vertakking-naar-vertakking zijn uitgeschakeld in de WAN-configuratie, wordt de verbinding tussen vertakkings spokes niet toegestaan. VPN/ExpressRoute/gebruiker VPN worden beschouwd als vertakkings spaak in virtueel WAN

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

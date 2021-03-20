---
title: 'Azure-ExpressRoute: connectiviteits modellen'
description: Controleer de verbinding tussen het netwerk van de klant, Microsoft Azure en Microsoft 365 Services. Klanten kunnen MPLS-providers, Cloud uitwisselingen en Ethernet gebruiken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978748"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute-connectiviteitsmodellen
U kunt een verbinding maken tussen uw on-premises netwerk en de micro soft-Cloud op vier verschillende manieren, [CloudExchange co-locatie](#CloudExchange), [Point-to-Point Ethernet-verbinding](#Ethernet), [een wille keurige verbinding (IPVPN)](#IPVPN)en [ExpressRoute direct](#Direct). Connectiviteits providers kunnen een of meer connectiviteits modellen bieden. Overleg met uw connectiviteitsprovider om na te gaan welk model voor u het meest geschikt is.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagram van ExpressRoute-connectiviteitsmodellen":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Co-locatie op een Cloud Exchange
Als u zich in een faciliteit met een Cloud-Exchange bevindt, kunt u virtuele cross-connections naar de micro soft-Cloud best Ellen via de Ethernet-Exchange van de co-locatie provider. Co-locatieproviders kunnen Laag-2-overlappende verbindingen of beheerde Laag-3 overlappende verbindingen tussen uw infrastructuur in de co-locatiefaciliteit en de Microsoft Cloud aanbieden.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Point-to-Point Ethernet-verbindingen
U kunt uw on-premises datacenters/kantoren met de Microsoft Cloud verbinden via point-to-point Ethernet-koppelingen. Point-to-point Ethernet-providers kunnen Laag-2-verbindingen of beheerde Laag-3-verbindingen bieden tussen uw locatie en de Microsoft Cloud.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Any-to-any (IPVPN)-netwerken
U kunt uw WAN integreren met de Microsoft Cloud. IPVPN-providers (doorgaans MPLS VPN) bieden any-to-any connectiviteit tussen uw filialen en datacenters. De Microsoft Cloud kan ook worden verbonden met uw WAN, zodat het er net zo uitziet als een filiaal. WAN-providers bieden doorgaans beheerde Laag-3-connectiviteit. ExpressRoute-functies en -mogelijkheden zijn identiek in alle bovenstaande connectiviteitsmodellen.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Direct van ExpressRoute-sites
U kunt rechtstreeks verbinding maken met het wereld wijde netwerk van micro soft op een peering-locatie strategisch gedistribueerd over de hele wereld. [ExpressRoute direct](expressroute-erdirect-about.md) biedt een Dual 100 Gbps-of 10-Gbps-connectiviteit, die ondersteuning biedt voor actieve/actieve connectiviteit op schaal.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over ExpressRoute-verbindingen en -routeringsdomeinen. Zie [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Meer informatie over ExpressRoute-functies. Zie [Technisch overzicht van ExpressRoute](expressroute-introduction.md)
* Zoek een serviceprovider Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md), [NAT](expressroute-nat.md) en [QoS](expressroute-qos.md).
* Configureer uw ExpressRoute-verbinding.
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-portal-resource-manager.md)
  * [Routering configureren](expressroute-howto-routing-portal-resource-manager.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
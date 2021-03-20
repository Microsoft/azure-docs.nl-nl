---
title: 'Azure-ExpressRoute: vereisten'
description: Deze pagina bevat een lijst met vereisten waaraan moet worden voldaan voordat u een Azure ExpressRoute-circuit kunt bestellen. Het bevat een controle lijst.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204789"
---
# <a name="expressroute-prerequisites--checklist"></a>Vereisten en controlelijst voor ExpressRoute
Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services, moet u controleren of er is voldaan aan de vereisten die in de volgende secties worden genoemd.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-account
* Een geldig en actief Microsoft Azure-account. Dit account is vereist om het ExpressRoute-circuit in te stellen. ExpressRoute-circuits zijn resources in Azure-abonnementen. Een Azure-abonnement is een vereiste, zelfs als connectiviteit is beperkt tot niet-Azure micro soft-Cloud Services, zoals Microsoft 365.
* Een actief Microsoft 365 abonnement (als u Microsoft 365-Services gebruikt). Zie de sectie Microsoft 365 specifieke vereisten in dit artikel voor meer informatie.

## <a name="connectivity-provider"></a>Connectiveitsprovider

* U kunt met een [ExpressRoute-connectiviteitspartner](expressroute-locations.md#partners) werken om verbinding te maken met de Microsoft Cloud. U kunt op [drie manieren](expressroute-introduction.md) een verbinding instellen tussen uw on-premises netwerk en Microsoft.
* Als uw provider geen ExpressRoute-connectiviteitspartner is, kunt u via een [cloudexchange-provider](expressroute-locations.md#connectivity-through-exchange-providers) verbinding maken met de Microsoft Cloud.

## <a name="network-requirements"></a>Netwerkvereisten
* **Redundantie op elke peering-locatie**: micro soft vereist dat REDUNDANTe BGP-sessies worden ingesteld tussen de routers van micro soft en de peering routers op elk ExpressRoute-circuit (zelfs wanneer u slechts [één fysieke verbinding hebt met een Cloud-Exchange](expressroute-faqs.md#onep2plink)).
* **Redundantie voor nood herstel**: micro soft raadt u ten zeerste aan om ten minste twee ExpressRoute-circuits in verschillende peering-locaties in te stellen om een single point of failure te voor komen.
* **Routering**: afhankelijk van hoe u verbinding maakt met de Microsoft Cloud, moet u of uw provider de BGP-sessies voor [routeringsdomeinen](expressroute-circuit-peerings.md) instellen en beheren. Sommige Ethernet-connectiviteitsproviders of cloudexchange-providers bieden BGP-beheer aan als extra service.
* **NAT**: Microsoft accepteert alleen openbare IP-adressen via Microsoft-peering. Als u privé IP-adressen in uw on-premises netwerk gebruikt, moet u of uw provider de privé IP-adressen [met behulp van de NAT](expressroute-nat.md) vertalen naar openbare IP-adressen.
* **QoS**: Skype voor Bedrijven heeft verschillende services (zoals spraak, video, tekst) die allemaal een andere QoS-behandeling vereisen. U en uw provider moeten de [QoS-vereisten](expressroute-qos.md) volgen.
* **Netwerkbeveiliging**: overweeg [netwerkbeveiliging](/azure/cloud-adoption-framework/reference/networking-vdc) als u via ExpressRoute verbinding maakt met de Microsoft Cloud.

## <a name="microsoft-365"></a>Microsoft 365
Als u Microsoft 365 op ExpressRoute wilt inschakelen, raadpleegt u de volgende documenten voor meer informatie over Microsoft 365 vereisten.

* [Azure-ExpressRoute voor Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Route ring met ExpressRoute voor Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Hoge beschikbaarheid en failover met ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 Url's en IP-adresbereiken](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Netwerk planning en prestaties afstemmen voor Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Netwerk-en migratie planning voor Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integratie met on-premises omgevingen Microsoft 365](/microsoft-365/enterprise/microsoft-365-integration)
* [Geavanceerde trainingsvideo's voor ExpressRoute in Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Volgende stappen
* Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
* Zoek een ExpressRoute-connectiviteitsprovider. Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md), [NAT](expressroute-nat.md) en [QoS](expressroute-qos.md).
* Configureer uw ExpressRoute-verbinding.
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175771"
---
### <a name="what-is-expressroute-global-reach"></a>Wat is ExpressRoute Global Reach?

ExpressRoute Global Reach is een Azure-service die uw on-premises netwerken verbindt via de ExpressRoute-service in het wereldwijde netwerk van Microsoft. Als u bijvoorbeeld een privédatacenter in Californië hebt dat is verbonden met ExpressRoute in Silicon Valley, en een ander privédatacenter in Texas dat is verbonden met ExpressRoute in Dallas, kunt u met ExpressRoute Global Reach uw privédatacenters met elkaar verbinden via twee ExpressRoute-verbindingen. Verkeer tussen uw datacenters gaat dan via de netwerkbackbone van Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hoe kan ik ExpressRoute Global Reach inschakelen of uitschakelen?

U schakelt ExpressRoute Global Reach in door uw ExpressRoute-circuits met elkaar te verbinden. U kunt de functie uitschakelen door de verbinding met de circuits te verbreken. Bekijk de [configuratie](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Heb ik ExpressRoute Premium nodig voor ExpressRoute Global Reach?

Als uw ExpressRoute-circuits zich in dezelfde geopolitieke regio bevinden, hebt u geen ExpressRoute Premium nodig om ze met elkaar te verbinden. Als twee ExpressRoute-circuits zich in verschillende geopolitieke regio's bevinden, hebt u ExpressRoute Premium voor beide circuits nodig om deze met elkaar te verbinden. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hoe worden kosten in rekening gebracht voor ExpressRoute Global Reach?

ExpressRoute maakt connectiviteit mogelijk vanaf uw on-premises netwerk naar Microsoft Cloud Services. ExpressRoute Global Reach maakt connectiviteit mogelijk tussen uw eigen on-premises netwerken via uw bestaande ExpressRoute-circuits, door gebruik te maken met het wereldwijde netwerk van Microsoft. ExpressRoute Global Reach wordt afzonderlijk van de bestaande ExpressRoute-service in rekening gebracht. Er zijn extra kosten verbonden aan het inschakelen van deze functie op elk ExpressRoute-circuit. Verkeer tussen uw on-premises netwerken dat mogelijk wordt gemaakt met ExpressRoute Global Reach, wordt gefactureerd met een tarief voor uitgaand verkeer bij de bron en een tarief voor inkomend verkeer bij de bestemming. De tarieven zijn gebaseerd op de zone waar de circuits zich bevinden.

### <a name="where-is-expressroute-global-reach-supported"></a>Waar wordt ExpressRoute Global Reach ondersteund?

ExpressRoute Global Reach wordt ondersteund in [landen/regio's of plaatsen selecteren](../articles/expressroute/expressroute-global-reach.md). De ExpressRoute-circuits moeten worden gemaakt op de peeringlocaties in deze landen/regio's of plaatsen.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Ik heb meer dan twee on-premises netwerken, elk verbonden met een ExpressRoute-circuit. Kan ik ExpressRoute Global Reach gebruiken om al mijn on-premises netwerken te verbinden?

Ja, dat kan, zolang de circuits zich in de ondersteunde landen/regio's bevinden. U kunt per keer twee ExpressRoute-circuits verbinden. Als u een volledig meshed netwerk wilt maken, moet u alle circuitparen opsommen en de configuratie herhalen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan ik ExpressRoute Global Reach gebruiken tussen twee ExpressRoute-circuits op dezelfde peeringlocatie?

Nee. De twee circuits moeten afkomstig zijn van verschillende peeringlocaties. Als een metropool in een land/regio met ondersteuning meer dan één ExpressRoute-peeringlocatie heeft, kunt u de ExpressRoute-circuits die op verschillende peeringlocaties in deze metropool zijn gemaakt, met elkaar verbinden. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Als ExpressRoute Global Reach is ingeschakeld tussen circuit X en circuit Y, en tussen circuit Y en circuit Z, kunnen mijn on-premises netwerken die zijn verbonden met circuit X en circuit Z, dan met elkaar communiceren via het netwerk van Microsoft?

Nee. Als u de connectiviteit tussen twee van uw on-premises netwerken wilt inschakelen, moet u de bijbehorende ExpressRoute-circuits expliciet verbinden. In het bovenstaande voorbeeld moet u circuit X en circuit Z verbinden. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Wat is de netwerkdoorvoer die ik kan verwachten tussen mijn on-premises netwerken, nadat ik ExpressRoute Global Reach heb ingeschakeld?

De netwerkdoorvoer tussen uw on-premises netwerken, mogelijk gemaakt met ExpressRoute Global Reach, wordt beperkt door de kleinste van de twee ExpressRoute-circuits. On-premises verkeer naar Azure, en on-premises verkeer naar on-premises, delen hetzelfde circuit en vallen onder dezelfde bandbreedtelimiet. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Wat zijn met ExpressRoute Global Reach de limieten voor het aantal routes dat ik kan adverteren en het aantal routes dat ik ontvang?

Het aantal routes dat u kunt adverteren naar Microsoft in Azure Privépeering, blijft 4.000 voor een Standard-circuit of 10.000 voor een Premium-circuit. Het aantal routes dat u van Microsoft ontvangt in Azure Privépeering, is de som van de routes van uw virtuele Azure-netwerken en de routes van uw andere on-premises netwerken die zijn verbonden via ExpressRoute Global Reach. Zorg ervoor dat u een geschikte maximumlimiet voor voorvoegsels instelt voor de on-premises router. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Wat is de SLA voor ExpressRoute Global Reach?

ExpressRoute Global Reach biedt dezelfde [SLA voor de beschikbaarheid](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) als de normale ExpressRoute-service.

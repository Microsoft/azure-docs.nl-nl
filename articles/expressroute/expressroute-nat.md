---
title: 'Azure ExpressRoute: NAT-vereisten voor circuits'
description: Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van de NAT voor ExpressRoute-circuits.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89569642"
---
# <a name="expressroute-nat-requirements"></a>NAT-vereisten voor ExpressRoute
Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services, moet u NAT's instellen en beheren. Sommige connecitiviteitsproviders bieden het instellen en beheren van NAT aan als een beheerde service. Neem contact op met uw connectiviteitsprovider om na te gaan of ze deze service leveren. Als dat niet het geval is, moet u voldoen aan de vereisten die hieronder worden beschreven. 

Bekijk de pagina [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en routeringsdomeinen) voor een overzicht van de verschillende routeringsdomeinen. Om te voldoen aan de vereisten voor openbare IP-adressen voor openbare Azure-peering en Microsoft-peering, wordt u aangeraden om NAT in te stellen tussen uw netwerk en Microsoft. In deze sectie vindt u een gedetailleerde beschrijving van de NAT-infrastructuur die u moet instellen.

## <a name="nat-requirements-for-microsoft-peering"></a>NAT-vereisten voor Microsoft-peering
Met het pad voor Microsoft-peering kunt u verbinding maken met Microsoft Cloud-services die niet worden ondersteund via het pad voor openbare Azure-peering. De lijst met Services bevat Microsoft 365 Services, zoals Exchange Online, share point online en Skype voor bedrijven. Microsoft verwacht bidirectionele connectiviteit op de Microsoft-peering te gaan ondersteunen. Verkeer dat is bestemd voor Microsoft Cloud-services moet met SNAT worden omgezet naar geldige openbare IPv4-adressen voordat het het Microsoft-netwerk binnenkomt. Verkeer dat is bestemd voor uw netwerk en afkomstig is van Microsoft Cloud-services, moet met SNAT worden omgezet aan de kant van uw internet om [asymmetrische routering](expressroute-asymmetric-routing.md) te voorkomen. In de afbeelding hieronder ziet u hoe de NAT moet worden ingesteld voor micro soft-peering.

![Diagram op hoog niveau van hoe de NAT moet worden ingesteld voor micro soft-peering.](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Verkeer dat afkomstig is van uw netwerk en is bestemd voor Microsoft
* U moet ervoor zorgen dat verkeer het pad voor Microsoft-peering binnenkomt met een geldig openbaar IPv4-adres. Microsoft moet de eigenaar van de IPv4 NAT-adresgroep kunnen controleren in het regionale Routing Internet Registry (RIR) of een Internet Routing Registry (IRR). Er wordt een controle uitgevoerd op basis van het AS-nummer waaraan het wordt gekoppeld en de IP-adressen die voor de NAT worden gebruikt. Raadpleeg de pagina [ExpressRoute routing requirements](expressroute-routing.md) (Routeringsvereisten voor ExpressRoute) voor meer informatie over routeringsregisters.
* IP-adressen die worden gebruikt voor de configuratie van openbare Azure-peering en andere ExpressRoute-circuits mogen niet aan Microsoft worden geadverteerd via de BGP-sessie. Er is geen lengtebeperking voor het NAT IP-voorvoegsel dat via deze peering wordt geadverteerd.
  
  > [!IMPORTANT]
  > De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Verkeer dat afkomstig is van Microsoft en is bestemd voor Microsoft
* In bepaalde scenario's moet Microsoft connectiviteit starten met service-eindpunten die worden gehost in uw netwerk. Een typisch voor beeld van het scenario is de verbinding met ADFS-servers die worden gehost in uw netwerk van Microsoft 365. In dergelijke gevallen moet u vanuit uw netwerk geschikte voorvoegsels naar de Microsoft-peering laten lekken. 
* U moet Microsoft-verkeer met SNAT omzetten aan de kant van internet voor service-eindpunten in uw netwerk om [asymmetrische routering](expressroute-asymmetric-routing.md) te voorkomen. Aanvragen **en antwoorden** met een doel-IP die overeenkomt met een route ontvangen via ExpressRoute worden altijd verzonden via ExpressRoute. Er is sprake van asymmetrische routering als de aanvraag is ontvangen via internet en het antwoord wordt verzonden via ExpressRoute. Als het inkomende Microsoft-verkeer met SNAT wordt omgezet aan de kant van internet, wordt antwoordverkeer terug naar de kant van internet gedwongen, waarmee het probleem wordt opgelost.

![Asymmetrische routering met ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>NAT-vereisten voor openbare Azure-peering

> [!NOTE]
> Open bare Azure-peering is niet beschikbaar voor nieuwe circuits.
> 

Met het pad voor openbare Azure-peering kunt u verbinding maken met alle services die via de openbare IP-adressen worden gehost in Azure. Deze lijst bevat services die worden vermeld in de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) en alle services die door ISV's worden gehost op Microsoft Azure. 

> [!IMPORTANT]
> Connectiviteit met Microsoft Azure-services via openbare peering wordt altijd gestart vanuit uw netwerk naar het Microsoft-netwerk. Daarom kunnen sessies met uw netwerk niet vanuit Microsoft Azure-services via ExpressRoute worden gestart. Als dit toch wordt geprobeerd, maken pakketten die naar deze aangekondigde IP-adressen worden verzonden, gebruik van internet in plaats van ExpressRoute.
> 

Verkeer dat is bestemd voor Microsoft Azure via openbare peering moet met SNAT worden omgezet naar geldige openbare IPv4-adressen voordat het het Microsoft-netwerk binnenkomt. In onderstaande afbeelding ziet u een algemeen beeld van hoe de NAT kan worden ingesteld om te voldoen aan bovenstaande vereiste.

![Diagram op hoog niveau van hoe de NAT kan worden ingesteld om te worden omgezet naar geldige open bare IPv4-adressen voordat ze het micro soft-netwerk binnenkomen.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP-adresgroep en route-advertenties
U moet ervoor zorgen dat verkeer het pad voor openbare Azure-peering binnenkomt met een geldig openbaar IPv4-adres. Microsoft moet het eigenaarschap van de IPv4 NAT-adresgroep kunnen controleren in het regionale Routing Internet Registry (RIR) of een Internet Routing Registry (IRR). Er wordt een controle uitgevoerd op basis van het AS-nummer waaraan het wordt gekoppeld en de IP-adressen die voor de NAT worden gebruikt. Raadpleeg de pagina [ExpressRoute routing requirements](expressroute-routing.md) (Routeringsvereisten voor ExpressRoute) voor meer informatie over routeringsregisters.

Er zijn geen lengtebeperkingen voor het NAT IP-voorvoegsel dat via deze peering wordt geadverteerd. U moet de NAT-adresgroep controleren en ervoor zorgen dat u geen NAT-sessies tekort komt.

> [!IMPORTANT]
> De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md) en [QoS](expressroute-qos.md).
* Voor informatie over werkstromen raadpleegt u [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Werkstromen voor de inrichting van ExpressRoute-circuits en circuittoestanden).
* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-portal-resource-manager.md)
  * [Routering configureren](expressroute-howto-routing-portal-resource-manager.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)


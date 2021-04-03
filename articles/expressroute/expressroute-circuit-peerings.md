---
title: 'Azure-ExpressRoute: circuits en peering'
description: Op deze pagina vindt u een overzicht van ExpressRoute-circuits en routerings domeinen/peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 87fed1d2ac4f5fa85c01d7af10bec10c1412744f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202341"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-circuits en peering

ExpressRoute-circuits maken verbinding met uw on-premises infra structuur met micro soft via een connectiviteits provider. Dit artikel helpt u inzicht te krijgen in ExpressRoute-circuits en routerings domeinen/peering. In de volgende afbeelding ziet u een logische weer gave van de connectiviteit tussen uw WAN en micro soft.

![Diagram waarin wordt weer gegeven hoe ExpressRoute-circuits uw on-premises infra structuur met micro soft verbinden via een connectiviteits provider.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Open bare Azure-peering is afgeschaft en is niet beschikbaar voor nieuwe ExpressRoute-circuits. Nieuwe circuits ondersteunen micro soft-peering en persoonlijke peering.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-circuits

Een ExpressRoute-circuit vertegenwoordigt een logische verbinding tussen uw on-premises infra structuur en micro soft-Cloud Services via een connectiviteits provider. U kunt meerdere ExpressRoute-circuits best Ellen. Elk circuit kan zich in dezelfde of verschillende regio's bevindt en kan worden aangesloten op uw locatie via verschillende connectiviteits providers.

ExpressRoute-circuits worden niet toegewezen aan fysieke entiteiten. Een circuit wordt uniek geïdentificeerd door een standaard-GUID die wordt aangeroepen als een service sleutel (s-Key). De service sleutel is het enige stukje informatie dat wordt uitgewisseld tussen micro soft, de connectiviteits provider en u. De s-sleutel is geen geheim voor beveiligings doeleinden. Er is een 1:1-toewijzing tussen een ExpressRoute-circuit en de s-sleutel.

Nieuwe ExpressRoute-circuits kunnen twee onafhankelijke peerings bevatten: persoonlijke peering en micro soft-peering. Dat bestaande ExpressRoute-circuits drie peerings kunnen bevatten: Azure Public, Azure private en micro soft. Elke peering is een paar onafhankelijke BGP-sessies, die allemaal redundant zijn geconfigureerd voor maximale Beschik baarheid. Er is een toewijzing van 1: N (1 <= N <= 3) tussen een ExpressRoute-circuit en routerings domeinen. Een ExpressRoute-circuit kan elk een, twee of alle drie peerings hebben ingeschakeld per ExpressRoute-circuit.

Elk circuit heeft een vaste band breedte (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) en wordt toegewezen aan een connectiviteits provider en een peering-locatie. De band breedte die u selecteert, wordt gedeeld met alle circuit peerings

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Quota's, limieten en beperkingen

Voor elk ExpressRoute-circuit gelden standaard quota's en limieten. Raadpleeg de pagina [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor actuele informatie over quota's.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute-peering

Aan een ExpressRoute-circuit zijn meerdere routerings domeinen/peerings gekoppeld: Azure openbaar, Azure private en micro soft. Elke peering is op dezelfde manier geconfigureerd op een paar routers (in de configuratie voor het delen van actieve en actieve of laad taken) voor hoge Beschik baarheid. Azure-Services worden gecategoriseerd als *Azure Public* en *Azure private* om de IP-adresserings schema's weer te geven.

![Diagram waarin wordt getoond hoe Azure Public-, Azure private-en micro soft-peerings worden geconfigureerd in een ExpressRoute-circuit.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Persoonlijke Azure-peering

Azure Compute-Services, d.w.z. virtuele machines (IaaS) en Cloud Services (PaaS), die in een virtueel netwerk worden geïmplementeerd, kunnen worden verbonden via het particuliere peering-domein. Het domein van de privé-peering wordt beschouwd als een vertrouwde uitbrei ding van uw basis netwerk in Microsoft Azure. U kunt twee richtings connectiviteit instellen tussen uw basis netwerk en Azure Virtual Networks (VNets). Met deze peering kunt u rechtstreeks verbinding maken met virtuele machines en Cloud Services op hun privé-IP-adressen.  

U kunt meer dan één virtueel netwerk verbinden met het privé-peering domein. Bekijk de [pagina met veelgestelde vragen](expressroute-faqs.md) voor informatie over limieten en beperkingen. U kunt de pagina [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor actuele informatie over limieten bekijken.  Raadpleeg de pagina [route ring](expressroute-routing.md) voor gedetailleerde informatie over de routerings configuratie.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Connectiviteit met micro soft onlineservices (Microsoft 365 en Azure PaaS Services) vindt plaats via micro soft-peering. We scha kelen bidirectionele connectiviteit tussen uw WAN-en micro soft-Cloud Services via het micro soft peering-routerings domein. U moet verbinding maken met micro soft-Cloud Services alleen via open bare IP-adressen die eigendom zijn van u of uw connectiviteits provider en u moet voldoen aan alle gedefinieerde regels. Zie de pagina met ExpressRoute- [vereisten](expressroute-prerequisites.md) voor meer informatie.

Raadpleeg de [pagina met veelgestelde vragen](expressroute-faqs.md) voor meer informatie over ondersteunde services, kosten en configuratie details. Zie de pagina [ExpressRoute locations](expressroute-locations.md) voor informatie over de lijst met connectiviteits providers die ondersteuning bieden voor micro soft-peering.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Peering-vergelijking

De volgende tabel vergelijkt de drie peerings:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

U kunt een of meer van de routerings domeinen inschakelen als onderdeel van uw ExpressRoute-circuit. U kunt ervoor kiezen om alle routerings domeinen te laten plaatsen op hetzelfde VPN als u deze wilt combi neren in één routerings domein. U kunt ze ook op verschillende routerings domeinen plaatsen, vergelijkbaar met het diagram. De aanbevolen configuratie is dat privé-peering rechtstreeks is verbonden met het basis netwerk en dat de koppelingen voor open bare en micro soft-peering zijn verbonden met uw DMZ.

Voor elke peering zijn afzonderlijke BGP-sessies vereist (één paar voor elk type peering). De BGP-sessie paren bieden een Maxi maal beschik bare koppeling. Als u verbinding maakt via providers van laag 2-connectiviteit, bent u verantwoordelijk voor het configureren en beheren van route ring. U kunt meer te weten komen door de [werk stromen](expressroute-workflows.md) te controleren voor het instellen van ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute-status

ExpressRoute-circuits kunnen worden bewaakt voor Beschik baarheid, connectiviteit met VNets en bandbreedte gebruik met behulp van [Netwerkprestatiemeter](../networking/network-monitoring-overview.md) (NPM).

NPM bewaakt de status van persoonlijke Azure-peering en micro soft-peering. Bekijk onze [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zoek een serviceprovider Zie [ExpressRoute-service providers en-locaties](expressroute-locations.md).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Configureer uw ExpressRoute-verbinding.
  * [Create and manage ExpressRoute circuits](expressroute-howto-circuit-portal-resource-manager.md) (ExpressRoute-circuits maken en beheren)
  * [Routering (peering) configureren voor ExpressRoute-circuits](expressroute-howto-routing-portal-resource-manager.md)
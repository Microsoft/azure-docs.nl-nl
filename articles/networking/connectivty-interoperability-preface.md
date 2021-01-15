---
title: 'Interoperabiliteit in Azure: Setup testen | Microsoft Docs'
description: In dit artikel wordt een test installatie beschreven waarmee u de interoperabiliteit tussen ExpressRoute, een site-naar-site-VPN en de peering van virtuele netwerken in azure kunt analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234134"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabiliteit in Azure: instellingen testen

In dit artikel wordt een test installatie beschreven die u kunt gebruiken om te analyseren hoe Azure Networking Services werkt op het niveau van het besturings vlak en het gegevenslaagniveau. Laten we eens kijken naar de Azure-netwerk onderdelen:

-   **Azure ExpressRoute**: gebruik privé-peering in azure ExpressRoute om rechtstreeks persoonlijke IP-ruimten in uw on-premises netwerk te verbinden met uw Azure Virtual Network-implementaties. ExpressRoute kan u helpen een grotere band breedte en een particuliere verbinding te bezorgen. Veel ExpressRoute-eco-partners bieden ExpressRoute-connectiviteit met Sla's. Zie [Inleiding tot ExpressRoute][ExpressRoute]voor meer informatie over ExpressRoute en over het configureren van ExpressRoute.
-   **Site-naar-site-VPN**: u kunt Azure VPN gateway als een site-naar-site-VPN gebruiken om een on-premises netwerk veilig te verbinden met Azure via internet of door gebruik te maken van ExpressRoute. Zie [VPN gateway configureren][VPN]voor meer informatie over het configureren van een site-naar-site-VPN om verbinding te maken met Azure.
-   **Vnet-peering**: gebruik Virtual Network (VNet) peering om verbinding te maken tussen VNets in azure Virtual Network. Zie de [zelf studie over vnet-peering][VNet]voor meer informatie over vnet-peering.

## <a name="test-setup"></a>Setup testen

In de volgende afbeelding ziet u de test installatie:

![1][1]

De hart van de test installatie is de hub VNet in azure regio 1. De hub VNet is op de volgende manieren verbonden met verschillende netwerken:

-   De hub VNet is verbonden met de spoke-VNet met behulp van VNet-peering. De spoke VNet heeft externe toegang tot beide gateways in het hub-VNet.
-   De hub VNet is verbonden met het vertakkings-VNet met behulp van site-naar-site-VPN. De connectiviteit maakt gebruik van eBGP voor Exchange-routes.
-   De hub VNet is verbonden met het on-premises locatie 1-netwerk door ExpressRoute privé-peering als primair pad te gebruiken. Het maakt gebruik van site-naar-site-VPN-verbinding als back-uppad. In de rest van dit artikel verwijzen we naar dit ExpressRoute-circuit als ExpressRoute 1. Standaard bieden ExpressRoute-circuits redundante connectiviteit voor hoge Beschik baarheid. Op ExpressRoute 1 is de subinterface van de secundaire klant zijde (CE) van de router die de secundaire micro soft Enter prise edge-router (MSEE) vormt, uitgeschakeld. Een rode lijn boven de pijl met dubbele regel in de voor gaande afbeelding vertegenwoordigt de subinterface van de uitgeschakelde CE-router.
-   De hub VNet is verbonden met het on-premises locatie 2-netwerk door gebruik te maken van een andere persoonlijke ExpressRoute-peering. In de rest van dit artikel verwijzen we naar dit tweede ExpressRoute-circuit als ExpressRoute 2.
-   ExpressRoute 1 verbindt ook de hub VNet en het on-premises locatie 1-netwerk naar een extern VNet in azure regio 2.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute-en site-naar-site-VPN-verbinding

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-naar-site-VPN via ExpressRoute

U kunt een site-naar-site-VPN configureren door ExpressRoute micro soft-peering te gebruiken voor het privé uitwisselen van gegevens tussen uw on-premises netwerk en uw Azure VNets. Met deze configuratie kunt u gegevens uitwisselen met vertrouwelijkheid, authenticiteit en integriteit. De gegevens uitwisseling is ook anti-replay. Zie [site-naar-site VPN via ExpressRoute micro soft-peering][S2S-Over-ExR]voor meer informatie over het configureren van een site-naar-site IPSec VPN in de tunnel modus met behulp van ExpressRoute micro soft-peering. 

De primaire beperking van het configureren van een site-naar-site-VPN dat gebruikmaakt van micro soft-peering is door voer. De door Voer via de IPsec-tunnel wordt beperkt door de capaciteit van de VPN-gateway. De door Voer van de VPN-gateway is lager dan ExpressRoute door voer. In dit scenario zorgt het gebruik van de IPsec-tunnel voor zeer veilig verkeer en het gebruik van privé-peering voor al het andere verkeer voor het optimaliseren van het ExpressRoute bandbreedte gebruik.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-naar-site-VPN als een beveiligd failover-pad voor ExpressRoute

ExpressRoute fungeert als een redundant circuit paar om hoge Beschik baarheid te garanderen. U kunt geo-redundante ExpressRoute-connectiviteit configureren in verschillende Azure-regio's. Net als tijdens de test installatie, in een Azure-regio, kunt u ook een site-naar-site-VPN gebruiken om een pad voor de ExpressRoute-verbinding te maken. Wanneer dezelfde voor voegsels worden geadverteerd via zowel ExpressRoute als een site-naar-site-VPN, krijgen de ExpressRoute van Azure prioriteit. Om asymmetrische route ring tussen ExpressRoute en de site-naar-site-VPN te voor komen, moet de on-premises netwerk configuratie ook reciprocate met ExpressRoute-connectiviteit voordat de site-naar-site-VPN-verbinding wordt gebruikt.

Zie [ExpressRoute en site-naar-site][ExR-S2S-CoEx]-samen werking voor meer informatie over het configureren van naast elkaar bestaande verbindingen voor ExpressRoute en een site-naar-site-VPN.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Back-end-connectiviteit met spoke VNets-en vertakkings locaties uitbreiden

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke VNet-connectiviteit met behulp van VNet-peering

De hub-en spoke VNet-architectuur wordt veel gebruikt. De hub is een VNet in azure dat fungeert als een centraal punt van connectiviteit tussen uw spoke-VNets en uw on-premises netwerk. De spokes zijn VNets die peer met de hub en die u kunt gebruiken om werk belastingen te isoleren. Verkeer loopt tussen het on-premises Data Center en de hub via een ExpressRoute of een VPN-verbinding. Zie [een hub-spoke-netwerk topologie in azure implementeren][Hub-n-Spoke]voor meer informatie over de architectuur.

In VNet-peering binnen een regio kan spoke VNets hub VNet-gateways (zowel VPN-als ExpressRoute-gateways) gebruiken om te communiceren met externe netwerken.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Vertakkings-VNet-connectiviteit met behulp van site-naar-site-VPN

Mogelijk wilt u VNets, die zich in verschillende regio's bevinden, en on-premises netwerken met elkaar via een hub-VNet. De systeem eigen Azure-oplossing voor deze configuratie is een site-naar-site-VPN-verbinding met behulp van een VPN. U kunt ook een virtueel netwerk apparaat (NVA) gebruiken voor route ring in de hub.

Zie [Wat is VPN gateway?][VPN] en [Implementeer een Maxi maal beschik bare NVA][Deploy-NVA]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [configuratie gegevens][Configuration] voor de test topologie.

Meer informatie over de [controle vlak analyse][Control-Analysis] van de test installatie en de weer gaven van verschillende VNETS of vlan's in de topologie.

Meer informatie over het [gegevens vlak analyse][Data-Analysis] van de weer gaven test installatie en Azure-netwerk bewaking.

Raadpleeg de [Veelgestelde vragen over ExpressRoute][ExR-FAQ] voor het volgende:
-   Meer informatie over het aantal ExpressRoute-circuits waarmee u verbinding kunt maken met een ExpressRoute-gateway.
-   Meer informatie over het aantal ExpressRoute-gateways waarmee u verbinding kunt maken met een ExpressRoute-circuit.
-   Meer informatie over andere schaal limieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram van de test topologie"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
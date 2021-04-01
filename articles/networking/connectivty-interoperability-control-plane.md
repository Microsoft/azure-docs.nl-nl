---
title: 'Interoperabiliteit in Azure: beheer vlak analyse'
description: Dit artikel bevat de controle vlak analyse van de test instellingen die u kunt gebruiken voor het analyseren van de interoperabiliteit tussen ExpressRoute, een site-naar-site-VPN en de peering van virtuele netwerken in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4dfd869b92e042e71eed1ee692d90fc44a8ac6c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234236"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabiliteit in Azure: beheer vlak analyse

In dit artikel wordt de controle vlak analyse van de [test installatie][Setup]beschreven. U kunt ook de configuratie van de [test installatie][Configuration] en de [gegevens vlak analyse][Data-Analysis] van de test installatie controleren.

Met beheer vlak analyse worden routes onderzocht die tussen netwerken binnen een topologie worden uitgewisseld. Met beheer vlak analyse kunt u beter begrijpen hoe verschillende netwerken de topologie weer geven.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspectief voor hub en spoke VNet

De volgende afbeelding illustreert het netwerk vanuit het perspectief van een hub-netwerk (VNet) en een spoke-VNet (gemarkeerd in blauw). De afbeelding toont ook het autonome systeem nummer (ASN) van verschillende netwerken en routes die tussen verschillende netwerken worden uitgewisseld: 

![1][1]

Het ASN van de Azure ExpressRoute-gateway van VNet wijkt af van het ASN van micro soft Enter prise Edge-routers (Msee's). Een ExpressRoute-gateway maakt gebruik van een persoonlijk ASN (een waarde van **65515**) en msee's open bare ASN (een waarde van **12076**) wereld wijd. Wanneer u ExpressRoute-peering configureert, omdat MSEE de peer is, gebruikt u **12076** als peer-ASN. Aan de kant van Azure wordt met MSEE eBGP-peering tot stand gebracht met de ExpressRoute-gateway. De dubbele eBGP-peering die de MSEE voor elke ExpressRoute-peering tot stand brengt, is transparant op het niveau van het besturings vlak. Wanneer u een ExpressRoute-route tabel bekijkt, ziet u daarom de ExpressRoute-gateway-ASN van het VNet voor de voor voegsels van het VNet. 

In de volgende afbeelding ziet u een voor beeld van een ExpressRoute-route tabel: 

![5][5]

In Azure is het ASN alleen significant vanuit een peering-perspectief. De ASN van zowel de ExpressRoute-gateway als de VPN-gateway in azure VPN Gateway is standaard **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>On-premises locatie 1 en het externe VNet-perspectief via ExpressRoute 1

On-premises locatie 1 en het externe VNet zijn via ExpressRoute 1 verbonden met het hub-VNet. Ze delen hetzelfde perspectief van de topologie, zoals wordt weer gegeven in het volgende diagram:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>On-premises locatie 1 en het filiaal VNet-perspectief via een site-naar-site-VPN

On-premises locatie 1 en de vertakkings-VNet zijn verbonden met de VPN-gateway van een hub-VNet via een site-naar-site-VPN-verbinding. Ze delen hetzelfde perspectief van de topologie, zoals wordt weer gegeven in het volgende diagram:

![3][3]

## <a name="on-premises-location-2-perspective"></a>On-premises locatie 2 perspectief

On-premises locatie 2 is verbonden met een hub-VNet via particuliere peering van ExpressRoute 2: 

![4][4]

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

Meer informatie over het [analyseren van gegevens][Data-Analysis] in de weer gaven test installatie en Azure-netwerk bewaking.

Raadpleeg de [Veelgestelde vragen over ExpressRoute][ExR-FAQ] voor het volgende:
-   Meer informatie over het aantal ExpressRoute-circuits waarmee u verbinding kunt maken met een ExpressRoute-gateway.
-   Meer informatie over het aantal ExpressRoute-gateways waarmee u verbinding kunt maken met een ExpressRoute-circuit.
-   Meer informatie over andere schaal limieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspectief voor hub en spoke VNet van de topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Locatie 1 en extern VNet-perspectief van de topologie via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Locatie 1 en vertakkings-VNet perspectief van de topologie via een site-naar-site-VPN"
[4]: ./media/backend-interoperability/Loc2View.png "Locatie 2 perspectief van de topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 route tabel"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
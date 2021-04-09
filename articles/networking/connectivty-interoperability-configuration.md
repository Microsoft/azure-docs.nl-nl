---
title: 'Interoperabiliteit in azure back-end-connectiviteits functies: Configuratie Details | Microsoft Docs'
description: In dit artikel worden de configuratie gegevens voor de test installatie beschreven waarmee u de interoperabiliteit tussen ExpressRoute, een site-naar-site-VPN en de peering van virtuele netwerken in azure kunt analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233267"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabiliteit in azure back-end-connectiviteits functies: Configuratie Details testen

In dit artikel worden de configuratie details van de [test installatie][Setup]beschreven. De test installatie helpt u bij het analyseren van de werking van Azure Networking Services op het niveau van het niveau van de Control en het gegevenslaagniveau.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke VNet-connectiviteit met behulp van VNet-peering

In de volgende afbeelding ziet u de details van Azure Virtual Network peering van een spoke Virtual Network (VNet). Zie [VNet-peering beheren][VNet-Config]voor meer informatie over het instellen van peering tussen twee VNets. Als u wilt dat de spoke VNet gebruikmaakt van de gateways die zijn verbonden met de hub VNet, selecteert u **externe gateways gebruiken**.

[![i]][1]

In de volgende afbeelding ziet u de VNet-peering Details van de hub VNet. Als u wilt dat de hub vnet toestaat dat de spoke VNet de gateways van de hub gebruikt, selecteert u **Gateway doorvoer toestaan**.

[![twee]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Vertakkings-VNet-connectiviteit met behulp van een site-naar-site-VPN

Stel de site-naar-site-VPN-verbinding tussen de hub en de vertakkings VNets in met behulp van VPN-gateways in azure VPN Gateway. Standaard gebruiken VPN-gateways en Azure ExpressRoute-gateways een privé-waarde voor autonoom systeem nummer (ASN) van **65515**. U kunt de ASN-waarde wijzigen in VPN Gateway. In de test installatie wordt de ASN-waarde van de branch VNet VPN-gateway gewijzigd in **65516** om eBGP-route ring tussen de hub en vertakking VNets te ondersteunen.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>On-premises locatie 1 connectiviteit met behulp van ExpressRoute en een site-naar-site-VPN

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 configuratie Details

In de volgende afbeelding ziet u de configuratie van het ExpressRoute-circuit van Azure regio 1 naar de on-premises locatie 1 Customer Edge (CE)-routers:

[![3]][4]

In de volgende afbeelding ziet u de verbindings configuratie tussen het circuit ExpressRoute 1 en de hub-VNet:

[![5,0]][5]

De volgende lijst bevat de configuratie van de primaire CE-router voor ExpressRoute particuliere peering-connectiviteit. (Cisco ASR1000-routers worden als CE-routers gebruikt in de test installatie.) Wanneer site-naar-site VPN-en ExpressRoute-circuits parallel worden geconfigureerd om een on-premises netwerk te verbinden met Azure, wordt in azure standaard prioriteit gegeven aan het ExpressRoute-circuit. Om asymmetrische route ring te voor komen, moet het on-premises netwerk ook prioriteit geven aan ExpressRoute-connectiviteit via VPN-verbinding tussen sites. Met de volgende configuratie wordt prioriteit bepaald met behulp van het kenmerk **lokale voorkeurs** BGP:

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>Details van site-naar-site VPN-configuratie

De volgende lijst bevat de configuratie van de primaire CE-router voor site-naar-site VPN-connectiviteit:

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>On-premises locatie 2 connectiviteit met behulp van ExpressRoute

Een tweede ExpressRoute-circuit, dichter nabij de on-premises locatie 2, verbindt on-premises locatie 2 met de hub VNet. In de volgende afbeelding ziet u de tweede ExpressRoute-configuratie:

[![6,5]][6]

In de volgende afbeelding ziet u de verbindings configuratie tussen het tweede ExpressRoute-circuit en de hub-VNet:

[![7]][7]

ExpressRoute 1 verbindt zowel de hub-VNet als de on-premises locatie 1 met een extern VNet in een andere Azure-regio:

[![8]][8]

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

Meer informatie over de [controle vlak analyse][Control-Analysis] van de test installatie en de weer gaven van verschillende VNETS of vlan's in de topologie.

Meer informatie over het [analyseren van gegevens][Data-Analysis] in de weer gaven test installatie en Azure-netwerk bewaking.

Raadpleeg de [Veelgestelde vragen over ExpressRoute][ExR-FAQ] voor het volgende:
-   Meer informatie over het aantal ExpressRoute-circuits waarmee u verbinding kunt maken met een ExpressRoute-gateway.
-   Meer informatie over het aantal ExpressRoute-gateways waarmee u verbinding kunt maken met een ExpressRoute-circuit.
-   Meer informatie over andere schaal limieten van ExpressRoute.


<!--Image References-->
[]: ./media/backend-interoperability/SpokeVNet_peering.png "vnet-peering" voor 1 spoke-vnet
[]: ./media/backend-interoperability/HubVNet-peering.png "vnet-peering voor twee hub vnet"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN gateway configuratie van een vertakking VNet"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 configuratie"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "verbindings configuratie van ExpressRoute 1 naar een hub VNet ExR-gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2-configuratie"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "configuratie van de verbinding van ExpressRoute 2 met een hub VNet ExR-gateway"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "configuratie van de verbinding van ExpressRoute 2 met een externe VNet-ExR gateway"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
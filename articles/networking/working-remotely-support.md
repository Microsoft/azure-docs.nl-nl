---
title: Op afstand werken met Azure-netwerk services
description: Op deze pagina wordt beschreven hoe u Azure-netwerk services kunt gebruiken die beschikbaar zijn voor het werken op afstand en het oplossen van problemen met verkeer als gevolg van een groter aantal mensen die op afstand werken.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98231839"
---
# <a name="working-remotely-using-azure-networking-services"></a>Op afstand werken met Azure-netwerk services

>[!NOTE]
> In dit artikel wordt beschreven hoe u Azure-netwerk services, micro soft-netwerken en het Azure-partner ecosysteem kunt gebruiken om op afstand te werken en netwerk problemen te verhelpen die u mogelijk hebt gefacing vanwege de COVID-19-crisis.

In dit artikel worden de opties beschreven die beschikbaar zijn voor organisaties om externe toegang voor hun gebruikers in te stellen of om hun bestaande oplossingen aan te vullen met extra capaciteit tijdens peri Oden van piek gebruik. Netwerk architecten worden geconfronteerd met de volgende uitdagingen:

- Adresseer een toename in het netwerk gebruik.
- Bieden betrouw bare veilige connectiviteit voor meer werk nemers van hun bedrijf en klanten.
- Bied connectiviteit met externe locaties over de hele wereld.

Niet alle netwerken (bijvoorbeeld particuliere WAN-en corporate core-netwerken) leiden tot minder last van de maximale belasting van externe werk nemers. De knel punten worden vaak alleen gerapporteerd in thuis breedband netwerken en VPN-gateways van on-premises netwerken van bedrijven.

Met netwerk planners kunt u de knel punten vereenvoudigen en de netwerk congestie verhelpen, omdat u er rekening mee moet houden dat verschillende verkeers typen verschillende netwerk bewerkings prioriteiten nodig hebben en door het omleiden/distribueren van Smart loads. Het medecine verkeer van dokter-patiënten in realtime is bijvoorbeeld een hoge urgentie en vertraging/jitter gevoelig. Terwijl de replicatie van hetzelfde verkeer tussen de opslag niet wordt vertraagd. Het voormalige verkeer moet worden gerouteerd via het meest optimale netwerkpad met een hogere Quality of service. dat het acceptabel is om het later verkeer via een sub-optimale route te routeren.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Het delen van onze aanbevolen procedures-Azure Network is ontworpen voor elasticiteit en hoge Beschik baarheid

Azure is zodanig ontworpen dat het gebruik van de bronnen plotseling kan worden gewijzigd en kan aanzienlijk helpen tijdens de peri Oden van piek gebruik. Micro soft onderhoudt ook een van de grootste netwerken van de wereld. Het netwerk van micro soft is ontworpen voor hoge Beschik baarheid en kan verschillende soorten fouten veroorzaken: vanuit één netwerk element mislukt het mislukken van een hele regio.

Het micro soft-netwerk is ontworpen om te voldoen aan de vereisten en biedt optimale prestaties voor verschillende soorten netwerk verkeer, waaronder een vertraging gevoelig multimedia verkeer voor Skype en teams, CDN, realtime big data analyse, Azure Storage, Bing en Xbox. Om optimale prestaties te bieden voor verschillende soorten verkeer, trekt het micro soft-netwerk alle verkeer in dat bestemd is voor of door middel van eventuele bronnen zo dicht mogelijk bij de oorsprong van het verkeer.

>[!NOTE] 
>Met de Azure-netwerk functies die hieronder worden beschreven, maakt gebruik van het verkeer aantrekkings gedrag van het wereld wijde netwerk van micro soft om een betere klanten ervaring te bieden. Het verkeer aantrekkings gedrag van het micro soft-netwerk helpt het laden van verkeer zo snel mogelijk van de eerste/laatste mijlen te voor komen, waardoor er sprake is van congestie tijdens peri Oden van piek gebruik.
>

## <a name="enable-employees-to-work-remotely"></a>Werk nemers in staat stellen om op afstand te werken

Azure VPN-gateway ondersteunt zowel Point-to-site (P2S) als site-naar-site (S2S) VPN-verbindingen. Met behulp van de Azure VPN-gateway kunt u de verbindingen van uw werk nemers schalen om veilig toegang te krijgen tot uw Azure geïmplementeerde resources en uw on-premises resources. Zie [How to enable users to Remote work](../vpn-gateway/work-remotely-support.md)(Engelstalig) voor meer informatie. 

Als u gebruikmaakt van Secure Sockets Tunneling Protocol (SSTP), is het aantal gelijktijdige verbindingen beperkt tot 128. Om een groter aantal verbindingen te krijgen, worden er overgangen naar OpenVPN of IKEv2 Voorst Ellen. Zie [overgang naar het openvpn-protocol of IKEv2 van SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
)voor meer informatie.

Om toegang te krijgen tot uw resources die zijn geïmplementeerd in azure, kunnen externe ontwikkel aars Azure Bastion-oplossing gebruiken in plaats van een VPN-verbinding voor het verkrijgen van beveiligde shell toegang (RDP of SSH) zonder open bare IP-adressen op de Vm's die worden geopend. Zie [op afstand werken met Azure Bastion](../bastion/work-remotely-support.md)voor meer informatie.

Voor het samen voegen van een grootschalige VPN-verbinding, ter ondersteuning van elke-op-een-verbinding tussen bronnen in verschillende on-premises wereld wijde locaties, in verschillende regionale hub-en spoke-netwerken en voor het optimaliseren van het gebruik van meerdere thuis breedband netwerken kunt u Azure Virtual WAN gebruiken. Zie voor meer informatie lastig om aan de slag te gaan met [uw eigen wensen? Hier vindt u meer informatie over Azure Virtual WAN](../virtual-wan/work-remotely-support.md).

Een andere manier om een extern personeel te ondersteunen is het implementeren van een VDI (Virtual Desktop Infrastructure) die wordt gehost in uw virtuele Azure-netwerk, beveiligd met een Azure Firewall. Windows virtueel bureau blad (WVD) is bijvoorbeeld een desktop-en app Virtualization-service die wordt uitgevoerd in Azure. Met Windows virtueel bureau blad kunt u een schaal bare en flexibele omgeving in uw Azure-abonnement instellen zonder dat u extra gateway servers hoeft uit te voeren. U bent alleen verantwoordelijk voor de virtuele WVD-machines in uw virtuele netwerk. Zie [Azure firewall ondersteuning voor externe werk](../firewall/remote-work-support.md)voor meer informatie. 

Azure beschikt ook over een uitgebreide set energiebeheer partners. Onze partners netwerk virtuele apparaten op Azure kunnen ook de schaal van VPN-verbindingen helpen schalen. Zie [NVA-overwegingen (Network Virtual Appliance) voor extern werk](../vpn-gateway/nva-work-remotely-support.md)voor meer informatie.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>De verbinding van werk nemers tot wereld wijd gedistribueerde resources uitbreiden

Met de volgende Azure-Services kunnen werk nemers toegang krijgen tot uw wereld wijd gedistribueerde resources. Uw resources kunnen zich in een van de Azure-regio's, on-premises netwerken of zelfs in andere open bare of persoonlijke Clouds bevindt. 

- **Azure Virtual Network-peering**: als u uw resources in meer dan één Azure-regio's implementeert en/of als u de connectiviteit van werk nemers met meerdere virtuele netwerken samenvoegt, kunt u verbinding maken tussen de meerdere virtuele netwerken van Azure met behulp van peering op virtueel netwerk. Zie [peering van virtuele netwerken][VNet-peer]voor meer informatie.

- **Oplossing op basis van Azure VPN**: voor uw externe werk nemers die zijn verbonden met Azure via P2S of S2S VPN, kunt u toegang tot on-premises netwerken inschakelen door S2S VPN tussen uw on-premises netwerken en Azure VPN-gateway te configureren. Zie [een site-naar-site-verbinding maken][S2S]voor meer informatie.

- **ExpressRoute**: het gebruik van ExpressRoute-persoonlijke peering is een persoonlijke verbinding tussen uw Azure-implementaties en on-premises infra structuur of uw infra structuur in een functie voor co-locatie mogelijk. ExpressRoute maakt via micro soft-peering ook toegang tot open bare eind punten in micro soft vanuit uw on-premises netwerk. ExpressRoute-verbindingen gaan niet via het openbare internet. Ze bieden veilige connectiviteit, betrouw baarheid, hogere door Voer, met lagere en consistente latenties dan typische verbindingen via internet. Raadpleeg [Overzicht van ExpressRoute][ExR] voor meer informatie. Door gebruik te maken van uw bestaande netwerk provider die al deel uitmaakt van ons [ExpressRoute-partner ecosysteem][ExR-eco] , kan u helpen de tijd te beperken om grote band breedte verbindingen met micro soft te krijgen.  Met behulp van [ExpressRoute direct][ExR-D] kunt u uw on-premises netwerk rechtstreeks verbinden met de micro soft-backbone. ExpressRoute direct biedt twee verschillende opties voor de regel frequentie van Dual 10 Gbps of 100 Gbps. 

- **Virtuele WAN van Azure**: Azure Virtual WAN maakt naadloze interoperabiliteit mogelijk tussen uw VPN-verbindingen en ExpressRoute-circuits. Zoals eerder vermeld, ondersteunt Azure Virtual WAN ook verbindingen tussen bronnen in verschillende on-premises wereld wijde locaties, in verschillende regionale hub-en spoke-netwerken

## <a name="scale-customer-connectivity-to-frontend-resources"></a>De connectiviteit van een klant met front-end-bronnen schalen

Wanneer meer mensen online gaan, hebben veel bedrijfs websites het verkeer van de klant verbeterd. Azure-toepassing gateway kunt u helpen bij het beheren van deze verhoogde frontend-workload. Zie [Application Gateway ondersteuning voor hoog verkeer](../application-gateway/high-traffic-support.md)voor meer informatie.

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Micro soft ondersteuning voor multi-Cloud verkeer

Micro soft kan wereld wijde connectiviteit bieden voor uw implementaties in andere open bare Clouds. Azure Virtual WAN, VPN of ExpressRoute kan u hierbij helpen. Als u de verbinding tussen Azure en andere Clouds wilt uitbreiden, kunt u S2S VPN tussen de twee Clouds configureren. U kunt ook verbinding maken tussen Azure en andere open bare Clouds met behulp van ExpressRoute. Oracle Cloud maakt deel uit van het ExpressRoute-partner ecosysteem. U kunt [een directe interverbinding instellen tussen Azure en de Cloud infrastructuur van Oracle][Az-OCI]. De meeste service providers die deel uitmaken van het ExpressRoute partner ecosysteem bieden ook een persoonlijke connectiviteit met andere open bare Clouds. Met deze service providers kunt u een persoonlijke verbinding tot stand brengen tussen uw implementaties in Azure en andere Clouds via ExpressRoute.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen wordt beschreven hoe u verschillende Azure-netwerk functies kunt gebruiken om gebruikers te schalen die op afstand kunnen werken:

| **Artikel** | **Beschrijving** |
| --- | --- |
| [Gebruikers in staat stellen om extern te werken](../vpn-gateway/work-remotely-support.md) | Bekijk de beschik bare opties voor het instellen van externe toegang voor gebruikers of het aanvullen van de bestaande oplossingen met aanvullende capaciteit voor uw organisatie.|
| [Is het lastig om aan de slag te kunnen? Hier vindt u meer informatie over Azure Virtual WAN](../virtual-wan/work-remotely-support.md) | Gebruik Azure Virtual WAN om de externe connectiviteits behoeften van uw organisatie te verhelpen.|
| [Ondersteuning voor intensief verkeer in Application Gateway](../application-gateway/high-traffic-support.md) | Gebruik Application Gateway met Web Application firewall (WAF) voor een schaal bare en veilige manier om verkeer naar uw webtoepassingen te beheren. |
| [Aandachtspunten voor het virtuele netwerk apparaat (NVA) voor extern werk](../vpn-gateway/nva-work-remotely-support.md)|Lees de richt lijnen over het gebruik van Nva's in azure om oplossingen voor externe toegang te bieden. |
| [Overgang naar het OpenVPN-protocol of IKEv2 van SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | De limiet van de 128 gelijktijdige verbinding van SSTP oplossen door over te stappen op het OpenVPN-protocol of IKEv2.|
| [Extern werken met Azure Bastion](../bastion/work-remotely-support.md) | Een veilige en naadloze RDP/SSH-verbinding bieden met virtuele machines in het virtuele Azure-netwerk, direct in het Azure Portal, zonder gebruik te maken van een openbaar IP-adres. |
| [Azure ExpressRoute gebruiken om hybride connectiviteit te maken ter ondersteuning van externe gebruikers](../expressroute/work-remotely-support.md) | Gebruik ExpressRoute voor hybride connectiviteit zodat gebruikers in uw organisatie op afstand kunnen werken.|
| [Azure Firewall ondersteuning voor externe werk](../firewall/remote-work-support.md)|Beveilig uw Azure Virtual Network-Resources met behulp van Azure Firewall. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md
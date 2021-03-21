---
title: Overzicht van Azure Virtual WAN | Microsoft Docs
description: Lees meer over geautomatiseerde schaalbare verbindingen tussen filialen, beschikbare regio's en partners via Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 641ed73f507d35d7af548d0164ef8e80979be217
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530473"
---
# <a name="what-is-azure-virtual-wan"></a>Wat is Azure Virtual WAN?

Azure Virtual WAN is een netwerkservice die een groot aantal netwerk-, beveiligings- en routeringsfuncties samenbrengt om één operationele interface te bieden. Deze functies omvatten vertakkingsconnectiviteit (via connectiviteitsautomatisering van Virtual WAN Partner-apparaten zoals SD-WAN of VPN CPE), site-naar-site-VPN-connectiviteit, connectiviteit via VPN voor externe gebruikers (punt-naar-site), persoonlijke connectiviteit (ExpressRoute), intra-cloud-connectiviteit (transitieve connectiviteit voor virtuele netwerken), VPN-ExpressRoute-interconnectiviteit, routering, Azure Firewall en versleuteling voor persoonlijke connectiviteit. U hoeft niet al deze use cases te hebben om Virtual WAN te gaan gebruiken. U kunt gewoon aan de slag met slechts één use case en vervolgens uw netwerk aanpassen tijdens de ontwikkeling ervan.

De Virtual WAN-architectuur is een hub- en spoke-architectuur met schaal en prestaties ingebouwd voor vertakkingen (VPN/SD-WAN-apparaten), gebruikers (Azure VPN/OpenVPN/IKEv2-clients), ExpressRoute-circuits en virtuele netwerken. Het maakt een [netwerkarchitectuur voor globale transit](virtual-wan-global-transit-network-architecture.md) mogelijk, waarbij de in de cloud gehoste 'hub' transitieve connectiviteit mogelijk maakt tussen eindpunten die kunnen worden gedistribueerd in verschillende typen spokes.

Azure-regio's fungeren als hubs waarmee u verbinding kunt maken. Alle hubs zijn verbonden in full mesh in een standaard virtuele WAN, waardoor de gebruiker de Microsoft-backbone eenvoudig kan gebruiken voor any-to-any-connectiviteit (elke willekeurige spoke). Gebruikers kunnen spoke-connectiviteit met SD-WAN-/VPN-apparaten handmatig instellen in Azure Virtual WAN of gebruikmaken van de partneroplossing voor Azure Virtual WAN CPE (SD-WAN/VPN) om connectiviteit met Azure in te stellen. Er is een lijst met partners die connectiviteitsautomatisering ondersteunen (mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en connectiviteit op te zetten) met Azure Virtual WAN. Zie het artikel [Virtual WAN-partners en -locaties](virtual-wan-locations-partners.md) voor meer informatie.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Met dit artikel krijgt u snel inzicht in de netwerkverbindingen in Azure Virtual WAN. Virtual WAN biedt de volgende voordelen:

* **Geïntegreerde verbindingsoplossingen in de hub en spoke:** automatiseer de interlokale configuratie en verbindingen tussen on-premises locaties en een Azure-hub.
* **Geautomatiseerde installatie en configuratie van spokes:** zorg voor een naadloze verbinding van uw virtuele netwerken en workloads met de Azure-hub.
* **Intuïtieve probleemoplossing:** u beschikt over een overzicht van de end-to-end stroom in Azure en kunt aan de hand van deze informatie de vereiste acties ondernemen.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Virtuele WAN's Basic en Standard

Er zijn twee soorten virtuele WAN's: Basic en Standard. In de volgende tabel staan de beschikbare configuraties voor elk type.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Zie [Een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md) voor stappen om een virtuele WAN te upgraden.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

Raadpleeg de volgende artikelen voor meer informatie over de Virtual WAN-architectuur en hoe u migreert naar Virtual WAN:

* [Virtuele WAN-architectuur](migrate-from-hub-spoke-topology.md)
* [Wereldwijde architectuur voor doorvoernetwerk](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resources

Als u een end-to-end virtuele WAN wilt configureren, maakt u de volgende resources:

* **virtualWAN:** De virtualWAN-resource vertegenwoordigt een virtuele overlay van uw Azure-netwerk en is een verzameling van meerdere resources. Het bevat koppelingen naar al uw virtuele hubs die u wilt opnemen in het virtuele WAN. Virtual WAN-resources zijn van elkaar geïsoleerd en kunnen geen gemeenschappelijke hub bevatten. Virtuele hubs in Virtual WAN communiceren niet met elkaar.

* **Hub:** een virtuele hub is een virtueel netwerk dat door Microsoft wordt beheerd. De hub bevat verschillende service-eindpunten die verbindingen mogelijk maken. Vanuit uw on-premises netwerk (vpnsite) kunt u verbinding maken met een VPN Gateway binnen de virtuele hub, ExpressRoute-circuits verbinden met een virtuele hub, of zelfs mobiele gebruikers verbinden met een punt-naar-site-gateway in de virtuele hub. De hub is de kern van uw netwerk in een regio. Een Azure-regio mag maar één hub bevatten.

  Een hubgateway is niet hetzelfde als een gateway voor het virtuele netwerk die u voor ExpressRoute en VPN Gateway gebruikt. Wanneer u Virtual WAN gebruikt, maakt u bijvoorbeeld geen site-naar-site-verbinding vanaf uw on-premises locatie rechtstreeks naar uw VNet. In plaats daarvan maakt u een site-naar-site-verbinding naar de hub. Het verkeer verloopt altijd via de hubgateway. Dit houdt in dat uw VNet's hun eigen gateway voor het virtuele netwerk niet nodig hebben. In Virtual WAN kunnen uw VNet's eenvoudig schalen via de virtuele hub en de gateway van de virtuele hub.

* **Hub-verbinding met het virtuele netwerk:** de resource van de hubverbinding met het virtuele netwerk wordt gebruikt om de hub naadloos met het virtuele netwerk te verbinden.

* **Hub-naar-hub-verbinding:** Hubs zijn allemaal met elkaar verbonden in een virtuele WAN. Dit betekent dat een vertakking, gebruiker of VNet die is verbonden met een lokale hub kan communiceren met een andere vertakking of VNet met behulp van de full mesh-architectuur van de verbonden hubs. U kunt VNets ook verbinden binnen een hub die verkeer via de virtuele hub leidt, evenals VNets op de hub, met behulp van het hub-naar-hub-framework.

* **Tabel met de hubroute:**  u kunt een virtuele hubroute maken en de route toepassen op de routetabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub.

**Aanvullende Virtual WAN-resources**

* **Site:** Deze resource wordt alleen gebruikt voor site-naar-site-verbindingen. De siteresource is **vpnsite**. Deze staat voor uw on-premises VPN-apparaat en de bijbehorende instellingen. Wanneer u met een Virtual WAN-partner werkt, beschikt u over een geïntegreerde oplossing die deze gegevens automatisch naar Azure exporteert.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typen connectiviteit

Met Virtual WAN zijn de volgende typen connectiviteit mogelijk: Site-naar-site-VPN, gebruikers-VPN (punt-naar-site) en ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Site-naar-site-VPN-verbindingen

U kunt verbinding maken met uw resources in Azure via een site-to-site-IPsec/IKE (IKEv2)-verbinding. Raadpleeg [Een site-naar-site-verbinding maken met Virtual WAN](virtual-wan-site-to-site-portal.md) voor meer informatie. 

Voor dit type verbinding is een VPN-apparaat of een Virtual WAN-partnerapparaat vereist. Virtual WAN-partners bieden automatisering voor connectiviteit, wat de mogelijkheid is om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en verbinding te maken met Azure Virtual WAN. Raadpleeg het artikel [Virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor een lijst met de beschikbare partners en locaties. Als uw VPN/SD-WAN-apparaat niet in de genoemde koppeling wordt vermeld, kunt u de stapsgewijze instructies in [Een site-naar-site-verbinding maken met Azure Virtual WAN](virtual-wan-site-to-site-portal.md) gebruiken om de verbinding in te stellen.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Gebruikers-VPN-verbindingen (punt-naar-site)

U kunt verbinding maken met uw resources in Azure via een IPsec/IKE (IKEv2)- of OpenVPN-verbinding. Voor dit type verbinding moet een client op de clientcomputer zijn geconfigureerd. Raadpleeg [Een site-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md) voor meer informatie.

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-verbindingen
Met ExpressRoute kunt u uw on-premises netwerk uitbreiden naar Azure via een privéverbinding. Raadpleeg [Een ExpressRoute-verbinding maken met Virtual WAN](virtual-wan-expressroute-portal.md) als u de verbinding tot stand wilt brengen.

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Hub-naar-VNet-verbindingen

U kunt een virtueel Azure-netwerk verbinden met een virtuele hub. Zie [uw VNet verbinden met een hub](virtual-wan-site-to-site-portal.md#vnet)voor meer informatie.

### <a name="transit-connectivity"></a><a name="transit"></a>Transitconnectiviteit

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Transitconnectiviteit tussen VNets

Virtual WAN maakt transitconnectiviteit tussen VNets mogelijk. VNets maken verbinding met een virtuele hub via een virtueel-netwerkverbinding. Transitconnectiviteit tussen de VNets in **Standard Virtual WAN** wordt mogelijk gemaakt door de aanwezigheid van een router in elke virtuele hub. Deze router wordt geïnstantieerd wanneer de virtuele hub voor het eerst wordt gemaakt.

De router kan vier routeringsstatussen hebben: Ingericht, Inrichten, Mislukt of Geen. De **Routeringsstatus** is in de Azure-portal te vinden door naar de pagina Virtuele Hub te navigeren.

* De status **Geen** geeft aan dat de virtuele hub de router niet heeft ingericht. Dit kan gebeuren als het Virtual WAN van het type *Basic* is, of als de virtuele hub is geïmplementeerd voordat de service beschikbaar werd gemaakt.
* De status **Mislukt** status geeft aan dat er een fout is opgetreden tijdens het instantiëren. Als u de router wilt instantiëren of opnieuw wilt instellen, kunt u de optie **Router opnieuw instellen** vinden door naar de overzichtspagina van de virtuele hub in de Azure Portal te navigeren.

Elke virtuele-hub-router ondersteunt een geaggregeerde doorvoer van maximaal 50 Gbps. In connectiviteit tussen de virtuele netwerkverbindingen wordt uitgegaan van een totaal van 2000 VM-werkbelastingen voor alle VNets die zijn verbonden met een virtuele hub.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Transitconnectiviteit tussen VPN en ExpressRoute

Virtual WAN maakt transitconnectiviteit tussen VPN en ExpressRoute mogelijk. Dit betekent dat sites met een VPN-verbinding of externe gebruikers kunnen communiceren met sites die verbonden zijn met ExpressRoute. Er is ook een impliciete aanname dat de **vertakking-tot-vertakkingvlag** is ingeschakeld en dat BGP wordt ondersteund in VPN- en ExpressRoute-verbindingen. Deze vlag is te vinden in de Azure Virtual WAN-instellingen in Azure Portal. Alle routebeheer wordt verzorgd door de virtuele-hub-router, die ook transitconnectiviteit tussen virtuele netwerken mogelijk maakt.

### <a name="custom-routing"></a><a name="routing"></a>Aangepaste routering

Virtual WAN biedt geavanceerde routeringsverbeteringen. De mogelijkheid om aangepaste routetabellen in te stellen, virtuele netwerkroutering te optimaliseren met routekoppeling en -doorgifte, routeringstabellen te groeperen met labels en talloze routeringsscenario's voor virtuele netwerkapparaten (NVA) en gedeelde services te vereenvoudigen.

### <a name="global-vnet-peering"></a><a name="global"></a>Global VNet Peering

Global VNet Peering biedt een mechanisme voor het verbinden van twee VNets in verschillende regio's. In Virtual WAN worden VNets met virtuele hubs verbonden door virtueel-netwerkverbindingen. De gebruiker hoeft globale VNet-peering niet expliciet in te stellen. Voor VNets die verbonden zijn met virtuele hubs in dezelfde regio, worden VNet-peeringkosten in rekening gebracht. Voor VNets die verbonden zijn met virtuele hubs in een andere regio worden globale VNet-peeringkosten in rekening gebracht.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute-verkeersversleuteling

Azure Virtual WAN biedt de mogelijkheid uw ExpressRoute-verkeer te versleutelen. De techniek biedt een versleutelde transit tussen de on-premises netwerken en virtuele Azure-netwerken via ExpressRoute, zonder via het openbare Internet te gaan of openbare IP-adressen te gebruiken. Zie voor meer informatie [IPsec over ExpressRoute voor Virtual WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Locaties

Zie het artikel [Virtual WAN-partners en -locaties](virtual-wan-locations-partners.md) voor informatie over de locatie.

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Routeringstabellen voor Basic en Standard virtuele WAN's

Routeringstabellen hebben nu functies voor koppeling en doorgifte. Een vooraf bestaande routeringstabel is een routeringstabel die deze functies niet heeft. Als u al bestaande routes in hubroutering hebt en u de nieuwe mogelijkheden wilt gebruiken, moet u rekening houden met het volgende:

* **Standard Virtual WAN-klanten met vooraf bestaande routes in virtuele hub**: Als u bestaande routes hebt in de sectie Routering voor de hub in Azure Portal, verwijder die dan eerst en maak vervolgens nieuwe routetabellen (beschikbaar in de sectie Routeringstabellen voor de hub in Azure Portal). U wordt sterk aangeraden om de stappen voor verwijderen uit te voeren voor alle hubs in een virtueel WAN.

* **Basic Virtual WAN-klanten met vooraf bestaande routes in virtuele hub**: Als u bestaande routes hebt in de sectie Routering voor de hub in Azure Portal, verwijder die dan eerst en **upgrade** uw virtuele WAN Basic naar Standard. Zie [Een virtueel WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md). U wordt sterk aangeraden om de stappen voor verwijderen uit te voeren voor alle hubs in een virtueel WAN.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Wat is er nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor virtuele WAN-onderdelen op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN).

## <a name="next-steps"></a>Volgende stappen

[Een site-naar-site-verbinding maken met Virtual WAN](virtual-wan-site-to-site-portal.md)

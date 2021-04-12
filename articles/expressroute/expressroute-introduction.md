---
title: 'Overzicht van Azure ExpressRoute: Verbinding maken via een privéverbinding'
description: In het technische overzicht van ExpressRoute wordt uitgelegd hoe een ExpressRoute-verbinding kan worden gebruikt om uw on-premises netwerk via een persoonlijke verbinding uit te breiden naar Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: e1cef56d15838d80079decc6e1da7ad830bd1cd9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029757"
---
# <a name="what-is-azure-expressroute"></a>Wat is Azure ExpressRoute?
Met ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding met de hulp van een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft-cloudservices, zoals Microsoft Azure en Microsoft 365.

Via een connectiviteitsprovider in een colocatiefaciliteit is connectiviteit mogelijk vanuit een any-to-any (IP VPN) netwerk, een point-to-point Ethernet-netwerk of een virtuele overlappende verbinding. ExpressRoute-verbindingen gaan niet via het openbare internet. Daardoor zijn ExpressRoute-verbindingen betrouwbaarder en sneller, en hebben ze consistente wachttijden en betere beveiliging dan gewone verbindingen via internet. Zie [ExpressRoute connectivity models](expressroute-connectivity-models.md) (ExpressRoute-connectiviteitsmodellen) voor meer informatie over verbinding maken tussen uw netwerk en Microsoft met behulp van ExpressRoute.

![Overzicht van ExpressRoute-verbindingen](./media/expressroute-introduction/expressroute-connection-overview.png)

> [!NOTE]
> In de context van ExpressRoute beschrijft de micro soft Edge de Edge-routers op de micro soft-zijde van het ExpressRoute-circuit. Dit is het ingangs punt van het ExpressRoute-circuit in het netwerk van micro soft.
> 

## <a name="key-benefits"></a>Belangrijkste voordelen

* Laag-3-connectiviteit tussen uw on-premises netwerk en de Microsoft Cloud via een connectiviteitsprovider. Connectiviteit is mogelijk van een any-to-any (IPVPN) netwerk, een point-to-point Ethernet-verbinding of langs een virtuele overlappende verbinding via een Ethernet-exchange.
* Connectiviteit met Microsoft Cloud-services tussen alle gebieden in de geopolitieke regio.
* Globale connectiviteit met Microsoft-services tussen alle regio's met de ExpressRoute Premium-invoegtoepassing.
* Dynamische routering tussen uw netwerk en Microsoft via BGP.
* Ingebouwde redundantie op elke peeringlocatie voor hogere betrouwbaarheid.
* [SLA](https://azure.microsoft.com/support/legal/sla/) voor verbindingsbedrijfstijd.
* QoS-ondersteuning voor Skype voor Bedrijven.

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

## <a name="features"></a>Functies

### <a name="layer-3-connectivity"></a>Laag 3-connectiviteit
Microsoft maakt gebruik van BGP, een standaardprotocol voor dynamische routering, voor het uitwisselen van routes tussen uw on-premises netwerk, uw exemplaren in Azure en openbare Microsoft-adressen. We stellen meerdere BGP-sessies met uw netwerk in voor verschillende verkeersprofielen. Meer informatie vindt u in het artikel [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuit en -routeringsdomeinen).

### <a name="redundancy"></a>Redundantie
Elk ExpressRoute-circuit bestaat uit twee verbindingen naar twee Microsoft Enterprise-randrouters (MSEE's) op een [ExpressRoute-locatie](./expressroute-locations.md#expressroute-locations) vanaf de connectiviteitsprovider/uw netwerkrand. Microsoft vereist een dubbele BGP-verbinding van de connectiviteitsprovider/uw netwerkkant; één voor elke MSEE. U kunt ervoor kiezen om geen redundante apparaten/Ethernet-circuits aan uw kant te implementeren. Connectiviteitsproviders gebruiken redundante apparaten echter om ervoor te zorgen dat uw verbindingen op een redundante manier worden doorgegeven aan Microsoft. Onze [SLA](https://azure.microsoft.com/support/legal/sla/) is alleen geldig als er een redundante Laag-3-connectiviteit is geconfigureerd.

### <a name="connectivity-to-microsoft-cloud-services"></a>Connectiviteit met Microsoft Cloud-services
ExpressRoute-verbindingen maken toegang mogelijk tot de volgende services:
* Microsoft Azure-services
* Microsoft 365-services

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Ga naar de pagina [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor een gedetailleerde lijst met services die via ExpressRoute worden ondersteund.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connectiviteit met alle regio's binnen een geopolitieke regio
U kunt verbinding maken met Microsoft op een van onze [peeringlocaties](expressroute-locations.md), zodat u toegang hebt tot regio's binnen de geopolitieke regio.

Als u bijvoorbeeld verbinding maakt met Microsoft in Amsterdam via ExpressRoute, hebt u toegang tot alle Microsoft-cloudservices die worden gehost in Noord- en West-Europa. Voor een overzicht van de geopolitieke regio's, bijbehorende Microsoft-cloudregio's en bijbehorende ExpressRoute-peeringlocaties, raadpleegt u het artikel [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Globale connectiviteit met ExpressRoute Premium
U kunt [ExpressRoute Premium](expressroute-faqs.md) inschakelen om connectiviteit uit te breiden tot buiten de geopolitieke grenzen. Als u bijvoorbeeld via ExpressRoute verbinding maakt met Microsoft in Amsterdam, hebt u toegang tot alle Microsoft-cloudservices die worden gehost in alle regio's van de wereld. U hebt ook toegang tot services die zijn geïmplementeerd in Zuid-Amerika of Australië als waarop u toegang hebt tot regio's in Noord- en West-Europa. Nationale clouds worden uitgesloten.

### <a name="local-connectivity-with-expressroute-local"></a>Lokale verbinding met ExpressRoute Local
U kunt gegevenskosten effectief overdragen door de [lokale SKU](expressroute-faqs.md) in te schakelen. Met de lokale SKU kunt u uw gegevens overbrengen naar een ExpressRoute-locatie in de buurt van de gewenste Azure-regio. Bij Local is gegevensoverdracht inbegrepen in de kosten voor de ExpressRoute-poort. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Overal on-premises connectiviteit met ExpressRoute Global Reach
U kunt ExpressRoute Global Reach inschakelen zodat er gegevens tussen al uw on-premises sites kunnen worden uitgewisseld, door uw ExpressRoute-circuits met elkaar te verbinden. Stel, u hebt bijvoorbeeld een privédatacentrum in Californië verbonden met een ExpressRoute-circuit in Silicon Valley, en een ander privédatacentrum in Texas met een ExpressRoute-circuit in Dallas. Met ExpressRoute Global Reach kunt u uw privédatacentrums samen via deze twee ExpressRoute-circuits verbinden. Het verkeer tussen datacenters verplaatst zich via het netwerk van Microsoft.

Zie [ExpressRoute Global Reach](expressroute-global-reach.md) voor meer informatie.
### <a name="rich-connectivity-partner-ecosystem"></a>Uitgebreid connectiviteitsecosysteem van partners
ExpressRoute heeft een voortdurend groeiend ecosysteem van connectiviteitsproviders en systeemintegratorpartners. Raadpleeg het artikel [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md) voor de meest recente informatie.

### <a name="connectivity-to-national-clouds"></a>Connectiviteit met nationale clouds
Microsoft stuurt geïsoleerde cloudomgevingen aan voor speciale geopolitieke regio's en klantsegmenten. Raadpleeg de pagina [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md) voor een lijst van nationale clouds en providers.

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct biedt klanten de mogelijkheid om rechtstreeks verbinding te maken met het wereldwijde netwerk van Microsoft op peeringlocaties die strategisch over de wereld zijn verdeeld. ExpressRoute Direct biedt twee verbindingsmogelijkheden van 100 Gbps, die ondersteuning bieden voor actief/actief-verbindingen op schaal.

Belangrijke functies die ExpressRoute Direct biedt, zijn onder andere de volgende:

* Massale gegevensopname in services als Storage en Cosmos DB
* Fysieke isolatie voor branches waarvoor bepaalde wettelijke regels gelden waardoor speciale en geïsoleerde verbindingen een vereiste zijn, zoals het bankwezen, de overheid en de detailhandel
* Gedetailleerde controle van circuitdistributie op basis van bedrijfsonderdelen

Zie [About ExpressRoute Direct](./expressroute-erdirect-about.md) (Over ExpressRoute Direct) voor meer informatie.

### <a name="bandwidth-options"></a>Bandbreedte-opties
U kunt ExpressRoute-circuits aanschaffen voor een breed scala aan bandbreedten. Hieronder vindt u een lijst met de ondersteunde bandbreedten. Controleer ook de lijst met ondersteunde bandbreedten van uw connectiviteitsproviders.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamische schaling van bandbreedte
U kunt de bandbreedte van het ExpressRoute-circuit (zo goed mogelijk) vergroten zonder de verbindingen te moeten verbreken. Zie [Wijzigen van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md#modify) voor meer informatie.

### <a name="flexible-billing-models"></a>Flexibele factureringsmodellen
U kunt een factureringsmodel selecteren dat voor u het meest geschikt is. Kies een van de volgende factureringsmodellen. Zie [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

* **Onbeperkt gegevensverkeer**. De facturering is gebaseerd op een maandtarief; alle binnenkomende en uitgaande gegevensoverdracht is verder gratis.
* **Naar gebruik**. De facturering is gebaseerd op een maandtarief; alle binnenkomende gegevensoverdracht is gratis. Uitgaande gegevensoverdracht wordt in rekening gebracht per GB aan gegevensoverdracht. De tarieven voor gegevensoverdracht verschillen per regio.
* **Premium-invoegtoepassing voor ExpressRoute**. ExpressRoute Premium is een invoegtoepassing voor het ExpressRoute-circuit. De Premium-invoegtoepassing voor ExpressRoute biedt de volgende mogelijkheden: 
  * Ruimere routelimieten voor openbare en persoonlijke Azure-peering van 4000 routes naar 10.000 routes.
  * Globale connectiviteit voor services. Een ExpressRoute-circuit, gemaakt in een willekeurige regio (met uitzondering van nationale clouds), hebben toegang tot resources in elke andere regio ter wereld. Zo is een virtueel netwerk, gemaakt in Europa - west, toegankelijk via een ExpressRoute-circuit dat is ingericht in Silicon Valley.
  * Aantal VNet-koppelingen per ExpressRoute-circuit verhoogd van 10 tot een hogere limiet, afhankelijk van de bandbreedte van het circuit.

## <a name="faq"></a>Veelgestelde vragen
Zie [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor veelgestelde vragen over ExpressRoute.

## <a name="whats-new"></a><a name="new"></a>Wat is er nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor ExpressRoute-functies op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute).

## <a name="next-steps"></a>Volgende stappen
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Meer informatie over [ExpressRoute-connectiviteitsmodellen](expressroute-connectivity-models.md).
* Zoek een serviceprovider Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).

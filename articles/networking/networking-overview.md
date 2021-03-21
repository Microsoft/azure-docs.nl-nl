---
title: Overzicht van Azure-netwerkservices
description: Meer informatie over netwerk services in azure, waaronder connectiviteit, toepassings beveiliging, levering van toepassingen en netwerk bewakings Services.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: d02f85647d947c2f28527ba6f0397c2a98e73840
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234185"
---
# <a name="azure-networking-services-overview"></a>Overzicht van Azure-netwerkservices

De netwerk services in azure bieden diverse netwerk mogelijkheden die samen of afzonderlijk kunnen worden gebruikt. Klik op een van de volgende belang rijke mogelijkheden om meer te weten te komen over:
- [**Connectiviteits Services**](#connect): Azure-resources en on-premises resources verbinden met een of meer van deze netwerk services in Azure-Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN gateway, virtueel netwerk NAT Gateway, Azure DNS, peering-service en Azure Bastion.
- [**Application Protection Services**](#protect): Bescherm uw toepassingen met behulp van een of een combi natie van deze netwerk services in azure-private link, DDoS Protection, firewall, netwerk beveiligings groepen, Web Application Firewall en Virtual Network-eind punten.
- [**Services**](#deliver)voor het leveren van toepassingen: toepassingen leveren in het Azure-netwerk met behulp van een of een combi natie van deze netwerk services in azure-Content Delivery Network (CDN), Azure front-deur Service, Traffic Manager, Application Gateway, Internet Analyzer en Load Balancer.
- [**Netwerk bewaking**](#monitor): Controleer uw netwerk bronnen met behulp van een of een combi natie van deze netwerk services in Azure-Network Watcher, ExpressRoute Monitor, Azure monitor of VNet-Terminal toegangs punt (tik).

## <a name="connectivity-services"></a><a name="connect"></a>Connectiviteitsservices
 
In deze sectie worden de services beschreven die verbinding bieden tussen Azure-resources, connectiviteit van een on-premises netwerk met Azure-resources en vertakkings-naar-vertakkings connectiviteit in azure-Virtual Network (VNet), ExpressRoute, VPN Gateway, virtueel WAN, virtueel netwerk NAT gateway, Azure DNS, Azure peering-service en Azure Bastion.


### <a name="virtual-network"></a><a name="vnet"></a>Virtueel netwerk

Azure Virtual Network (VNet) is de basisbouwsteen voor uw privénetwerk in Azure. U kunt een VNets gebruiken om het volgende te doen:
- **Communiceren tussen Azure-resources**: u kunt vm's en verschillende andere typen Azure-resources implementeren in een virtueel netwerk, zoals Azure app service omgevingen, de Azure Kubernetes-service (AKS) en Azure virtual machine Scale sets. Zie [Integratie van virtuele netwerkservices](../virtual-network/virtual-network-for-azure-services.md) voor een volledige lijst met Azure-resources die u in een virtueel netwerk kunt implementeren.
- **Communiceren tussen** elkaar: u kunt virtuele netwerken met elkaar verbinden, waardoor resources in beide virtuele netwerken met elkaar kunnen communiceren met behulp van peering op virtueel netwerk. De virtuele netwerken die u met elkaar verbindt, kunnen zich in dezelfde of verschillende Azure-regio's bevinden. Zie [peering van virtuele netwerken](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.
- **Communiceren met Internet**: alle resources in een VNet kunnen standaard uitgaand verkeer naar Internet communiceren. U kunt binnenkomend communiceren met een resource door er een openbaar IP-adres of een openbare Load Balancer aan toe te wijzen. U kunt ook [open bare IP-adressen](../virtual-network/virtual-network-public-ip-address.md) of open bare [Load Balancer](../load-balancer/load-balancer-overview.md) gebruiken om uw uitgaande verbindingen te beheren.
- **Communiceren met on-premises netwerken**: u kunt uw on-premises computers en netwerken verbinden met een virtueel netwerk met behulp van [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../expressroute/expressroute-introduction.md).

Zie [Wat is Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)voor meer informatie.

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Met ExpressRoute kunt u uw on-premises netwerken in de micro soft Cloud uitbreiden via een persoonlijke verbinding die wordt vereenvoudigd door een connectiviteits provider. Deze verbinding is een privéverbinding. Verkeer gaat niet via internet. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Microsoft 365 en Dynamics 365.  Zie [Wat is ExpressRoute?](../expressroute/expressroute-introduction.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway helpt u bij het maken van versleutelde cross-premises verbindingen met uw virtuele netwerk vanaf on-premises locaties of het maken van versleutelde verbindingen tussen VNets. Er zijn verschillende configuraties beschikbaar voor VPN Gateway verbindingen, zoals site-naar-site, punt-naar-site of VNet-naar-VNet.
In het volgende diagram ziet u meerdere site-naar-site-VPN-verbindingen met hetzelfde virtuele netwerk.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Verbindingen van site-naar-site Azure-VPN Gateway":::

Zie [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)voor meer informatie over verschillende typen VPN-verbindingen.

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuele WAN
Azure Virtual WAN is een netwerk service die zorgt voor geoptimaliseerde en geautomatiseerde vertakkings connectiviteit met, en via Azure. Azure-regio's fungeren als hubs die u kunt gebruiken om uw filialen te koppelen aan. U kunt de Azure-backbone gebruiken om ook vertakkingen te verbinden en te profiteren van de connectiviteit van Branch-to-VNet. Met Azure Virtual WAN worden veel Azure Cloud Connectivity Services, zoals site-naar-site VPN, ExpressRoute, punt-naar-site-gebruikers-VPN, in één operationele interface gecombineerd. Connectiviteit met Azure VNets wordt tot stand gebracht met behulp van virtuele netwerk verbindingen. Zie [Wat is virtueel WAN van Azure?](../virtual-wan/virtual-wan-about.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtual WAN-diagram":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS is een hostingservice voor DNS-domeinen die naamomzetting biedt met behulp van Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services. Zie [Wat is Azure DNS?](../dns/dns-overview.md)voor meer informatie.

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
De Azure Bastion-service is een nieuwe, volledig door het platform beheerde PaaS-service die u in uw virtuele netwerk inricht. De service biedt beveiligde en naadloze RDP-/SSH-connectiviteit in uw virtuele machine, rechtstreeks in Azure Portal via TLS. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP nodig. Zie [Wat is Azure Bastion?](../bastion/bastion-overview.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure Bastion-architectuur":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>NAT-gateway van virtueel netwerk
Virtual Network NAT (Network Address Translation) vereenvoudigt uitsluitend uitgaande internetconnectiviteit voor virtuele netwerken. Indien geconfigureerd op een subnet, maken alle uitgaande verbindingen gebruik van uw opgegeven statische openbare IP-adressen. Uitgaande connectiviteit is mogelijk zonder load balancer of openbare IP-adressen die rechtstreeks zijn gekoppeld aan virtuele machines. Zie [Wat is virtueel netwerk NAT gateway?](../virtual-network/nat-overview.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="NAT-gateway van virtueel netwerk":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure peering-service
Azure peering service verbetert de connectiviteit van klanten aan micro soft-Cloud Services, zoals Microsoft 365, Dynamics 365, SaaS-Services (Software as a Service), Azure of andere micro soft-services die toegankelijk zijn via het open bare Internet. Zie [Wat is Azure peering service?](../peering-service/about.md)voor meer informatie.

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

Azure Edge-zone is een familie van Microsoft Azure waarmee gegevens verwerking kan worden gesloten voor de gebruiker. U kunt Vm's, containers en andere geselecteerde Azure-Services implementeren in Edge-zones om de vereisten voor de lage latentie en hoge door Voer voor de toepassingen te verhelpen. Zie [Wat is Azure Edge-zones?](edge-zones-overview.md)voor meer informatie.

### <a name="azure-orbital"></a><a name="orbital"></a>Azure-orbital

Azure Orbital is een volledig beheerd, op de cloud gebaseerd grondstation als een service waarmee u kunt communiceren met uw ruimtevaartuig of satellieten, gegevens kunt downlink en uplinken, uw gegevens in de cloud kunt verwerken, services met Azure-Services in unieke scenario's kunt samenstellen en producten voor uw klanten kunt genereren. Dit systeem is gebaseerd op de globale Azure-infrastructuur en een globaal Fiber-netwerk met lage latentie. Zie [Wat is Azure Orbital?](azure-orbital-overview.md)voor meer informatie.

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Communicatie diagram van Azure orbital":::

## <a name="application-protection-services"></a><a name="protect"></a>Services voor toepassings beveiliging

In deze sectie worden netwerk services in azure beschreven waarmee u uw netwerk bronnen kunt beveiligen. Beveilig uw toepassingen met een of meer combi natie van deze netwerk services in azure-DDoS Protection, persoonlijke koppeling, firewall, Web Application firewall, netwerk beveiligings groepen en Virtual Network Service-eind punten.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../ddos-protection/manage-ddos-protection.md) biedt tegen maatregelen tegen de meest geavanceerde DDoS bedreigingen. De service biedt verbeterde mogelijkheden voor DDoS-beperking voor uw toepassing en resources die zijn geïmplementeerd in uw virtuele netwerken. Klanten die Azure DDoS Protection gebruiken, hebben bovendien toegang tot DDoS snelle reactie ondersteuning om DDoS-experts te benaderen tijdens een actieve aanval.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Met [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL database) en Azure gehoste klanten-partner services via een persoonlijk eind punt in uw virtuele netwerk.
Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt een eigen Private Link-service maken in uw virtuele netwerk en deze aanbieden bij klanten.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Overzicht Privé-eindpunt":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Met behulp van Azure Firewall kunt u toepassingen en beleids regels voor de toepassing en het netwerk op verschillende locaties en in virtuele netwerken centraal maken, afdwingen en registreren. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren. 

Raadpleeg de [Azure firewall documentatie](../firewall/overview.md)voor meer informatie over Azure firewall.

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Firewalloverzicht":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
[Azure Web Application firewall](../web-application-firewall/overview.md) (WAF) biedt beveiliging voor uw webtoepassingen van veelvoorkomende webtoepassingen en zwakke plekken, zoals SQL-injectie en scripting op meerdere sites. Azure WAF voorziet in out-of-Box-beveiliging van OWASP Top 10 van beveiligings problemen via beheerde regels. Daarnaast kunnen klanten aangepaste regels configureren, die door de klant beheerde regels zijn om extra beveiliging te bieden op basis van het bron-IP-bereik, en om kenmerken zoals kopteksten, cookies, formulier gegevens velden of query reeks parameters op te geven.

Klanten kunnen ervoor kiezen om [Azure WAF te implementeren met Application Gateway](../web-application-firewall/ag/ag-overview.md) die regionale beveiliging biedt aan entiteiten in open bare en privé-adres ruimte. Klanten kunnen er ook voor kiezen om [Azure WAF te implementeren met de voor deur](../web-application-firewall/afds/afds-overview.md) die beveiliging biedt aan de rand van het netwerk tot open bare eind punten.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web Application Firewall":::

### <a name="network-security-groups"></a><a name="nsg"></a>Netwerk beveiligings groepen
U kunt netwerkverkeer naar en van Azure-resources in een virtueel Azure-netwerk filteren met een netwerkbeveiligingsgroep. Zie [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md) voor meer informatie.

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Service-eindpunten
Met service-eindpunten van Virtual Network (VNet) kunt u de privé-adresruimte van uw virtuele netwerk en de identiteit van uw VNet uitbreiden naar Azure-services, via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw VNet naar de Azure-service blijft altijd in het Microsoft Azure-backbonenetwerk. Zie [service-eind punten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Service-eindpunten voor virtueel netwerk":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Services voor het leveren van toepassingen

In deze sectie worden de netwerk services in azure beschreven waarmee u toepassingen Content Delivery Network, Azure front-deur service, Traffic Manager, Load Balancer en Application Gateway kunt leveren.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) biedt ontwikkelaars een globale oplossing voor het snel leveren van inhoud met hoge bandbreedte door de inhoud op strategische, fysieke knooppunten in de hele wereld in de cache op te slaan. Zie voor meer informatie over Azure CDN [Azure Content Delivery Network](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Met de Azure Front Door Service kunt u de internationale routering van uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. Met Front Door kunt u uw internationale (multiregionale) klant- en bedrijfstoepassingen transformeren in robuuste, hoogwaardige, gepersonaliseerde moderne toepassingen, API’s en inhoud die een wereldwijd bereik hebben met Azure. Zie [Azure front-deur](../frontdoor/front-door-overview.md)voor meer informatie.

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Overzicht van de voor deur-service":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt. Traffic Manager biedt een reeks verkeers routerings methoden voor het distribueren van verkeer zoals prioriteit, gewicht, prestaties, geografisch, meerdere waarden of subnet. Zie [Traffic Manager routerings methoden](../traffic-manager/traffic-manager-routing-methods.md)voor meer informatie over routerings methoden voor verkeer.

In het volgende diagram ziet u route ring op basis van eind punten met Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure Traffic Manager ' Priority ' Traffic-routerings methode":::

Zie [Wat is Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md) voor meer informatie over Traffic Manager.

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
De Azure Load Balancer biedt hoge prestaties en lage latentie taak verdeling laag 4 voor alle UDP-en TCP-protocollen. Het beheert binnenkomende en uitgaande verbindingen. U kunt open bare en interne eind punten met taak verdeling configureren. U kunt regels definiëren voor het toewijzen van binnenkomende verbindingen aan bestemmingen van back-endservers door gebruik te maken van TCP-en HTTP-beschikbaarheids opties voor het beheren van de beschik baarheid van de service. Lees het artikel [overzicht van Load Balancer](../load-balancer/load-balancer-overview.md) voor meer informatie over Load Balancer.

In de volgende afbeelding ziet u een toepassing met meerdere lagen op Internet die gebruikmaakt van zowel externe als interne load balancers:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer-voor beeld":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Het is een ADC (Application Delivery controller) als een service met verschillende mogelijkheden voor taak verdeling voor laag 7 voor uw toepassingen. Zie [Wat is Azure-toepassing gateway?](../application-gateway/overview.md)voor meer informatie.

Het volgende diagram toont op URL-pad gebaseerde route ring met Application Gateway.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway-voor beeld":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Netwerk bewakings Services
In deze sectie worden netwerk services in azure beschreven waarmee u uw netwerk bronnen Network Watcher, Azure Monitor voor netwerken, ExpressRoute monitor, Azure Monitor en Virtual Network TIKT kunt bewaken.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher biedt hulpprogramma's voor het controleren, diagnosticeren en weergeven van metrische gegevens en het in- of uitschakelen van logboekregistratie voor resources in een virtueel Azure-netwerk. Zie [Wat is Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie.

### <a name="azure-monitor-for-networks-preview"></a>Preview-versie van Azure Monitor voor netwerken
Azure Monitor voor netwerken biedt een uitgebreid overzicht van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen, zonder dat hiervoor configuratie is vereist. Het biedt ook toegang tot netwerk bewakings mogelijkheden, zoals [verbindings monitor](../network-watcher/connection-monitor-overview.md), [flow logboek registratie voor netwerk beveiligings groepen](../network-watcher/network-watcher-nsg-flow-logging-overview.md)en [Traffic Analytics](../network-watcher/traffic-analytics.md). Zie [Azure monitor voor netwerken preview](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie.

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitor voor ExpressRoute
Zie [ExpressRoute controle, metrische gegevens en waarschuwingen](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie over het weer geven van metrische gegevens voor ExpressRoute-circuits, bron logboeken en-waarschuwingen.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Met Azure Monitor worden de beschikbaarheid en prestaties van uw toepassing gemaximaliseerd, door een uitgebreide oplossing te bieden voor het verzamelen en analyseren van, en werken met telemetriegegevens vanuit uw cloudomgeving en on-premises omgeving. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn. Zie [Azure monitor Overview](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie.
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network Tik
Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een [virtuele netwerk apparaat](https://azure.microsoft.com/solutions/network-appliances/) -partner.

In de volgende afbeelding ziet u hoe virtuele netwerk tikken werkt:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Hoe Virtual Network TIKT werkt":::

Zie [Wat is Virtual Network Tik](../virtual-network/virtual-network-tap-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste virtuele netwerk en Verbind een aantal Vm's hiervoor door de stappen in het artikel [uw eerste virtuele netwerk maken](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) te volt ooien.
- Verbind uw computer met een virtueel netwerk door de stappen in het [artikel een punt-naar-site-verbinding configureren](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)te volt ooien.
- Taak verdeling van Internet verkeer naar open bare servers door de stappen in het artikel [een Internet gerichte Load Balancer maken](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) uit te voeren.
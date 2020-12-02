---
title: Concepten en vereisten voor netwerk beveiliging in azure | Microsoft Docs
description: Dit artikel bevat algemene uitleg over de basis beginselen en vereisten voor de beveiliging van het netwerk en informatie over wat Azure biedt op elk van deze gebieden.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 8a82fd6e6dc1bea0804c429f3af46884f004de69
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489765"
---
# <a name="azure-network-security-overview"></a>Overzicht van Azure-netwerkbeveiliging

Netwerk beveiliging kan worden gedefinieerd als het proces van het beveiligen van bronnen tegen onbevoegde toegang of aanvallen door besturings elementen toe te passen op netwerk verkeer. Het doel is om ervoor te zorgen dat alleen rechtmatig verkeer wordt toegestaan. Azure bevat een robuuste netwerk infrastructuur ter ondersteuning van uw toepassings-en service connectiviteits vereisten. De netwerk verbinding is mogelijk tussen bronnen in azure, tussen on-premises en Azure gehoste resources, en naar en van Internet en Azure.

In dit artikel worden enkele van de opties beschreven die Azure biedt op het gebied van netwerk beveiliging. U vindt meer informatie over:

* Azure-netwerken
* Netwerktoegangsbeheer
* Azure Firewall
* Beveiligde externe toegang en cross-premises connectiviteit
* Beschikbaarheid
* Naamomzetting
* Architectuur van perimeter netwerk (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Bewaking en detectie van bedreigingen

## <a name="azure-networking"></a>Azure-netwerken

Azure vereist dat virtuele machines zijn verbonden met een Azure-Virtual Network. Een virtueel netwerk is een logische constructie die boven op de fysieke Azure-netwerk infrastructuur is gebouwd. Elk virtueel netwerk is geïsoleerd van alle andere virtuele netwerken. Dit helpt ervoor te zorgen dat het netwerk verkeer in uw implementaties niet toegankelijk is voor andere Azure-klanten.

Meer informatie:

* [Overzicht van virtueel netwerk](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Netwerktoegangsbeheer

Netwerk toegangs beheer is de handeling van het beperken van de connectiviteit met en van specifieke apparaten of subnetten binnen een virtueel netwerk. Het doel van netwerk toegangs beheer is om de toegang tot uw virtuele machines en services te beperken tot goedgekeurde gebruikers en apparaten. Toegangs beheer is gebaseerd op beslissingen over het toestaan of weigeren van verbindingen van uw virtuele machine of service.

Azure ondersteunt verschillende typen netwerk toegangs beheer, zoals:

* Besturings element Network Layer
* Route beheer en geforceerde tunneling
* Beveiligings apparatuur voor virtuele netwerken

### <a name="network-layer-control"></a>Besturings element Network Layer

Voor een veilige implementatie is een zekere mate van toegangs beheer van het netwerk vereist. Het doel van netwerk toegangs beheer is het beperken van de communicatie van de virtuele machine met de benodigde systemen. Andere communicatie pogingen worden geblokkeerd.

> [!NOTE]
> Opslag firewalls worden behandeld in het artikel [overzicht van Azure Storage-beveiliging](../../storage/blobs/security-recommendations.md)

#### <a name="network-security-rules-nsgs"></a>Netwerk beveiligings regels (Nsg's)

Als u toegangs beheer op basis van een netwerk niveau (op basis van IP-adres en TCP-of UDP-protocollen) nodig hebt, kunt u netwerk beveiligings groepen (Nsg's) gebruiken. Een NSG is een elementaire, stateful pakket filtering firewall en biedt u de mogelijkheid om de toegang te beheren op basis van een [5-tuple](https://www.techopedia.com/definition/28190/5-tuple). Nsg's bevatten functionaliteit om het beheer te vereenvoudigen en de kans op Configuratie fouten te verminderen:

* Uitgebreide **beveiligings regels** vereenvoudigen NSG regel definitie en bieden u de mogelijkheid om complexe regels te maken in plaats van meerdere eenvoudige regels te hoeven maken om hetzelfde resultaat te krijgen.
* **Service Tags** zijn micro soft heeft labels gemaakt die een groep IP-adressen vertegenwoordigen. Ze worden dynamisch bijgewerkt om IP-adresbereiken op te nemen die voldoen aan de voor waarden die in het label worden opgenomen. Als u bijvoorbeeld een regel wilt maken die van toepassing is op alle Azure Storage in de regio Oost, kunt u opslag gebruiken.
* Met **toepassings beveiligings groepen** kunt u resources implementeren in toepassings groepen en de toegang tot deze resources beheren door regels te maken die gebruikmaken van die toepassings groepen. Als u bijvoorbeeld webservers hebt geïmplementeerd voor de toepassings groep webservers, kunt u een regel maken waarmee NSG verkeer van Internet naar alle systemen in de toepassings groep webservers wordt 443 toegestaan.

Nsg's bieden geen inspectie van toepassings lagen of geverifieerde toegangs beheer.

Meer informatie:

* [Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Just-in-time-VM-toegang

[Azure Security Center](../../security-center/security-center-introduction.md) kan de Nsg's op vm's beheren en de toegang tot de virtuele machine vergren delen totdat een gebruiker met de juiste toegangs beheer functie van Azure [RBAC](../../role-based-access-control/overview.md) -machtigingen voor Azure op basis van rollen toegang heeft aangevraagd. Wanneer de gebruiker een autorisatie met succes heeft uitgevoerd, worden de Nsg's zodanig gewijzigd dat de toegang tot de geselecteerde poorten voor de opgegeven tijd is toegestaan. Wanneer de tijd verstrijkt, worden de Nsg's hersteld naar de vorige beveiligde status.

Meer informatie:

* [Azure Security Center just-in-time-toegang](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Service-eindpunten

Service-eind punten zijn een andere manier om controle over uw verkeer toe te passen. U kunt de communicatie met ondersteunde services beperken tot alleen uw VNets via een directe verbinding. Verkeer van uw VNet naar de opgegeven Azure-service blijft op het Microsoft Azure backbone-netwerk.  

Meer informatie:

* [Service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Route beheer en geforceerde tunneling

De mogelijkheid om het routerings gedrag voor uw virtuele netwerken te beheren is van cruciaal belang. Als route ring onjuist is geconfigureerd, kunnen toepassingen en services die op uw virtuele machine worden gehost, verbinding maken met niet-geautoriseerde apparaten, inclusief systemen die eigendom zijn van en worden beheerd door potentiële aanvallers.

Azure-netwerken bieden ondersteuning voor de mogelijkheid om het routerings gedrag voor netwerk verkeer op uw virtuele netwerken aan te passen. Hierdoor kunt u de standaard vermeldingen van de routerings tabel in het virtuele netwerk wijzigen. Met behulp van het gedrag van route ring kunt u ervoor zorgen dat alle verkeer van een bepaald apparaat of een groep apparaten het virtuele netwerk binnenkomt of verlaat via een specifieke locatie.

U kunt bijvoorbeeld een beveiligings apparaat voor virtuele netwerken op uw virtuele netwerk hebben. U wilt ervoor zorgen dat al het verkeer van en naar het virtuele netwerk via dat virtuele beveiligings apparaat loopt. U kunt dit doen door door de [gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) (udr's) te configureren in Azure.

[Geforceerde tunneling](https://www.petri.com/azure-forced-tunneling) is een mechanisme dat u kunt gebruiken om ervoor te zorgen dat uw services geen verbinding met apparaten op Internet mogen initiëren. Houd er rekening mee dat dit verschilt van het accepteren van binnenkomende verbindingen en vervolgens reageert. Front-end-webservers moeten reageren op aanvragen van Internet hosts, waardoor het Internet verkeer naar deze webservers kan worden verzonden en de webservers kunnen reageren.

Wat u niet wilt toestaan is een front-end-webserver voor het initiëren van een uitgaande aanvraag. Dergelijke aanvragen kunnen een beveiligings risico vormen, omdat deze verbindingen kunnen worden gebruikt voor het downloaden van malware. Zelfs als u wilt dat deze front-endservers uitgaande aanvragen naar Internet initiëren, kunt u ervoor zorgen dat deze door uw on-premises Web-proxy's worden door lopen. Zo kunt u gebruikmaken van URL-filtering en logboek registratie.

In plaats daarvan zou u geforceerde tunneling wilt gebruiken om dit te voor komen. Wanneer u geforceerde tunneling inschakelt, worden alle verbindingen met Internet geforceerd via uw on-premises gateway. U kunt geforceerde tunneling configureren door gebruik te maken van Udr's.

Meer informatie:

* [Wat zijn door de gebruiker gedefinieerde routes en door sturen via IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Beveiligings apparatuur voor virtuele netwerken

Hoewel Nsg's, Udr's en geforceerde tunneling u een beveiligings niveau biedt op het netwerk en de transport lagen van het [OSI-model](https://en.wikipedia.org/wiki/OSI_model), wilt u mogelijk ook beveiliging inschakelen op hogere niveaus dan het netwerk.

Uw beveiligings vereisten kunnen bijvoorbeeld het volgende omvatten:

* Verificatie en autorisatie voordat u toegang tot uw toepassing toestaat
* Inbraak detectie en reactie op inbraak
* Application Layer-inspectie voor protocollen op hoog niveau
* URL-filtering
* Anti virus-en antimalware op netwerk niveau
* Anti-bot-beveiliging
* Toegangs beheer voor toepassingen
* Aanvullende DDoS-beveiliging (boven de DDoS-beveiliging die wordt verschaft door de Azure-infra structuur zelf)

U kunt toegang krijgen tot deze verbeterde beveiligings functies van het netwerk met behulp van een Azure-partner oplossing. Ga naar de [Azure Marketplace](https://azure.microsoft.com/marketplace/)en zoek naar ' Beveiliging ' en ' netwerk beveiliging ' om de meest recente oplossingen voor Azure-partner netwerk beveiliging te vinden.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid. Enkele functies zijn onder andere:

* Hoge beschikbaarheid
* Schaal baarheid van Cloud
* Regels voor het filteren van de FQDN van toepassingen
* Regels voor het filteren van netwerkverkeer

Meer informatie:

* [Overzicht van Azure Firewall](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Beveiligde externe toegang en cross-premises connectiviteit

De installatie, configuratie en het beheer van uw Azure-resources moeten op afstand worden uitgevoerd. Daarnaast wilt u mogelijk [hybride it](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) -oplossingen implementeren met onderdelen on-premises en in de open bare Azure-Cloud. Deze scenario's vereisen beveiligde externe toegang.

Azure Networking ondersteunt de volgende veilige scenario's voor externe toegang:

* Afzonderlijke werk stations verbinden met een virtueel netwerk
* Uw on-premises netwerk verbinden met een virtueel netwerk met een VPN
* Uw on-premises netwerk verbinden met een virtueel netwerk met een specifieke WAN-koppeling
* Virtuele netwerken met elkaar verbinden

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Afzonderlijke werk stations verbinden met een virtueel netwerk

U wilt mogelijk afzonderlijke ontwikkel aars of operationele mede werkers in staat stellen om virtuele machines en services in azure te beheren. Stel dat u toegang nodig hebt tot een virtuele machine in een virtueel netwerk. Maar uw beveiligings beleid staat geen externe RDP-of SSH-toegang toe voor afzonderlijke virtuele machines. In dit geval kunt u een [punt-naar-site-VPN-](../../vpn-gateway/point-to-site-about.md) verbinding gebruiken.

Met de punt-naar-site-VPN-verbinding kunt u een persoonlijke en beveiligde verbinding instellen tussen de gebruiker en het virtuele netwerk. Wanneer de VPN-verbinding tot stand is gebracht, kan de gebruiker RDP of SSH via de VPN-koppeling naar een virtuele machine in het virtuele netwerk. (Hierbij wordt ervan uitgegaan dat de gebruiker kan worden geverifieerd en geautoriseerd.) Punt-naar-site-VPN ondersteunt:

* SSTP (Secure Socket Tunneling Protocol), een eigen op SSL gebaseerd VPN-protocol. Een SSL-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls de TCP-poort 443 openen, die door TLS/SSL wordt gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows die SSTP (Windows 7 en hoger) hebben.

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Meer informatie:

* [Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van Power shell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Uw on-premises netwerk verbinden met een virtueel netwerk met een VPN

Mogelijk wilt u uw hele bedrijfs netwerk of delen ervan koppelen aan een virtueel netwerk. Dit is gebruikelijk in hybride IT-scenario's, waarbij organisaties [hun on-premises Data Center uitbreiden naar Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). In veel gevallen hosten organisaties onderdelen van een service in Azure en delen ze on-premises. Dit kan bijvoorbeeld gebeuren wanneer een oplossing front-end webservers bevat in Azure en back-end-data bases. Met deze typen cross-premises verbindingen wordt het beheer van Azure-bronnen ook veiliger en kunnen scenario's zoals het uitbreiden van Active Directory domein controllers naar Azure worden ingeschakeld.

Een manier om dit te bereiken is het gebruik van een [site-naar-site-VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Het verschil tussen een site-naar-site-VPN en een punt-naar-site-VPN is dat de laatste één apparaat verbindt met een virtueel netwerk. Een site-naar-site-VPN verbindt een volledig netwerk (zoals uw on-premises netwerk) met een virtueel netwerk. Site-naar-site-Vpn's naar een virtueel netwerk gebruiken het uiterst veilige IPsec-tunnel modus VPN-protocol.

Meer informatie:

* [Maak een resource manager-VNet met een site-naar-site-VPN-verbinding met behulp van de Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Informatie over VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Uw on-premises netwerk verbinden met een virtueel netwerk met een specifieke WAN-koppeling

Punt-naar-site-en site-naar-site-VPN-verbindingen zijn effectief voor het inschakelen van cross-premises connectiviteit. Sommige organisaties denken echter dat ze de volgende nadelen hebben:

* VPN-verbindingen verplaatsen gegevens via internet. Dit maakt deze verbindingen mogelijk voor mogelijke beveiligings problemen die betrekking hebben op het verplaatsen van gegevens via een openbaar netwerk. Bovendien kan de betrouw baarheid en beschik baarheid voor Internet verbindingen niet worden gegarandeerd.
* VPN-verbindingen met virtuele netwerken hebben mogelijk niet de band breedte voor sommige toepassingen en doel einden, omdat deze Maxi maal ongeveer 200 Mbps groot zijn.

Organisaties waarvoor het hoogste beveiligings niveau en de beschik baarheid voor hun cross-premises verbindingen nodig zijn, gebruiken meestal specifieke WAN-koppelingen om verbinding te maken met externe sites. Azure biedt u de mogelijkheid om een specifieke WAN-koppeling te gebruiken die u kunt gebruiken om uw on-premises netwerk te verbinden met een virtueel netwerk. Azure ExpressRoute, Express route direct en Express route Global Reach maken dit mogelijk.

Meer informatie:

* [Technisch overzicht van ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direct](../../expressroute/expressroute-erdirect-about.md)
* [Wereld wijd bereik snelle route](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtuele netwerken met elkaar verbinden

Het is mogelijk om veel virtuele netwerken te gebruiken voor uw implementaties. Er zijn verschillende redenen waarom u dit kunt doen. U kunt het beheer vereenvoudigen of u wilt een betere beveiliging. Ongeacht de motivatie voor het plaatsen van resources op verschillende virtuele netwerken, is het mogelijk dat u de resources op elk van de netwerken met elkaar wilt verbinden.

Een optie is voor services op één virtueel netwerk om verbinding te maken met Services op een ander virtueel netwerk, door een ' loop back ' via internet. De verbinding wordt gestart op één virtueel netwerk, gaat via internet en vervolgens weer terug naar het virtuele netwerk van de bestemming. Met deze optie wordt de verbinding met de beveiligings problemen besproken die inherent zijn aan Internet communicatie.

Een betere optie is het maken van een site-naar-site-VPN dat verbinding maakt tussen twee virtuele netwerken. Deze methode maakt gebruik van hetzelfde [IPSec-tunnel modus](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) protocol als de cross-premises site-naar-site VPN-verbinding die hierboven wordt genoemd.

Het voor deel van deze benadering is dat de VPN-verbinding tot stand is gebracht via de Azure-netwerk infrastructuur, in plaats van via Internet verbinding te maken. Dit biedt een extra beveiligingslaag, vergeleken met site-naar-site-Vpn's die via Internet verbinding maken.

Meer informatie:

* [Een VNet-naar-VNet-verbinding configureren met behulp van Azure Resource Manager en Power shell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Een andere manier om verbinding te maken met uw virtuele netwerken is  [VNET-peering](../../virtual-network/virtual-network-peering-overview.md). Met deze functie kunt u twee Azure-netwerken met elkaar verbinden zodat communicatie tussen de micro soft-backbone-infra structuur plaatsvindt zonder dat deze ooit via internet gaat. VNET-peering kan twee VNETs binnen dezelfde regio of twee VNETs tussen Azure-regio's verbinden. Nsg's kan worden gebruikt om de connectiviteit tussen verschillende subnetten of systemen te beperken.

## <a name="availability"></a>Beschikbaarheid

Beschik baarheid is een belang rijk onderdeel van elk beveiligings programma. Als uw gebruikers en systemen geen toegang hebben tot wat ze nodig hebben om toegang te krijgen tot het netwerk, kan de service als aangetast worden beschouwd. Azure heeft netwerk technologieën die ondersteuning bieden voor de volgende mechanismen voor hoge Beschik baarheid:

* Taak verdeling op basis van HTTP
* Taak verdeling netwerk niveau
* Globale taak verdeling

Taak verdeling is een mechanisme dat is ontworpen om verbindingen gelijkmatig te verdelen over meerdere apparaten. De doel stellingen van taak verdeling zijn:

* Om de beschik baarheid te verg Roten. Wanneer u verbindingen met meerdere apparaten verdeelt, kunnen een of meer van de apparaten niet meer beschikbaar zijn zonder de service in gevaar te brengen. De services die op de overige online apparaten worden uitgevoerd, kunnen de inhoud van de service blijven gebruiken.
* Om de prestaties te verbeteren. Wanneer u verbindingen met meerdere apparaten verdeelt, hoeft één apparaat niet alle bewerkingen te verwerken. In plaats daarvan worden de bewerkings-en geheugen vereisten voor het uitvoeren van de inhoud verdeeld over meerdere apparaten.

### <a name="http-based-load-balancing"></a>Taak verdeling op basis van HTTP

Organisaties die webgebaseerde services uitvoeren, willen vaak een op HTTP gebaseerde load balancer voor deze webservices. Dit zorgt voor een adequaat prestatie niveau en hoge Beschik baarheid. Traditionele load balancers op basis van het netwerk zijn afhankelijk van netwerk-en trans port Layer-protocollen. Op HTTP gebaseerde load balancers kunt u aan de andere kant besluiten nemen op basis van de kenmerken van het HTTP-protocol.

Azure-toepassing gateway biedt op HTTP gebaseerde taak verdeling voor uw webservices. Application Gateway ondersteunt:

* Sessieaffiniteit op basis van cookies. Op deze manier zorgt u ervoor dat verbindingen die tot stand zijn gebracht op een van de servers achter die load balancer intact blijven tussen de client en de server. Dit garandeert de stabiliteit van trans acties.
* TLS-offload. Wanneer een client verbinding maakt met de load balancer, wordt die sessie versleuteld met behulp van het HTTPS-protocol (TLS). Om de prestaties te verbeteren, kunt u echter het HTTP (niet-versleutelde) protocol gebruiken om verbinding te maken tussen de load balancer en de webserver achter de load balancer. Dit wordt ' TLS-offload ' genoemd, omdat de webservers achter de load balancer geen gebruik kunnen maken van de processor overhead die bij de versleuteling betrokken is. De webservers kunnen daarom sneller aanvragen.
* Op URL gebaseerde route ring van inhoud. Met deze functie kunnen de load balancer besluiten nemen over het door sturen van verbindingen op basis van de doel-URL. Dit biedt veel meer flexibiliteit dan oplossingen die taak verdelings beslissingen nemen op basis van IP-adressen.

Meer informatie:

* [Overzicht van Application Gateway](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Taak verdeling netwerk niveau

In tegens telling tot HTTP-gebaseerde taak verdeling, nemen taak verdeling op netwerk niveau beslissingen op basis van IP-adres en poort (TCP-of UDP-) getallen.
U kunt profiteren van de voor delen van taak verdeling op netwerk niveau in azure met behulp van Azure Load Balancer. Enkele belang rijke kenmerken van Load Balancer zijn:

* Taak verdeling op netwerk niveau op basis van IP-adres en poort nummers.
* Ondersteuning voor een Application Layer-Protocol.
* Taak verdelingen voor virtuele Azure-machines en Cloud Services-rolinstanties.
* Kan worden gebruikt voor zowel Internet gerichte (externe taak verdeling) als niet-Internet gerichte (interne taak verdeling) toepassingen en virtuele machines.
* Eindpunt bewaking, die wordt gebruikt om te bepalen of de services achter de load balancer niet meer beschikbaar zijn.

Meer informatie:

* [Internet gerichte load balancer tussen meerdere virtuele machines of services](../../load-balancer/load-balancer-overview.md)
* [Overzicht van interne load balancer](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Globale taak verdeling

Sommige organisaties willen het hoogste niveau van Beschik baarheid mogelijk maken. Een manier om dit doel te bereiken, is door toepassingen te hosten in wereld wijd gedistribueerde data centers. Wanneer een toepassing wordt gehost in data centers overal ter wereld, is het mogelijk dat een hele geopolitieke regio niet meer beschikbaar is en dat de toepassing nog steeds actief is.

Deze strategie voor taak verdeling kan ook prestatie voordelen opleveren. U kunt aanvragen voor de service omleiden naar het Data Center dat zich het dichtst bij het apparaat bevindt dat de aanvraag indient.

In azure kunt u profiteren van de voor delen van globale taak verdeling met behulp van Azure Traffic Manager.

Meer informatie:

* [Wat is Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Naamomzetting

Naam omzetting is een belang rijke functie voor alle services die u in azure host. Vanuit het oogpunt van beveiliging kan de functie voor naam omzetting ertoe leiden dat een aanvaller aanvragen van uw sites omleiden naar de site van een aanvaller. Veilige naam omzetting is een vereiste voor alle gehoste services in de Cloud.

Er zijn twee typen naam omzetting die u moet aanpakken:

* Interne naam omzetting. Dit wordt gebruikt door services in uw virtuele netwerken, uw on-premises netwerken of beide. Namen die worden gebruikt voor interne naam omzetting, zijn niet toegankelijk via internet. Voor een optimale beveiliging is het belang rijk dat uw interne naam omzettings schema niet toegankelijk is voor externe gebruikers.
* Externe naam omzetting. Dit wordt gebruikt door personen en apparaten buiten uw on-premises netwerken en virtuele netwerken. Dit zijn de namen die zichtbaar zijn voor Internet en worden gebruikt om verbinding te maken met uw Cloud Services.

Voor een interne naam omzetting hebt u twee opties:

* Een DNS-server voor het virtuele netwerk. Wanneer u een nieuw virtueel netwerk maakt, wordt er een DNS-server voor u gemaakt. Deze DNS-server kan de namen van de computers die zich op dat virtuele netwerk bevinden, omzetten. Deze DNS-server kan niet worden geconfigureerd, wordt beheerd door Azure Fabric Manager en kan daarom helpen bij het beveiligen van uw oplossing voor naam omzetting.
* Uw eigen DNS-server meenemen. U hebt de mogelijkheid om een DNS-server van uw eigen keuze te plaatsen in uw virtuele netwerk. Deze DNS-server kan een Active Directory geïntegreerde DNS-server zijn, of een speciale DNS-server oplossing die door een Azure-partner wordt verschaft, die u kunt verkrijgen via de Azure Marketplace.

Meer informatie:

* [Overzicht van virtueel netwerk](../../virtual-network/virtual-networks-overview.md)
* [DNS-servers beheren die worden gebruikt door een virtueel netwerk](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Voor externe naam omzetting hebt u twee opties:

* Host uw eigen externe DNS-server on-premises.
* Host uw eigen externe DNS-server met een service provider.

Veel grote organisaties hosten hun eigen DNS-servers on-premises. Ze kunnen dit doen omdat ze de netwerk expertise en wereld wijde aanwezigheid hebben.

In de meeste gevallen is het beter om uw DNS-naamomzettingsservices te hosten met een service provider. Deze service providers hebben de netwerk expertise en wereld wijde aanwezigheid om zeer hoge Beschik baarheid te garanderen voor uw services voor naam omzetting. Beschik baarheid is essentieel voor DNS-services, want als uw naam omzettings Services mislukken, kan niemand uw services op internet bereiken.

Azure biedt u een Maxi maal beschik bare, externe DNS-oplossing met hoge prestaties in de vorm van Azure DNS. Deze oplossing voor externe naam omzetting maakt gebruik van de wereld wijde Azure DNS-infra structuur. U kunt uw domein hosten in azure, met dezelfde referenties, Api's, hulpprogram ma's en facturering als uw andere Azure-Services. Als onderdeel van Azure neemt het ook de sterkste beveiligings controles over die zijn ingebouwd in het platform.

Meer informatie:

* [Overzicht van Azure DNS](../../dns/dns-overview.md)
* Met [Azure DNS persoonlijke zones](../../dns/private-dns-overview.md) kunt u privé-DNS-namen voor Azure-resources configureren in plaats van de automatisch toegewezen namen zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen.

## <a name="perimeter-network-architecture"></a>Perimeter netwerk architectuur

Veel grote organisaties gebruiken perimeter netwerken om hun netwerken te segmenteren en maken een buffer zone tussen internet en hun services. Het perimeter gedeelte van het netwerk wordt beschouwd als een zone met weinig beveiliging, en er worden geen assets met een hoge waarde in dat netwerk segment geplaatst. Normaal gesp roken ziet u netwerk beveiligings apparaten met een netwerk interface op het perimeter netwerk segment. Een andere netwerk interface is verbonden met een netwerk met virtuele machines en services die binnenkomende verbindingen van het Internet accepteren.

U kunt perimeter netwerken op verschillende manieren ontwerpen. De beslissing om een perimeter netwerk te implementeren en vervolgens welk type perimeter netwerk u moet gebruiken als u er een wilt gebruiken, is afhankelijk van de vereisten van uw netwerk beveiliging.

Meer informatie:

* [Microsoft Cloud Services en netwerk beveiliging](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

DDoS-aanvallen (Distributed Denial of Service-aanvallen) vormen een van de grootste beschikbaarheids- en beveiligingsproblemen voor klanten die hun toepassingen verplaatsen naar de cloud. Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.
Micro soft biedt DDoS-beveiliging **, ook wel bekend als onderdeel** van het Azure-platform. Dit is gratis en omvat altijd bewaking en realtime beperken van veelvoorkomende aanvallen op netwerk niveau. Naast de beveiligingen die zijn opgenomen in DDoS Protection **Basic** kunt u de optie **standaard** inschakelen. DDoS Protection standaard functies zijn:

* **Systeem eigen platform integratie:** Systeem eigen geïntegreerd in Azure. Bevat configuratie via de Azure Portal. DDoS Protection standaard begrijpt uw resources en resource configuratie.
* **Beveiliging op basis van een bocht:** Vereenvoudigde configuratie beveiligt onmiddellijk alle resources in een virtueel netwerk zodra DDoS Protection standaard is ingeschakeld. Er is geen interventie-of gebruikers definitie vereist. DDoS Protection Standard onmiddellijk en vermindert de aanval automatisch, zodra deze is gedetecteerd.
* **Bewaking over altijd verkeer:** De patronen van uw toepassings verkeer worden 24 uur per dag, 7 dagen per week gecontroleerd en er wordt gezocht naar indica toren van DDoS-aanvallen. De beperking wordt toegepast wanneer het beveiligings beleid wordt overschreden.
* **Rapporten voor risico beperking van aanvallen** Rapporten voor het beperken van aanvallen maken gebruik van geaggregeerde netwerk stroom gegevens om gedetailleerde informatie te geven over aanvallen die gericht zijn op uw resources.
* **Stroom logboeken voor aanvallen beperken** Met de stroom logboeken voor aanvallen beperken kunt u het verloren verkeer, doorgestuurd verkeer en andere aanvals gegevens in bijna realtime bekijken tijdens een actieve DDoS-aanval.
* **Adaptieve afstemming:** Intelligent verkeer profile ring leert het verkeer van uw toepassing gedurende een bepaalde periode en selecteert en werkt het profiel dat het meest geschikt is voor uw service. Het profiel wordt aangepast naarmate het verkeer na verloop van tijd verandert. Layer 3 tot Layer 7 Protection: biedt volledige stack DDoS-beveiliging, wanneer deze wordt gebruikt met een Web Application Firewall.
* **Uitgebreide beperkings schaal:** Meer dan 60 verschillende typen aanvallen kunnen worden gereduceerd, met globale capaciteit om te beschermen tegen de grootste bekende DDoS-aanvallen.
* **Maat staven voor aanvallen:** Een overzicht van de metrische gegevens van elke aanval is toegankelijk via Azure Monitor.
* **Waarschuwing voor aanvallen:** Waarschuwingen kunnen worden geconfigureerd bij het starten en stoppen van een aanval en over de duur van de aanval, met behulp van ingebouwde aanvals waarden. Waarschuwingen worden geïntegreerd in uw operationele software, zoals Microsoft Azure controle logboeken, Splunk, Azure Storage, E-mail en de Azure Portal.
* **Kosten garantie:**  Service-tegoeden voor gegevens overdracht en toepassingen voor gedocumenteerde DDoS-aanvallen.
* **DDoS snel reageren** DDoS Protection Standard-klanten hebben nu toegang tot het snelle antwoord team tijdens een actieve aanval. DRR kan u helpen bij het onderzoeken van aanvallen, aangepaste problemen tijdens een aanval en een oplossing na een aanval.


Meer informatie:

* [Overzicht van DDOS-beveiliging](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Met de Azure front-deur service kunt u de wereld wijde route ring van uw webverkeer definiëren, beheren en bewaken. Het optimaliseert de route ring van uw verkeer voor de beste prestaties en hoge Beschik baarheid. Met Azure Front Door kunt u regels voor toegangsbeheer maken voor aangepaste webtoepassingsfirewalls (WAF) om uw HTTP/HTTPS-workload te beschermen tegen exploitatie op basis van klant-IP-adressen, landcode en http-parameters. Daarnaast kunt u met de voor deur ook regels voor het beperken van de frequentie voor het botsen van schadelijk bot-verkeer maken, inclusief TLS-offloading en per HTTP/HTTPS-aanvraag, verwerking van de toepassingslaag.

Het Front Door-platform wordt zelf beschermd door Azure DDoS Protection Basic. Voor verdere bescherming kan Azure DDoS Protection Standard worden ingeschakeld op uw VNETs en bronnen beschermen tegen netwerklaagaanvallen (TCP/UDP) via automatische afstemming en risicobeperking. De voor deur is een omgekeerde proxy van laag 7, het webverkeer kan alleen worden door gegeven aan back-endservers en andere typen verkeer wordt niet standaard geblokkeerd.

Meer informatie:

* Voor meer informatie over de volledige set mogelijkheden van Azure front-deur kunt u het [overzicht van de Azure front-deur](../../frontdoor/front-door-overview.md) bekijken

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt. Traffic Manager maakt gebruik van DNS om aanvragen van clients door te sturen naar de meest geschikte eindpunten op basis van een methode om verkeer te routeren en van de status van de eindpunten. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost. Traffic Manager bewaakt de eind punten en stuurt geen verkeer door naar eind punt dat niet beschikbaar is.

Meer informatie:

* [Overzicht van Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Bewaking en detectie van bedreigingen

Azure biedt mogelijkheden om u in dit sleutel gebied te helpen met vroegtijdige detectie, bewaking en het verzamelen en controleren van netwerk verkeer.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kan u helpen problemen op te lossen en biedt een hele nieuwe set hulpprogram ma's om u te helpen bij het identificeren van beveiligings problemen.

De [weer gave beveiligings groep](../../network-watcher/network-watcher-security-group-view-overview.md) helpt bij het controleren en de beveiliging van virtual machines. Gebruik deze functie om programmatische controles uit te voeren, waarbij u het basislijn beleid dat door uw organisatie is gedefinieerd, vergelijkt met de juiste regels voor elk van uw virtuele machines. Dit kan u helpen bij het identificeren van een configuratie drift.

Met [pakket opname](../../network-watcher/network-watcher-packet-capture-overview.md) kunt u netwerk verkeer van en naar de virtuele machine vastleggen. U kunt netwerk statistieken verzamelen en toepassings problemen oplossen. Dit kan nuttig zijn bij het onderzoek van indringers in het netwerk. U kunt deze functie ook gebruiken in combi natie met Azure Functions om netwerk opnames te starten als reactie op specifieke Azure-waarschuwingen.

Zie [overzicht van Azure Network Watcher-bewaking](../../network-watcher/network-watcher-monitoring-overview.md)voor meer informatie over Network Watcher en hoe u een deel van de functionaliteit in uw Labs kunt testen.

> [!NOTE]
> Controleer de [pagina Azure updates](https://azure.microsoft.com/updates/?product=network-watcher)voor de meest recente meldingen over de beschik baarheid en de status van deze service.

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt u bedreigingen te voor komen, te detecteren en erop te reageren, en biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligings bewaking en beleids beheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en die werkt met een grote reeks beveiligings oplossingen.

Security Center helpt u bij het optimaliseren en bewaken van netwerk beveiliging door:

* Aanbevelingen voor netwerk beveiliging opgeven.
* De status van de netwerk beveiligings configuratie bewaken.
* U wordt gewaarschuwd voor bedreigingen op basis van het netwerk, zowel op het eind punt als op het netwerk.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Virtual Network Tik

Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een virtuele netwerk apparaat-partner. U kunt hetzelfde virtuele netwerk tikken op resource om verkeer van meerdere netwerk interfaces in dezelfde of verschillende abonnementen samen te voegen.

Meer informatie:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Logboekregistratie

Logboek registratie op netwerk niveau is een belang rijke functie voor elk netwerk beveiligings scenario. In azure kunt u logboek gegevens ophalen die zijn verkregen voor Nsg's om logboek registratie gegevens op netwerk niveau op te halen. Met NSG-logboek registratie krijgt u informatie over:

* [Activiteiten logboeken](../../azure-monitor/platform/platform-logs-overview.md). Gebruik deze logboeken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnementen. Deze logboeken zijn standaard ingeschakeld en kunnen worden gebruikt binnen het Azure Portal. Voorheen bekend als audit of operationele Logboeken.
* Gebeurtenis Logboeken. Deze logboeken bevatten informatie over welke NSG-regels zijn toegepast.
* Item Logboeken. In deze logboeken kunt u zien hoe vaak elke NSG regel is toegepast om verkeer te weigeren of toe te staan.

U kunt ook [micro soft power bi](https://powerbi.microsoft.com/what-is-power-bi/), een krachtig hulp programma voor gegevens visualisatie, gebruiken om deze logboeken weer te geven en te analyseren.
Meer informatie:

* [Azure Monitor logboeken voor netwerk beveiligings groepen (Nsg's)](../../virtual-network/virtual-network-nsg-manage-log.md)
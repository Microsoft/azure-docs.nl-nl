---
title: Aanbevolen procedures voor netwerk beveiliging-Microsoft Azure
description: Dit artikel bevat een aantal aanbevolen procedures voor netwerk beveiliging met ingebouwde Azure-functies.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 4793216a12b17c4e4ea03f62d5a0ba512febc232
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735723"
---
# <a name="azure-best-practices-for-network-security"></a>Best practices van Azure voor netwerkbeveiliging
In dit artikel wordt een verzameling van aanbevolen procedures voor Azure beschreven om uw netwerk beveiliging te verbeteren. Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure-netwerken en de ervaringen van klanten, zoals uzelf.

In dit artikel wordt voor elke best practice uitgelegd:

* Wat de best practice is
* Waarom u die best practice wilt inschakelen
* Wat kan het gevolg zijn als u de best practice niet inschakelt
* Mogelijke alternatieven voor de best practice
* Informatie over het inschakelen van de best practice

Deze best practices zijn gebaseerd op een consensus advies en Azure-platform mogelijkheden en-functie sets, zoals ze bestaan op het moment dat dit artikel werd geschreven. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regel matig bijgewerkt om deze wijzigingen weer te geven.

## <a name="use-strong-network-controls"></a>Sterke netwerk besturings elementen gebruiken
U kunt [Azure virtual machines (vm's)](https://azure.microsoft.com/services/virtual-machines/) en toestellen verbinden met andere apparaten in het netwerk door ze te plaatsen in [Azure Virtual Networks](../../virtual-network/index.yml). Dat wil zeggen dat u virtuele netwerk interface kaarten kunt verbinden met een virtueel netwerk om TCP/IP-communicatie tussen netwerk apparaten toe te staan. Virtuele machines die zijn verbonden met een virtueel Azure-netwerk, kunnen verbinding maken met apparaten in hetzelfde virtuele netwerk, verschillende virtuele netwerken, Internet of uw eigen on-premises netwerken.

Wanneer u uw netwerk en de beveiliging van uw netwerk plant, raden we u aan om het volgende te centraliseren:

- Beheer van basis netwerk functies zoals ExpressRoute, virtueel netwerk en subnet inrichten en IP-adres sering.
- Governance van netwerk beveiligings elementen, zoals een virtueel netwerk apparaat, zoals ExpressRoute, virtuele netwerken en subnet inrichten en IP-adres sering.

Als u een gemeen schappelijke set hulpprogram ma's voor beheer gebruikt om uw netwerk en de beveiliging van uw netwerk te bewaken, kunt u de zicht baarheid duidelijk weer geven. Een eenvoudige, Unified Security-strategie vermindert fouten omdat deze het leren van mensen en de betrouw baarheid van Automation verg Roten.

## <a name="logically-segment-subnets"></a>Logische segment subnetten
Virtuele Azure-netwerken zijn vergelijkbaar met Lan's in uw on-premises netwerk. Het idee achter een virtueel Azure-netwerk is dat u een netwerk maakt op basis van een enkele privé-IP-adres ruimte, waarop u al uw virtuele machines van Azure kunt plaatsen. De beschik bare privé-IP-adres ruimten bevinden zich in de klassen A (10.0.0.0/8), klasse B (172.16.0.0/12) en klasse C (192.168.0.0/16).

De aanbevolen procedures voor het logisch segmenteren van subnetten zijn:

**Aanbevolen procedure**: niet toestaan regels met een brede bereik toe te wijzen (bijvoorbeeld 0.0.0.0 t/m 255.255.255.255 toestaan).  
**Details**: Zorg ervoor dat de procedure voor het oplossen van problemen zich afmoedigt of dit type regels kan instellen. Op deze manier kunnen regels leiden tot een valse beveiligings gevoel en worden ze vaak door rode teams gevonden en misbruikt.

**Best Practice**: Segmenteer de grotere adres ruimte in subnetten.   
**Details**: gebruik op [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)gebaseerde principes voor het maken van uw subnetten.

**Aanbevolen procedure**: netwerk toegangs beheer tussen subnetten maken. Route ring tussen subnetten gebeurt automatisch en u hoeft geen routerings tabellen te configureren. Standaard zijn er geen besturings elementen voor netwerk toegang tussen de subnetten die u maakt in een virtueel Azure-netwerk.   
**Details**: gebruik een [netwerk beveiligings groep](../../virtual-network/virtual-network-vnet-plan-design-arm.md) om te beschermen tegen ongevraagd verkeer in azure-subnetten. Netwerk beveiligings groepen zijn eenvoudige, stateful pakket inspectie apparaten die gebruikmaken van de 5-tuple benadering (bron-IP, bron poort, doel-IP, doel poort en laag 4-Protocol) om regels voor toestaan/weigeren voor netwerk verkeer te maken. U kunt verkeer naar en van meerdere IP-adressen, of van en naar hele subnetten, toestaan of weigeren voor een enkel IP-adres.

Wanneer u netwerk beveiligings groepen gebruikt voor netwerk toegangs beheer tussen subnetten, kunt u resources die horen bij dezelfde beveiligings zone of-rol in hun eigen subnets plaatsen.

**Best Practice**: Vermijd kleine virtuele netwerken en subnetten om eenvoud en flexibiliteit te garanderen.   
**Details**: de meeste organisaties voegen meer resources toe dan oorspronkelijk gepland, en het opnieuw toewijzen van adressen is arbeids intensief. Het gebruik van kleine subnetten beperkt een beperkte beveiligings waarde en het toewijzen van een netwerk beveiligings groep aan elk subnet voegt overhead toe. Geef subnetten breed op om ervoor te zorgen dat u flexibiliteit hebt voor de groei.

**Best Practice**: beheer van regel voor netwerk beveiligings groep vereenvoudigen door het definiëren van [toepassings beveiligings groepen](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Details**: Definieer een toepassings beveiligings groep voor lijsten met IP-adressen die u in de toekomst kunt wijzigen of die in veel netwerk beveiligings groepen worden gebruikt. Zorg ervoor dat u de toepassings beveiligings groepen duidelijk beveiligt, zodat anderen hun inhoud en doel kunnen begrijpen.

## <a name="adopt-a-zero-trust-approach"></a>Een vertrouwens benadering van nul aannemen
Netwerken op basis van een perimeter netwerk maken gebruik van de veronderstelling dat alle systemen binnen een Network kunnen worden vertrouwd. Maar de werk nemers van vandaag hebben vanaf elke locatie op verschillende apparaten en apps toegang tot de resources van de organisatie, waardoor de beveiligings maatregelen voor de beveiliging niet relevant zijn. Toegangs beheer beleid dat alleen gericht is op personen die toegang hebben tot een resource, is niet voldoende. Beveiligings beheerders moeten ook *bepalen hoe* een bron wordt geopend om het evenwicht tussen beveiliging en productiviteit te Master te krijgen.

Netwerken moeten zich ontwikkelen van traditionele beveiligingen, omdat netwerken kwetsbaar kunnen zijn voor inbreuken: een aanvaller kan een enkel eind punt binnen de vertrouwde grens doen en vervolgens snel een aanvaller binnen uitvouwen in het hele netwerk. Met een [vertrouwens relatie van nul](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) worden de vertrouwens relatie op basis van de netwerk locatie in een-verbinding overbodig. In plaats daarvan kunnen architecturen met een vertrouwens relatie tussen apparaten en gebruikers worden gebruikt om de toegang tot de gegevens en bronnen van de organisatie te delegeren. Voor nieuwe initiatieven neemt u geen vertrouwens benaderingen op die vertrouwens relatie op het moment van de toegang valideren.

Aanbevolen procedures zijn:

**Best Practice**: Geef voorwaardelijke toegang tot resources op basis van apparaat, identiteit, Assurance, netwerk locatie en meer.  
**Details**: met [voorwaardelijke toegang van Azure AD](../../active-directory/conditional-access/overview.md) kunt u de juiste toegangs controles Toep assen door geautomatiseerde beslissingen voor toegangs beheer te implementeren op basis van de vereiste voor waarden. Zie [toegang tot Azure management beheren met voorwaardelijke toegang](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)voor meer informatie.

**Aanbevolen procedure**: Schakel poort toegang alleen in na workflowgoedkeuring.  
**Details**: u kunt [just-in-time-VM-toegang in azure Security Center](../../security-center/security-center-just-in-time.md) gebruiken om inkomend verkeer naar uw Azure-vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind.

**Best Practice**: verleen tijdelijke machtigingen voor het uitvoeren van geprivilegieerde taken, waarmee wordt voor komen dat kwaadwillende of niet-gemachtigde gebruikers toegang krijgen nadat de machtigingen zijn verlopen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben.  
**Details**: gebruik just-in-time-toegang in azure AD privileged Identity Management of in een oplossing van derden om machtigingen te verlenen voor het uitvoeren van geprivilegieerde taken.

Een vertrouwens relatie van nul is de volgende evolutie in netwerk beveiliging. De status van Cyber aanvallen-organisaties draagt bij aan het nemen van de uitgangspunt-uittredingen, maar deze benadering mag niet worden beperkt. Geen vertrouwde netwerken beveiligen Bedrijfs gegevens en bronnen en zorgen ervoor dat organisaties een moderne werk plek kunnen bouwen door gebruik te maken van technologieën waarmee werk nemers op elk moment en overal en op elke manier productief moeten zijn.

## <a name="control-routing-behavior"></a>Routerings gedrag beheren
Wanneer u een virtuele machine in een virtueel Azure-netwerk plaatst, kan de VM verbinding maken met een wille keurige andere VM in hetzelfde virtuele netwerk, zelfs als de andere Vm's zich op verschillende subnetten bevinden. Dit is mogelijk omdat een verzameling systeem routes die standaard is ingeschakeld, dit type communicatie mogelijk maakt. Met deze standaard routes kunnen Vm's op hetzelfde virtuele netwerk verbindingen met elkaar initiëren en met internet (alleen voor uitgaande communicatie met het Internet).

Hoewel de standaard systeem routes handig zijn voor veel implementatie scenario's, zijn er situaties waarin u de routerings configuratie voor uw implementaties wilt aanpassen. U kunt het adres van de volgende hop configureren om specifieke bestemmingen te bereiken.

U kunt het beste door de [gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) configureren wanneer u een beveiligings apparaat voor een virtueel netwerk implementeert. We praten hierover in een latere sectie met [de titel Beveilig uw essentiële Azure-service resources alleen voor uw virtuele netwerken](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Door de gebruiker gedefinieerde routes zijn niet vereist en de standaard systeem routes werken meestal.
>
>

## <a name="use-virtual-network-appliances"></a>Virtuele netwerk apparaten gebruiken
Netwerk beveiligings groepen en door de gebruiker gedefinieerde route ring kunnen een zekere mate van netwerk beveiliging bieden op het netwerk en de transport lagen van het [OSI-model](https://en.wikipedia.org/wiki/OSI_model). Maar in sommige gevallen wilt u mogelijk de beveiliging op hoge niveaus van de stack inschakelen. In dergelijke situaties wordt u aangeraden virtuele netwerk beveiligings apparaten te implementeren die worden meegeleverd door Azure-partners.

Azure-netwerk beveiligings apparaten kunnen betere beveiliging bieden dan bij welke besturings elementen op netwerk niveau. De netwerk beveiligings mogelijkheden van virtuele netwerk beveiligings apparaten zijn onder andere:


* Firewall
* Inbraak detectie/inbraak preventie
* Beheer van beveiligingsproblemen
* Toepassingsbeheer
* Anomalie detectie op basis van het netwerk
* Webfiltering
* Antivirus
* Botnet-beveiliging

Ga naar [Azure Marketplace](https://azure.microsoft.com/marketplace/) en zoek naar ' Beveiliging ' en ' netwerk beveiliging ' om beschik bare Azure Virtual Network-beveiligings apparaten te vinden.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Perimeter netwerken voor beveiligings zones implementeren
Een [perimeter netwerk](/azure/architecture/vdc/networking-virtual-datacenter) (ook wel DMZ genoemd) is een fysiek of logisch netwerk segment dat een extra beveiligingslaag vormt tussen uw assets en Internet. Gespecialiseerde apparaten voor netwerk toegangs beheer aan de rand van een perimeter netwerk bieden alleen gewenst verkeer in uw virtuele netwerk.

Perimeter netwerken zijn handig omdat u zich kunt richten op het beheer, de bewaking, logboek registratie en rapportage van uw netwerk toegang op de apparaten aan de rand van uw virtuele Azure-netwerk. Een perimeter netwerk is de locatie waar u normaal gesp roken DDoS-preventie (Distributed Denial of service), inbreuk detectie/indringings systemen (ID'S/IP'S), firewall regels en-beleids regels, webfiltering, antimalware van het netwerk, en meer inschakelt. De netwerk beveiligings apparaten bevinden zich tussen internet en uw virtuele Azure-netwerk en hebben een interface op beide netwerken.

Hoewel dit het basis ontwerp is van een perimeter netwerk, zijn er tal van verschillende ontwerpen, zoals back-to-back, Tri-Home en meerdere locaties.

Op basis van het eerder genoemde aantal vertrouwens relaties wordt u aangeraden een perimeter netwerk te gebruiken voor alle hoge beveiligings implementaties om het niveau van netwerk beveiliging en toegangs beheer voor uw Azure-resources te verbeteren. U kunt Azure of een oplossing van derden gebruiken om een extra beveiligingslaag tussen uw assets en Internet te bieden:

- Systeem eigen besturings elementen van Azure. [Azure firewall](../../firewall/overview.md) en de [Web Application firewall in Application Gateway](../../application-gateway/features.md#web-application-firewall) bieden basis beveiliging met een volledig stateful firewall als een service, ingebouwde hoge Beschik baarheid, onbeperkte Cloud schaal baarheid, FQDN-filtering, ondersteuning voor OWASP-kern regel sets en eenvoudige installatie en configuratie.
- Aanbiedingen van derden. Zoek in de [Azure Marketplace](https://azuremarketplace.microsoft.com/) naar de Next Generation firewall (NGFW) en andere aanbiedingen van derden die vertrouwde beveiligings Programma's en een aanzienlijk verbeterd niveau van netwerk beveiliging bieden. De configuratie is mogelijk complexer, maar een aanbieding van derden kan u de mogelijkheid bieden om bestaande mogelijkheden en vaardig heden te gebruiken.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Voorkom bloot stelling aan Internet met specifieke WAN-koppelingen
Veel organisaties hebben de hybride IT-route gekozen. Met hybride IT zijn sommige informatie assets van het bedrijf in Azure en blijven ze on-premises. In veel gevallen worden sommige onderdelen van een service uitgevoerd in azure, terwijl andere onderdelen on-premises blijven.

In een hybride IT-scenario is er meestal een deel van het type cross-premises-connectiviteit. Met cross-premises connectiviteit kan het bedrijf zijn on-premises netwerken verbinden met virtuele Azure-netwerken. Er zijn twee cross-premises connectiviteits oplossingen beschikbaar:

* [Site-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Het is een vertrouwde, betrouw bare en opgezette technologie, maar de verbinding vindt plaats via internet. De band breedte is beperkt tot een maximum van 1,25 Gbps. Site-naar-site-VPN is een wenselijke optie in sommige scenario's.
* **Azure-ExpressRoute**. We raden u aan [ExpressRoute](../../expressroute/expressroute-introduction.md) te gebruiken voor uw cross-premises-connectiviteit. Met ExpressRoute kunt u uw on-premises netwerken uitbreiden naar de Microsoft Cloud via een privéverbinding van een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services zoals Azure, Microsoft 365 en Dynamics 365. ExpressRoute is een specifieke WAN-verbinding tussen uw on-premises locatie of een micro soft Exchange host-provider. Omdat dit een telecommunicatie-verbinding is, worden uw gegevens niet via internet gereisd en worden ze niet blootgesteld aan de potentiële Risico's van Internet communicatie.

De locatie van uw ExpressRoute-verbinding kan invloed hebben op de firewall capaciteit, schaal baarheid, betrouw baarheid en netwerk verkeer. U moet nagaan waar ExpressRoute wordt beëindigd in bestaande netwerken (on-premises). U kunt:

- Beëindig buiten de firewall (het model van het perimeter netwerk) als u inzicht in het verkeer nodig hebt als u een bestaande praktijk van het isoleren van data centers moet voortzetten of als u alleen extranet resources op Azure plaatst.
- Beëindig in de firewall (het paradigma van de netwerk extensie). Dit is de standaard aanbeveling. In alle andere gevallen raden we u aan Azure als een ne Data Center te behandelen.

## <a name="optimize-uptime-and-performance"></a>Uptime en prestaties optimaliseren
Als een service niet beschikbaar is, is er geen toegang tot de gegevens. Als de prestaties zo slecht zijn dat de gegevens niet kunnen worden gebruikt, kunt u overwegen om te voor komen dat de gegevens niet toegankelijk zijn. Vanuit het oogpunt van beveiliging moet u alles doen wat u kunt doen om ervoor te zorgen dat uw services de optimale uptime en prestaties hebben.

Een populaire en efficiënte methode voor het verbeteren van de beschik baarheid en prestaties is taak verdeling. Taak verdeling is een methode voor het distribueren van netwerk verkeer op servers die deel uitmaken van een service. Als u bijvoorbeeld front-end webservers hebt als onderdeel van uw service, kunt u taak verdeling gebruiken om het verkeer te verdelen over meerdere front-endwebservers.

Met deze verdeling van verkeer wordt de beschik baarheid verhoogd omdat een van de webservers niet meer beschikbaar is, de load balancer het verzenden van verkeer naar die server stopt en doorstuurt naar de servers die nog online zijn. Taak verdeling helpt ook de prestaties te verbeteren, omdat de processor, het netwerk en de geheugen overhead voor het uitvoeren van aanvragen worden gedistribueerd op alle servers met gelijke taak verdeling.

U wordt aangeraden de taak verdeling te gebruiken wanneer dat mogelijk is, en wat van toepassing is op uw services. Hieronder vindt u scenario's op het niveau van het virtuele Azure-netwerk en het globale niveau, samen met opties voor taak verdeling voor elk.

**Scenario**: u hebt een toepassing die:

- Vereist dat aanvragen van dezelfde gebruiker/client sessie dezelfde back-end-virtuele machine bereiken. Voor beelden hiervan zijn winkel wagentje-apps en web-mail servers.
- Accepteert alleen een beveiligde verbinding, dus niet-versleutelde communicatie met de server is geen acceptabele optie.
- Vereist dat meerdere HTTP-aanvragen op dezelfde langlopende TCP-verbinding worden gerouteerd of verdeeld over verschillende back-endservers.

**Optie voor taak verdeling**: gebruik [Azure-toepassing gateway](../../application-gateway/overview.md), een HTTP-webverkeer Load Balancer. Application Gateway ondersteunt end-to-end TLS-versleuteling en [TLS-beëindiging](../../application-gateway/overview.md) bij de gateway. Webservers kunnen dan niet worden belast met het versleutelen en ontsleutelen van de overhead en verkeer dat niet is versleuteld naar de back-endservers.

**Scenario**: u moet binnenkomende verbindingen van het Internet verdelen over uw servers in een virtueel Azure-netwerk. Scenario's zijn wanneer u:

- Beschikken over stateless toepassingen die binnenkomende aanvragen van het Internet accepteren.
- Plak sessies of TLS-offload zijn niet vereist. Sticky Sessions is een methode die wordt gebruikt voor de taak verdeling van toepassingen, om server affiniteit te garanderen.

**Optie voor taak verdeling**: gebruik de Azure Portal om [een externe Load Balancer te maken](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) die binnenkomende aanvragen over meerdere vm's verspreidt om een hoger niveau van Beschik baarheid te bieden.

**Scenario**: u moet verbindingen met de virtuele machines die zich niet op internet bevinden, verdelen. In de meeste gevallen worden de verbindingen die zijn geaccepteerd voor taak verdeling, geïnitieerd door apparaten op een virtueel Azure-netwerk, zoals SQL Server instanties of interne webservers.   
**Optie voor taak verdeling**: gebruik de Azure Portal om [een interne Load Balancer te maken](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) die binnenkomende aanvragen verspreidt over meerdere vm's om een hoger niveau van Beschik baarheid te bieden.

**Scenario**: u hebt wereld wijde taak verdeling nodig omdat:

- Beschikken over een Cloud oplossing die veel wordt gedistribueerd over meerdere regio's en waarvoor het hoogste niveau van uptime (Beschik baarheid) mogelijk is.
- U hebt het hoogste niveau van de uptime mogelijk om ervoor te zorgen dat uw service beschikbaar is, zelfs als een volledig Data Center niet meer beschikbaar is.

**Optie voor taak verdeling**: Azure Traffic Manager gebruiken. Traffic Manager maakt het mogelijk om verbindingen met uw services te verdelen op basis van de locatie van de gebruiker.

Als de gebruiker bijvoorbeeld een aanvraag voor uw service van de EU doet, wordt de verbinding omgeleid naar uw services die zich in een EU-Data Center bevinden. Dit deel van Traffic Manager globale taak verdeling helpt de prestaties te verbeteren omdat verbinding wordt gemaakt met het dichtstbijzijnde Data Center sneller dan verbinding maken met data centers die ver weg zijn.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>RDP/SSH-toegang tot virtuele machines uitschakelen
Het is mogelijk om Azure virtual machines te bereiken met behulp van [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) en het SSH-protocol ( [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) ). Deze protocollen maken het beheer van VM's vanaf externe locaties mogelijk en worden standaard gebruikt in datacenters.

Het potentiële beveiligings probleem met het gebruik van deze protocollen via internet is dat aanvallers schadelijke [technieken kunnen](https://en.wikipedia.org/wiki/Brute-force_attack) gebruiken om toegang te krijgen tot virtuele machines van Azure. Nadat de aanvallers toegang hebben verkregen, kunnen ze uw VM gebruiken als een startpunt voor aanvallen op andere computers in uw virtuele netwerk of zelfs voor aanvallen op netwerkapparaten buiten Azure.

U wordt aangeraden directe RDP-en SSH-toegang tot uw Azure virtual machines via internet uit te scha kelen. Nadat directe RDP-en SSH-toegang via internet is uitgeschakeld, hebt u andere opties die u kunt gebruiken om toegang te krijgen tot deze Vm's voor extern beheer.

**Scenario**: een enkele gebruiker in staat stellen om via Internet verbinding te maken met een virtueel Azure-netwerk.   
**Optie**: [punt-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) is een andere term voor een VPN-client/server-verbinding voor externe toegang. Nadat de punt-naar-site-verbinding tot stand is gebracht, kan de gebruiker RDP of SSH gebruiken om verbinding te maken met virtuele machines die zich bevinden in het virtuele Azure-netwerk waarmee de gebruiker verbinding maakt via een punt-naar-site-VPN. Hierbij wordt ervan uitgegaan dat de gebruiker gemachtigd is om deze Vm's te bereiken.

Punt-naar-site-VPN is veiliger dan directe RDP-of SSH-verbindingen omdat de gebruiker twee keer moet verifiëren voordat er verbinding wordt gemaakt met een virtuele machine. Eerst moet de gebruiker worden geverifieerd (en gemachtigd) om een punt-naar-site-VPN-verbinding tot stand te brengen. Ten tweede moet de gebruiker worden geverifieerd (en gemachtigd) om de RDP-of SSH-sessie tot stand te brengen.

**Scenario**: Hiermee kunnen gebruikers in uw on-premises netwerk verbinding maken met virtuele machines op uw virtuele Azure-netwerk.   
**Optie**: een [site-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) verbindt een volledig netwerk met een ander netwerk via internet. U kunt een site-naar-site-VPN gebruiken om uw on-premises netwerk te verbinden met een virtueel Azure-netwerk. Gebruikers in uw on-premises netwerk verbinden met behulp van het RDP-of SSH-protocol via de site-naar-site-VPN-verbinding. U hoeft geen directe RDP-of SSH-toegang via Internet toe te staan.

**Scenario**: gebruik een specifieke WAN-koppeling om functionaliteit te bieden die vergelijkbaar is met de site-naar-site-VPN.   
**Optie**: gebruik [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Het biedt vergelijk bare functionaliteit als het site-naar-site-VPN. Dit zijn de belangrijkste verschillen:

- De speciale WAN-verbinding kan niet worden gepasseren op internet.
- Speciale WAN-koppelingen zijn doorgaans stabieler en beter.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Beveilig uw essentiële Azure-service resources alleen voor uw virtuele netwerken
Gebruik de persoonlijke Azure-koppeling om toegang te krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL Database) via een persoonlijk eind punt in uw virtuele netwerk. Met persoonlijke eind punten kunt u uw kritieke Azure-service resources alleen op uw virtuele netwerken beveiligen. Verkeer van uw virtuele netwerk naar de Azure-service blijft altijd op het Microsoft Azure backbone-netwerk. Het is niet meer nodig om Azure PaaS services te gebruiken om uw virtuele netwerk beschikbaar te maken voor het open bare Internet. 

Persoonlijke Azure-koppeling bieden de volgende voor delen:
- **Verbeterde beveiliging voor uw Azure-service resources**: met Azure private link kunnen Azure-service resources worden beveiligd met uw virtuele netwerk met behulp van een persoonlijk eind punt. Het beveiligen van service resources naar een persoonlijk eind punt in een virtueel netwerk biedt een betere beveiliging door het volledig verwijderen van open bare Internet toegang tot bronnen en het toestaan van verkeer alleen vanuit een privé-eind punt in uw virtuele netwerk.
- **Persoonlijke toegang tot Azure-service resources op het Azure-platform**: Verbind uw virtuele netwerk met Services in azure met behulp van privé-eind punten. Er is geen openbaar IP-adres nodig. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk.
- **Toegang vanaf on-premises en peered netwerken**: Access Services die worden uitgevoerd in azure vanuit on-premises via ExpressRoute persoonlijke peering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eind punten. U hoeft geen ExpressRoute Microsoft-peering in te stellen of door het internet te gaan om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken.
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met Services in regio B.
- **Eenvoudig in te stellen en te beheren**: u hebt geen gereserveerde open bare IP-adressen meer nodig in uw virtuele netwerken om Azure-resources te beveiligen via een IP-firewall. Er zijn geen NAT-of gateway apparaten vereist voor het instellen van de persoonlijke eind punten. Privé-eind punten worden geconfigureerd via een eenvoudige werk stroom. Aan de kant van de service kunt u ook de verbindings aanvragen op uw Azure-service resource met gemak beheren. Persoonlijke Azure-koppeling werkt ook voor consumenten en services die deel uitmaken van verschillende Azure Active Directory tenants. 
    
Zie [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md)voor meer informatie over persoonlijke eind punten en de Azure-Services en-regio's waarvoor privé-eind punten beschikbaar zijn.


## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.
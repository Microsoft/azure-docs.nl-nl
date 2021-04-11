---
title: Veelgestelde vragen-Azure ExpressRoute | Microsoft Docs
description: De veelgestelde vragen over ExpressRoute bevatten informatie over ondersteunde Azure-Services, kosten, gegevens en verbindingen, SLA, providers en locaties, band breedte en andere technische informatie.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: efa5c3192ca6f51c219cc308a776e6db7212103c
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552237"
---
# <a name="expressroute-faq"></a>Veelgestelde vragen ExpressRoute

## <a name="what-is-expressroute"></a>Wat is ExpressRoute?

ExpressRoute is een Azure-service waarmee u particuliere verbindingen kunt maken tussen micro soft-data centers en infra structuur op uw locatie of in een functie voor samen locatie. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden betere beveiliging, betrouw baarheid en snelheden met lagere latenties dan typische verbindingen via internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Wat zijn de voor delen van het gebruik van ExpressRoute-en particuliere netwerk verbindingen?

ExpressRoute-verbindingen gaan niet via het openbare internet. Ze bieden betere beveiliging, betrouw baarheid en snelheden, met lagere en consistente latenties dan typische verbindingen via internet. In sommige gevallen kan het gebruik van ExpressRoute-verbindingen voor het overdragen van gegevens tussen on-premises apparaten en Azure aanzienlijke kosten voordelen opleveren.

### <a name="where-is-the-service-available"></a>Waar is de service beschikbaar?

Zie deze pagina voor service locatie en beschik baarheid: [ExpressRoute partners en locaties](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hoe kan ik ExpressRoute gebruiken om verbinding te maken met micro soft als ik geen partnerschappen met een van de ExpressRoute-vervoerders partners heb?

U kunt een regionale transporteur en land-Ethernet-verbindingen selecteren op een van de ondersteunde Exchange-provider locaties. Vervolgens kunt u met micro soft een peer op de locatie van de provider. Raadpleeg de laatste sectie van [ExpressRoute-partners en-locaties](expressroute-locations.md) om te zien of uw service provider aanwezig is op een van de Exchange-locaties. Vervolgens kunt u een ExpressRoute-circuit best Ellen via de service provider om verbinding te maken met Azure.

### <a name="how-much-does-expressroute-cost"></a>Wat kost ExpressRoute?

Bekijk de [prijs](https://azure.microsoft.com/pricing/details/expressroute/) informatie voor prijs gegevens.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Als ik betaal voor een ExpressRoute-circuit van een bepaalde band breedte, heb ik dan deze band breedte toegewezen aan binnenkomend en uitgaand verkeer afzonderlijk?

Ja, de band breedte van het ExpressRoute-circuit is duplex. Als u bijvoorbeeld een ExpressRoute-circuit van 200 Mbps aanschaft, kunt u 200 Mbps voor binnenkomend verkeer en 200 Mbps voor uitgaand verkeer aanschaffen.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Als ik betaal voor een ExpressRoute-circuit van een bepaalde band breedte, moet de particuliere verbinding die ik aanschaf van mijn netwerk serviceprovider dezelfde snelheid hebben?

Nee. U kunt een particuliere verbinding aanschaffen met elke snelheid van uw service provider. Uw verbinding met Azure is echter beperkt tot de band breedte van het ExpressRoute-circuit dat u aanschaft.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Als ik voor een ExpressRoute-circuit van een bepaalde band breedte betaal, heb ik dan de mogelijkheid om meer dan mijn beschik bare band breedte te gebruiken?

Ja, u kunt Maxi maal twee keer de bandbreedte limiet gebruiken die u hebt aangeschaft met behulp van de band breedte die beschikbaar is op de secundaire verbinding van uw ExpressRoute-circuit. De ingebouwde redundantie van uw circuit wordt geconfigureerd met behulp van primaire en secundaire verbindingen, elk van de aangeschafte band breedte, op twee micro soft Enter prise Edge-routers (Msee's). De band breedte die beschikbaar is via uw secundaire verbinding, kan zo nodig worden gebruikt voor extra verkeer. Omdat de secundaire verbinding is bedoeld voor redundantie, is deze echter niet gegarandeerd en mag niet worden gebruikt voor extra verkeer gedurende een langere periode. Zie voor meer informatie over het gebruik van beide verbindingen voor het verzenden van verkeer [gebruiken als pad voor in behandeling](./expressroute-optimize-routing.md#solution-use-as-path-prepending).

Als u van plan bent om alleen de primaire verbinding te gebruiken om verkeer te verzenden, wordt de band breedte van de verbinding vast en wordt geprobeerd het abonnement te verbreken, waardoor het pakket langer wordt. Als verkeer via een ExpressRoute-gateway loopt, is de band breedte voor de gateway-SKU vast en niet bursteel. Zie [over ExpressRoute virtuele netwerk gateways](expressroute-about-virtual-network-gateways.md#aggthroughput)voor de band breedte van elke gateway-SKU.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan ik dezelfde particuliere netwerk verbinding gebruiken met het virtuele netwerk en andere Azure-Services tegelijk?

Ja. Wanneer u een ExpressRoute-circuit hebt ingesteld, kunt u toegang krijgen tot services binnen een virtueel netwerk en andere Azure-Services tegelijk. U maakt verbinding met virtuele netwerken via het pad van de persoonlijke peer en naar andere services via het micro soft-peering-pad.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hoe worden VNets geadverteerd op ExpressRoute-privé-peering?

De ExpressRoute-gateway adverteert de *adres ruimte (n)* van het Azure VNet, dat u niet kunt opnemen/uitsluiten op het niveau van het subnet. Het is altijd de VNet-adres ruimte die wordt geadverteerd. Als VNet-peering wordt gebruikt en de peered VNet is ingeschakeld, wordt ook de adres ruimte van het gekoppelde VNet geadverteerd.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hoeveel voor voegsels kunnen worden geadverteerd van een VNet naar on-premises op ExpressRoute privé-peering?

Er zijn Maxi maal 1000 voor voegsels die worden geadverteerd op één ExpressRoute-verbinding of via VNet-peering met behulp van gateway-door voer. Als u bijvoorbeeld 999 adres ruimten hebt op één VNet dat is verbonden met een ExpressRoute-circuit, worden alle 999 van die voor voegsels op locatie geadverteerd. Als u een VNet hebt ingeschakeld om Gateway-overdracht toe te staan met 1 adres ruimte en 500 spaken VNets ingeschakeld met de optie externe gateway toestaan, adverteert het VNet dat is geïmplementeerd met de gateway 501 voor voegsels aan on-premises.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Wat gebeurt er als ik de limiet voor het voor voegsel voor een ExpressRoute-verbinding overschrijd?

De verbinding tussen het ExpressRoute-circuit en de gateway (en de peered VNets met behulp van gateway-door Voer, indien van toepassing) gaat verder. Er wordt opnieuw ingesteld wanneer de limiet voor het voor voegsel niet langer wordt overschreden.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kan ik routes filteren die afkomstig zijn van mijn on-premises netwerk?

De enige manier voor het filteren/insluiten van routes bevindt zich op de on-premises edge-router. Door de gebruiker gedefinieerde routes kunnen in het VNet worden toegevoegd om de specifieke route ring te beïnvloeden, maar dit is statisch en maakt geen deel uit van de BGP-advertisement.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Biedt ExpressRoute een Service Level Agreement (SLA)?

Zie de pagina [EXPRESSROUTE Sla](https://azure.microsoft.com/support/legal/sla/) voor meer informatie.

## <a name="supported-services"></a>Ondersteunde services

ExpressRoute ondersteunt [drie routerings domeinen](expressroute-circuit-peerings.md) voor diverse typen services: persoonlijke peering, micro soft-peering en open bare peering (afgeschaft).

### <a name="private-peering"></a>Persoonlijke peering

**Geboden**

* Virtuele netwerken, inclusief alle virtuele machines en Cloud Services

### <a name="microsoft-peering"></a>Microsoft-peering

Als uw ExpressRoute-circuit is ingeschakeld voor Azure micro soft-peering, kunt u toegang krijgen tot de [open bare IP-](../virtual-network/public-ip-addresses.md#public-ip-addresses) adresbereiken die worden gebruikt in azure via het circuit. Azure micro soft-peering biedt toegang tot services die momenteel worden gehost op Azure (met geografische beperkingen, afhankelijk van de SKU van uw circuit). Als u de beschik baarheid voor een specifieke service wilt valideren, kunt u de documentatie voor die service controleren om te zien of er een gereserveerd bereik voor die service is gepubliceerd. Zoek vervolgens de IP-bereiken van de doel service op en vergelijk met de bereiken die worden vermeld in het [Azure IP-bereik en de service Tags – open bare Cloud XML-bestand](https://www.microsoft.com/download/details.aspx?id=56519). U kunt ook een ondersteunings ticket voor de betreffende service openen ter verduidelijking.

**Geboden**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI-beschikbaar via een regionale community van Azure, Zie [hier](/power-bi/service-admin-where-is-my-tenant-located) voor meer informatie over de regio van uw Power bi Tenant.
* Azure Active Directory
* [Azure-DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services Community)
* Open bare IP-adressen van Azure voor IaaS (Virtual Machines, Virtual Network gateways, load balancers, enz.)  
* De meeste andere Azure-Services worden ook ondersteund. Neem rechtstreeks contact op met de service die u wilt gebruiken om de ondersteuning te controleren.

**Niet ondersteund:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-factor Authentication-Server (verouderd)
* Traffic Manager
* Logic Apps

### <a name="public-peering"></a>Openbare peering

Open bare peering is uitgeschakeld op nieuwe ExpressRoute-circuits. Azure-Services zijn nu beschikbaar op micro soft-peering. Als u een circuit hebt gemaakt voordat open bare peering werd afgeschaft, kunt u kiezen voor het gebruik van micro soft-peering of open bare peering, afhankelijk van de services die u wilt gebruiken.

Zie [ExpressRoute Public peering](about-public-peering.md)(Engelstalig) voor meer informatie over en configuratie stappen voor open bare peering.

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Waarom zie ' geadverteerde open bare voor voegsels ' als ' validatie vereist ' tijdens het configureren van micro soft-peering?

Microsoft controleert of de opgegeven geadverteerde openbare voorvoegsels en peer-ASN (of klant-ASN) aan u zijn toegewezen in het IRR (Internet Routing Registry). Als u de openbare voorvoegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routeringsregister, wordt de automatische validatie niet voltooid en is handmatige validatie vereist. Als de automatische validatie mislukt, wordt het bericht Validatie vereist weergegeven.

Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voor voegsels in het routerings register en verzendt deze documenten voor hand matige validatie door een ondersteunings ticket te openen, zoals hieronder wordt weer gegeven.

![Scherm opname met een nieuwe ondersteunings aanvraag (ondersteunings ticket) voor ' eigendoms bewijs voor open bare voor voegsels '.](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Wordt Dynamics 365 ondersteund op ExpressRoute?

De omgevingen Dynamics 365 en Common Data Service (CDS) worden gehost op Azure en daarom kunnen klanten profiteren van de onderliggende ExpressRoute-ondersteuning voor Azure-resources. U kunt verbinding maken met de service-eind punten als uw router filter de Azure-regio's bevat waarin uw Dynamics 365/CDS-omgevingen worden gehost.

> [!NOTE]
> [ExpressRoute Premium](#expressroute-premium) is **niet** vereist voor Dynamics 365-connectiviteit via Azure ExpressRoute als het ExpressRoute-circuit in dezelfde [geopolitieke regio](./expressroute-locations-providers.md#expressroute-locations)wordt geïmplementeerd.

## <a name="data-and-connections"></a>Gegevens en verbindingen

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Gelden er limieten voor de hoeveelheid gegevens die ik kan overdragen met behulp van ExpressRoute?

Er is geen limiet ingesteld voor de hoeveelheid gegevens overdracht. Raadpleeg de [prijs informatie](https://azure.microsoft.com/pricing/details/expressroute/) voor informatie over de bandbreedte tarieven.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Welke verbindings snelheden worden ondersteund door ExpressRoute?

Ondersteunde aanbiedingen voor band breedte:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Welke service providers zijn er beschikbaar?

Zie [ExpressRoute-partners en-locaties](expressroute-locations.md) voor de lijst met service providers en locaties.

## <a name="technical-details"></a>Technische details

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Wat zijn de technische vereisten voor het verbinden van mijn on-premises locatie met Azure?

Zie de [pagina met ExpressRoute-vereisten](expressroute-prerequisites.md) voor vereisten.

### <a name="are-connections-to-expressroute-redundant"></a>Zijn er verbindingen met ExpressRoute redundant?

Ja. Elk ExpressRoute-circuit heeft een redundant paar Kruis verbindingen geconfigureerd om hoge Beschik baarheid te bieden.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Gaat de verbinding verloren als een van mijn ExpressRoute-koppelingen mislukt?

Als een van de cross Connections mislukt, wordt de verbinding niet verbroken. Er is een redundante verbinding beschikbaar ter ondersteuning van de belasting van uw netwerk en biedt een hoge Beschik baarheid van uw ExpressRoute-circuit. U kunt ook een circuit maken op een andere locatie voor peering om tolerantie op circuit niveau te krijgen.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hoe kan ik de redundantie voor privé-peering implementeren?

Meerdere ExpressRoute-circuits van verschillende peering locaties of Maxi maal vier verbindingen vanaf dezelfde peering-locatie kunnen worden aangesloten op hetzelfde virtuele netwerk om hoge Beschik baarheid te bieden in het geval dat één circuit niet beschikbaar is. U kunt vervolgens [hogere gewichten toewijzen](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection) aan een van de lokale verbindingen om de voor keur aan een specifiek circuit te geven. Het wordt ten zeerste aanbevolen om ten minste twee ExpressRoute-circuits in te stellen om afzonderlijke storings punten te voor komen. 

Bekijk [hier](./designing-for-high-availability-with-expressroute.md) wat u kunt ontwerpen voor hoge Beschik baarheid en dat u [hier](./designing-for-disaster-recovery-with-expressroute-privatepeering.md) kunt ontwerpen voor herstel na nood gevallen.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hoe kan ik redundantie implementeren op micro soft-peering?

Het wordt ten zeerste aanbevolen wanneer klanten micro soft-peering gebruiken om toegang te krijgen tot open bare Azure-Services, zoals Azure Storage of Azure SQL, en klanten die gebruikmaken van micro soft-peering voor Microsoft 365 dat ze meerdere circuits op verschillende peering-locaties implementeren om afzonderlijke storings punten te voor komen. Klanten kunnen hetzelfde voor voegsel op beide circuits adverteren en in [afwachting](./expressroute-optimize-routing.md#solution-use-as-path-prepending) van het pad gebruiken, of verschillende voor voegsels adverteren om het pad van on-premises te bepalen.

Bekijk [hier](./designing-for-high-availability-with-expressroute.md) wat u kunt ontwerpen voor hoge Beschik baarheid.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hoe kan ik zorgen voor hoge Beschik baarheid in een virtueel netwerk dat is verbonden met ExpressRoute?

U kunt hoge Beschik baarheid bezorgen door Maxi maal 16 ExpressRoute-circuits op dezelfde peering-locatie aan uw virtuele netwerk te koppelen, of door ExpressRoute-circuits op verschillende peering locaties (bijvoorbeeld Singapore, Singapore2) te verbinden met het virtuele netwerk. Als een ExpressRoute-circuit wordt uitgeschakeld, wordt er een failover voor de connectiviteit uitgevoerd naar een ander ExpressRoute-circuit. Standaard wordt het verkeer dat uw virtuele netwerk verlaat, doorgestuurd op basis van een gelijke prijs routering met meerdere paden (ECMP). U kunt het verbindings gewicht gebruiken om de voor keur aan een circuit te geven. Zie optimalisatie van ExpressRoute- [route ring](expressroute-optimize-routing.md)voor meer informatie.

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hoe kan ik ervoor te zorgen dat mijn verkeer dat is bestemd voor open bare Azure-Services, zoals Azure Storage en Azure SQL op micro soft-peering of open bare peering, de voor keur heeft op het ExpressRoute-pad?

U moet het *lokale voorkeurs* kenmerk op uw router (s) implementeren om ervoor te zorgen dat het pad van on-premises naar Azure altijd de voor keur heeft voor uw ExpressRoute-circuit (s).

Zie [hier](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings) voor meer informatie over het selecteren van BGP-paden en algemene router configuraties. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Als ik niet op de kant ben van een Cloud uitwisseling en mijn service provider een Point-to-point-verbinding biedt, moet ik dan twee fysieke verbindingen tussen mijn on-premises netwerk en micro soft best Ellen?

Als uw service provider twee virtuele Ethernet-circuits kan maken via de fysieke verbinding, hebt u slechts één fysieke verbinding nodig. De fysieke verbinding (bijvoorbeeld een optisch glas) wordt beëindigd op een apparaat met laag 1 (L1) (Zie de afbeelding). De twee virtuele Ethernet-circuits zijn gelabeld met verschillende VLAN-Id's, een voor het primaire circuit en een voor de secundaire. Deze VLAN-Id's bevinden zich in de buitenste 802.1 Q Ethernet-header. De interne 802.1 Q-Ethernet-header (niet weer gegeven) wordt toegewezen aan een specifiek [ExpressRoute-routerings domein](expressroute-circuit-peerings.md).

![Diagram waarin de primaire en secundaire virtuele circuits van laag 1 (L1) worden gemarkeerd die de fysieke verbinding vormen tussen de switches op een klant site en een ExpressRoute locatie.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan ik een van mijn VLAN'S uitbreiden naar Azure met behulp van ExpressRoute?

Nee. Laag 2-connectiviteits extensies worden niet ondersteund in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan ik meer dan één ExpressRoute-circuit in mijn abonnement hebben?

Ja. U kunt meer dan één ExpressRoute-circuit in uw abonnement hebben. De standaard limiet is ingesteld op 10. Als dat nodig is, kunt u contact opnemen met Microsoft Ondersteuning om de limiet te verhogen.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan ik ExpressRoute-circuits van verschillende service providers hebben?

Ja. U kunt ExpressRoute-circuits met veel service providers hebben. Elk ExpressRoute-circuit is alleen gekoppeld aan een service provider. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Ik zie twee ExpressRoute-peering locaties in dezelfde metro, bijvoorbeeld Singapore en Singapore2. Welke peering-locatie moet ik kiezen om mijn ExpressRoute-circuit te maken?
Als uw service provider ExpressRoute op beide sites biedt, kunt u samen werken met uw provider en een van beide locaties kiezen om ExpressRoute in te stellen. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan ik meerdere ExpressRoute-circuits hebben in dezelfde metro lijn? Kan ik deze koppelen aan hetzelfde virtuele netwerk?

Ja. U kunt meerdere ExpressRoute-circuits met dezelfde of verschillende service providers hebben. Als de metro meerdere ExpressRoute-peering locaties heeft en de circuits op verschillende peering-locaties worden gemaakt, kunt u deze koppelen aan hetzelfde virtuele netwerk. Als de circuits op dezelfde peering-locatie worden gemaakt, kunt u Maxi maal vier circuits koppelen aan hetzelfde virtuele netwerk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hoe kan ik mijn virtuele netwerken met een ExpressRoute-circuit verbinden?

De basis stappen zijn:

* Stel een ExpressRoute-circuit in en zorg ervoor dat de service provider deze functie inschakelt.
* U, of de provider, moet de BGP-peering (s) configureren.
* Koppel het virtuele netwerk aan het ExpressRoute-circuit.

Zie [ExpressRoute-werk stromen voor circuit inrichting en circuit statussen](expressroute-workflows.md)voor meer informatie.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Zijn er connectiviteits grenzen voor mijn ExpressRoute-circuit?

Ja. Het artikel [ExpressRoute partners en locaties](expressroute-locations.md) bevat een overzicht van de connectiviteits grenzen voor een ExpressRoute-circuit. Connectiviteit voor een ExpressRoute-circuit is beperkt tot één geopolitieke regio. Connectiviteit kan worden uitgebreid naar geopolitieke regio's door de functie ExpressRoute Premium in te scha kelen.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan ik aan meer dan één virtueel netwerk koppelen aan een ExpressRoute-circuit?

Ja. U kunt Maxi maal 10 virtuele netwerken verbindingen hebben met een standaard ExpressRoute-circuit en Maxi maal 100 op een [Premium ExpressRoute-circuit](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Ik heb meerdere Azure-abonnementen die virtuele netwerken bevatten. Kan ik virtuele netwerken die zich in afzonderlijke abonnementen bevinden, verbinden met een enkel ExpressRoute-circuit?

Ja. U kunt Maxi maal 10 virtuele netwerken in hetzelfde abonnement als het circuit of andere abonnementen koppelen met een enkel ExpressRoute-circuit. Deze limiet kan worden verhoogd door de functie ExpressRoute Premium in te scha kelen. Houd er rekening mee dat de kosten voor connectiviteit en band breedte voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. alle virtuele netwerken delen dezelfde band breedte.

Zie [een ExpressRoute-circuit delen over meerdere abonnementen](expressroute-howto-linkvnet-arm.md)voor meer informatie.

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Ik heb meerdere Azure-abonnementen gekoppeld aan verschillende Azure Active Directory tenants of Enterprise Overeenkomst inschrijvingen. Kan ik virtuele netwerken die zich in afzonderlijke tenants en registraties bevinden, verbinden met een enkel ExpressRoute-circuit dat zich niet in dezelfde Tenant of inschrijving bevindt?

Ja. ExpressRoute-autorisaties kunnen abonnementen, tenants en inschrijvings grenzen hebben, zonder dat hiervoor aanvullende configuratie is vereist. Houd er rekening mee dat de kosten voor connectiviteit en band breedte voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. alle virtuele netwerken delen dezelfde band breedte.

Zie [een ExpressRoute-circuit delen over meerdere abonnementen](expressroute-howto-linkvnet-arm.md)voor meer informatie.

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Zijn er virtuele netwerken die zijn verbonden met hetzelfde circuit geïsoleerd van elkaar?

Nee. Vanuit een bewerkings perspectief worden alle virtuele netwerken die zijn gekoppeld aan hetzelfde ExpressRoute-circuit deel uitmaken van hetzelfde routerings domein en zijn ze niet van elkaar geïsoleerd. Als u isolatie van de route nodig hebt, moet u een apart ExpressRoute-circuit maken.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan ik een virtueel netwerk hebben verbonden met meer dan één ExpressRoute-circuit?

Ja. U kunt één virtueel netwerk met Maxi maal vier ExpressRoute-circuits koppelen aan dezelfde of een andere peering-locatie. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan ik toegang krijgen tot internet vanuit mijn virtuele netwerken die zijn verbonden met ExpressRoute-circuits?

Ja. Als u geen standaard routes (0.0.0.0/0) of Internet route voorvoegsels via de BGP-sessie hebt geadverteerd, kunt u verbinding maken met Internet vanuit een virtueel netwerk dat is gekoppeld aan een ExpressRoute-circuit.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan ik Internet verbinding blok keren met virtuele netwerken die zijn verbonden met ExpressRoute-circuits?

Ja. U kunt standaard routes (0.0.0.0/0) adverteren om alle Internet connectiviteit met virtuele machines die binnen een virtueel netwerk zijn geïmplementeerd, te blok keren en alle verkeer via het ExpressRoute-circuit te routeren.

> [!NOTE]
> Als de geadverteerde route van 0.0.0.0/0 wordt ingetrokken vanuit de routes die worden geadverteerd (bijvoorbeeld vanwege een storing of een onjuiste configuratie), biedt Azure een [systeem route](../virtual-network/virtual-networks-udr-overview.md#system-routes) naar resources op de verbonden Virtual Network om verbinding te maken met internet.  Om ervoor te zorgen dat uitgaand verkeer naar Internet wordt geblokkeerd, wordt u aangeraden een netwerk beveiligings groep te plaatsen op alle subnetten met een uitgaande regel voor weigeren voor Internet verkeer.

Als u standaard routes adverteert, forceren we het verkeer naar services die worden aangeboden via micro soft-peering (zoals Azure Storage en SQL data base) terug naar uw eigen locatie. U moet uw routers configureren om verkeer naar Azure te retour neren via het pad naar micro soft-peering of via internet. Als u een service-eind punt voor de service hebt ingeschakeld, wordt het verkeer naar de service niet naar uw locatie afgedwongen. Het verkeer blijft binnen het Azure-backbone-netwerk. Zie [service-eind punten voor virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json) voor meer informatie over service-eind punten

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Kunnen virtuele netwerken die zijn gekoppeld aan hetzelfde ExpressRoute-circuit met elkaar communiceren?

Ja. Virtuele machines die zijn geïmplementeerd in virtuele netwerken die zijn verbonden met hetzelfde ExpressRoute-circuit kunnen met elkaar communiceren. U kunt het beste de [peering van het virtuele netwerk](../virtual-network/virtual-network-peering-overview.md) instellen om deze communicatie te vergemakkelijken.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>Kan ik een site-naar-site-VPN-verbinding met mijn virtuele netwerk instellen in combi natie met ExpressRoute?

Ja. ExpressRoute kan naast site-naar-site-Vpn's bestaan. Zie [ExpressRoute-en site-to-site-verbindingen configureren](expressroute-howto-coexist-resource-manager.md).

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Hoe kan ik route ring tussen mijn site-naar-site VPN-verbinding en mijn ExpressRoute inschakelen?

Als u route ring wilt inschakelen tussen uw vertakking die is verbonden met ExpressRoute en uw vertakking is verbonden met een site-naar-site-VPN-verbinding, moet u [Azure route server](../route-server/expressroute-vpn-support.md)instellen.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Waarom is er een openbaar IP-adres gekoppeld aan de ExpressRoute-gateway in een virtueel netwerk?

Het open bare IP-adres wordt alleen gebruikt voor intern beheer en vormt geen beveiligings risico op uw virtuele netwerk.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Gelden er limieten voor het aantal routes dat ik kan adverteren?

Ja. We accepteren Maxi maal 4000 route voorvoegsels voor privé-peering en 200 voor micro soft-peering. U kunt dit verhogen tot 10.000-routes voor privé-peering als u de ExpressRoute Premium-functie inschakelt.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Zijn er beperkingen op IP-adresbereiken die ik via de BGP-sessie kan adverteren?

We accepteren geen persoonlijke voor voegsels (RFC1918) voor de micro soft-peering BGP-sessie. We accepteren elke voorvoegsel grootte (Maxi maal/32) op zowel micro soft als de persoonlijke peering.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Wat gebeurt er als de BGP-limieten worden overschreden?

BGP-sessies worden verwijderd. Deze worden opnieuw ingesteld zodra het aantal voor voegsels onder de limiet komt.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Wat is de ExpressRoute BGP Hold-tijd? Kan deze worden aangepast?

De wacht tijd is 180. De keep-alive-berichten worden elke 60 seconden verzonden. Dit zijn de vaste instellingen van de micro soft-kant die niet kunnen worden gewijzigd. Het is mogelijk om verschillende timers te configureren en de BGP-sessie parameters worden dienovereenkomstig onderhandeld.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan ik de band breedte van een ExpressRoute-circuit wijzigen?

Ja, u kunt proberen de band breedte van uw ExpressRoute-circuit te verg Roten in de Azure Portal of door Power shell te gebruiken. Als er capaciteit beschikbaar is op de fysieke poort waarop het circuit is gemaakt, is uw wijziging geslaagd. 

Als uw wijziging mislukt, betekent dit dat er onvoldoende capaciteit is op de huidige poort en dat u een nieuw ExpressRoute-circuit met de hogere band breedte moet maken, of dat er geen extra capaciteit is op die locatie. in dat geval kunt u de band breedte niet verhogen. 

U moet ook uw connectiviteits provider opvolgen om ervoor te zorgen dat ze de versnellingen in hun netwerken bijwerken om de bandbreedte verhoging te ondersteunen. U kunt de band breedte van uw ExpressRoute-circuit echter niet verlagen. U moet een nieuw ExpressRoute-circuit met lagere band breedte maken en het oude circuit verwijderen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hoe kan ik de band breedte van een ExpressRoute-circuit wijzigen?

U kunt de band breedte van het ExpressRoute-circuit bijwerken met behulp van de REST API-of Power shell-cmdlet.

### <a name="i-received-a-notification-about-maintenance-on-my-expressroute-circuit-what-is-the-technical-impact-of-this-maintenance"></a>Ik heb een melding ontvangen over onderhoud op mijn ExpressRoute-circuit. Wat is de technische impact van dit onderhoud?

Als u uw circuit in de [modus actief-actief gebruikt,](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute#active-active-connections)moet u Mini maal profiteren van het gemak. We voeren onderhoud uit op de primaire en secundaire verbindingen van uw circuit afzonderlijk. Gepland onderhoud wordt doorgaans buiten kantoor uren uitgevoerd in de tijd zone van de locatie van de peering en u kunt geen onderhouds tijd selecteren.

### <a name="i-received-a-notification-about-a-software-upgrade-or-maintenance-on-my-expressroute-gateway-what-is-the-technical-impact-of-this-maintenance"></a>Ik heb een melding ontvangen over een software-upgrade of onderhoud op mijn ExpressRoute-gateway. Wat is de technische impact van dit onderhoud?

U moet mini maal profiteren van een software-upgrade of het onderhoud van uw gateway. De ExpressRoute-gateway bestaat uit meerdere instanties en tijdens upgrades worden instanties een voor een tegelijk offline genomen. Dit kan ertoe leiden dat uw gateway de onderlaagde netwerk doorvoer tijdelijk ondersteunt naar het virtuele netwerk. de gateway zelf heeft geen uitval tijd.


## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Wat is ExpressRoute Premium?

ExpressRoute Premium is een verzameling van de volgende functies:

* Verhoogde routerings tabel limiet van 4000 routes naar 10.000 routes voor persoonlijke peering.
* Het aantal VNets-en ExpressRoute-Global Reach verbindingen dat op een ExpressRoute-circuit kan worden ingeschakeld (de standaard waarde is 10). Zie de tabel met ExpressRoute- [limieten](#limits) voor meer informatie.
* Connectiviteit met Microsoft 365
* Wereld wijde connectiviteit via het micro soft-basis netwerk. U kunt nu een VNet in een geopolitieke regio koppelen met een ExpressRoute-circuit in een andere regio.<br>
    **Voorbeelden:**

    *  U kunt een VNet dat is gemaakt in Europa West koppelen aan een ExpressRoute-circuit dat is gemaakt in silicone dal. 
    *  Op de micro soft-peering worden voor voegsels van andere geopolitieke regio's geadverteerd, zodat u verbinding kunt maken met bijvoorbeeld SQL Azure in Europa West vanuit een circuit in silicone dal.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hoeveel VNets-en ExpressRoute-Global Reach verbindingen kan ik inschakelen op een ExpressRoute-circuit als ik ExpressRoute Premium inschakel?

In de volgende tabellen worden de limieten voor ExpressRoute en het aantal VNets-en Global Reach ExpressRoute-verbindingen per ExpressRoute-circuit weer gegeven:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hoe kan ik ExpressRoute Premium inschakelen?

De Premium-functies van ExpressRoute kunnen worden ingeschakeld wanneer de functie is ingeschakeld en kan worden afgesloten door de status van het circuit bij te werken. U kunt ExpressRoute Premium inschakelen tijdens het maken van een circuit, of u kunt de REST API/Power shell-cmdlet aanroepen.

### <a name="how-do-i-disable-expressroute-premium"></a>Hoe kan ik ExpressRoute Premium uitschakelen?

U kunt ExpressRoute Premium uitschakelen door de REST API-of Power shell-cmdlet aan te roepen. U moet ervoor zorgen dat uw connectiviteit moet worden geschaald om te voldoen aan de standaard limieten voordat u ExpressRoute Premium uitschakelt. Als uw gebruik groter wordt dan de standaard limieten, mislukt de aanvraag om ExpressRoute Premium uit te scha kelen.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan ik kiezen welke functies Ik wil gebruiken in de functie set Premium?

Nee. U kunt de functies niet selecteren. We scha kelen alle functies in wanneer u ExpressRoute Premium inschakelt.

### <a name="how-much-does-expressroute-premium-cost"></a>Wat heeft ExpressRoute Premium kosten?

Raadpleeg de [prijs informatie](https://azure.microsoft.com/pricing/details/expressroute/) voor de kosten.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Betaal ik voor ExpressRoute Premium naast de standaard kosten voor ExpressRoute?

Ja. ExpressRoute Premium-kosten zijn van toepassing op ExpressRoute-circuit kosten en kosten die zijn vereist voor de connectiviteits provider.

## <a name="expressroute-local"></a>Lokale ExpressRoute

### <a name="what-is-expressroute-local"></a>Wat is ExpressRoute lokaal?

ExpressRoute local is een SKU van ExpressRoute-circuit, naast de standaard-SKU en de Premium-SKU. Een belang rijke functie van Local is dat een lokaal circuit op een ExpressRoute-peering-locatie u alleen toegang biedt tot een of twee Azure-regio's in of in de buurt van dezelfde metro. Met een standaard circuit hebt u daarentegen toegang tot alle Azure-regio's in een geopolitieke regio en een Premium-circuit naar alle Azure-regio's. U kunt met een lokale SKU alleen routes adverteren (via micro soft en privé-peering) vanuit de bijbehorende lokale regio van het ExpressRoute-circuit. U kunt geen routes voor andere regio's ontvangen die afwijken van de gedefinieerde lokale regio.

### <a name="what-are-the-benefits-of-expressroute-local"></a>Wat zijn de voor delen van ExpressRoute lokale?

Wanneer u een uitgangs gegevens overdracht moet betalen voor uw Standard-of Premium ExpressRoute-circuit, betaalt u geen uitgaande gegevens overdracht afzonderlijk voor uw lokale circuit van ExpressRoute. Met andere woorden, de prijs van de lokale ExpressRoute omvat ook kosten voor gegevens overdracht. ExpressRoute local is een rendabelere oplossing als u grote hoeveel heden gegevens wilt overdragen en u uw gegevens via een particuliere verbinding met een ExpressRoute-peering-locatie in de buurt van uw gewenste Azure-regio's kunt brengen. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Welke functies zijn er beschikbaar en wat bevinden zich niet op ExpressRoute lokale locatie?

Vergeleken met een standaard ExpressRoute-circuit heeft een lokaal circuit dezelfde set functies, behalve:
* Bereik van toegang tot Azure-regio's zoals hierboven wordt beschreven
* ExpressRoute Global Reach is niet beschikbaar op de lokale locatie

ExpressRoute Local heeft ook dezelfde limieten voor bronnen (bijvoorbeeld het aantal VNets per circuit) als standaard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Waar is ExpressRoute lokaal beschikbaar en welke Azure-regio's wordt elke peering-locatie toegewezen aan?

ExpressRoute local is beschikbaar op de peering locaties waar een of twee Azure-regio's sluitend zijn. Het is niet beschikbaar op een peering-locatie waar zich geen Azure-regio bevindt in die staat of provincie of land/regio. Zie de exacte toewijzingen op [de pagina locaties](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute voor Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Hoe kan ik maakt u een ExpressRoute-circuit om verbinding te maken met Microsoft 365-Services?

1. Bekijk de [pagina met ExpressRoute-vereisten](expressroute-prerequisites.md) om te controleren of u aan de vereisten voldoet.
2. Om ervoor te zorgen dat aan de connectiviteits vereisten wordt voldaan, bekijkt u de lijst met service providers en locaties in het artikel [ExpressRoute partners en locaties](expressroute-locations.md) .
3. Plan uw capaciteits vereisten door [netwerk planning en prestaties afstemmen voor Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)te controleren.
4. Volg de stappen in de werk stromen voor het instellen van connectiviteit [ExpressRoute werk stromen voor circuit inrichting en circuit statussen](expressroute-workflows.md).

> [!IMPORTANT]
> Zorg ervoor dat u de ExpressRoute Premium-invoeg toepassing hebt ingeschakeld bij het configureren van connectiviteit met Microsoft 365 Services.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Kunnen mijn huidige ExpressRoute-circuits de connectiviteit ondersteunen voor Microsoft 365 Services?

Ja. Uw bestaande ExpressRoute-circuit kan worden geconfigureerd voor de ondersteuning van connectiviteit met Microsoft 365 Services. Zorg ervoor dat u voldoende capaciteit hebt om verbinding te maken met Microsoft 365 Services en dat u Premium-invoeg toepassing hebt ingeschakeld. [Netwerk planning en prestaties afstemmen voor Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) helpt u bij het plannen van de connectiviteits behoeften. Zie ook [een ExpressRoute-circuit maken en wijzigen](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Welke Microsoft 365 Services kunnen worden gebruikt via een ExpressRoute-verbinding?

Raadpleeg [Microsoft 365 url's en IP-](/microsoft-365/enterprise/urls-and-ip-address-ranges) adresbereiken pagina voor een bijgewerkte lijst met services die via ExpressRoute worden ondersteund.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Wat heeft ExpressRoute voor de kosten van Microsoft 365 Services?

Voor Microsoft 365 Services moet Premium-invoeg toepassing zijn ingeschakeld. Zie de [pagina prijs informatie](https://azure.microsoft.com/pricing/details/expressroute/) voor de kosten.

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>In welke regio's is ExpressRoute voor Microsoft 365 ondersteund?

Zie [ExpressRoute-partners en-locaties](expressroute-locations.md) voor informatie.

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan ik via internet toegang krijgen tot Microsoft 365, zelfs als ExpressRoute is geconfigureerd voor mijn organisatie?

Ja. Microsoft 365 service-eind punten zijn bereikbaar via internet, zelfs als ExpressRoute is geconfigureerd voor uw netwerk. Neem contact op met het netwerk team van uw organisatie als het netwerk op uw locatie is geconfigureerd om verbinding te maken met Microsoft 365 Services via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Hoe kan ik een hoge Beschik baarheid plannen voor Microsoft 365 netwerk verkeer in azure ExpressRoute?
Raadpleeg de aanbeveling voor [hoge Beschik baarheid en failover met Azure ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Heb ik toegang tot Office 365-Services voor de Amerikaanse overheid (GCC) via een Azure Amerikaanse overheid ExpressRoute-circuit?

Ja. Office 365 GCC service-eind punten zijn bereikbaar via de Azure-ExpressRoute voor de Amerikaanse overheid. U moet echter eerst een ondersteunings ticket openen op de Azure Portal om de voor voegsels te bieden die u aan micro soft wilt adverteren. Uw verbinding met Office 365 GCC Services wordt tot stand gebracht nadat het ondersteunings ticket is opgelost. 

## <a name="route-filters-for-microsoft-peering"></a>Route filters voor micro soft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Ik schakel micro soft-peering voor de eerste keer in, welke routes worden weer geven?

Er worden geen routes weer geven. U moet een route filter aan uw circuit koppelen om voorvoegsel advertenties te starten. Zie [route filters configureren voor micro soft-peering](how-to-routefilter-powershell.md)voor instructies.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ik heb micro soft-peering ingeschakeld en nu probeer ik Exchange Online te selecteren, maar er wordt een fout melding weer geven dat ik niet heeft gemachtigd.

Wanneer u route filters gebruikt, kan elke klant micro soft-peering inschakelen. Voor het gebruik van Microsoft 365 Services moet u echter nog steeds toestemming geven door Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Ik heb micro soft-peering ingeschakeld vóór 1 augustus 2017, hoe kan ik profiteren van route filters?

Uw bestaande circuit gaat verder met het adverteren van de voor voegsels voor Microsoft 365. Als u advertenties voor open bare Azure-voor voegsels wilt toevoegen aan dezelfde micro soft-peering, kunt u een route filter maken, de services selecteren die u nodig hebt (inclusief de Microsoft 365 service (s) die u nodig hebt) en het filter koppelen aan uw micro soft-peering. Zie [route filters configureren voor micro soft-peering](how-to-routefilter-powershell.md)voor instructies.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ik heb micro soft-peering op één locatie, nu probeer ik op een andere locatie in te scha kelen en ik zie geen voor voegsels.

* Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd.

* Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld. Er worden standaard geen voor voegsels weer geven.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Privacy

### <a name="does-the-expressroute-service-store-customer-data"></a>Worden klant gegevens opgeslagen in de ExpressRoute-service?

Nee.

---
title: 'Architectuur: wereld wijde doorvoer netwerk architectuur'
titleSuffix: Azure Virtual WAN
description: Meer informatie over hoe Azure Virtual WAN een wereld wijde doorvoer netwerk architectuur mogelijk maakt door alomtegenwoordige, een wille keurige connectiviteit tussen Cloud werkbelastingen in te scha kelen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: cfb75b6383d8ca449b4bc54b9d21cb16b3a4ad40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428180"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Wereld wijde doorvoer netwerk architectuur en virtueel WAN

Moderne ondernemingen vereisen alomtegenwoordige-connectiviteit tussen Hyper-gedistribueerde toepassingen, gegevens en gebruikers in de Cloud en on-premises. Wereld wijde doorvoer netwerk architectuur wordt door ondernemingen goedgekeurd voor het consolideren, verbinden en beheren van de Cloud gerichte moderne, wereld wijde IT-footprint.

De wereld wijde doorvoer netwerk architectuur is gebaseerd op een klassiek hub-en-spoke-verbindings model, waarbij de hub van het gehoste netwerk van de Cloud transitieve connectiviteit mogelijk maakt tussen eind punten die kunnen worden gedistribueerd in verschillende typen spokes.

In dit model kan een spoke zijn:
* Virtueel netwerk (VNets)
* Fysieke branche site
* Externe gebruiker
* Internet

![hub en spoke](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Afbeelding 1: Global Transit hub-en-spoke-netwerk**

In afbeelding 1 ziet u de logische weer gave van het wereld wijde doorvoer netwerk waarin geografisch gedistribueerde gebruikers, fysieke sites en VNets zijn verbonden via een netwerkhub die wordt gehost in de Cloud. Met deze architectuur kan een logische Transit connectiviteit met één hop tussen de netwerk eindpunten worden ingeschakeld.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Wereld wijd Transit netwerk met virtuele WAN

Azure Virtual WAN is een door micro soft beheerde Cloud netwerk service. Alle netwerk onderdelen waarvan deze service bestaat, worden door micro soft gehost en beheerd. Zie het artikel [overzicht van Virtual WAN](virtual-wan-about.md) voor meer informatie over Virtual WAN.

Met Azure Virtual WAN kunt u een wereld wijde doorvoer netwerk architectuur door alomtegenwoordige, een-op-een-verbinding te maken tussen wereld wijd gedistribueerde sets Cloud werkbelastingen in VNets, filiaal sites, SaaS-en PaaS-toepassingen en gebruikers.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Afbeelding 2: wereld wijd Transit netwerk en virtueel WAN**

In de virtuele WAN-architectuur van Azure worden virtuele WAN-hubs ingericht in azure-regio's, waarmee u uw vertakkingen, VNets en externe gebruikers verbinding kunt laten maken. De fysieke Branch sites zijn verbonden met de hub via Premium of Standard ExpressRoute of site-naar-site-Vpn's, VNets zijn verbonden met de hub via VNet-verbindingen en externe gebruikers kunnen rechtstreeks verbinding maken met de hub met behulp van gebruikers VPN (punt-naar-site-Vpn's). Virtual WAN biedt ook ondersteuning voor een VNet-verbinding tussen regio's waarbij een VNet in één regio kan worden verbonden met een virtuele WAN-hub in een andere regio.

U kunt een virtueel WAN opzetten door één virtuele WAN-hub te maken in de regio met het grootste aantal spokes (vertakkingen, VNets, gebruikers) en vervolgens de spokes in andere regio's te verbinden met de hub. Dit is een goede optie wanneer een bedrijfs footprint voornamelijk in één regio met een paar externe spokes is.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Hub-naar-hub-connectiviteit

Een Cloud footprint in de onderneming kan meerdere Cloud regio's omvatten en is optimaal (wachttijd) om toegang te krijgen tot de Cloud vanuit een regio die het dichtst bij hun fysieke site en gebruikers ligt. Een van de belangrijkste principes van de wereld wijde doorvoer netwerk-architectuur is het inschakelen van connectiviteit tussen regio's tussen alle Cloud-en on-premises netwerk eindpunten. Dit betekent dat verkeer van een vertakking dat is verbonden met de cloud in één regio een andere vertakking of een VNet in een andere regio kan bereiken met behulp van hub-naar-hub-connectiviteit ingeschakeld door het [globale Azure-netwerk](https://azure.microsoft.com/global-infrastructure/global-network/).

![meerdere regio's](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Afbeelding 3: Virtual WAN-verbindingen voor meerdere regio's**

Wanneer meerdere hubs zijn ingeschakeld in één virtueel WAN, worden de hubs automatisch onderling verbonden via hub-naar-hub-koppelingen, waardoor de wereld wijde connectiviteit tussen filialen en Vnets die over meerdere regio's worden verdeeld. 

Daarnaast kunnen hubs die allemaal deel uitmaken van hetzelfde virtuele WAN, worden gekoppeld aan verschillende regionale toegangs-en beveiligings beleidsregels. Zie [beveiliging en beleids beheer](#security) verderop in dit artikel voor meer informatie.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Elke verbinding

Wereld wijde doorvoer netwerk architectuur maakt het mogelijk om elke verbinding via virtuele WAN-hubs te maken. Deze architectuur elimineert of vermindert de nood zaak van een volledige mesh-of gedeeltelijke mesh-verbinding tussen spokes, die ingewik kelder zijn om te bouwen en te onderhouden. Daarnaast is routering beheer in hub-en-spoke versus net-netwerken eenvoudiger te configureren en te onderhouden.

Elke verbinding (in de context van een wereld wijde architectuur) biedt een onderneming met wereld wijd gedistribueerde gebruikers, vertakkingen, data centers, VNets en toepassingen om met elkaar verbinding te maken via de "transit" hub (s). Azure Virtual WAN fungeert als het wereld wijde doorvoer systeem.

![alle](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Afbeelding 4: paden voor virtuele WAN-verkeer**

Azure Virtual WAN ondersteunt de volgende globale doorvoer connectiviteits paden. De letters tussen haakjes worden toegewezen aan afbeelding 4.

* Vertakking-naar-VNet (a)
* Vertakking-naar-vertakking (b)
  * ExpressRoute Global Reach en virtuele WAN
* Externe gebruiker-naar-VNet (c)
* Externe gebruiker-to-Branch (d)
* VNet-naar-VNet (e)
* Vertakking-naar-hub-hub-naar-vertakking (f)
* Vertakking-naar-hub-hub-naar-VNet (g)
* VNet-naar-hub-hub-naar-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Vertakking-naar-VNet (a) en vertakking-naar-VNet-Kruis regio (g)

Vertakking-naar-VNet is het primaire pad dat door Azure Virtual WAN wordt ondersteund. Met dit pad kunt u vertakkingen verbinden met Azure IAAS Enter prise-workloads die zijn geïmplementeerd in azure VNets. Vertakkingen kunnen worden verbonden met het virtuele WAN via ExpressRoute of site-naar-site-VPN. Het verkeer transitt naar VNets die zijn verbonden met de virtuele WAN-hubs via VNet-verbindingen. Expliciete [Gateway overdracht](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is niet vereist voor virtuele WAN, omdat door Virtual WAN automatisch gateway-transit naar vertakkings site wordt ingeschakeld. Zie het artikel over [virtuele WAN-partners](virtual-wan-configure-automation-providers.md) voor het verbinden van een SD-WAN-verbinding met een virtueel WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach en virtuele WAN

ExpressRoute is een privé-en robuuste manier om uw on-premises netwerken te verbinden met de Microsoft Cloud. Virtual WAN ondersteunt verbindingen met Express route-circuits. Voor het verbinden van een filiaal site met een virtueel WAN met Express route is 1) circuit (Premium of Standard circuit 2) een Global Reach ingeschakelde locatie.

ExpressRoute Global Reach is een invoeg functie voor ExpressRoute. Met Global Reach kunt u ExpressRoute-circuits samen koppelen om een privé netwerk tussen uw on-premises netwerken te maken. Voor vertakkingen die zijn verbonden met Azure Virtual WAN met ExpressRoute moet de ExpressRoute Global Reach met elkaar communiceren.

In dit model kan elke vertakking die is verbonden met de virtuele WAN-hub met behulp van ExpressRoute, verbinding maken met VNets met behulp van het pad van de vertakking-naar-VNet. Vertakkings-naar-vertakking verkeer kan de hub niet door Voer omdat ExpressRoute Global Reach een meer optimaal pad via Azure WAN mogelijk maakt.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Vertakking-naar-vertakking (b) en vertakking-naar-vertakking Kruis regio (f)

Vertakkingen kunnen worden verbonden met een virtuele WAN-hub van Azure met behulp van ExpressRoute-circuits en/of site-naar-site-VPN-verbindingen. U kunt de vertakkingen verbinden met de virtuele WAN-hub in de regio die het dichtst bij de vertakking ligt.

Met deze optie kunnen bedrijven gebruikmaken van de Azure-backbone om vertakkingen te verbinden. Hoewel deze mogelijkheid echter beschikbaar is, moet u de voor delen van het verbinden van branches via Azure Virtual WAN, vergeleken met een particulier WAN, afwegen.  

> [!NOTE]
> Het uitschakelen van vertakking-naar-vertakking-connectiviteit in virtuele WAN-Virtual WAN kan worden geconfigureerd om vertakking-naar-Branch-verbindingen uit te scha kelen. Deze configuratie blokkeert de route doorgifte tussen VPN (S2S en P2S) en Express route verbonden sites. Deze configuratie is niet van invloed op verspreiding en connectiviteit van het filiaal naar vnet en Vnet-naar-Vnet. Deze instelling configureren met behulp van Azure portal: Kies onder configuratie menu voor virtuele WAN de optie instellen: vertakking-naar-vertakking uitgeschakeld. 

### <a name="remote-user-to-vnet-c"></a>Externe gebruiker-naar-VNet (c)

U kunt directe, veilige externe toegang tot Azure inschakelen met behulp van punt-naar-site-verbinding van een externe gebruikers client naar een virtueel WAN. Externe gebruikers van ondernemingen hoeven niet langer hairpin te maken met de Cloud met behulp van een bedrijfs netwerk.

### <a name="remote-user-to-branch-d"></a>Externe gebruiker-to-Branch (d)

Via het externe User-to-Branch-pad kunnen externe gebruikers die gebruikmaken van een punt-naar-site-verbinding met Azure toegang hebben tot on-premises werk belastingen en toepassingen door middel van door Voer via de Cloud. Dit pad biedt externe gebruikers de flexibiliteit om werk belastingen te openen die zowel in azure als on-premises zijn geïmplementeerd. Ondernemingen kunnen de centrale Cloud service voor externe toegang inschakelen in azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-naar-VNet-door Voer (e) en VNet-naar-VNet-Kruis regio (h)

Met de VNet-naar-VNet-transit kan VNets verbinding maken met elkaar om toepassingen met meerdere lagen die zijn geïmplementeerd over meerdere VNets te verbinden. U kunt eventueel VNets met elkaar verbinden via VNet-peering. Dit kan ook geschikt zijn voor bepaalde scenario's waarbij door Voer via de VWAN-hub niet nodig is.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Geforceerde tunneling en standaard route in azure Virtual WAN

Geforceerde tunneling kan worden ingeschakeld door het configureren van de standaard route inschakelen op een VPN-, ExpressRoute-of Virtual Network verbinding in een virtueel WAN.

Een virtuele hub geeft een geleerde standaard route door aan een VPN-verbinding tussen een virtueel netwerk en een site-naar-site als de standaard vlag inschakelen is ingeschakeld op de verbinding. 

Deze vlag wordt weergegeven als de gebruiker een verbinding met een virtueel netwerk, een VPN-verbinding of een ExpressRoute aanpast. Deze vlag wordt standaard uitgeschakeld wanneer een site of een ExpressRoute-circuit met een hub wordt verbonden. De vlag is standaard ingeschakeld wanneer een virtuele netwerkverbinding wordt toegevoegd om een VNet te verbinden met een virtuele hub. De standaardroute is niet afkomstig van de Virtual WAN-hub. De standaardroute wordt doorgegeven als deze al bekend is bij de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub, of als voor een andere verbonden site geforceerd tunnelen is ingeschakeld.

## <a name="security-and-policy-control"></a><a name="security"></a>Beveiliging en beleids beheer

De virtuele WAN-hubs van Azure verdeelen alle netwerk eindpunten via het hybride netwerk en kunnen mogelijk alle verkeer van het tussenliggende netwerk zien. Virtuele WAN-hubs kunnen worden geconverteerd naar beveiligde virtuele hubs door de Azure Firewall binnen VWAN hubs te implementeren om beveiliging, toegang en beleids beheer op basis van de Cloud mogelijk te maken. Integratie van Azure-firewalls in virtuele WAN-hubs kan worden uitgevoerd door Azure Firewall Manager.

[Azure firewall Manager](../firewall-manager/index.yml) biedt de mogelijkheden voor het beheren en schalen van beveiliging voor wereld wijde Transit netwerken. Azure Firewall Manager biedt de mogelijkheid om route ring, globaal beleids beheer, geavanceerde Internet beveiligings Services via derden samen met de Azure Firewall centraal te beheren.

![beveiligde virtuele hub met Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Afbeelding 5: beveiligde virtuele hub met Azure Firewall**

Azure Firewall naar het virtuele WAN ondersteunt de volgende globale beveiligde doorvoer paden voor verbindingen. De letters tussen haakjes worden toegewezen aan afbeelding 5.

* Beveiligde door Voer van VNet-naar-VNet (e)
* VNet-naar-Internet of een beveiligings service van derden (i)
* Branch-to-Internet of een beveiligings service van derden (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Door Voer beveiligd VNet-naar-VNet (e)

Met het beveiligde VNet-naar-VNet-verkeer kan VNets met elkaar verbinding maken via de Azure Firewall in de virtuele WAN-hub.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-naar-Internet of een beveiligings service van derden (i)

Met VNet-naar-Internet kan VNets verbinding maken met Internet via de Azure Firewall in de virtuele WAN-hub. Verkeer naar Internet via ondersteunde beveiligings services van derden stroomt niet via de Azure Firewall. U kunt het pad voor de Vnet-naar-Internet configureren via de ondersteunde beveiligings service van derden met behulp van Azure Firewall Manager.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Branch-to-Internet of een beveiligings service van derden (j)
Via de vertakking-naar-Internet kunnen filialen verbinding maken met Internet via de Azure Firewall in de virtuele WAN-hub. Verkeer naar Internet via ondersteunde beveiligings services van derden stroomt niet via de Azure Firewall. U kunt het pad van de filiaal naar het Internet configureren via de ondersteunde beveiligings service van derden met behulp van Azure Firewall Manager. 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>Door de filiaal beveiligde Transit cross-Region (f)

Vertakkingen kunnen worden verbonden met een beveiligde virtuele hub met Azure Firewall met behulp van ExpressRoute-circuits en/of site-naar-site-VPN-verbindingen. U kunt de vertakkingen verbinden met de virtuele WAN-hub in de regio die het dichtst bij de vertakking ligt.

Met deze optie kunnen bedrijven gebruikmaken van de Azure-backbone om vertakkingen te verbinden. Hoewel deze mogelijkheid echter beschikbaar is, moet u de voor delen van het verbinden van branches via Azure Virtual WAN, vergeleken met een particulier WAN, afwegen.  

> [!NOTE]
> Verwerking tussen de hub van verkeer via de firewall wordt momenteel niet ondersteund. Verkeer tussen hubs wordt doorgestuurd naar de juiste vertakking in de beveiligde virtuele hub, maar de Azure Firewall in elke hub worden omzeild.

### <a name="branch-to-vnet-secured-transit-g"></a>Door Voer beveiligd door VNet (g)

Met de voor het beveiligd door VNet beveiligde transit kunnen filialen communiceren met virtuele netwerken in dezelfde regio als de virtuele WAN-hub en een ander virtueel netwerk dat is verbonden met een andere virtuele WAN-hub in een andere regio.

> [!NOTE]
> Inter-hub met firewall wordt momenteel niet ondersteund. Verkeer tussen hubs gaat direct over op de Azure Firewall in elke hub.  Verkeer via een verbinding die is bestemd voor een virtueel netwerk in dezelfde regio wordt verwerkt door de Azure Firewall in de beveiligde hub.


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>Hoe kan ik standaard route (0.0.0.0/0) in een beveiligde virtuele hub inschakelen

Azure Firewall geïmplementeerd in een virtuele WAN-hub (beveiligde virtuele hub) kan worden geconfigureerd als standaard router voor het internet of een vertrouwde beveiligings provider voor alle vertakkingen (verbonden door VPN of Express route), spoke Vnets en gebruikers (verbonden via P2S VPN). Deze configuratie moet worden uitgevoerd met behulp van Azure Firewall Manager.  Zie verkeer naar uw hub routeren om al het verkeer van branches (inclusief gebruikers) en Vnets op Internet via de Azure Firewall te configureren. 

Dit is een configuratie met twee stappen:

1. Configureer route ring van Internet verkeer met behulp van het instellings menu voor de veilige virtuele hub. Vnets en vertakkingen configureren die verkeer via de firewall naar Internet kunnen verzenden.

2. Configureer welke verbindingen (Vnet en vertakking) verkeer naar Internet (0.0.0.0/0) kunnen routeren via de Azure FW in de hub of de vertrouwde beveiligings provider. Met deze stap zorgt u ervoor dat de standaard route wordt door gegeven aan geselecteerde branches en Vnets die via de verbindingen zijn gekoppeld aan de virtuele WAN-hub. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>Verkeer naar een on-premises firewall in een beveiligde virtuele hub forceren

Als er al een standaard route is geleerd (via BGP) door de virtuele hub van een van de vertakkingen (VPN of de sites), wordt deze standaard route overschreven door de standaard route die is geleerd van Azure Firewall Manager-instelling. In dit geval wordt al het verkeer dat de hub vanaf Vnets en vertakkingen die bestemd zijn voor Internet, doorgestuurd naar de Azure Firewall of vertrouwde beveiligings provider.

> [!NOTE]
> Momenteel is er geen optie voor het selecteren van een on-premises firewall of Azure Firewall (en een vertrouwde beveiligings provider) voor Internet gebonden verkeer dat afkomstig is van Vnets, branches of gebruikers. De standaard route die is geleerd van de instelling van Azure Firewall Manager is altijd de voor keur boven de standaard route die is geleerd van een van de vertakkingen.


## <a name="next-steps"></a>Volgende stappen

Maak een verbinding met behulp van Virtual WAN en implementeer Azure Firewall in VWAN-hub (s).

* [Site-naar-site-verbindingen met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-verbindingen met behulp van Virtual WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager voor het implementeren van Azure FW in VWAN](../firewall-manager/index.yml)

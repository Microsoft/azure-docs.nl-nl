---
title: 'Azure-ExpressRoute: route ring optimaliseren'
description: Deze pagina bevat gedetailleerde informatie over het optimaliseren van routering wanneer u meerdere ExpressRoute-circuits hebt die Microsoft verbinden met uw bedrijfsnetwerk.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/11/2019
ms.author: duau
ms.openlocfilehash: f35f1d390762d3f83176d7b36db8959dc5ed0157
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204874"
---
# <a name="optimize-expressroute-routing"></a>ExpressRoute-routering optimaliseren
Als u meerdere ExpressRoute-circuits hebt, hebt u meer dan één pad om verbinding te maken met Microsoft. Dat betekent dat suboptimale routering kan plaatsvinden, met andere woorden, dat verkeer soms een langer pad aflegt om Microsoft te bereiken en Microsoft om uw netwerk te bereiken. Hoe langer het netwerkpad, hoe groter de latentie. Latentie heeft een directe invloed op toepassingsprestaties en gebruikerservaring. In dit artikel wordt dit probleem geïllustreerd en wordt uitgelegd hoe u routering optimaliseert met behulp van de standaardrouteringstechnologieën.

## <a name="path-selection-on-microsoft-and-public-peerings"></a>Padselectie op micro soft en open bare peerings
Het is belang rijk om ervoor te zorgen dat bij gebruik van micro soft of open bare peering dat verkeer over het gewenste pad loopt als u een of meer ExpressRoute-circuits hebt, evenals paden naar het Internet via een Internet Exchange (IX) of Internet service provider (ISP). BGP gebruikt een selectie algoritme voor het beste pad op basis van een aantal factoren, waaronder de langste voorvoegsel overeenkomst (LPM). Klanten moeten het *lokale voorkeurs* kenmerk implementeren om ervoor te zorgen dat verkeer dat is bestemd voor Azure via micro soft of open bare peering over het ExpressRoute-pad, zodat het pad altijd de voor keur heeft voor ExpressRoute. 

> [!NOTE]
> De standaard lokale voor keur is doorgaans 100. Hogere lokale voor keuren zijn meer voor keur. 
>
>

Bekijk het volgende voorbeeld scenario:

![Diagram waarin het ExpressRoute Case 1-probleem wordt weer gegeven: suboptimale route ring van klant naar micro soft](./media/expressroute-optimize-routing/expressroute-localPreference.png)

In het bovenstaande voor beeld kunt u de lokale voor keur als volgt configureren in ExpressRoute-paden. 

**Cisco IOS-XE-configuratie van R1-perspectief:**

- R1 (config) #route: ExR toestaan 10
- R1 (config-route-map) #set lokale-voorkeurs 150

- R1 (config) #router BGP 345
- R1 (config-router) #neighbor 1.1.1.2 extern-as 12076
- R1 (config-router) #neighbor 1.1.1.2-activering
- R1 (config-router) #neighbor 1.1.1.2 route kaart voor keur-ExR in

**Junos-configuratie van R1 perspectief:**

- user@R1aantal ingestelde protocollen BGP-groep ibgp intern
- user@R1# set-protocollen BGP-groep ibgp Local-preference 150



## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Suboptimale routering van klant naar Microsoft
We gaan het routeringsprobleem bekijken aan de hand van een voorbeeld. Stel, u hebt twee kantoren in de VS, één in Los Angeles en één in New York. Uw kantoren zijn aangesloten op een Wide Area Network (WAN). Dit kan uw eigen backbone-netwerk zijn of het IP VPN van uw serviceprovider. U hebt twee ExpressRoute-circuits, één in US - west en één in US - oost. Deze zijn ook aangesloten op het WAN. U hebt uiteraard twee paden om verbinding te maken met het Microsoft-netwerk. Stel nu dat u zowel in US - west als in US - oost een Azure-implementatie hebt (bijvoorbeeld Azure App Service). Het is uw bedoeling om uw gebruikers in Los Angeles te verbinden met Azure US - west en uw gebruikers in New York met Azure US - oost, omdat uw servicebeheerder adverteert dat gebruikers in elk kantoor voor optimale ervaringen gebruik kunnen maken van de Azure-services die zich zo dichtbij mogelijk bevinden. Helaas werkt dit plan prima voor de gebruikers aan de oostkust maar niet voor de gebruikers aan de westkust. Dit heeft de volgende oorzaak. In elk ExpressRoute-circuit adverteren we aan u zowel het voorvoegsel in Azure US - oost (23.100.0.0/16) als het voorvoegsel in Azure US - west (13.100.0.0/16). Als u niet weet welk voorvoegsel bij welke regio hoort, kunt u ze niet op verschillende manieren behandelen. Misschien 'denkt' uw WAN-netwerk dat beide voorvoegsels zich dichter bij US - oost bevinden dan bij US - west, en worden de gebruikers van beide kantoren daarom omgeleid naar het ExpressRoute-circuit in US - oost. Dit leidt tot veel ontevreden gebruikers in het kantoor in Los Angeles.

![Probleem ExpressRoute casus 1: Suboptimale routering van klant naar Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Oplossing: gebruik BGP-community's
Om routering te optimaliseren voor de gebruikers op beide kantoren, moet u weten welk voorvoegsel van Azure US - west is en welk van Azure US - oost. Deze informatie wordt versleuteld met [BGP-communitywaarden](expressroute-routing.md). We hebben een unieke waarde voor de BGP-Community toegewezen voor elke Azure-regio, bijvoorbeeld ' 12076:51004 ' voor VS Oost, ' 12076:51006 ' voor VS West. Nu u weet welk voorvoegsel bij welke Azure-regio hoort, kunt u configureren welk ExpressRoute-circuit de voorkeur heeft. Omdat we het BGP gebruiken om routeringsinformatie uit te wisselen, kunt u de routering beïnvloeden met de lokale voorkeur van het BGP. In ons voorbeeld kunt u in US - west aan 13.100.0.0/16 een hogere lokale-voorkeurswaarde toekennen dan in US - oost, en in US - oost kunt u aan 23.100.0.0/16 een hogere lokale-voorkeurswaarde toekennen dan in US - west. Als beide paden naar Microsoft beschikbaar zijn, zorgt deze configuratie ervoor dat uw gebruikers in Los Angeles het ExpressRoute-circuit in US - west gebruiken om verbinding te maken met Azure US - west, en uw gebruikers in New York de ExpressRoute in US - oost nemen naar Azure US - oost. Routering is nu aan beide zijden geoptimaliseerd. 

![Oplossing Expressroute casus 1: BGP-community's gebruiken](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Dezelfde techniek, het gebruik van lokale voorkeur, kan ook worden toegepast op routering van klant naar Azure Virtual Network. We voegen geen BGP Community-waarde toe aan de voorvoegsels die vanuit Azure zijn aangekondigd aan uw netwerk. Maar omdat u weet welke Virtual Network-implementatie zich dicht bij uw kantoor bevindt, kunt u uw routers zo configureren dat het ene ExpressRoute-circuit de voorkeur krijgt boven het andere.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Suboptimale routering van Microsoft naar de klant
Hier volgt nog een voorbeeld waarin verbindingen van Microsoft een langer pad afleggen om uw netwerk te bereiken. In dit geval maakt u gebruik van on-premises Exchange-servers en Exchange Online in een [hybride omgeving](/exchange/exchange-hybrid). Uw kantoren zijn verbonden met een WAN. U adverteert de voorvoegsels van uw on-premises servers in beide kantoren aan Microsoft door middel van de twee ExpressRoute-circuits. Voor bijvoorbeeld postvakmigratie start Exchange Online verbindingen met de on-premises servers. Helaas wordt de verbinding met uw kantoor in Los Angeles omgeleid naar het ExpressRoute-circuit in US - oost, waarna deze het gehele continent doorkruist om weer aan de westkust uit te komen. De oorzaak van het probleem is vergelijkbaar met die in het eerste voorbeeld. Zonder aanwijzingen weet het Microsoft-netwerk niet welk klantvoorvoegsel zich dichter bij US - oost bevindt en welk dichter bij US - west. Dus soms wordt het verkeerde pad naar uw kantoor in Los Angeles gekozen.

![Probleem ExpressRoute casus 2: Suboptimale routering van Microsoft naar de klant](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Oplossing: AS-padtoevoeging
Er zijn twee oplossingen voor het probleem. Voor de eerste oplossing adverteert u gewoon het on-premises voorvoegsel voor uw kantoor in Los Angeles, 177.2.0.0/31, op het ExpressRoute-circuit in US - west en het on-premises voorvoegsel voor uw kantoor in New York, 177.2.0.2/31, op het ExpressRoute-circuit in US - oost. Als gevolg hiervan is er slechts één pad waarlangs Microsoft verbinding maakt met elk kantoor. Er is geen dubbelzinnigheid en routering is geoptimaliseerd. Bij dit ontwerp moet u echter nadenken over een failoverstrategie. Als het pad naar Microsoft via ExpressRoute wordt verbroken, moet u ervoor zorgen dat Exchange Online nog steeds verbinding kan maken met uw on-premises servers. 

Voor de tweede oplossing blijft u beide voorvoegsels op beide ExpressRoute-circuits adverteren en geeft u daarnaast aan welk voorvoegsel zich het dichtst bij welk kantoor bevindt. Omdat we BGP AS-padtoevoeging ondersteunen, kunt u het AS-pad voor uw voorvoegsel configureren om routering te beïnvloeden. In dit voorbeeld kunt u het AS-pad voor 172.2.0.0/31 in US - oost verlengen zodat het ExpressRoute-circuit in US - west de voorkeur krijgt voor verkeer dat is bestemd voor dit voorvoegsel (omdat ons netwerk 'denkt' dat het pad naar dit voorvoegsel korter is in het westen). En zo verlengt u ook het AS-pad voor 172.2.0.2/31 in US - west, zodat het ExpressRoute-circuit in US - oost de voorkeur krijgt. Routering is geoptimaliseerd voor beide kantoren. Als bij dit ontwerp één ExpressRoute-circuit wordt verbroken, kan Exchange Online u nog steeds bereiken via een ander ExpressRoute-circuit en uw WAN. 

> [!IMPORTANT]
> We verwijderen persoonlijke AS-nummers in het AS-pad voor de voor voegsels die worden ontvangen op micro soft-peering wanneer peering gebruikmaakt van een privé AS-nummer. U moet een peer met een openbaar systeem hebben en openbaar als nummers toevoegen in het AS-pad om de route ring voor micro soft-peering te beïnvloeden.
> 
> 

![Oplossing ExpressRoute casus 2: Toevoeging van AS PATH gebruiken](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Hoewel de hier getoonde voorbeelden voor Microsoft- en openbare peerings zijn, ondersteunen we dezelfde mogelijkheden voor persoonlijke peering. De AS-padtoevoeging werkt ook binnen één ExpressRoute-circuit om de selectie van primaire en secundaire paden te beïnvloeden.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Suboptimale routering tussen virtuele netwerken
Met ExpressRoute kunt u VNet-communicatie (communicatie van Virtual Network naar Virtual Network) inschakelen door de VNets te koppelen aan een ExpressRoute-circuit. Wanneer u ze koppelt aan meerdere ExpressRoute-circuits, kan er suboptimale routering tussen de VNets optreden. Een voorbeeld. U hebt twee ExpressRoute-circuits, één in US - west en één in US - oost. In elke regio hebt u twee VNets. Uw webservers zijn geïmplementeerd in het ene VNet en de toepassingsservers in het andere. Ten behoeve van redundantie kunt u de twee VNets in elke regio koppelen aan zowel het lokale ExpressRoute-circuit als aan het externe ExpressRoute-circuit. Zoals u hieronder kunt zien, lopen er vanuit elk VNet twee paden naar het andere VNet. De VNets weten niet welk ExpressRoute-circuit lokaal is en welk circuit extern. Omdat er ECMP-routering (Equal-Cost-Multi-Path) plaatsvindt om het verkeer tussen VNets gelijk te verdelen, kan het daardoor gebeuren dat verkeer een langere weg aflegt en naar het externe ExpressRoute-circuit wordt omgeleid.

![Probleem ExpressRoute casus 3: Suboptimale routering tussen virtuele netwerken](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Oplossing: meer gewicht toewijzen aan lokale verbinding
De oplossing is eenvoudig. Omdat u weet waar de VNets en circuits zich bevinden, kunt u ons vertellen aan welk pad elke VNet de voorkeur moet geven. Voor dit voorbeeld wijst u aan de lokale verbinding een hoger gewicht toe dan aan de externe verbinding (klik [hier](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection) voor een configuratievoorbeeld). Wanneer een VNet op meerdere verbindingen het voorvoegsel van het andere VNet ontvangt, krijgt de verbinding met het hoogste gewicht de voorkeur om verkeer te verzenden dat voor dat voorvoegsel is bestemd.

![Oplossing ExpressRoute casus 3: Meer gewicht toewijzen aan lokale verbinding](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Als u meerdere ExpressRoute-circuits hebt, kunt u de routering vanuit VNet naar uw on-premises netwerk ook beïnvloeden door het gewicht van een verbinding te configureren in plaats van AS PATH aan het begin toe te voegen, een techniek die in het tweede scenario hierboven is beschreven. Wanneer er wordt bepaald hoe het verkeer moet worden verzonden, wordt er voor elk voorvoegsel altijd eerst gekeken naar het gewicht van de verbinding en dan pas naar de AS PATH-lengte.
>
>
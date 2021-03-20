---
title: 'Azure ExpressRoute: ontwerpen voor hoge Beschik baarheid'
description: Deze pagina bevat architectuur aanbevelingen voor hoge Beschik baarheid tijdens het gebruik van Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 3602c3944e8731263fbb55f024c276783950329f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202358"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Ontwerpen voor hoge Beschik baarheid met ExpressRoute

ExpressRoute is ontworpen voor hoge Beschik baarheid om de communicatie van particuliere netwerken naar micro soft-resources te verzorgen. Met andere woorden: er is geen Single Point of Failure in het pad ExpressRoute in het micro soft-netwerk. Om de beschik baarheid te maximaliseren, moeten de klant en het service provider segment van uw ExpressRoute-circuit ook worden ontworpen voor hoge Beschik baarheid. In dit artikel kijken we eerst naar de overwegingen voor de netwerk architectuur voor het bouwen van robuuste netwerk connectiviteit met behulp van een ExpressRoute. vervolgens bekijken we de functies voor het afstemmen van de hoge Beschik baarheid van uw ExpressRoute-circuit.

>[!NOTE]
>De concepten die in dit artikel worden beschreven, zijn ook van toepassing wanneer een ExpressRoute-circuit wordt gemaakt onder een virtueel WAN of daarbuiten.
>

## <a name="architecture-considerations"></a>Architectuur overwegingen

In de volgende afbeelding ziet u de aanbevolen manier om verbinding te maken met behulp van een ExpressRoute-circuit voor het maximaliseren van de beschik baarheid van een ExpressRoute-circuit.

 [![1]][1]

Voor maximale Beschik baarheid is het essentieel om de redundantie van het ExpressRoute-circuit in het volledige netwerk te hand haven. Met andere woorden, u moet de redundantie binnen uw on-premises netwerk behouden en de redundantie niet in uw netwerk van de service provider vertasten. Het onderhoud van redundantie op het minimale impliceert het voor komen van één punt van netwerk fouten. Met redundante voeding en koeling voor de netwerk apparaten kunt u de maximale Beschik baarheid nog verder verbeteren.

### <a name="first-mile-physical-layer-design-considerations"></a>Ontwerp overwegingen voor de fysieke laag van de eerste mijl

 Als u de primaire en secundaire verbindingen van een ExpressRoute-circuit op dezelfde locatie van de klant (CPE) beëindigt, wordt de hoge Beschik baarheid in uw on-premises netwerk in gevaar. Als u de primaire en secundaire verbindingen configureert via dezelfde poort van een CPE (door de twee verbindingen in verschillende Subinterfaces te beëindigen of door de twee verbindingen in het partner netwerk samen te voegen), dwingt u de partner ook af om hoge Beschik baarheid te bemachtigen voor het netwerk segment. Deze inbreuk wordt geïllustreerd in de volgende afbeelding.

[![2]][2]

Als u daarentegen de primaire en secundaire verbindingen van een ExpressRoute-circuit op verschillende geografische locaties beëindigt, kunt u de netwerk prestaties van de connectiviteit in gevaar brengen. Als verkeer actief wordt verdeeld over de primaire en de secundaire verbindingen die op verschillende geografische locaties worden beëindigd, zou een potentieel aanzienlijk verschil in de netwerk latentie tussen de twee paden resulteren in een suboptimale netwerk prestaties. 

Zie [ontwerpen voor herstel na nood gevallen met ExpressRoute][DR]voor geo-redundante ontwerp overwegingen.

### <a name="active-active-connections"></a>Actief-actieve verbindingen

Micro soft Network is geconfigureerd voor het uitvoeren van de primaire en secundaire verbindingen van ExpressRoute-circuits in de modus actief-actief. Via uw route advertenties kunt u echter de redundante verbindingen van een ExpressRoute-circuit afdwingen in de modus actief-passief. Het adverteren van meer specifieke routes en BGP als voor aanstaande paden zijn de algemene technieken die worden gebruikt om één pad boven de andere te maken.

Om hoge Beschik baarheid te verbeteren, is het raadzaam om zowel de verbindingen van een ExpressRoute-circuit in de modus actief-actief te laten worden uitgevoerd. Als u de verbindingen in de modus actief-actief laat, wordt door micro soft Network Load het verkeer over de verbindingen per stroom verdeeld.

Het uitvoeren van de primaire en secundaire verbindingen van een ExpressRoute-circuit in Active-passieve modus bevindt zich het risico van beide verbindingen die mislukken na een fout in het actieve pad. De veelvoorkomende oorzaken voor het mislukken van de overschakeling van het niet actief beheren van de passieve verbinding en passieve verbinding die verouderde routes adverteren.

Als u de primaire en secundaire verbindingen van een ExpressRoute-circuit in de modus actief-actief uitvoert, worden er bij een ExpressRoute-verbindings fout alleen de helft van de stromen uitgevoerd en wordt deze omgeleid. De modus actief-actief helpt de gemiddelde tijd om te herstellen (MTTR) aanzienlijk te verbeteren.

### <a name="nat-for-microsoft-peering"></a>NAT voor micro soft-peering 

Micro soft-peering is ontworpen voor communicatie tussen open bare eind punten. On-premises privé-eind punten zijn dus netwerk adressen vertaald (gecommuniceerd) met het open bare IP-adres van de klant of het partner netwerk voordat ze communiceren via micro soft-peering. Ervan uitgaande dat u de primaire en secundaire verbindingen in de modus actief-actief gebruikt, waar en hoe u NAT een invloed heeft op hoe snel u een fout in een van de ExpressRoute-verbindingen herstelt. Er worden twee verschillende NAT-opties geïllustreerd in de volgende afbeelding:

[![3]][3]

In de optie 1 wordt NAT toegepast na het splitsen van het verkeer tussen de primaire en secundaire verbindingen van de ExpressRoute. Om aan de stateful-vereisten van NAT te voldoen, worden onafhankelijke NAT-Pools gebruikt tussen de primaire en secundaire apparaten, zodat het retour verkeer zou aankomen op hetzelfde edge-apparaat via de stroom egressed.

In de optie 2 wordt een gemeen schappelijke NAT-groep gebruikt voordat het verkeer tussen de primaire en secundaire verbindingen van de ExpressRoute wordt gesplitst. Het is belang rijk om het onderscheid te maken dat de gemeen schappelijke NAT-groep vóór het splitsen van het verkeer niet leidt tot een storing in één punt, waardoor de hoge Beschik baarheid wordt gecompromisd.

Met de optie 1, na een ExpressRoute-verbindings fout, wordt de mogelijkheid om de bijbehorende NAT-groep te bereiken, verbroken. Daarom moeten alle verbroken stromen opnieuw worden ingesteld door de TCP-of toepassingslaag na de bijbehorende time-out van het venster. Als een van de NAT-groepen wordt gebruikt voor het uitvoeren van een front-end van een van de on-premises servers en als de bijbehorende connectiviteit mislukt, kan de on-premises servers niet worden bereikt vanuit Azure totdat de verbinding is hersteld.

Overwegende dat met de optie 2 de NAT bereikbaar is, zelfs na een primaire of secundaire verbindings fout. Daarom kan de netwerklaag zelf de pakketten omleiden en sneller herstel uitvoeren na de fout. 

> [!NOTE]
> Als u NAT-optie 1 (onafhankelijke NAT-Pools voor primaire en secundaire ExpressRoute-verbindingen) gebruikt en een poort van een IP-adres uit een van de NAT-groep aan een on-premises server toewijst, kan de server niet bereikbaar zijn via het ExpressRoute-circuit wanneer de bijbehorende verbinding mislukt.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Functies voor het afstemmen van privé-peering

In deze sectie laten we optioneel kijken (afhankelijk van uw Azure-implementatie en hoe gevoelig u MTTR) functies waarmee u de hoge Beschik baarheid van uw ExpressRoute-circuit kunt verbeteren. We gaan de zone-bewuste implementatie van ExpressRoute-gateways voor virtuele netwerken en bidirectionele forwarding-detectie (BFD) bekijken.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>ExpressRoute virtuele netwerk gateways voor beschikbaarheids zone

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u ervoor kiest om een zone-redundante Azure IaaS-implementatie te implementeren, wilt u wellicht ook zone-redundante virtuele netwerk gateways configureren die ExpressRoute persoonlijke peering beëindigen. Zie [over zone-redundante virtuele netwerk gateways in azure-beschikbaarheidszones][zone redundant vgw]voor meer informatie. Zie [een zone-redundante virtuele netwerk gateway maken in azure-beschikbaarheidszones om een][conf zone redundant vgw]zone-redundante virtuele netwerk gateway te configureren.

### <a name="improving-failure-detection-time"></a>Fout detectie tijd verbeteren

ExpressRoute biedt ondersteuning voor BFD via privé-peering. BFD vermindert de detectie tijd van de fout over het laag 2-netwerk tussen micro soft Enter prise Edge (Msee's) en hun BGP-neighbors op de on-premises zijde van ongeveer drie minuten (standaard) tot minder dan een seconde. De detectie tijd van de snelle fout helpt hastening fout herstel te herstellen. Zie [Configure BFD over ExpressRoute][BFD]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een hoge Beschik baarheid van een ExpressRoute-circuit connectiviteit kunt ontwerpen. Een ExpressRoute-circuit peering-punt is vastgemaakt aan een geografische locatie en kan daarom worden beïnvloed door een onherstelbare fout die van invloed is op de volledige locatie. 

Zie [ontwerpen voor herstel na nood gevallen met persoonlijke ExpressRoute-peering][DR]voor het ontwerp van overwegingen bij het bouwen van geo-redundante netwerk connectiviteit met micro soft backbone. Dit kan van invloed zijn op een hele regio.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "aanbevolen manier om verbinding te maken met ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "meest optimale connectiviteit voor laatste mijl"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-opties"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
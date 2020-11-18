---
title: Azure Standard Load Balancer en Beschikbaarheidszones
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met Azure Standard Load Balancer en Beschikbaarheidszones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: fca6b40f4bd871041d14d119eb44e8366d8562ee
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700457"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer van het type Standard en beschikbaarheidszones

Azure Standard Load Balancer ondersteunt scenario's met beschikbaarheids zones. U kunt standaard load balancer gebruiken om de beschik baarheid in het hele scenario te verg Roten door resources uit te lijnen met en distributie over zones. Beschikbaarheids zones in combi natie met standaard load balancer zijn een expansieve, en een flexibele set functies die veel verschillende scenario's kunnen maken.  Lees dit document voor meer informatie over deze [concepten](#concepts) en [ontwerp richtlijnen](#design)voor het basis scenario.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Beschikbaarheidszones concepten toegepast op Load Balancer

Een load balancer neemt zone configuratie over van de onderdelen: 

* Front-end
* Regels
* Definitie van back-end-groep

In de context van beschikbaarheids zones worden het gedrag en de eigenschappen van een load balancer regel beschreven als zone-redundante of zonegebonden.  In de context van de load balancer houdt het zone-redundantie altijd in **meerdere zones** en zonegebonden het isoleren van de service naar **één zone**. Een Azure Load Balancer heeft twee typen: openbaar en intern. Beide soorten load balancer ondersteunen zone redundantie en zonegebonden-implementatie.  Beide load balancer typen kunnen verkeer naar behoefte omleiden over zones.

## <a name="frontend"></a>Front-end

Een load balancer frontend is een front-end-IP-configuratie die verwijst naar een openbaar IP-adres of een privé-IP-adres binnen het subnet van een virtueel netwerk. Het vormt het eind punt met gelijke taak verdeling waar uw service wordt weer gegeven.

Een load balancer resource kan regels bevatten met zonegebonden en een zone-redundante front-end. Wanneer een openbaar of particulier IP-adres aan een zone wordt gegarandeerd, kan de zonality (of een gebrek aan) niet worden gewijzigd. Als u de zonality van een openbaar of privé IP-adres wilt wijzigen of weglaten, maakt u de IP-adressen in de juiste zone opnieuw. 

Beschikbaarheids zones hebben geen invloed op de beperkingen voor meerdere frontends. Bekijk [meerdere frontends voor Load Balancer](load-balancer-multivip-overview.md) voor meer informatie over deze mogelijkheid.

### <a name="zone-redundant"></a>Zone-redundant 

In een regio met beschikbaarheids zones kan een standaard-front load balancer-frontend zone-redundant zijn. Meerdere zones kunnen worden gebruikt voor inkomend of uitgaand verkeer in een regio. Dit verkeer wordt geleverd door één IP-adres. DNS-redundantie schema's zijn niet vereist. 

Een enkel IP-adres van de front-end heeft geen zone storingen. Het front-end-IP-adres kan worden gebruikt voor het bereiken van alle leden van de back-end-backend-groep die niet van invloed zijn op de zone. Een of meer beschikbaarheids zones kunnen mislukken en het gegevenspad blijft actief zolang er één zone in de regio in orde is. 

Het IP-adres van de frontend wordt tegelijkertijd bediend door meerdere onafhankelijke infrastructuur implementaties in meerdere beschikbaarheids zones. Nieuwe pogingen of het opnieuw inbrengen van wijzigingen in andere zones die niet worden beïnvloed door de fout in de zone. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Afbeelding: redundante zone load balancer*

### <a name="zonal"></a>Zonegebonden

U kunt ervoor kiezen een front-end te garanderen voor één zone, die wordt aangeduid als een zonegebonden-front- *End*.  Dit scenario betekent dat elke binnenkomende of uitgaande stroom wordt bediend door één zone in een regio.  Uw front-end-shares worden meegezonden met de status van de zone.  Het gegevenspad wordt niet beïnvloed door storingen in andere zones dan waar het is gegarandeerd. U kunt zonegebonden-frontends gebruiken om een IP-adres per beschikbaarheids zone beschikbaar te maken.  

Daarnaast wordt het gebruik van zonegebonden-frontends rechtstreeks voor eind punten met gelijke taak verdeling binnen elke zone ondersteund. U kunt deze configuratie gebruiken voor het weer geven van eind punten met gelijke taak verdeling voor elke zone afzonderlijk te bewaken. Voor open bare eind punten kunt u ze integreren met een product voor DNS-taak verdeling, zoals [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en één DNS-naam gebruiken.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Afbeelding: zonegebonden load balancer*

Als u deze concepten (zone-redundante en zonegebonden voor dezelfde back-end) wilt laten door lopen, bekijkt u [meerdere frontends voor Azure Load Balancer](load-balancer-multivip-overview.md).

Voor een open bare load balancer-frontend voegt u een para meter **zones** toe aan het open bare IP-adres. Naar dit open bare IP-adres wordt verwezen door de frontend-IP-configuratie die wordt gebruikt door de betreffende regel.

Voor een interne load balancer frontend voegt u een para meter **zones** toe aan de interne Load Balancer frontend-IP-configuratie. Met een zonegebonden-front-end wordt een IP-adres in een subnet gegarandeerd naar een specifieke zone.

## <a name="backend"></a>Back-end

Azure Load Balancer werkt met exemplaren van virtuele machines. Deze instanties kunnen zelfstandige, beschikbaarheids sets of virtuele-machine schaal sets zijn. Elke virtuele machine in één virtueel netwerk kan deel uitmaken van de back-end-groep, ongeacht de zone waar de virtuele machine is gegarandeerd.

Als u uw front-end en back-end wilt uitlijnen en garanderen met één zone, plaatst u alleen virtuele machines binnen dezelfde zone in de bijbehorende back-end-pool.

Als u virtuele machines over meerdere zones wilt adresseren, plaatst u virtuele machines van meerdere zones in dezelfde back-endadresgroep. 

Wanneer u schaal sets voor virtuele machines gebruikt, plaatst u een of meer virtuele-machine schaal sets in dezelfde back-endadresgroep. Schaal sets kunnen zich in één of meerdere zones bevinden.

## <a name="outbound-connections"></a>Uitgaande verbindingen

Zone-redundante en zonegebonden zijn van toepassing op [uitgaande verbindingen](load-balancer-outbound-connections.md). Een zone-redundant openbaar IP-adres dat wordt gebruikt voor uitgaande verbindingen, wordt door alle zones bediend. Een openbaar IP-adres van zonegebonden wordt alleen bediend door de zone waarin het wordt gegarandeerd. 

Voor uitgaande verbinding met SNAT-poort toewijzingen blijft de zone storingen ongewijzigd en uw scenario biedt geen uitgaande SNAT-connectiviteit als deze niet wordt beïnvloed door een zone fout. Voor zone fouten kan het nodig zijn dat de verbindingen opnieuw tot stand worden gebracht voor zone-redundante scenario's als verkeer wordt uitgevoerd door een kwets bare zone. Stromen in andere zones dan de betrokken zones worden niet beïnvloed.

Het algoritme voor de toewijzing van de SNAT-poort is hetzelfde met of zonder beschikbaarheids zones.

## <a name="health-probes"></a>Statuscontroles

De bestaande definities voor de Health-tests blijven behouden, zonder beschikbaarheids zones. We hebben echter het status model op een infrastructuur niveau uitgebreid. 

Wanneer u een zone-redundante front-end gebruikt, wordt de interne status controle door de load balancer uitgebreid. Met de load balancer wordt de beschik baarheid van een virtuele machine onafhankelijk van elke zone gecontroleerd en worden de paden voor zones die zijn mislukt zonder tussen komst, afgesloten.  

Andere zones die deze VM kunnen bereiken, kunnen de virtuele machine blijven gebruiken vanaf hun respectieve frontends. Tijdens fout gebeurtenissen kan elke zone verschillende distributies van nieuwe stromen hebben en de algehele status van uw service beveiligen.

## <a name="design-considerations"></a><a name="design"></a> Ontwerp overwegingen

De Load Balancer is flexibel in de context van beschikbaarheids zones. U kunt kiezen om te uitlijnen op zones of zone-redundante voor elke regel. Grotere Beschik baarheid kan de prijs van een verhoogde complexiteit zijn. Ontwerp voor Beschik baarheid voor optimale prestaties.

### <a name="zone-redundancy"></a>Zone-redundantie

Load Balancer maakt het eenvoudig om één IP-adres te hebben als een zone-redundante front-end. Een zone-redundant IP-adres kan worden gebruikt voor een zonegebonden-bron in elke zone.  Het IP-adres kan een of meer zone storingen bewaken, zolang een zone in orde blijft binnen de regio.  In plaats daarvan is een zonegebonden-front-end een verlaging van de service naar een enkele zone en deelt het verspreidings niveau met de betreffende zone.

Zone-redundantie impliceert niet Hitless DataPath of besturings vlak; het is een gegevenslaag. Zone-redundante stromen kunnen alle zones en de stromen van een klant gebruiken om alle zones in een regio te gebruiken. Als er een zone fout optreedt, worden de verkeers stromen met gezonde zones niet beïnvloed. 

Verkeers stromen die gebruikmaken van een zone op het moment van de zone storing, kunnen worden beïnvloed, maar toepassingen kunnen worden hersteld. Verkeer blijft in de in orde zijnde zones binnen de regio na het opnieuw verzenden als Azure is geconvergeerd rond de zone storing.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Grenzen van meerdere zones

Het is belang rijk om te begrijpen dat elke keer dat een service zones kruist, het lot wordt gedeeld met niet één zone, maar mogelijk meerdere zones. Als gevolg hiervan heeft uw service mogelijk geen Beschik baarheid opgedaan via niet-zonegebonden-implementaties.

Vermijd onbedoelde afhankelijkheden voor meerdere zones, waardoor de beschik baarheid wordt gerealiseerd bij het gebruik van beschikbaarheids zones. Als uw toepassing meerdere essentiële onderdelen bevat, moet u ervoor zorgen dat er geen zone meer is.

Eén belang rijk onderdeel van uw toepassing kan van invloed zijn op uw hele toepassing als deze alleen bestaat in een andere zone dan de nagelaten zone (s). Denk na over de herstel bewerking van de zone en hoe uw toepassing convergeert. Krijg inzicht in de manier waarop uw toepassing reageert op defecten delen. Bekijk de belangrijkste punten en gebruik deze voor vragen zoals u in uw specifieke scenario kunt zien.

- Als uw toepassing twee onderdelen heeft:

    * Het IP-adres
    * Virtuele machine met beheerde schijf

Ze worden geconfigureerd in zone 1 en zone 2. Wanneer zone 1 mislukt, wordt uw service niet bewaard. Vermijd zones met zonegebonden-scenario's tenzij u zeker weet dat u een potentieel gevaarlijke fout modus maakt. Dit scenario mag flexibiliteit bieden.

- Als uw toepassing twee onderdelen heeft:

    * Het IP-adres
    * Virtuele machine met beheerde schijf

Ze zijn geconfigureerd voor zone-redundantie en zone 1. Uw service blijft de zone fout van zone 2, zone 3 of beide, tenzij zone 1 is mislukt. U verliest echter een mogelijke reden om de status van uw service te controleren als u er zeker van bent dat u de beschik baarheid van de front-end kunt bereiken.  Overweeg het ontwikkelen van een uitgebreidere status-en capaciteits model.  U kunt zone-redundante en zonegebonden-concepten samen gebruiken om inzicht en beheer baarheid uit te breiden.

- Als uw toepassing twee onderdelen heeft:

    * Zone-redundante load balancer frontend
    * Schaalset voor virtuele machines in meerdere zones in drie zones

Uw resources in zones die niet worden beïnvloed door de fout, zijn beschikbaar. Het kan zijn dat uw service capaciteit tijdens de storing wordt gedegradeerd. Vanuit het oogpunt van een infra structuur kan uw implementatie een of meer zone storingen overleven. In dit scenario worden de volgende vragen gegenereerd:

  - Begrijpt u hoe uw toepassing reageert op fouten en gedegradeerde capaciteit?
  - Moet u in uw service veiligheids maatregelen hebben om zo nodig een failover naar een regio paar af te dwingen?
  - Hoe kunt u een dergelijk scenario bewaken, detecteren en beperken? U kunt standaard load balancer diagnose gebruiken om de bewaking van uw service prestaties te verbeteren. Bedenk wat er beschikbaar is en wat mogelijk moet worden uitgebreid.

- Zones kunnen fouten gemakkelijker te begrijpen en bevatten. Zone storing wijkt af van andere fouten bij time-outs, nieuwe pogingen en uitstel-algoritmen. Azure Load Balancer biedt zone-redundante paden. De load balancer probeert snel, op pakket niveau, in realtime te herstellen. Tijdens het aanbrengen van een storing kunnen er herverzendingen of herinrichtingen optreden. Het is belang rijk om te begrijpen hoe uw toepassing met fouten kan omgaan. Uw taakverdelings schema blijft bewaard, maar u moet de volgende vragen plannen:

  - Als er een storing optreedt in een zone, kent uw service deze fout en als de status verloren gaat, hoe kunt u deze herstellen?
  - Wanneer een zone wordt geretourneerd, weet uw toepassing hoe u veilig kunt convergeren?

Bekijk de [ontwerp patronen voor Azure-Clouds](/azure/architecture/patterns/) om de flexibiliteit van uw toepassing te verbeteren tot fouten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [Standard Load Balancer](./load-balancer-overview.md)
- Meer informatie over het [laden van virtuele machines binnen een zone met behulp van een Standard Load Balancer met een zonegebonden](./quickstart-load-balancer-standard-public-cli.md) -front-end
- Meer informatie over het verdelen [van virtuele machines over zones met behulp van een Standard Load Balancer met een zone-redundante front-end](./quickstart-load-balancer-standard-public-cli.md)
- Meer informatie over [ontwerp patronen voor Azure-Clouds](/azure/architecture/patterns/) voor het verbeteren van de tolerantie van uw toepassing tot fout scenario's.
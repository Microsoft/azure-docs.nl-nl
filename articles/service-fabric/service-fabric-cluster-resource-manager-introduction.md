---
title: Introductie van de Service Fabric cluster resource manager
description: Meer informatie over de Service Fabric cluster resource manager, een manier om de integratie van de services van uw toepassing te beheren.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75563323"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introductie van de Service Fabric cluster resource manager
Traditioneel beheer van IT-systemen of onlineservices het reserveren van specifieke fysieke of virtuele machines aan deze specifieke services of systemen. Services zijn ontworpen als lagen. Er is een ' Web '-laag en een ' gegevens '-of ' opslag-' laag. Toepassingen hebben een laag met berichten die in en uit worden geplaatst, evenals een set computers die is toegewezen aan caching. Aan elke laag of elk type werk belasting zijn specifieke machines toegewezen: aan de Data Base zijn een paar computers toegewezen, de webservers zijn enkele. Als een bepaald type werk belasting heeft geleid tot de computers waarop het was ingeschakeld om te worden uitgevoerd, hebt u meer computers met diezelfde configuratie aan die laag toegevoegd. Niet alle werk belastingen zouden echter eenvoudig kunnen worden uitgebreid, met name met de gegevenslaag, vervangt u doorgaans computers door grotere machines. Simpel. Als een machine is mislukt, is het gedeelte van de totale toepassing met lagere capaciteit uitgevoerd tot de machine kan worden hersteld. Nog steeds redelijk eenvoudig (indien niet noodzakelijkerwijs plezier).

Nu is de wereld wijde service-en software architectuur gewijzigd. Het is veel meer gebruikelijk dat toepassingen een scale-out ontwerp hebben toegepast. Het maken van toepassingen met containers of micro Services (of beide) is gebruikelijk. U kunt nu nog maar een paar machines hebben, maar slechts één exemplaar van een werk belasting. Ze kunnen zelfs op hetzelfde moment meerdere verschillende werk belastingen uitvoeren. U beschikt nu over tien tallen verschillende soorten services (geen gebruik maakt van de resources van een volledige machine), mogelijk honderden verschillende instanties van die services. Elk benoemd exemplaar heeft een of meer instanties of replica's voor hoge Beschik baarheid (HA). Afhankelijk van de grootte van deze werk belastingen en hoe bezet ze zijn, kunt u zich op honderden of duizenden machines aanmelden. 

Plotseling beheren van uw omgeving is niet zo eenvoudig als het beheren van een aantal machines die zijn toegewezen aan enkele typen werk belastingen. Uw servers zijn virtueel en hebben geen namen meer (u hebt mindsets van [huis dieren naar vee](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) na alle). Configuratie is minder over de computers en meer over de services zelf. Hardware die is toegewezen aan één exemplaar van een werk belasting is grotendeels een ding van het verleden. Services zelf zijn kleine gedistribueerde systemen die meerdere kleinere stukjes basishardware beslaan.

Omdat uw app niet langer uit meerdere lagen bestaat, hebt u nu veel meer combi Naties om mee te werken. Wie beslist welke typen werk belastingen kunnen worden uitgevoerd op welke hardware of hoeveel er? Welke werk belastingen werken goed op dezelfde hardware en welke conflicteren er? Wanneer een machine uitvalt hoe weet u wat er op die computer werd uitgevoerd? Wie is verantwoordelijk voor het opnieuw uitvoeren van de werk belasting? Wacht u totdat de (virtuele) machine terugkeert of automatisch een failover uitvoert naar andere computers en de werk belasting blijft gebruiken? Is menselijke tussen komst vereist? Hoe zit het met upgrades in deze omgeving?

Als ontwikkel aars en Opera tors die in deze omgeving handelen, willen we de complexiteit graag helpen beheren. Het maken van een inhuren en proberen om de complexiteit te verbergen met mensen is waarschijnlijk niet het juiste antwoord, dus wat gebeurt er dan?

## <a name="introducing-orchestrators"></a>Kennismaken met Orchestrator
Een ' Orchestrator ' is de algemene term voor een software pakket dat beheerders helpt bij het beheren van deze typen omgevingen. Orchestrator zijn de onderdelen die in aanvragen worden uitgevoerd, zoals ' Ik wil graag vijf exemplaren van deze service in mijn omgeving. ' Ze proberen de omgeving te laten overeenkomen met de gewenste status, ongeacht wat er gebeurt.

Orchestrator (niet voor mensen) zijn wat actie ondernemen wanneer een machine uitvalt of een werk belasting om onverwachte redenen wordt beëindigd. De meeste Orchestrator doen meer dan alleen met fouten. Andere functies die nodig zijn voor het beheren van nieuwe implementaties, het afhandelen van upgrades en het oplossen van Resource verbruik en governance. Alle Orchestrator is fundamenteel voor het onderhouden van de gewenste configuratie status in de omgeving. U wilt een Orchestrator kunnen vertellen wat u wilt en het zware werk doen. Aurora op Mesos, docker Data Center/docker Swarm, Kubernetes en Service Fabric zijn alle voor beelden van Orchestrator. Deze orchestrator is actief ontwikkeld om te voldoen aan de behoeften van echte workloads in productie omgevingen. 

## <a name="orchestration-as-a-service"></a>Indeling als een service
Cluster resource manager is het systeem onderdeel dat verantwoordelijk is voor de integratie van Service Fabric. De taak van cluster resource manager wordt onderverdeeld in drie delen:

1. Regels afdwingen
2. Uw omgeving optimaliseren
3. Hulp bij andere processen

### <a name="what-it-isnt"></a>Wat het niet is
In toepassingen in de traditionele N-laag is er altijd een [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Dit was meestal een netwerk Load Balancer (NLB) of een toepassings Load Balancer (ALB), afhankelijk van waar het zich in de netwerk stack bevindt. Sommige load balancers zijn gebaseerd op hardware, zoals F5's BigIP-aanbieding, andere zijn software, zoals de NLB van micro soft. In andere omgevingen ziet u mogelijk iets zoals HAProxy, nginx, Istio of Envoy in deze rol. In deze architecturen moet taak verdeling worden uitgevoerd om te zorgen dat stateless workloads (ongeveer) dezelfde hoeveelheid werk worden ontvangen. Strategieën voor het gelijkmatig verdelen van de belasting. Sommige balancers verzenden elke andere aanroep naar een andere server. Andere aangelegde sessies die zijn vastgemaakt/persistentie. Geavanceerdere balancers maken gebruik van werkelijke taak verdeling of rapportage om een aanroep te routeren op basis van de verwachte kosten en de huidige belasting van de machine.

Netwerk balancers of bericht routers hebben geprobeerd om ervoor te zorgen dat de web-of worker-laag ruwweg is gebalanceerd. Strategieën voor het verdelen van de gegevenslaag zijn verschillend en zijn afhankelijk van het mechanisme voor gegevens opslag. Het verenigen van de gegevenslaag is afhankelijk van gegevens sharding, caching, beheerde weer gaven, opgeslagen procedures en andere Store-specifieke mechanismen.

Hoewel sommige van deze strategieën interessant zijn, is de Service Fabric cluster resource manager niet net zoals een netwerk load balancer of een cache. Een netwerk Load Balancer balanceert de frontend door verkeer over de front-ends te spreiden. De Service Fabric cluster resource manager heeft een andere strategie. Met Service Fabric worden *Services* naar eigen inzicht verplaatst, waarbij het verkeer of de belasting wordt verwacht. Het kan bijvoorbeeld Services verplaatsen naar knoop punten die momenteel koud zijn omdat de services die er niet veel werk uitmaken. De knoop punten zijn mogelijk koud omdat de aanwezige services zijn verwijderd of elders zijn verplaatst. Een ander voor beeld: cluster resource manager kan ook een service van een machine verwijderen. Mogelijk wordt de computer bijgewerkt of wordt deze overbelast vanwege een piek in het verbruik door de services die erop worden uitgevoerd. Het is ook mogelijk dat de resource vereisten van de service zijn verg root. Als gevolg hiervan zijn er onvoldoende resources op deze machine om deze te kunnen blijven uitvoeren. 

Omdat cluster resource manager verantwoordelijk is voor het verplaatsen van services, bevat het een andere set functies vergeleken met wat u in een netwerk load balancer zou vinden. De reden hiervoor is dat netwerk taakverdeling netwerk verkeer levert aan de locatie van de services, zelfs als die locatie niet ideaal is voor het uitvoeren van de service zelf. De Service Fabric cluster resource manager maakt gebruik van de fundamentele verschillende strategieën om ervoor te zorgen dat de resources in het cluster efficiënt worden gebruikt.

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-architecture.md) voor meer informatie over de architectuur en informatie stroom in het cluster resource manager
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie over metrische gegevens
- Meer informatie over het configureren van Services vindt u op het [configureren van services](service-fabric-cluster-resource-manager-configure-services.md)
- Metrische gegevens zijn de manier waarop de Service Fabric cluster resource manager het verbruik en de capaciteit in het cluster beheert. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over metrische gegevens en hoe u deze kunt configureren
- Cluster resource manager werkt met de beheer mogelijkheden van Service Fabric. Lees [dit artikel](service-fabric-cluster-resource-manager-management-integration.md) voor meer informatie over deze integratie.
- Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.

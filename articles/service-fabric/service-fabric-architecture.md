---
title: Architectuur van Azure Service Fabric
description: In dit artikel wordt de architectuur van Service Fabric beschreven, een platform voor gedistribueerde systemen dat wordt gebruikt voor het bouwen van schaal bare, betrouw bare en eenvoudig te beheren toepassingen voor de Cloud.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76024704"
---
# <a name="service-fabric-architecture"></a>Service Fabric-architectuur

Service Fabric is gebouwd met gelaagde subsystemen. Met deze subsystemen kunt u toepassingen schrijven die:

* Zeer beschikbaar
* Schaalbaar
* Beheer bare
* Testable

In het volgende diagram ziet u de belangrijkste subsystemen van Service Fabric.

![Diagram van Service Fabric architectuur](media/service-fabric-architecture/service-fabric-architecture.png)

In een gedistribueerd systeem is de mogelijkheid om veilig te communiceren tussen knoop punten in een cluster van cruciaal belang. De basis van de stack is het transport subsysteem waarmee beveiligde communicatie tussen knoop punten wordt geboden. Boven het Trans Port-subsysteem bevindt zich het subsysteem Federatie, waarmee de verschillende knoop punten in één entiteit (benoemde clusters) worden geclusterd, zodat Service Fabric fouten kan detecteren, leider kan doen en consistente route ring kunt bieden. Het subsysteem voor betrouw baarheid, belaagd boven op het subsysteem Federatie, is verantwoordelijk voor de betrouw baarheid van Service Fabric Services via mechanismen zoals replicatie, resource beheer en failover. Het subsysteem Federatie ligt ook onder het subsysteem voor hosting en activering, waarmee de levens cyclus van een toepassing op één knoop punt wordt beheerd. Het management-subsysteem beheert de levens cyclus van toepassingen en services. Met het subsysteem test baarheid kunnen ontwikkel aars van toepassingen hun services testen via gesimuleerde fouten vóór en na de implementatie van toepassingen en services in productie omgevingen. Service Fabric biedt de mogelijkheid om service locaties op te lossen via het communicatie subsysteem. De programmeer modellen voor toepassingen die aan ontwikkel aars worden blootgesteld, worden in een laag op deze subsystemen weer gegeven, samen met het toepassings model om hulp middelen in te scha kelen.

## <a name="transport-subsystem"></a>Transport subsysteem

Het Trans Port-subsysteem implementeert een Point-to-Point data gram communicatie kanaal. Dit kanaal wordt gebruikt voor communicatie binnen service Fabric-clusters en communicatie tussen het service Fabric-cluster en clients. Het ondersteunt communicatie patronen met één richting en vragen/antwoorden, die de basis vormen voor het implementeren van broadcast en multi cast in de Federation-laag. Het Trans Port subsysteem beveiligt communicatie met x509-certificaten of Windows-beveiliging. Dit subsysteem wordt intern gebruikt door Service Fabric en is niet rechtstreeks toegankelijk voor ontwikkel aars voor het Program meren van toepassingen.

## <a name="federation-subsystem"></a>Federatie subsysteem

Als u de oorzaak van een set knoop punten in een gedistribueerd systeem wilt hebben, moet u een consistente weer gave van het systeem hebben. Het subsysteem Federatie maakt gebruik van de communicatie primitieven van het Trans Port-subsysteem en hechten aan de verschillende knoop punten in één uniform cluster. Het biedt de primitieve gedistribueerde systemen die nodig zijn voor de andere subsystemen-fout detectie, leider verkiezing en consistente route ring. Het subsysteem Federatie is gebouwd boven op gedistribueerde hash-tabellen met een 128-bits token ruimte. Het subsysteem maakt een ring topologie voor de knoop punten, waarbij aan elk knoop punt in de ring een subset van de token ruimte voor eigendom wordt toegewezen. Voor een fout detectie maakt de laag gebruik van een lease mechanisme op basis van harten en arbitrage. Het subsysteem Federatie zorgt ook voor ingewikkelde deelname-en vertrek protocollen die op elk moment slechts één eigenaar van een token bestaan. Dit biedt een toonaangevend verkiezing en consistente routerings garanties.

## <a name="reliability-subsystem"></a>Betrouwbaarheids subsysteem

Het subsysteem voor betrouw baarheid biedt het mechanisme om de status van een Service Fabric service Maxi maal beschikbaar te maken via het gebruik van de *Replicator*, *failover Manager* en *resource Balancer*.

* De Replicator zorgt ervoor dat de status wijzigingen in de primaire service replica automatisch worden gerepliceerd naar secundaire replica's, waarbij de consistentie tussen de primaire en secundaire replica's in een service replicaset wordt gehandhaafd. De Replicator is verantwoordelijk voor quorum beheer tussen de replica's in de replicaset. Er wordt gecommuniceerd met de failover-eenheid voor het ophalen van de lijst met bewerkingen die moeten worden gerepliceerd en de reconfiguratie-agent geeft deze de configuratie van de replicaset. Deze configuratie geeft aan welke replica's de bewerkingen moeten worden gerepliceerd. Service Fabric biedt een standaard-Replicator, de zogeheten infrastructuur Replicator, die door de programmeer model-API kan worden gebruikt om de service status Maxi maal beschikbaar en betrouwbaar te maken.
* De Failover Manager zorgt ervoor dat wanneer knoop punten worden toegevoegd aan of verwijderd uit het cluster, de belasting automatisch opnieuw wordt gedistribueerd over de beschik bare knoop punten. Als een knoop punt in het cluster uitvalt, zal het cluster automatisch de service replica's opnieuw configureren om de beschik baarheid te behouden.
* De Resource Manager plaatst service replica's over fout domeinen in het cluster en zorgt ervoor dat alle failover-eenheden operationeel zijn. De Resource Manager verdeelt ook service bronnen in de onderliggende gedeelde groep van cluster knooppunten om optimale gelijkmatige belasting distributie te krijgen.

## <a name="management-subsystem"></a>Management-subsysteem

Het subsysteem voor beheer biedt end-to-end service-en toepassings levenscyclus beheer. Met Power shell-cmdlets en-beheer-Api's kunt u toepassingen inrichten, implementeren, patches uitvoeren en de implementatie ervan ongedaan maken zonder verlies van Beschik baarheid. Het management-subsysteem voert dit uit via de volgende services.

* **Cluster beheer**: dit is de primaire service die samenwerkt met de failover Manager van betrouw baarheid om de toepassingen op de knoop punten te plaatsen op basis van de beperkingen van de service plaatsing. De resource manager in het failover subsysteem zorgt ervoor dat de beperkingen nooit worden verbroken. De Cluster beheerder beheert de levens cyclus van de toepassingen van het inrichten tot het ongedaan maken van de inrichting. Het wordt geïntegreerd met de Health Manager om ervoor te zorgen dat de beschik baarheid van de toepassing niet tijdens de upgrades van een semantisch ziekte perspectief verloren gaat.
* **Health Manager**: met deze service kan de status van toepassingen, services en cluster entiteiten worden gecontroleerd. Cluster entiteiten (zoals knoop punten, service partities en replica's) kunnen status gegevens rapporteren, die vervolgens worden geaggregeerd in de gecentraliseerde Health Store. Deze status informatie biedt een algemene moment opname van een bepaald tijdstip van de services en knoop punten die over meerdere knoop punten in het cluster worden verdeeld, zodat u eventuele corrigerende maat regelen kunt nemen. Met Health query-Api's kunt u een query uitvoeren op de status gebeurtenissen die zijn gerapporteerd aan het subsysteem Health. De Health query-Api's retour neren de onbewerkte status gegevens die zijn opgeslagen in de Health Store of de geaggregeerde, geïnterpreteerde status gegevens voor een specifieke cluster entiteit.
* **Image Store**: deze service biedt opslag en distributie van de binaire bestanden van de toepassing. Deze service biedt een eenvoudige gedistribueerde bestands opslag waar de toepassingen naar worden geüpload en gedownload.

## <a name="hosting-subsystem"></a>Hosting subsysteem

De Cluster beheerder informeert het hosting subsysteem (dat wordt uitgevoerd op elk knoop punt) welke services moeten worden beheerd voor een bepaald knoop punt. Het hosting subsysteem beheert vervolgens de levens cyclus van de toepassing op dat knoop punt. Er wordt gecommuniceerd met de betrouw baarheid en status onderdelen om ervoor te zorgen dat de replica's goed worden geplaatst en in orde zijn.

## <a name="communication-subsystem"></a>Communicatie subsysteem

Dit subsysteem biedt betrouw bare berichten binnen het cluster en de service detectie via de naamgevings service. Met de naamgevings service worden service namen omgezet in een locatie in het cluster en kunnen gebruikers service namen en-eigenschappen beheren. Met behulp van de naamgevings service kunnen clients veilig communiceren met een wille keurig knoop punt in het cluster om een service naam op te lossen en meta gegevens van de service te verkrijgen. Gebruikers van Service Fabric kunnen met behulp van een eenvoudige naamgevings client-API services en clients ontwikkelen waarmee de huidige netwerk locatie kan worden opgelost, ondanks de dynamiek van het knoop punt of het opnieuw aanpassen van het cluster.

## <a name="testability-subsystem"></a>Subsysteem voor test baarheid

Test baarheid is een suite met hulpprogram ma's die speciaal is ontworpen voor het testen van services die zijn gebouwd op Service Fabric. Met de hulpprogram ma's kunnen ontwikkel aars eenvoudig zinvolle fouten opleggen en test scenario's uitvoeren om de talrijke statussen en overgangen te controleren die gedurende de levens duur van een service worden ervaren, op een gecontroleerde en veilige manier. Test baarheid biedt ook een mechanisme voor het uitvoeren van langere tests die verschillende mogelijke storingen kunnen herhalen zonder dat de beschik baarheid verloren gaat. Dit biedt u een test-in-productie omgeving.

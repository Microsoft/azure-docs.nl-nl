---
title: Azure Service Fabric-gebeurtenissen
description: Meer informatie over de Service Fabric gebeurtenissen die uit het vak zijn gegeven om u te helpen bij het bewaken van uw Azure Service Fabric-cluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451730"
---
# <a name="service-fabric-events"></a>Service Fabric-gebeurtenissen 

Het Service Fabric-platform schrijft diverse gestructureerde gebeurtenissen voor belang rijke operationele activiteiten binnen uw cluster. Dit bereik van het cluster wordt bijgewerkt naar de beslissingen over het plaatsen van replica's. Elke gebeurtenis die Service Fabric beschikbaar maakt, wordt toegewezen aan een van de volgende entiteiten in het cluster:
* Cluster
* Toepassing
* Service
* Partitie
* Replica 
* Container

Voor een volledige lijst van gebeurtenissen die worden weer gegeven in de [lijst met Service Fabric gebeurtenissen van](service-fabric-diagnostics-event-generation-operational.md)het platform.

Hier volgen enkele voor beelden van scenario's waarvan u gebeurtenissen in uw cluster zou moeten zien. 
* Gebeurtenissen voor levens cyclus van knoop punt: als knoop punten worden weer gegeven, kunt u deze uitbreiden, schalen, opnieuw opstarten en activeren/deactiveren. deze gebeurtenissen zijn zichtbaar in de vorm van wat er is gebeurd en helpt u te bepalen of er iets mis is met de computer zelf, of dat er een API is aangeroepen die via SF was om de status van een knoop punt te wijzigen.
* Cluster upgrade: als uw cluster wordt geüpgraded (SF-versie of configuratie wijziging), ziet u dat de upgrade is gestart, worden alle upgrade domeinen door lopen en worden voltooid (of teruggedraaid). 
* Toepassings upgrades: net als bij cluster upgrades is er een uitgebreide set gebeurtenissen die de upgrade doorloopt. Deze gebeurtenissen kunnen nuttig zijn om te begrijpen wanneer een upgrade is gepland, de huidige status van een upgrade en de algehele volg orde van de gebeurtenissen. Dit is handig om te zien welke upgrades zijn geïmplementeerd en of een terugdraai bewerking is geactiveerd.
* Toepassings-en service-implementatie/verwijderen: er zijn gebeurtenissen voor elke toepassing, service en container die worden gemaakt of verwijderd, en die handig zijn bij het schalen in of uit, bijvoorbeeld het aantal replica's verhogen
* Partitie verplaatsen (herconfiguratie): wanneer een stateful partitie een herconfiguratie doorloopt (een wijziging in de replicaset), wordt een gebeurtenis vastgelegd in het logboek. Dit is handig als u wilt weten hoe vaak uw partitie replicaset wordt gewijzigd of mislukt, of dat het knoop punt op elk gewenst moment de primaire replica uitvoert.
* Chaos-gebeurtenissen: wanneer u de [chaos](service-fabric-controlled-chaos.md) -service van service Fabric gebruikt, ziet u gebeurtenissen elke keer dat de service wordt gestart of gestopt, of wanneer deze een fout in het systeem injecteert.
* Status gebeurtenissen: Service Fabric alle status gebeurtenissen weer gegeven wanneer een waarschuwing of fout status rapport wordt gemaakt, of een entiteit wordt teruggestuurd naar de status OK of het status rapport verloopt. Deze gebeurtenissen zijn zeer nuttig om historische status statistieken voor een entiteit bij te houden. 

## <a name="how-to-access-events"></a>Toegang tot gebeurtenissen

Er zijn een aantal verschillende manieren om Service Fabric gebeurtenissen toegankelijk te maken:
* De gebeurtenissen worden geregistreerd via de standaard kanalen, zoals ETW/Windows-gebeurtenis logboeken en kunnen worden gevisualiseerd door elk bewakings programma dat ondersteuning biedt voor deze gegevens, zoals Azure Monitor Logboeken. Clusters die zijn gemaakt in de portal, hebben standaard de diagnostische gegevens ingeschakeld en hebben de Windows Azure Diagnostics-agent de gebeurtenissen naar Azure Table Storage verzenden, maar u moet deze wel integreren met uw log Analytics-resource. Meer informatie over het configureren van de [Azure Diagnostics-agent](service-fabric-diagnostics-event-aggregation-wad.md) voor het wijzigen van de diagnostische configuratie van uw cluster om meer logboeken of prestatie meter items op te halen en de integratie van de [Azure monitor logboeken](service-fabric-diagnostics-event-analysis-oms.md)
* De rest Api's van de Event Store-service waarmee u rechtstreeks query's kunt uitvoeren op het cluster of via de Service Fabric-client bibliotheek. Zie [query Event Store-api's voor cluster gebeurtenissen](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het bewaken van [het cluster en het platform](service-fabric-diagnostics-event-generation-infra.md).
* Meer informatie over het event Store service- [Event Store service-overzicht](service-fabric-diagnostics-eventstore.md)

---
title: Gebeurtenis archief van Azure Service Fabric
description: Meer informatie over de Event Store van Azure Service Fabric, een manier om de status van een cluster of workloads op elk gewenst moment te begrijpen en te controleren.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: 0b6258e160794eaf7d0c05775f8fd3b796e3ba47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91354816"
---
# <a name="eventstore-overview"></a>Overzicht van Event Store

>[!NOTE]
>Vanaf Service Fabric versie 6,4. de Event Store-Api's zijn alleen beschikbaar voor Windows-clusters die alleen op Azure worden uitgevoerd. We werken aan het porteren van deze functionaliteit naar Linux en met de zelfstandige clusters.

## <a name="overview"></a>Overzicht

De Event Store-service is geïntroduceerd in versie 6,2 en is een bewakings optie in Service Fabric. EventStore biedt een manier om op elk moment meer inzicht te krijgen in de status van uw cluster of workloads.
EventStore is een stateful Service Fabric-service die gebeurtenissen van het cluster bijhoudt. De gebeurtenissen worden weergegeven via Service Fabric Explorer, REST en API's. EventStore voert rechtstreeks een query uit op het cluster om diagnostische gegevens op te halen uit een entiteit in uw cluster en moet worden gebruikt voor het volgende:

* Het vaststellen van problemen tijdens de ontwikkeling of het testen, of waar u een bewakingspijplijn zou kunnen gebruiken
* Bevestigen dat beheeracties die u op het cluster uitvoert correct worden verwerkt
* Een 'momentopname' verkrijgen van hoe Service Fabric met een bepaalde entiteit interactie aangaat

![Scherm afbeelding toont het tabblad gebeurtenissen van het deel venster knoop punten met verschillende gebeurtenissen, met inbegrip van een NodeDown-gebeurtenis.](media/service-fabric-diagnostics-eventstore/eventstore.png)

Zie [service Fabric gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md)voor een volledige lijst met gebeurtenissen die beschikbaar zijn in de Event Store.

>[!NOTE]
>Vanaf Service Fabric versie 6,4. de Event Store-Api's en UX zijn algemeen beschikbaar voor Azure Windows-clusters. We werken aan het porteren van deze functionaliteit naar Linux en met de zelfstandige clusters.

De Event Store-service kan worden opgevraagd voor gebeurtenissen die beschikbaar zijn voor elke entiteit en elk entiteits type in uw cluster. Dit betekent dat u een query kunt uitvoeren voor gebeurtenissen op de volgende niveaus:
* Cluster: gebeurtenissen die specifiek zijn voor het cluster zelf (bijvoorbeeld cluster upgrade)
* Knoop punten: alle gebeurtenissen op knooppunt niveau
* Node: gebeurtenissen die specifiek zijn voor één knoop punt, geïdentificeerd door `nodeName`
* Toepassingen: alle gebeurtenissen op toepassings niveau
* Toepassing: gebeurtenissen die specifiek zijn voor een toepassing die wordt geïdentificeerd door `applicationId`
* Services: gebeurtenissen van alle services in uw clusters
* Service: gebeurtenissen van een specifieke service geïdentificeerd door `serviceId`
* Partities: gebeurtenissen van alle partities
* Partitie: gebeurtenissen van een specifieke partitie geïdentificeerd door `partitionId`
* Partitie Replica's: gebeurtenissen van alle replica's/instanties binnen een specifieke partitie geïdentificeerd door `partitionId`
* Partitie replica: gebeurtenissen van een specifieke replica/instantie geïdentificeerd door `replicaId` en `partitionId`

Raadpleeg de [API-naslag](/rest/api/servicefabric/sfclient-index-eventsstore)informatie voor Event Store voor meer informatie over de API.

De Event Store-service biedt ook de mogelijkheid om gebeurtenissen in uw cluster te correleren. Door te kijken naar gebeurtenissen die op hetzelfde moment zijn geschreven van verschillende entiteiten die van invloed kunnen zijn op elkaar, kan de Event Store-service deze gebeurtenissen koppelen aan hulp bij het identificeren van de oorzaken van activiteiten in uw cluster. Als een van uw toepassingen bijvoorbeeld wordt beschadigd zonder dat er wijzigingen zijn aangebracht, kijken de Event Store ook naar andere gebeurtenissen die worden weer gegeven door het platform en kunnen deze met een of-gebeurtenis worden gecorreleerd `Error` `Warning` . Dit helpt bij snellere detectie van fouten en de oorzaak van analyses.

## <a name="enable-eventstore-on-your-cluster"></a>Event Store inschakelen in uw cluster

### <a name="local-cluster"></a>Lokaal cluster

In [fabricSettings.jsin uw cluster](service-fabric-cluster-fabric-settings.md), voegt u EventStoreService als invoeg functie toe en voert u een upgrade van het cluster uit.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-cluster versie 6.5 +
Als uw Azure-cluster wordt bijgewerkt naar versie 6,5 of hoger, wordt Event Store automatisch ingeschakeld op uw cluster. Als u zich wilt afmelden, moet u uw cluster sjabloon bijwerken met het volgende:

* Een API-versie van `2019-03-01` of hoger gebruiken 
* Voeg de volgende code toe aan de sectie eigenschappen in uw cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-cluster versie 6,4

Als u versie 6,4 gebruikt, kunt u uw Azure Resource Manager-sjabloon bewerken om de Event Store-service in te scha kelen. Dit doet u door een [cluster configuratie-upgrade uit](service-fabric-cluster-config-upgrade-azure.md) te voeren en de volgende code toe te voegen. u kunt PlacementConstraints gebruiken om de replica's van de Event Store-service te plaatsen op een specifiek NodeType, bijvoorbeeld een NodeType dat is toegewezen aan de systeem services. De `upgradeDescription` sectie configureert de configuratie-upgrade om een herstart te activeren voor de knoop punten. U kunt de sectie in een andere update verwijderen.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Volgende stappen
* Aan de slag met de Event Store-API: [de Event Store-api's in Azure service Fabric-clusters gebruiken](service-fabric-diagnostics-eventstore-query.md)
* Meer informatie over de lijst met gebeurtenissen die worden aangeboden door Event Store- [service Fabric gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md)
* Overzicht van controle en diagnostische gegevens in Service Fabric [bewaking en diagnostische gegevens voor service Fabric](service-fabric-diagnostics-overview.md)
* De volledige lijst met API-aanroepen weer geven- [event store rest API-verwijzing](/rest/api/servicefabric/sfclient-index-eventsstore)
* Meer informatie over het bewaken van [het cluster en het platform](service-fabric-diagnostics-event-generation-infra.md)bewaken.

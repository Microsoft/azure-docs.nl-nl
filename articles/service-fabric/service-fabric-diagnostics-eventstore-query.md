---
title: Query's uitvoeren voor cluster gebeurtenissen met behulp van de Event Store-Api's
description: Meer informatie over het gebruik van de Azure Service Fabric Event Store-Api's voor het opvragen van platform gebeurtenissen
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bed26227542cbf3ffc13ecc018aef9e659d026e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791625"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Query's uitvoeren op EventStore-API's voor clustergebeurtenissen

In dit artikel wordt beschreven hoe u een query kunt uitvoeren op de Event Store-Api's die beschikbaar zijn in Service Fabric versie 6,2 en hoger. Als u meer wilt weten over de Event Store-service, raadpleegt u het [overzicht van Event Store-Services](service-fabric-diagnostics-eventstore.md). De Event Store-service heeft momenteel alleen toegang tot gegevens voor de afgelopen zeven dagen (dit is gebaseerd op het Bewaar beleid voor diagnostische gegevens van uw cluster).

>[!NOTE]
>De Event Store-Api's zijn GA vanaf Service Fabric versie 6,4 voor alleen Windows-clusters die worden uitgevoerd op Azure.

De Event Store-Api's kunnen rechtstreeks worden geopend via een REST-eind punt of via een programma. Afhankelijk van de query, zijn er verschillende para meters die nodig zijn om de juiste gegevens te verzamelen. Deze para meters omvatten meestal:
* `api-version`: de versie van de Event Store-Api's die u gebruikt
* `StartTimeUtc`: Hiermee definieert u het begin van de periode die u wilt bekijken
* `EndTimeUtc`: einde van de tijds periode

Naast deze para meters zijn er ook optionele para meters beschikbaar, zoals:
* `timeout`: de standaard time-out voor 60 seconden overschrijven voor het uitvoeren van de aanvraag bewerking
* `eventstypesfilter`: Hiermee krijgt u de mogelijkheid om te filteren op specifieke gebeurtenis typen
* `ExcludeAnalysisEvents`: retourneert geen analyse gebeurtenissen. Standaard worden event Store-query's waar mogelijk geretourneerd met analyse gebeurtenissen. Analyse gebeurtenissen zijn rijkere operationele kanaal gebeurtenissen die aanvullende context of informatie bevatten dan een gewone Service Fabric gebeurtenis en meer diepte bieden.
* `SkipCorrelationLookup`: Zoek niet naar mogelijke gecorreleerde gebeurtenissen in het cluster. Standaard zal de Event Store proberen gebeurtenissen in een cluster te correleren en zo mogelijk uw gebeurtenissen aan elkaar koppelen. 

Elke entiteit in een cluster kan query's voor gebeurtenissen zijn. U kunt ook een query uitvoeren op gebeurtenissen voor alle entiteiten van het type. U kunt bijvoorbeeld een query uitvoeren op gebeurtenissen voor een specifiek knoop punt of voor alle knoop punten in uw cluster. De huidige set entiteiten waarvoor u een query kunt uitvoeren voor gebeurtenissen is (met de manier waarop de query wordt gestructureerd):
* Cluster `/EventsStore/Cluster/Events`
* Punt `/EventsStore/Nodes/Events`
* Subknooppuntsleutels `/EventsStore/Nodes/<NodeName>/$/Events`
* Toepassingen `/EventsStore/Applications/Events`
* Modules `/EventsStore/Applications/<AppName>/$/Events`
* Onderzoeksservices `/EventsStore/Services/Events`
* Service `/EventsStore/Services/<ServiceName>/$/Events`
* Partition `/EventsStore/Partitions/Events`
* Partitie `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replica's `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Exacte `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Bij het verwijzen naar een naam van een toepassing of service hoeft de query niet de "Fabric:/" te bevatten. beleids. Als de namen van uw toepassingen of services een '/' bevatten, kunt u deze ook overschakelen op ' ~ ' om de query te laten werken. Als uw toepassing bijvoorbeeld wordt weer gegeven als ' Fabric:/App1/FrontendApp ', worden uw app-specifieke query's gestructureerd als `/EventsStore/Applications/App1~FrontendApp/$/Events` .
>Daarnaast worden status rapporten voor services nu weer gegeven onder de bijbehorende toepassing, zodat u een query uitvoert voor `DeployedServiceHealthReportCreated` gebeurtenissen voor de juiste toepassings entiteit. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Query's uitvoeren op de Event Store via REST API-eind punten

U kunt de Event Store rechtstreeks via een REST-eind punt opvragen door `GET` aanvragen te doen: `<your cluster address>/EventsStore/<entity>/Events/` .

Als u bijvoorbeeld een query wilt uitvoeren voor alle cluster gebeurtenissen tussen `2018-04-03T18:00:00Z` en `2018-04-04T18:00:00Z` , ziet uw aanvraag er als volgt uit:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Dit kan resulteren in het retour neren van geen gebeurtenissen of de lijst met gebeurtenissen die zijn geretourneerd in JSON:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Hier kunnen we zien dat tussen `2018-04-03T18:00:00Z` en `2018-04-04T18:00:00Z` , de eerste upgrade van het cluster is voltooid toen het voor het eerst was, van `"CurrentClusterVersion": "0.0.0.0:"` tot `"TargetClusterVersion": "6.2:1.0"` , in `"OverallUpgradeElapsedTimeInMs": "120196.5212"` .

## <a name="query-the-eventstore-programmatically"></a>Query's uitvoeren op de Event Store via een programma

U kunt de Event Store ook programmatisch opvragen via de Service Fabric- [client bibliotheek](/dotnet/api/overview/azure/service-fabric#client-library).

Zodra u uw Service Fabric-client hebt ingesteld, kunt u een query uitvoeren op gebeurtenissen door de Event Store als volgt te openen: `sfhttpClient.EventStore.<request>`

Hier volgt een voor beeld van een aanvraag voor alle cluster gebeurtenissen tussen `2018-04-03T18:00:00Z` en met `2018-04-04T18:00:00Z` behulp van de `GetClusterEventListAsync` functie.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Hier volgt nog een voorbeeld waarin een query wordt uitgevoerd voor de clusterstatus en alle knooppuntgebeurtenissen in september 2018 en deze worden afgedrukt.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Voorbeeld scenario's en query's

Hier volgen enkele voor beelden van hoe u de Event Store-REST-Api's kunt aanroepen om de status van uw cluster te begrijpen.

*Cluster upgrades:*

Als u wilt zien de laatste keer dat het cluster is geslaagd of de laatste week probeerde te upgraden, kunt u een query uitvoeren op de Api's voor recent voltooide upgrades naar uw cluster, door een query uit te voeren voor de gebeurtenissen "ClusterUpgradeCompleted" in de Event Store: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemen bij cluster upgrades:*

Als er problemen zijn met een recente upgrade van het cluster, kunt u ook een query uitvoeren voor alle gebeurtenissen voor de cluster entiteit. U ziet verschillende gebeurtenissen, zoals het initiëren van upgrades en elk UD waarvoor de upgrade is doorgevoerd. Er worden ook gebeurtenissen weer geven voor het punt waarop de terugdraai actie is gestart en de bijbehorende status gebeurtenissen. Hier volgt de query die u zou gebruiken: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Knooppunt status wijzigingen:*

Gebruik de volgende query om de status wijzigingen van het knoop punt in de afgelopen dagen te bekijken: wanneer knoop punten naar boven of beneden zijn geactiveerd of worden gedeactiveerd (door het platform, de chaos-service of door gebruikers invoer). `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Toepassings gebeurtenissen:*

U kunt ook uw recente toepassings implementaties en-upgrades volgen. Gebruik de volgende query om alle toepassings gebeurtenissen in uw cluster weer te geven: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historische status voor een toepassing:*

Naast het weer geven van toepassings levenscyclus gebeurtenissen, wilt u mogelijk ook historische gegevens bekijken over de status van een bepaalde toepassing. U kunt dit doen door de naam van de toepassing op te geven waarvoor u de gegevens wilt verzamelen. Gebruik deze query om alle gebeurtenissen van de toepassings status op te halen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` . Als u status gebeurtenissen wilt toevoegen die mogelijk zijn verlopen (de TTL (time to Live) is voltooid, voegt u toe `,ApplicationHealthReportExpired` aan het einde van de query om twee typen gebeurtenissen te filteren.

*Historische status voor alle services in ' Mijntoep ':*

Status rapport gebeurtenissen voor services worden momenteel weer gegeven als `DeployedServicePackageNewHealthReport` gebeurtenissen onder de bijbehorende toepassings entiteit. Gebruik de volgende query om te zien hoe uw services zijn uitgevoerd voor ' App1 ': `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Herconfiguratie van partitie:*

Als u wilt zien welke partitie-verplaatsingen er in uw cluster zijn opgetreden, moet u een query uitvoeren voor de `PartitionReconfigured` gebeurtenis. Zo kunt u nagaan welke workloads op specifieke momenten op het knoop punt worden uitgevoerd, bij het vaststellen van problemen in uw cluster. Hier volgt een voor beeld van een query: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos-service:*

Er is een gebeurtenis voor wanneer de chaos-service wordt gestart of gestopt die op het cluster niveau wordt weer gegeven. Gebruik de volgende query om uw recente gebruik van de chaos-service te bekijken: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

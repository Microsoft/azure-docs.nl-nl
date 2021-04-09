---
title: Lijst met Azure Service Fabric-gebeurtenissen
description: Uitgebreide lijst met gebeurtenissen die door Azure Service Fabric worden verschaft om clusters te bewaken.
ms.topic: reference
ms.date: 2/25/2019
ms.openlocfilehash: 3f65d7c3ddec8263ff6f115df9616b48814afc93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628880"
---
# <a name="list-of-service-fabric-events"></a>Lijst met Service Fabric gebeurtenissen 

Service Fabric maakt een primaire set cluster gebeurtenissen beschikbaar om u te informeren over de status van uw cluster als [service Fabric-gebeurtenissen](service-fabric-diagnostics-events.md). Deze zijn gebaseerd op acties die worden uitgevoerd door Service Fabric op uw knoop punten en de cluster-of beheer beslissingen van een cluster eigenaar/-operator. Deze gebeurtenissen zijn toegankelijk via het configureren van een aantal manieren, waaronder het configureren van [Azure monitor logboeken met uw cluster](service-fabric-diagnostics-oms-setup.md)of het uitvoeren van query's op de [Event Store](service-fabric-diagnostics-eventstore.md). Op Windows-computers worden deze gebeurtenissen ingevoerd in het gebeurtenis logboek, zodat u Service Fabric gebeurtenissen in Logboeken kunt zien. 

Hier volgen enkele kenmerken van deze gebeurtenissen
* Elke gebeurtenis is gekoppeld aan een specifieke entiteit in het cluster, bijvoorbeeld toepassing, service, knoop punt, replica.
* Elke gebeurtenis bevat een aantal algemene velden: EventInstanceId, eventname en categorie.
* Elke gebeurtenis bevat velden die de gebeurtenis weer koppelen aan de entiteit waaraan deze is gekoppeld. De gebeurtenis ApplicationCreated heeft bijvoorbeeld velden die de naam van de gemaakte toepassing identificeren.
* Gebeurtenissen zijn zodanig gestructureerd dat ze kunnen worden gebruikt in een groot aantal hulpprogram ma's om verdere analyses uit te voeren. Daarnaast worden relevante gegevens voor een gebeurtenis gedefinieerd als afzonderlijke eigenschappen in plaats van een lange teken reeks. 
* Gebeurtenissen worden geschreven door verschillende subsystemen in Service Fabric worden aangeduid met de bron (taak) hieronder. Meer informatie is beschikbaar op deze subsystemen in [service Fabric architectuur](service-fabric-architecture.md) en [service Fabric technisch overzicht](service-fabric-technical-overview.md).

Hier volgt een lijst met deze Service Fabric gebeurtenissen ingedeeld op entiteit.

## <a name="cluster-events"></a>Clustergebeurtenissen

**Upgrade gebeurtenissen voor cluster**

Meer informatie over cluster upgrades vindt u [hier](service-fabric-cluster-upgrade-windows-server.md).

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Upgraden | Een upgrade van een cluster is gestart | CM | Informatief |
| 29628 | ClusterUpgradeCompleted | Upgraden | Een cluster upgrade is voltooid | CM | Informatief | 
| 29629 | ClusterUpgradeRollbackStarted | Upgraden | Een upgrade van een cluster is gestart om terug te draaien  | CM | Waarschuwing | 
| 29630 | ClusterUpgradeRollbackCompleted | Upgraden | Het terugdraaien van een cluster upgrade is voltooid | CM | Waarschuwing | 
| 29631 | ClusterUpgradeDomainCompleted | Upgraden | Een upgrade domein is bijgewerkt tijdens een cluster upgrade | CM | Informatief | 

## <a name="node-events"></a>Knooppunt gebeurtenissen

**Levenscyclus gebeurtenissen van knoop punt** 

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Het deactiveren van een knoop punt is voltooid | FM | Informatief | 
| 18603 | NodeUp | StateTransition | Het cluster heeft gedetecteerd dat een knoop punt is gestart | FM | Informatief | 
| 18604 | NodeDown | StateTransition | Het cluster heeft gedetecteerd dat een knoop punt is afgesloten. Wanneer een knoop punt opnieuw wordt opgestart, ziet u een NodeDown-gebeurtenis gevolgd door een NodeUp-gebeurtenis |  FM | Fout | 
| 18605 | NodeAddedToCluster | StateTransition |  Er is een nieuw knoop punt aan het cluster toegevoegd en Service Fabric kan toepassingen implementeren op dit knoop punt | FM | Informatief | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Er is een knoop punt uit het cluster verwijderd. Service Fabric worden toepassingen niet meer geïmplementeerd op dit knoop punt | FM | Informatief | 
| 18607 | NodeDeactivateStarted | StateTransition |  Het deactiveren van een knoop punt is gestart | FM | Informatief | 
| 25621 | NodeOpenSucceeded | StateTransition |  Een knoop punt is gestart | FabricNode | Informatief | 
| 25622 | NodeOpenFailed | StateTransition |  Een knoop punt kan niet worden gestart en kan niet worden toegevoegd aan de ring | FabricNode | Fout | 
| 25624 | NodeClosed | StateTransition |  Een knoop punt is afgesloten | FabricNode | Informatief | 
| 25626 | NodeAborted | StateTransition |  Een knoop punt is niet probleemloos afgesloten | FabricNode | Fout | 

**KTL-logboek registratie knooppunt gebeurtenissen** 

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau |
| --- | --- | --- | --- | --- | --- | 
| 50187 | SharedLogWriteThrottled | Gezondheidszorg | Schrijf bewerkingen naar een gedeeld logboek worden beperkt | KtlLoggerNode | Informatief | 
| 50188 | SharedLogWriteUnthrottled | Gezondheidszorg | Schrijf bewerkingen naar een gedeeld logboek zijn niet-beperkt | KtlLoggerNode | Informatief | 

## <a name="application-events"></a>Toepassings gebeurtenissen

**Toepassings levenscyclus gebeurtenissen**

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Duur | Er is een nieuwe toepassing gemaakt | CM | Informatief | 
| 29625 | ApplicationDeleted | Duur | Een bestaande toepassing is verwijderd | CM | Informatief | 
| 23083 | ApplicationProcessExited | Duur | Een proces binnen een toepassing is afgesloten | Hosting | Informatief | 

**Toepassings upgrade gebeurtenissen**

Meer informatie over toepassings upgrades vindt u [hier](service-fabric-application-upgrade.md).

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgraden | Een toepassings upgrade is gestart | CM | Informatief | 
| 29622 | ApplicationUpgradeCompleted | Upgraden | Een toepassings upgrade is voltooid | CM | Informatief | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgraden | Het terugdraaien van een toepassings upgrade is gestart |CM | Waarschuwing | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgraden | Het terugdraaien van een toepassings upgrade is voltooid | CM | Waarschuwing | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgraden | Een upgrade domein is bijgewerkt tijdens een toepassings upgrade | CM | Informatief | 

## <a name="service-events"></a>Service gebeurtenissen

**Service levenscyclus gebeurtenissen**

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Duur | Er is een nieuwe service gemaakt | FM | Informatief | 
| 18658 | ServiceDeleted | Duur | Er is een bestaande service verwijderd | FM | Informatief | 

## <a name="partition-events"></a>Partitie gebeurtenissen

**Partities verplaatsen**

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Duur | De herconfiguratie van een partitie is voltooid | RA | Informatief | 

## <a name="replica-events"></a>Replica gebeurtenissen

**Gebeurtenissen voor de levens cyclus van replica's**

| Gebeurtenis | Name | Categorie | Beschrijving |Bron (taak) | Niveau |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Duur | Betrouw bare woorden lijst is geopend | DistributedDictionary | Informatief |
| 61702 | ReliableDictionaryClosed | Duur | Betrouw bare woorden lijst is gesloten | DistributedDictionary | Informatief |
| 61703 | ReliableDictionaryCheckpointRecovered | Duur | De betrouw bare woorden lijst heeft het controle punt hersteld | DistributedDictionary | Informatief |
| 61704 | ReliableDictionaryCheckpointFilesSent | Duur | Replica heeft controlepunt bestanden van een betrouw bare woorden lijst verzonden | DistributedDictionary | Informatief |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Duur | Replica heeft controlepunt bestanden van een betrouw bare woorden lijst ontvangen | DistributedDictionary | Informatief |
| 61963 | ReliableQueueOpened | Duur | De betrouw bare wachtrij is geopend | DistributedQueue | Informatief |
| 61964 | ReliableQueueClosed | Duur | Betrouw bare wachtrij is gesloten | DistributedQueue | Informatief |
| 61965 | ReliableQueueCheckpointRecovered | Duur | Het controle punt is hersteld door de betrouw bare wachtrij | DistributedQueue | Informatief |
| 61966 | ReliableQueueCheckpointFilesSent | Duur | Replica heeft controlepunt bestanden van een betrouw bare wachtrij verzonden | DistributedQueue | Informatief |
| 63647 | ReliableQueueCheckpointFilesReceived | Duur | Replica heeft controlepunt bestanden van een betrouw bare wachtrij ontvangen | DistributedQueue | Informatief |
| 63648 | ReliableConcurrentQueueOpened | Duur | Betrouw bare gelijktijdige wachtrij is geopend | ReliableConcurrentQueue | Informatief |
| 63649 | ReliableConcurrentQueueClosed | Duur | Betrouw bare gelijktijdige wachtrij is gesloten | ReliableConcurrentQueue | Informatief |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Duur | Het controle punt is hersteld door de betrouw bare gelijktijdige wachtrij | ReliableConcurrentQueue | Informatief |
| 61687 | TStoreError | Fout | De betrouw bare verzameling heeft een onverwachte fout ontvangen | TStore | Fout |
| 63831 | PrimaryFullCopyInitiated | Duur | Primaire replica heeft een volledige kopie geïnitieerd | TReplicator | Informatief |
| 63832 | PrimaryPartialCopyInitiated | Duur | Primaire replica heeft een gedeeltelijke kopie gestart | TReplicator | Informatief |
| 16831 | BuildIdleReplicaStarted | Duur | Primaire replica is begonnen met het bouwen van een inactieve replica | Replicatie | Informatief |
| 16832 | BuildIdleReplicaCompleted | Duur | Primaire replica heeft het opbouwen van een inactieve replica voltooid | Replicatie | Informatief |
| 16833 | BuildIdleReplicaFailed | Duur | Primaire replica kan geen inactieve replica opbouwen | Replicatie | Waarschuwing |
| 16834 | PrimaryReplicationQueueFull | Gezondheidszorg | De replicatie wachtrij van de primaire replica is vol | Replicatie | Waarschuwing |
| 16835 | PrimaryReplicationQueueWarning | Gezondheidszorg | De replicatie wachtrij van de primaire replica is bijna vol | Replicatie | Waarschuwing |
| 16836 | PrimaryReplicationQueueWarningMitigated | Gezondheidszorg | De replicatie wachtrij van de primaire replica is OK | Replicatie | Informatief |
| 16837 | SecondaryReplicationQueueFull | Gezondheidszorg | De replicatie wachtrij van de secundaire replica is vol | Replicatie | Waarschuwing |
| 16838 | SecondaryReplicationQueueWarning | Gezondheidszorg | De replicatie wachtrij van de secundaire replica is bijna vol | Replicatie | Waarschuwing |
| 16839 | SecondaryReplicationQueueWarningMitigated | Gezondheidszorg | De replicatie wachtrij van de secundaire replica is OK | Replicatie | Informatief |
| 16840 | PrimaryFaultedSlowSecondary | Gezondheidszorg | De primaire replica heeft een fout met een langzame secundaire replica veroorzaakt | Replicatie | Waarschuwing |
| 16841 | ReplicatorFaulted | Gezondheidszorg | Replica is mislukt | Replicatie | Waarschuwing |

## <a name="container-events"></a>Container gebeurtenissen

**Levenscyclus gebeurtenissen van container** 

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Er is een container gestart | Hosting | Informatief | 1 |
| 23075 | ContainerDeactivated | Een container is gestopt | Hosting | Informatief | 1 |
| 23082 | ContainerExited | Een container is afgesloten: Controleer de vlag UnexpectedTermination | Hosting | Informatief | 1 |

## <a name="health-reports"></a>Status rapporten

Het [service Fabric status model](service-fabric-health-introduction.md) biedt een uitgebreide, flexibele en uitbreid bare status evaluatie en rapportage. Als u Service Fabric versie 6,2, worden status gegevens geschreven als platform gebeurtenissen om historische gegevens over de status te bieden. Om het volume van de status gebeurtenissen laag te blijven, schrijven we alleen het volgende als Service Fabric gebeurtenissen:

* Alle `Error` of `Warning` status rapporten
* `Ok` status rapporten tijdens overgangen
* Wanneer een `Error` of `Warning` Health-gebeurtenis verloopt. Dit kan worden gebruikt om te bepalen hoe lang een entiteit is beschadigd

**Gebeurtenissen van het cluster status rapport**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Er is een nieuw cluster status rapport beschikbaar | HM | Informatief | 1 |
| 54437 | ClusterHealthReportExpired | Een bestaand cluster status rapport is verlopen | HM | Informatief | 1 |

**Gebeurtenissen van knooppunt status rapport**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Er is een nieuw knooppunt status rapport beschikbaar | HM | Informatief | 1 |
| 54432 | NodeHealthReportExpired | Een bestaand status rapport van het knoop punt is verlopen | HM | Informatief | 1 |

**Gebeurtenissen van het status rapport van de toepassing**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Er is een nieuw status rapport voor de toepassing gemaakt. Dit geldt voor niet-geïmplementeerde toepassingen. | HM | Informatief | 1 |
| 54426 | DeployedApplicationNewHealthReport | Er is een nieuw geïmplementeerd toepassings status rapport gemaakt | HM | Informatief | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Er is een nieuw geïmplementeerd service status rapport gemaakt | HM | Informatief | 1 |
| 54434 | ApplicationHealthReportExpired | Een bestaand toepassings status rapport is verlopen | HM | Informatief | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Een bestaand status rapport voor geïmplementeerde toepassingen is verlopen | HM | Informatief | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Een bestaand geïmplementeerd service status rapport is verlopen | HM | Informatief | 1 |

**Gebeurtenissen van service status rapport**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Er is een nieuw service status rapport gemaakt | HM | Informatief | 1 |
| 54433 | ServiceHealthReportExpired | Een bestaand service status rapport is verlopen | HM | Informatief | 1 |

**Status rapport gebeurtenissen partitioneren**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Er is een nieuw partitie status rapport gemaakt | HM | Informatief | 1 |
| 54431 | PartitionHealthReportExpired | Een bestaand partitie status rapport is verlopen | HM | Informatief | 1 |

**Gebeurtenissen van het status rapport van de replica**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Er is een stateful replica status rapport gemaakt | HM | Informatief | 1 |
| 54430 | StatelessInstanceNewHealthReport | Er is een nieuw status rapport voor stateless instanties gemaakt | HM | Informatief | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Een bestaand status rapport met stateful replicatie is verlopen | HM | Informatief | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Een bestaand status rapport voor stateless instanties is verlopen | HM | Informatief | 1 |

## <a name="chaos-testing-events"></a>Chaos-test gebeurtenissen 

**Chaos-sessie gebeurtenissen**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Er is een chaos-test sessie gestart | Test baarheid | Informatief | 1 |
| 50023 | ChaosStopped | Een chaos-test sessie is gestopt | Test baarheid | Informatief | 1 |

**Chaos**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Een knoop punt is gepland om opnieuw te worden opgestart als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |
| 50087 | ChaosNodeRestartCompleted | Het opnieuw opstarten van een knoop punt is voltooid als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |

**Chaos toepassings gebeurtenissen**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Een code pakket opnieuw opstarten is gepland tijdens een chaos-test sessie | Test baarheid | Informatief | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Het opnieuw opstarten van het code pakket is voltooid tijdens een chaos-test sessie | Test baarheid | Informatief | 1 |

**Chaos-partitie gebeurtenissen**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Een primaire partitie is ingepland om te worden verplaatst als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Een secundaire partitie is ingepland om te worden verplaatst als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | De diep gaande analyse van de primaire partitie is beschikbaar | Test baarheid | Informatief | 1 |

**Chaos-replica gebeurtenissen**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Het opnieuw opstarten van een replica is gepland als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Het verwijderen van een replica is gepland als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Het verwijderen van een replica is voltooid als onderdeel van een chaos-test sessie | Test baarheid | Informatief | 1 |

## <a name="other-events"></a>Andere gebeurtenissen

**Correlatie gebeurtenissen**

| Gebeurtenis | Naam | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Er is een correlatie gedetecteerd | Test baarheid | Informatief | 1 |

## <a name="events-prior-to-version-62"></a>Gebeurtenissen vóór versie 6,2

Hier volgt een uitgebreide lijst met gebeurtenissen van Service Fabric vóór versie 6,2.

| Gebeurtenis | Name | Bron (taak) | Niveau |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informatief |
| 25621 | NodeOpenedSuccess | FabricNode | Informatief |
| 25622 | NodeOpenedFailed | FabricNode | Informatief |
| 25623 | NodeClosing | FabricNode | Informatief |
| 25624 | NodeClosed | FabricNode | Informatief |
| 25625 | NodeAborting | FabricNode | Informatief |
| 25626 | NodeAborted | FabricNode | Informatief |
| 29627 | ClusterUpgradeStart | CM | Informatief |
| 29628 | ClusterUpgradeComplete | CM | Informatief |
| 29629 | ClusterUpgradeRollback | CM | Informatief |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informatief |
| 29631 | ClusterUpgradeDomainComplete | CM | Informatief |
| 23074 | ContainerActivated | Hosting | Informatief |
| 23075 | ContainerDeactivated | Hosting | Informatief |
| 29620 | ApplicationCreated | CM | Informatief |
| 29621 | ApplicationUpgradeStart | CM | Informatief |
| 29622 | ApplicationUpgradeComplete | CM | Informatief |
| 29623 | ApplicationUpgradeRollback | CM | Informatief |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informatief |
| 29625 | ApplicationDeleted | CM | Informatief |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informatief |
| 18566 | ServiceCreated | FM | Informatief |
| 18567 | ServiceDeleted | FM | Informatief |

## <a name="next-steps"></a>Volgende stappen

* Bekijk een overzicht van de [Diagnostische gegevens in service Fabric](service-fabric-diagnostics-overview.md)
* Meer informatie over de Event Store in [service Fabric Event Store-overzicht](service-fabric-diagnostics-eventstore.md)
* Uw [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) configuratie wijzigen om meer logboeken te verzamelen
* [Application Insights instellen](service-fabric-diagnostics-event-analysis-appinsights.md) om uw operationele kanaal logboeken weer te geven

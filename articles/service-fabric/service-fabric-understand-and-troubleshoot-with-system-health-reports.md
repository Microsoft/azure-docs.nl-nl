---
title: Problemen met systeemstatusrapporten oplossen
description: Hierin worden de status rapporten beschreven die worden verzonden door Azure Service Fabric onderdelen en hun gebruik voor het oplossen van problemen met clusters of toepassingen
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: 483483746b2cce66588e9481bca7e0de391070b8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625888"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Systeemstatusrapporten gebruiken om fouten op te lossen
Azure Service Fabric-onderdelen bieden systeem status rapporten voor alle entiteiten in het cluster direct uit het vak. Met de [Health Store](service-fabric-health-introduction.md#health-store) worden entiteiten gemaakt en verwijderd op basis van de systeem rapporten. Ook worden deze ingedeeld in een-hiërarchie waarin entiteits interacties worden vastgelegd.

> [!NOTE]
> Meer informatie over gezondheids concepten vindt u in [service Fabric status model](service-fabric-health-introduction.md).
> 
> 

Systeem status rapporten bieden inzicht in de functionaliteit van het cluster en de toepassing, en markeren problemen. Voor toepassingen en services controleert de systeem status rapporten of de entiteiten zijn geïmplementeerd en goed werken vanuit het Service Fabric perspectief. De rapporten bieden geen status controle van de bedrijfs logica van de service of detectie van processen die niet reageren. Gebruikers services kunnen de status gegevens verrijken met informatie die specifiek is voor hun logica.

> [!NOTE]
> Status rapporten die worden verzonden door gebruikers-watchdog zijn alleen zichtbaar *nadat* de systeem onderdelen een entiteit hebben gemaakt. Wanneer een entiteit wordt verwijderd, worden alle bijbehorende status rapporten automatisch door de Health Store verwijderd. Hetzelfde geldt wanneer er een nieuw exemplaar van de entiteit wordt gemaakt. Een voor beeld hiervan is wanneer een nieuwe stateful persistente service replica-instantie wordt gemaakt. Alle rapporten die zijn gekoppeld aan het oude exemplaar, worden verwijderd en opgeschoond vanuit de Store.
> 
> 

De rapporten van systeem onderdelen worden geïdentificeerd door de bron, die begint met het '**systeem '.** 'com.microsoft.intune.mam'. Met watchdog kunnen niet hetzelfde voor voegsel voor hun bronnen worden gebruikt, omdat rapporten met ongeldige para meters worden afgewezen.

Laten we eens kijken naar enkele systeem rapporten om te begrijpen wat ze activeert en om te leren hoe de potentiële problemen die ze vertegenwoordigen, kunnen worden opgelost.

> [!NOTE]
> Service Fabric gaat door met het toevoegen van rapporten over belang rijke voor waarden die inzicht in wat er gebeurt in het cluster en de toepassingen. Bestaande rapporten kunnen worden uitgebreid met meer informatie om het probleem sneller op te lossen.
> 
> 

## <a name="cluster-system-health-reports"></a>Status rapporten van het cluster systeem
De cluster status entiteit wordt automatisch gemaakt in de Health Store. Als alles goed werkt, heeft het geen systeem rapport.

### <a name="neighborhood-loss"></a>Verlies van groepen
**System. Federation** meldt een fout wanneer een groeps verlies wordt gedetecteerd. Het rapport is afkomstig uit afzonderlijke knoop punten en de knoop punt-ID is opgenomen in de naam van de eigenschap. Als er in de hele Service Fabric ring één groep verloren gaat, kunt u doorgaans twee gebeurtenissen verwachten die beide zijden van het hiaat rapport vertegenwoordigen. Als er meer groepen verloren zijn, zijn er meer gebeurtenissen.

In het rapport wordt de time-out voor globale leases opgegeven als time-to-Live (TTL). Het rapport wordt elke helft van de TTL-duur opnieuw verzonden, zolang de voor waarde actief blijft. De gebeurtenis wordt automatisch verwijderd wanneer deze verloopt. Als u het gedrag verwijderen-verlopen hebt, zorgt u ervoor dat het rapport op Health Store de juiste wijze wordt opgeruimd, zelfs als het rapportage knooppunt niet beschikbaar is.

* **SourceId**: System. Federation
* **Eigenschap**: begint met **groep** en bevat knooppunt gegevens.
* **Volgende stappen**: onderzoek waarom de groep is verbroken. Controleer bijvoorbeeld de communicatie tussen cluster knooppunten.

### <a name="rebuild"></a>Opnieuw bouwen

De Failover Manager (FM)-service beheert informatie over de cluster knooppunten. Wanneer FM gegevens verliest en gegevens verlies oploopt, kan niet worden gegarandeerd dat het de meest bijgewerkte informatie over de cluster knooppunten bevat. In dit geval wordt het systeem opnieuw opgebouwd en worden de gegevens van alle knoop punten in het cluster door System.FM verzameld, zodat de status opnieuw kan worden samengesteld. Als gevolg van netwerk-of knooppunt problemen kan het opnieuw samen stellen vastlopen of vastgelopen raken. Dit kan gebeuren met de Failover Manager Master-service (FMM). De FMM is een stateless systeem service waarmee wordt bijgehouden waar alle FMs in het cluster zich bevinden. De primaire FMM is altijd het knoop punt met de ID die het dichtst in de buurt is van 0. Als dat knoop punt wordt verwijderd, wordt een opnieuw opbouwen geactiveerd.
Wanneer een van de voor gaande voor waarden plaatsvindt, wordt **System.fm** of **System. FMM** door een fouten rapport gemarkeerd. Opnieuw opbouwen kan in een van de twee fasen vastzitten:

* **Wachten op verzen** ding: er wordt gewacht op een antwoord op het broadcast bericht van de andere knoop punten.

  * **Volgende stappen**: onderzoeken of er een probleem is met de netwerk verbinding tussen knoop punten.
* **Wachten op knoop punten**: FM/FMM heeft al een broadcast-antwoord ontvangen van de andere knoop punten en wacht op een antwoord van specifieke knoop punten. Het status rapport geeft een lijst van de knoop punten waarvoor de FM/FMM wacht op een antwoord.
   * **Volgende stappen**: de netwerk verbinding tussen de FM-FMM en de weer gegeven knoop punten onderzoeken. Onderzoek elk weer gegeven knoop punt op andere mogelijke problemen.

* **Sourceid**: System.fm of System. FMM
* **Eigenschap**: opnieuw opbouwen.
* **Volgende stappen**: onderzoek de netwerk verbinding tussen de knoop punten en de status van alle specifieke knoop punten die worden vermeld in de beschrijving van het status rapport.

### <a name="seed-node-status"></a>Status van Seed-knoop punt
**System.fm** rapporteert een waarschuwing op cluster niveau als sommige Seed-knoop punten een slechte status hebben. Seed-knoop punten zijn de knoop punten die de beschik baarheid van het onderliggende cluster behouden. Deze knoop punten helpen ervoor te zorgen dat het cluster actief blijft door leases te maken met andere knoop punten en te fungeren als tiebreakers tijdens bepaalde soorten netwerk storingen. Als een meerderheid van de Seed-knoop punten in het cluster niet actief is en deze niet meer wordt teruggebracht, wordt het cluster automatisch afgesloten. 

Een Seed-knoop punt heeft een slechte status als de knooppunt status niet actief, verwijderd of onbekend is.
In het waarschuwings rapport voor de status van het Seed-knoop punt worden alle slechte Seed-knoop punten weer gegeven met gedetailleerde informatie.

* **Sourceid**: System.fm
* **Eigenschap**: SeedNodeStatus
* **Volgende stappen**: als deze waarschuwing in het cluster wordt weer gegeven, volgt u de onderstaande instructies om het probleem op te lossen: voor een cluster met Service Fabric versie 6,5 of hoger service Fabric: als het Seed-knoop punt uitvalt, probeert service Fabric het cluster automatisch te wijzigen in een niet-Seed-knoop punt. Als u dit wilt doen, moet u ervoor zorgen dat het aantal niet-Seed-knoop punten in het primaire knooppunt type groter is dan of gelijk is aan het aantal knoop punten van de lagere seeding. Indien nodig voegt u meer knoop punten toe aan het primaire knooppunt type om dit te doen.
Afhankelijk van de status van het cluster kan het enige tijd duren om het probleem op te lossen. Zodra dit is gebeurd, wordt het waarschuwings rapport automatisch gewist.

Om het waarschuwings rapport te wissen voor Service Fabric zelfstandige cluster, moeten alle Seed-knoop punten in orde zijn. Afhankelijk van waarom Seed-knoop punten niet in orde zijn, moeten er verschillende acties worden uitgevoerd: als het Seed-knoop punt niet beschikbaar is, moeten gebruikers dat Seed-knoop punt omhoog kunnen zetten. Als het Seed-knoop punt wordt verwijderd of onbekend, moet dit Seed-knoop punt [uit het cluster worden verwijderd](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Het waarschuwings rapport wordt automatisch gewist wanneer alle Seed-knoop punten in orde zijn.

Voor een cluster met Service Fabric oudere versie dan 6,5: in dit geval moet het waarschuwings rapport hand matig worden gewist. **Gebruikers moeten ervoor zorgen dat alle Seed-knoop punten in orde zijn voordat het rapport wordt gewist**: als het Seed-knoop punt niet beschikbaar is, moeten gebruikers dat Seed-knoop punt omhoog kunnen zetten. als het Seed-knoop punt wordt verwijderd of onbekend, moet het Seed-knoop punt uit het cluster worden verwijderd.
Nadat alle Seed-knoop punten in orde zijn geworden, gebruikt u de volgende opdracht uit Power shell om [het waarschuwings rapport te wissen](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Certificaat verloopt
**System. FabricNode** meldt een waarschuwing wanneer de certificaten die worden gebruikt door het knoop punt bijna verlopen. Er zijn drie certificaten per knoop punt: **Certificate_cluster**, **Certificate_server** en **Certificate_default_client**. Wanneer de verval datum ten minste twee weken duurt, is de status van het rapport OK. Wanneer de verloop tijd binnen twee weken ligt, is het rapport type een waarschuwing. De TTL van deze gebeurtenissen is oneindig en wordt verwijderd wanneer een knoop punt het cluster verlaat.

* **SourceId**: System. FabricNode
* **Eigenschap**: begint met **certificaat** en bevat meer informatie over het certificaat type.
* **Volgende stappen**: werk de certificaten bij als deze bijna verlopen.

### <a name="load-capacity-violation"></a>Schending van laad capaciteit
De Service Fabric Load Balancer een waarschuwing meldt wanneer een overschrijding van de capaciteit van een knoop punt wordt gedetecteerd.

* **SourceId**: System. PLB
* **Eigenschap**: begint met **capaciteit**.
* **Volgende stappen**: Controleer de gegeven metrische gegevens en Bekijk de huidige capaciteit van het knoop punt.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>De capaciteit van het knoop punt komt niet overeen voor de metrische gegevens voor resource beheer
System. hosting rapporteert een waarschuwing als gedefinieerde knooppunt capaciteit in het cluster manifest groter is dan de capaciteit van het werkelijke knoop punt voor de metrische gegevens van de resource governance (geheugen en CPU-kernen). Er wordt een status rapport weer gegeven wanneer het eerste service pakket dat gebruikmaakt van [resource governance](service-fabric-resource-governance.md) -registers op een opgegeven knoop punt.

* **SourceId**: System. hosting
* **Eigenschap**: **ResourceGovernance**.
* **Volgende stappen**: dit probleem kan een probleem zijn omdat service pakketten niet worden afgedwongen zoals verwacht en [resource governance](service-fabric-resource-governance.md) niet goed werkt. Werk het cluster manifest bij met de juiste knooppunt capaciteit voor deze metrische gegevens of geef ze niet op en laat Service Fabric automatisch beschik bare bronnen detecteren.

## <a name="application-system-health-reports"></a>Status rapporten van het toepassings systeem
System.CM, dat de Cluster Manager-service vertegenwoordigt, is de instantie die informatie over een toepassing beheert.

### <a name="state"></a>Staat
System.CM rapporten als OK wanneer de toepassing is gemaakt of bijgewerkt. Er wordt een melding van de Health Store wanneer de toepassing wordt verwijderd, zodat deze uit de Store kan worden verwijderd.

* **SourceId**: System.cm
* **Eigenschap**: status.
* **Volgende stappen**: als de toepassing is gemaakt of bijgewerkt, moet deze het status rapport van het cluster beheer bevatten. Als dat niet het geval is, controleert u de status van de toepassing door een query uit te geven. Gebruik bijvoorbeeld de Power shell-cmdlet **Get-ServiceFabricApplication-ApplicationName** *applicationnaam*.

In het volgende voor beeld ziet u de status gebeurtenis in de toepassing **Fabric:/WordCount** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Service systeem status rapporten
System.FM, dat de Failover Manager-service vertegenwoordigt, is de instantie die informatie over services beheert.

### <a name="state"></a>Staat
System.FM rapporteert als OK wanneer de service is gemaakt. De entiteit wordt uit het Health Store verwijderd wanneer de service wordt verwijderd.

* **SourceId**: System.fm
* **Eigenschap**: status.

In het volgende voor beeld ziet u de status gebeurtenis op de service **Fabric:/WordCount/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Fout met Service correlatie
**System. PLB** meldt een fout wanneer wordt gedetecteerd dat het bijwerken van een service is gecorreleerd met een andere service die een affiniteits keten maakt. Het rapport wordt gewist wanneer een geslaagde update plaatsvindt.

* **SourceId**: System. PLB
* **Eigenschap**: **ServiceDescription**.
* **Volgende stappen**: Controleer de gecorreleerde Service beschrijvingen.

## <a name="partition-system-health-reports"></a>Systeem status rapporten partitioneren
System.FM, dat de Failover Manager-service vertegenwoordigt, is de instantie die informatie over service partities beheert.

### <a name="state"></a>Staat
System.FM rapporten als OK wanneer de partitie is gemaakt en in orde is. De entiteit wordt uit het Health Store verwijderd wanneer de partitie wordt verwijderd.

Als de partitie minder dan het minimale aantal replica's bevat, wordt er een fout melding weer gegeven. Als de partitie niet kleiner is dan het minimum aantal replica's, maar deze lager is dan het aantal doel replica's, wordt een waarschuwing weer gegeven. Als de partitie zich in quorum verlies bevindt, wordt er een fout melding weer gegeven in System.FM.

Andere belang rijke gebeurtenissen bevatten een waarschuwing wanneer de herconfiguratie langer duurt dan verwacht en wanneer de build langer duurt dan verwacht. De verwachte tijden voor de build en herconfiguratie kunnen worden geconfigureerd op basis van de service scenario's. Als een service bijvoorbeeld een status van terabyte heeft, zoals Azure SQL Database, duurt het maken langer dan voor een service met een kleine hoeveelheid status.

* **SourceId**: System.fm
* **Eigenschap**: status.
* **Volgende stappen**: als de status niet OK is, is het mogelijk dat er geen replica's zijn gemaakt, geopend of gepromoveerd naar primair of secundair. 

Als in de beschrijving quorum verlies wordt beschreven, kunt u het gedetailleerde status rapport controleren voor replica's die niet beschikbaar zijn en ze back-ups maken om de partitie weer online te zetten.

Als in de beschrijving een partitie wordt beschreven die is vastgelopen in de [herconfiguratie](service-fabric-concepts-reconfiguration.md), bevat het status rapport van de primaire replica aanvullende informatie.

Voor andere System.FM-status rapporten zou er een rapport zijn over de replica's of de partitie of service van andere systeem onderdelen. 

In de volgende voor beelden worden enkele van deze rapporten beschreven. 

In het volgende voor beeld ziet u een gezonde partitie:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

In het volgende voor beeld ziet u de status van een partitie die lager is dan het aantal doel replica's. De volgende stap bestaat uit het ophalen van de beschrijving van de partitie, die laat zien hoe deze is geconfigureerd: **MinReplicaSetSize** is drie en **TargetReplicaSetSize** is zeven. Vervolgens wordt het aantal knoop punten in het cluster opgehaald, wat in dit geval vijf is. In dit geval kunnen er geen twee replica's worden geplaatst, omdat het doel aantal replica's hoger is dan het aantal beschik bare knoop punten.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

In het volgende voor beeld ziet u de status van een partitie die is vastgelopen in de herconfiguratie omdat de gebruiker niet het annulerings token in de **RunAsync** -methode heeft nageleefd. Het onderzoeken van het status rapport van een replica die is gemarkeerd als primair (P) kan helpen inzoomen op het probleem.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
In dit status rapport wordt de status weer gegeven van de replica's van de partitie die opnieuw wordt geconfigureerd: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Voor elke replica bevat het status rapport:
- Vorige configuratie functie
- Huidige configuratie functie
- [Replica status](service-fabric-concepts-replica-lifecycle.md)
- Knoop punt waarop de replica wordt uitgevoerd
- Replica-ID

In het geval van het voor beeld is verder onderzoek nodig. Onderzoek de status van elke afzonderlijke replica, te beginnen met de replica's die zijn gemarkeerd als `Primary` en `Secondary` (131482789658160654 en 131482789688598467) in het vorige voor beeld.

### <a name="replica-constraint-violation"></a>Schending van replica beperkingen
**System. PLB** meldt een waarschuwing als er een schending van de replica beperking wordt gedetecteerd en niet alle partitie replica's kunnen worden geplaatst. De rapport Details laten zien welke beperkingen en eigenschappen de replica plaatsing verhinderen.

* **SourceId**: System. PLB
* **Eigenschap**: begint met **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Status rapporten van het replica systeem
**System. ra**, dat het onderdeel reconfiguratie agent vertegenwoordigt, is de instantie voor de replica status.

### <a name="state"></a>Staat
System. RA-rapporten OK wanneer de replica is gemaakt.

* **SourceId**: System. ra
* **Eigenschap**: status.

In het volgende voor beeld ziet u een gezonde replica:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Deze eigenschap wordt gebruikt om waarschuwingen of fouten aan te geven bij het openen van een replica, het sluiten van een replica of het overstappen van een replica van de ene rol naar een andere. Zie voor meer informatie de [levens cyclus van replica's](service-fabric-concepts-replica-lifecycle.md). De fouten kunnen uitzonde ringen zijn die worden veroorzaakt door de API-aanroepen of tijdens de uitvoering van het proces van de servicehost. Als gevolg van de API-aanroepen van C#-code, voegt Service Fabric de uitzonde ring en Stack tracering toe aan het status rapport.

Deze waarschuwingen worden gegenereerd nadat de actie lokaal is een aantal keer opnieuw geprobeerd (afhankelijk van het beleid). Service Fabric de actie opnieuw wordt uitgevoerd tot een maximum drempel. Nadat de maximum drempel waarde is bereikt, kan deze proberen om de situatie te corrigeren. Deze poging kan ervoor zorgen dat deze waarschuwingen worden gewist, omdat de actie op dit knoop punt wordt weer gegeven. Als een replica bijvoorbeeld niet kan worden geopend op een knoop punt, wordt door Service Fabric een status waarschuwing gegenereerd. Als de replica nog steeds niet kan worden geopend, Service Fabric op zelf herstellen. Bij deze actie is het mogelijk dat u dezelfde bewerking probeert uit te voeren op een ander knoop punt. Deze poging zorgt ervoor dat de waarschuwing voor deze replica wordt gewist. 

* **SourceId**: System. ra
* **Eigenschap**: **ReplicaOpenStatus**, **ReplicaCloseStatus** en **ReplicaChangeRoleStatus**.
* **Volgende stappen**: onderzoek de service code of de crash dumps om te bepalen waarom de bewerking mislukt.

In het volgende voor beeld ziet u de status van een replica die wordt gegenereerd `TargetInvocationException` vanuit de open-methode. De beschrijving bevat het punt van de fout, het **IStatefulServiceReplica. Open**, het uitzonderings type **TargetInvocationException** en de stack-tracering.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

In het volgende voor beeld ziet u een replica die voortdurend vastloopt tijdens het sluiten:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Herconfiguratie
Deze eigenschap wordt gebruikt om aan te geven wanneer een replica die een [herconfiguratie](service-fabric-concepts-reconfiguration.md) uitvoert, detecteert dat de herconfiguratie is vastgelopen of vastgelopen. Dit status rapport kan zich op de replica bevindt waarvan de huidige rol primair is, behalve in het geval van een primaire herconfiguratie van wisselen, waarbij het mogelijk is op de replica die wordt gedegradeerd van primair naar actief secundair.

De herconfiguratie kan een van de volgende oorzaken hebben:

- Een actie op de lokale replica, dezelfde replica als de naam die de herconfiguratie uitvoert, wordt niet voltooid. In dit geval kunt u de status rapporten voor deze replica van andere onderdelen, System. RAP of System.RE, extra informatie geven.

- Een actie kan niet worden voltooid op een externe replica. Replica's waarvoor acties in behandeling zijn, worden weer gegeven in het status rapport. Verder onderzoek moet worden uitgevoerd op status rapporten voor deze externe replica's. Er kunnen ook communicatie problemen tussen dit knoop punt en het externe knoop punt optreden.

In zeldzame gevallen kan de herconfiguratie vastlopen vanwege communicatie of andere problemen tussen dit knoop punt en de Failover Manager service.

* **SourceId**: System. ra
* **Eigenschap**: opnieuw configureren.
* **Volgende stappen**: lokale of externe replica's onderzoeken, afhankelijk van de beschrijving van het status rapport.

In het volgende voor beeld ziet u een status rapport waarin een herconfiguratie is vastgelopen op de lokale replica. In dit voor beeld wordt het veroorzaakt door een service die het annulerings token niet benadert.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

In het volgende voor beeld ziet u een status rapport waarbij een herconfiguratie is vastgelopen op een reactie van twee externe replica's. In dit voor beeld zijn er drie replica's in de partitie, met inbegrip van de huidige primaire. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

In dit status rapport ziet u dat de herconfiguratie is vastgelopen tijdens het wachten op een reactie van twee replica's: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Voor elke replica wordt de volgende informatie gegeven:
- Vorige configuratie functie
- Huidige configuratie functie
- [Replica status](service-fabric-concepts-replica-lifecycle.md)
- Knoop punt-ID
- Replica-ID

De blok kering van de herconfiguratie opheffen:
- De **lagere** replica's moeten actief zijn. 
- De **inbuild** -replica's moeten de build en de overgang naar gereed volt ooien.

### <a name="slow-service-api-call"></a>API-aanroep voor langzame service
**System. rap** en **System. Replicator** rapporteren een waarschuwing als een aanroep van de gebruikers Service code langer duurt dan de geconfigureerde tijd. De waarschuwing wordt gewist wanneer de aanroep is voltooid.

* **SourceId**: System. rap of System. Replicator
* **Eigenschap**: de naam van de trage API. De beschrijving bevat meer informatie over het tijdstip waarop de API in behandeling is.
* **Volgende stappen**: onderzoek waarom de oproep langer duurt dan verwacht.

In het volgende voor beeld ziet u de status gebeurtenis van System. RAP voor een betrouw bare service die niet voldoet aan het annulerings token in **RunAsync**:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

De eigenschap en de tekst geven aan welke API is vastgelopen. De volgende stappen voor het uitvoeren van verschillende vastgelopen Api's verschillen. Elke API op de *IStatefulServiceReplica* of *IStatelessServiceInstance* is doorgaans een fout in de service code. In de volgende sectie wordt beschreven hoe deze vertalen naar het [reliable Services model](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: deze waarschuwing geeft aan dat een aanroep van `CreateServiceInstanceListeners` , `ICommunicationListener.OpenAsync` of als overschreven `OnOpenAsync` is vastgelopen.

- **IStatefulServiceReplica. Close** en **IStatefulServiceReplica. abort**: het meest voorkomende geval is een service die het door gegeven annulerings token niet nakomt `RunAsync` . Het kan ook zijn dat `ICommunicationListener.CloseAsync` , of als overschreven, `OnCloseAsync` niet is vastgelopen.

- **IStatefulServiceReplica. ChangeRole (S)** en **IStatefulServiceReplica. ChangeRole (N)**: het meest voorkomende geval is een service die niet voldoet aan het annulerings token dat is door gegeven aan `RunAsync` . In dit scenario is de beste oplossing om de replica opnieuw op te starten.

- **IStatefulServiceReplica. ChangeRole (P)**: het meest voorkomende geval is dat de service geen taak heeft geretourneerd van `RunAsync` .

Andere API-aanroepen die achterblijvend kunnen zijn op de **IReplicator** -interface. Bijvoorbeeld:

- **IReplicator. CatchupReplicaSet**: deze waarschuwing geeft een van de twee dingen aan. Er zijn onvoldoende replica's. Als u wilt zien of dit het geval is, bekijkt u de replica status van de replica's in de partitie of het System.FM status rapport voor een vastgelopen herconfiguratie. Of de replica's zijn geen bevestigings bewerkingen. De Power shell-cmdlet `Get-ServiceFabricDeployedReplicaDetail` kan worden gebruikt om de voortgang van alle replica's te bepalen. Het probleem is afhankelijk van replica's waarvan `LastAppliedReplicationSequenceNumber` de waarde zich achter de primaire waarde bevindt `CommittedSequenceNumber` .

- **IReplicator. BuildReplica ( \<Remote ReplicaId> )**: deze waarschuwing geeft aan dat er een probleem is met het bouw proces. Zie voor meer informatie de [levens cyclus van replica's](service-fabric-concepts-replica-lifecycle.md). Dit wordt mogelijk veroorzaakt door een onjuiste configuratie van het Replicator-adres. Zie [stateful reliable Services configureren](service-fabric-reliable-services-configuration.md) en [resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)voor meer informatie. Het is ook mogelijk dat er een probleem is op het externe knoop punt.

### <a name="replicator-system-health-reports"></a>Replicatie systeem status rapporten
De **replicatie wachtrij is vol:** 
 **System. Replicator** meldt een waarschuwing wanneer de replicatie wachtrij vol is. Op de primaire wordt de replicatie wachtrij doorgaans vol omdat een of meer secundaire replica's langzaam zijn om bewerkingen te bevestigen. Dit gebeurt meestal wanneer de service traag is om de bewerkingen toe te passen. De waarschuwing wordt gewist wanneer de wachtrij niet langer vol is.

* **SourceId**: System. Replicator
* **Eigenschap**: **PrimaryReplicationQueueStatus** of **SecondaryReplicationQueueStatus**, afhankelijk van de replica-rol.
* **Volgende stappen**: als het rapport zich op de primaire lijst bevindt, controleert u de verbinding tussen de knoop punten in het cluster. Als alle verbindingen in orde zijn, kan er ten minste één traag secundair zijn met een hoge latentie van de schijf om bewerkingen toe te passen. Als het rapport op de secundaire staat, controleert u eerst het schijf gebruik en de prestaties van het knoop punt. Controleer vervolgens de uitgaande verbinding van het langzame knoop punt naar de primaire.

**RemoteReplicatorConnectionStatus:** 
 **System. Replicator** op de primaire replica meldt een waarschuwing wanneer de verbinding met een secundaire (externe) Replicator niet in orde is. Het adres van de externe replicator wordt weer gegeven in het bericht van het rapport, waardoor het handiger is om te detecteren of de verkeerde configuratie is door gegeven of dat er netwerk problemen zijn tussen de replicaties.

* **SourceId**: System. Replicator
* **Eigenschap**: **RemoteReplicatorConnectionStatus**.
* **Volgende stappen**: Controleer het fout bericht en controleer of het adres van de externe Replicator juist is geconfigureerd. Als bijvoorbeeld de externe replicator wordt geopend met het Luister adres ' localhost ', is deze niet bereikbaar vanaf de buiten kant. Als het adres correct lijkt, controleert u de verbinding tussen het primaire knoop punt en het externe adres om mogelijke netwerk problemen op te sporen.

### <a name="replication-queue-full"></a>De replicatie wachtrij is vol
**System. Replicator** meldt een waarschuwing wanneer de replicatie wachtrij vol is. Op de primaire wordt de replicatie wachtrij doorgaans vol omdat een of meer secundaire replica's langzaam zijn om bewerkingen te bevestigen. Dit gebeurt meestal wanneer de service traag is om de bewerkingen toe te passen. De waarschuwing wordt gewist wanneer de wachtrij niet langer vol is.

* **SourceId**: System. Replicator
* **Eigenschap**: **PrimaryReplicationQueueStatus** of **SecondaryReplicationQueueStatus**, afhankelijk van de replica-rol.

### <a name="slow-naming-operations"></a>Trage naamgevings bewerkingen
**System. NamingService** rapporteert de status van de primaire replica wanneer een naamgevings bewerking langer duurt dan acceptabel is. Voor beelden van naamgevings bewerkingen zijn [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) of [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Meer methoden vindt u onder FabricClient. Ze kunnen bijvoorbeeld worden gevonden onder [Service Management-methoden](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) of [methoden voor eigenschaps beheer](/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Met de naamgevings service worden service namen omgezet naar een locatie in het cluster. Gebruikers kunnen deze gebruiken om service namen en-eigenschappen te beheren. Het is een Service Fabric gepartitioneerde service. Een van de partities vertegenwoordigt de *eigenaar* van de instantie, die meta gegevens bevat over alle service Fabric namen en services. De namen van de Service Fabric worden toegewezen aan verschillende partities, ook wel partities met de *naam eigenaar* genoemd, waardoor de service uitbreidbaar is. Meer informatie over de [naamgevings service](service-fabric-architecture.md).
> 
> 

Wanneer een naamgevings bewerking langer duurt dan verwacht, wordt de bewerking gemarkeerd met een waarschuwings rapport op de primaire replica van de naamgevings service partitie die de bewerking verzendt. Als de bewerking is voltooid, wordt de waarschuwing gewist. Als de bewerking is voltooid met een fout, bevat het status rapport Details over de fout.

* **SourceId**: System. NamingService
* **Eigenschap**: begint met het voor voegsel '**Duration_**' en identificeert de trage bewerking en de service Fabric naam waarop de bewerking wordt toegepast. Als er bijvoorbeeld een service op naam **Fabric maken:/MyApp/MyService** duurt te lang. de eigenschap is **Duration_AOCreateService. Fabric:/MyApp/MyService**. ' AO ' verwijst naar de rol van de naamgevings partitie voor deze naam en bewerking.
* **Volgende stappen**: Controleer om te zien waarom de naamgevings bewerking mislukt. Elke bewerking kan verschillende hoofd oorzaken hebben. Zo kan de service delete vastzitten. De service is mogelijk vastgelopen omdat de toepassingshost vastloopt op een knoop punt vanwege een gebruikers fout in de service code.

In het volgende voor beeld wordt een service bewerking maken weer gegeven. De bewerking duurde langer dan de geconfigureerde duur. "AO" nieuwe pogingen en verzenden van werk naar "nee" De laatste bewerking met een time-out is voltooid. In dit geval is dezelfde replica primair voor de rollen "AO" en "NO".

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication systeem status rapporten
**System. hosting** is de instantie van geïmplementeerde entiteiten.

### <a name="activation"></a>Activering
System. hosting rapporten als OK wanneer een toepassing is geactiveerd op het knoop punt. Anders wordt er een fout melding weer gegeven.

* **SourceId**: System. hosting
* **Eigenschap**: **Activering**, inclusief de implementatie versie.
* **Volgende stappen**: als de toepassing een slechte status heeft, onderzoekt u waarom de activering is mislukt.

In het volgende voor beeld ziet u een geslaagde activering:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Downloaden
System. hosting meldt een fout als het downloaden van het toepassings pakket mislukt.

* **SourceId**: System. hosting
* **Eigenschap**: **down load**, inclusief de implementatie versie.
* **Volgende stappen**: onderzoek waarom de down load is mislukt op het knoop punt.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage systeem status rapporten
**System. hosting** is de instantie van geïmplementeerde entiteiten.

### <a name="service-package-activation"></a>Activering van service pakket
System. hosting rapporten als OK als de activering van het service pakket op het knoop punt is geslaagd. Anders wordt er een fout melding weer gegeven.

* **SourceId**: System. hosting
* **Eigenschap**: activering.
* **Volgende stappen**: onderzoeken waarom de activering is mislukt.

### <a name="code-package-activation"></a>Activering van code pakket
System. hosting rapporten als OK voor elk code pakket als de activering is geslaagd. Als de activering mislukt, wordt een waarschuwing gerapporteerd zoals geconfigureerd. Als **code package** niet kan worden geactiveerd of beëindigd met een fout die groter is dan de geconfigureerde **CodePackageHealthErrorThreshold**, meldt hosting een fout. Als een service pack meerdere code pakketten bevat, wordt er een activerings rapport voor elk pakket gegenereerd.

* **SourceId**: System. hosting
* **Eigenschap**: gebruikt het voor voegsel **CodePackageActivation** en bevat de naam van het code pakket en het ingangs punt als *CodePackageActivation: CodePackageName: SetupEntryPoint/entry* Point. Bijvoorbeeld **CodePackageActivation: code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Registratie van Service type
System. hosting rapporten als OK als het Service type is geregistreerd. Er wordt een fout melding weer gegeven als de registratie niet op tijd is uitgevoerd, zoals is geconfigureerd met behulp van **ServiceTypeRegistrationTimeout**. Als de runtime is gesloten, is het Service type niet meer geregistreerd bij het knoop punt en wordt er een waarschuwing voor het hosten van het hosting rapport gerapporteerd.

* **SourceId**: System. hosting
* **Eigenschap**: gebruikt het voor voegsel **ServiceTypeRegistration** en bevat de naam van het Service type. Bijvoorbeeld **ServiceTypeRegistration: FileStoreServiceType**.

In het volgende voor beeld ziet u een goed geïmplementeerd service pakket:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Downloaden
System. hosting meldt een fout als het downloaden van het service pakket mislukt.

* **SourceId**: System. hosting
* **Eigenschap**: **down load**, inclusief de implementatie versie.
* **Volgende stappen**: onderzoek waarom de down load is mislukt op het knoop punt.

### <a name="upgrade-validation"></a>Upgrade validatie
System. hosting rapporteert een fout als tijdens de upgrade een validatie mislukt of als de upgrade mislukt op het knoop punt.

* **SourceId**: System. hosting
* **Eigenschap**: gebruikt het voor voegsel **FabricUpgradeValidation** en bevat de upgrade versie.
* **Beschrijving**: verwijst naar de fout die wordt aangetroffen.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Niet-gedefinieerde knooppunt capaciteit voor metrische gegevens van resource governance
System. hosting rapporteert een waarschuwing als knooppunt capaciteit niet is gedefinieerd in het cluster manifest en de configuratie voor automatische detectie is uitgeschakeld. Service Fabric genereert een status waarschuwing wanneer het service pakket die gebruikmaakt van [resource governance](service-fabric-resource-governance.md) -registers op een opgegeven knoop punt.

* **SourceId**: System. hosting
* **Eigenschap**: **ResourceGovernance**.
* **Volgende stappen**: de voorkeurs manier om dit probleem op te lossen is door het cluster manifest te wijzigen om automatische detectie van beschik bare resources in te scha kelen. Een andere manier is om het cluster manifest bij te werken met correct opgegeven knooppunt capaciteit voor deze metrische gegevens.

## <a name="next-steps"></a>Volgende stappen
* [Service Fabric status rapporten weer geven](service-fabric-view-entities-aggregated-health.md)

* [Service status rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)

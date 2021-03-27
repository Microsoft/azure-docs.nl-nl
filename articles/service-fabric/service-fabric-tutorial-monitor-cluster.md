---
title: Een Service Fabric-cluster bewaken in Azure
description: In deze zelfstudie leert u hoe u een cluster kunt bewaken door Service Fabric-gebeurtenissen te bekijken, query's uit te voeren op de EventStore-API's, prestatiemeteritems te controleren en statusrapporten te bekijken.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 3e9c8011647567b6f07423e113f98493d80d8402
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628778"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een Service Fabric-cluster bewaken in Azure

Bewaking en diagnose zijn essentieel voor het ontwikkelen, testen en implementeren van workloads in elke cloudomgeving. Deze zelfstudie is deel twee van een reeks en laat zien hoe u een Service Fabric-cluster kunt bewaken en diagnosticeren met gebeurtenissen, prestatiemeteritems en statusrapporten.   Lees voor meer informatie het overzicht over [clusterbewaking](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) en [infrastructuurbewaking](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Service Fabric-gebeurtenissen bekijken
> * Query's uitvoeren op EventStore-API's voor clustergebeurtenissen
> * Infrastructuur controleren/prestatiemeteritems verzamelen
> * Statusrapporten van het cluster bekijken

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure met behulp van een sjabloon
> * Een cluster bewaken
> * [Een cluster in- of uitschalen](service-fabric-tutorial-scale-cluster.md)
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure PowerShell](/powershell/azure/install-az-ps) of [Azure CLI](/cli/azure/install-azure-cli).
* Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken 
* [Diagnostische verzameling](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) voor het cluster instellen
* De [EventStore-service](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) in het cluster inschakelen
* [Azure Monitor-logboeken en de Log Analytics-agent](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) voor het cluster configureren

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Service Fabric-gebeurtenissen bekijken met behulp van Azure Monitor-logboeken

Met Azure Monitor-logboeken wordt telemetrie vastgelegd, verzameld en geanalyseerd op basis van toepassingen en services die worden gehost in de cloud en worden er analysehulpprogramma's geboden waarmee u de beschikbaarheid en prestaties hiervan kunt maximaliseren. U kunt query's uitvoeren in Azure Monitor-logboeken om meer inzicht te krijgen in wat er in het cluster plaatsvindt en problemen in uw cluster op te lossen.

Als u toegang wilt krijgen tot de Service Fabric Analytics-oplossing, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u de resourcegroep waarin u de oplossing Service Fabric-analyse hebt gemaakt.

Selecteer de resource **ServiceFabric(mysfomsworkspace)** .

In **Overzicht** worden de tegels weergegeven in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, inclusief een voor Service Fabric. Klik op de **Service Fabric**-grafiek om door te gaan naar de oplossing Service Fabric-analyse.

![Schermopname van de Service Fabric-graaf.](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

De volgende afbeelding toont de startpagina van de oplossing Service Fabric-analyse. Deze startpagina bevat een momentopname van wat er in uw cluster gebeurt.

![Service Fabric-oplossing](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Als u diagnostische gegevens hebt ingeschakeld bij het maken van het cluster, kunt u de volgende gebeurtenissen weergeven: 

* [Service Fabric-clustergebeurtenissen](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors-programmeermodelgebeurtenissen](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services-programmeermodelgebeurtenissen](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Naast de kant-en-klare Service Fabric-gebeurtenissen, kunnen ook meer gedetailleerde systeemgebeurtenissen worden verzameld door [de configuratie van de extensie voor diagnostische gegevens](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) bij te werken.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric-gebeurtenissen weergeven, met inbegrip van acties op knooppunten

Klik op de pagina Service Fabric-analyse op de grafiek voor **Clustergebeurtenissen**.  De logboeken voor alle verzamelde systeemgebeurtenissen worden weergegeven. Ter referentie: deze komen van de **WADServiceFabricSystemEventsTable** in het Azure Storage-account. De Reliable Services- en Actors-gebeurtenissen die u hierna ziet, komen ook uit de bijbehorende tabellen.
    
![Query's uitvoeren op het operationele kanaal](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

De query gebruikt de Kusto-querytaal, die u kunt aanpassen om beter af te stemmen op wat u zoekt. Als u bijvoorbeeld alle acties wilt vinden die worden uitgevoerd op knooppunten in het cluster, kunt u de volgende query gebruiken. De onderstaande gebeurtenis-id's vindt u in de [verwijzing voor gebeurtenissen op operationele kanalen](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto is een krachtige querytaal. Hier volgen enkele andere nuttige query's.

Maak een *ServiceFabricEvent*-opzoektabel als een door de gebruiker gedefinieerde functie door de query op te slaan als een functie met de alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Operationele gebeurtenissen retourneren die zijn vastgelegd in het afgelopen uur:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Operationele gebeurtenissen retourneren met EventId == 18604 en EventName == NodeDownOperational:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Operationele gebeurtenissen retourneren met EventId == 18604 en EventName == NodeUpOperational:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

Statusrapporten retourneren met HealthState == 3 (Fout) en extra eigenschappen uit het veld EventMessage extraheren:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Een tijdgrafiek met gebeurtenissen retourneren met de EventId ! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Operationele Service Fabric-gebeurtenissen ophalen die zijn geaggregeerd met de specifieke service en het specifieke knooppunt:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Het aantal Service Fabric-gebeurtenissen op EventId / EventName weergeven met behulp van een query voor verschillende resources:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric-toepassingsgebeurtenissen weergeven

U kunt gebeurtenissen weergeven voor de toepassingen Reliable Services en Reliable Actors die op het cluster zijn geïmplementeerd.  Klik op de pagina Service Fabric-analyse op de grafiek voor **Toepassingsgebeurtenissen**.

Voer de volgende query uit om gebeurtenissen van uw Reliable Services-toepassingen weer te geven:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

U kunt verschillende gebeurtenissen zien wanneer de service `runasync` wordt gestart en voltooid, wat doorgaans gebeurt bij implementaties en upgrades.

![Service Fabric-oplossing Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

U kunt ook gebeurtenissen vinden voor Reliable Service met ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Reliable Actor-gebeurtenissen kunnen op een vergelijkbare manier worden weergegeven:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Als u gedetailleerdere gebeurtenissen voor Reliable Actors wilt configureren, kunt u de `scheduledTransferKeywordFilter` in de configuratie voor de diagnostische extensie in de clustersjabloon wijzigen. Meer informatie over de waarden hiervoor vindt u in de [referentie voor Reliable Actors-gebeurtenissen](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Prestatiemeteritems weergeven met Azure Monitor-logboeken
Als u prestatiemeteritems wilt weergeven, gaat u naar de [Azure-portal](https://portal.azure.com) en de resourcegroep waarin u de oplossing Service Fabric-analyse hebt gemaakt. 

Selecteer de resource **ServiceFabric(mysfomsworkspace)** en selecteer vervolgens **Log Analytics-werkruimte** en **Geavanceerde instellingen**.

Klik op **Data** en klik vervolgens op **Windows-prestatiemeteritems**. Er is een lijst met standaard meteritems die u kunt inschakelen en u kunt ook het interval voor verzameling instellen. U kunt ook [aanvullende prestatiemeteritems toevoegen](service-fabric-diagnostics-event-generation-perf.md) om te verzamelen. In dit [artikel](/windows/desktop/PerfCtrs/specifying-a-counter-path) wordt verwezen naar de juiste indeling. Klik op **Opslaan** en klik vervolgens op **OK**.

Sluit de blade Geavanceerde instellingen en selecteer **Samenvatting van werkruimten** onder de kop **Algemeen**. Voor elk van de oplossingen die zijn ingeschakeld, is er een grafische tegel, met inbegrip van Service Fabric. Klik op de **Service Fabric**-grafiek om door te gaan naar de oplossing Service Fabric-analyse.

Er zijn grafische tegels voor operationeel-kanaalgebeurtenissen en Reliable Services-gebeurtenissen. De grafische weergave van de inkomende gegevens voor de meteritems die u hebt geselecteerd, wordt weergegeven onder **Metrische knooppuntgegevens**. 

Selecteer de grafiek **Metrische gegevens voor containers** om meer details weer te geven. U kunt ook query's uitvoeren op gegevens van prestatiemeteritems, net als bij clustergebeurtenissen, en filteren op de knooppunten, de naam van het prestatiemeteritem en waarden met behulp van de Kusto-querytaal.

## <a name="query-the-eventstore-service"></a>Query's uitvoeren op de EventStore-service
De [EventStore-service](service-fabric-diagnostics-eventstore.md) biedt een manier om op elk moment meer inzicht te krijgen in de status van uw cluster of workloads. EventStore is een stateful Service Fabric-service die gebeurtenissen van het cluster bijhoudt. De gebeurtenissen worden weergegeven via [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST en API's. EventStore voert rechtstreeks query's op het cluster uit om diagnostische gegevens op te halen voor elke entiteit in uw cluster. Als u een volledige lijst van gebeurtenissen die beschikbaar zijn in EventStore wilt weergeven, raadpleegt u [Service Fabric-gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md).

Op de EventStore-API's kunnen programmatisch query's worden uitgevoerd met behulp van de [Service Fabric-clientbibliotheek](/dotnet/api/overview/azure/service-fabric#client-library).

Hier volgt een voorbeeldaanvraag voor alle clustergebeurtenissen tussen 2018-04-03T18:00:00Z en 2018-04-04T18:00:00Z via de functie GetClusterEventListAsync.

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


## <a name="monitor-cluster-health"></a>Clusterstatus bewaken
Service Fabric introduceert een [statusmodel](service-fabric-health-introduction.md) met statusentiteiten waarop systeemonderdelen en bewaking lokale voorwaarden kunnen rapporteren die ze controleren. De [Health Store](service-fabric-health-introduction.md#health-store) aggregeert alle statusgegevens om te bepalen of de entiteiten in orde zijn.

Het cluster wordt automatisch ingevuld met statusrapporten die worden verzonden door de systeemonderdelen. Lees [Systeemstatusrapporten gebruiken om fouten op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) voor meer informatie.

Service Fabric geeft statusquery's weer voor alle ondersteunde [entiteitstypes](service-fabric-health-introduction.md#health-entities-and-hierarchy). Ze kunnen worden geopend via de API met behulp van methoden op [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), PowerShell-cmdlets en REST. Deze query's retourneren gegevens over de status van de entiteit: de geaggregeerde status, de statusgebeurtenissen van de entiteit, de status van onderliggende items (indien van toepassing), de beschadigde evaluaties (wanneer de entiteit niet in orde is) en de statusstatistieken van onderliggende items (indien van toepassing).

### <a name="get-cluster-health"></a>Clusterstatus ophalen
De [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) retourneert de status van de clusterentiteit en bevat de statussen van toepassingen en knooppunten (onderliggende items van het cluster).  Maak eerst verbinding met het cluster met behulp van de [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

De status van het cluster is 11 knooppunten, de systeemtoepassing en fabric:/Voting geconfigureerd zoals beschreven.

In het volgende voorbeeld wordt de status van het cluster opgehaald met een standaard statusbeleid. De 11 knooppunten zijn in orde, maar de samengevoegde status van het cluster is fout, omdat de toepassing fabric:/Voting een fout bevat. U ziet hoe de beschadigingsevaluaties details bevatten over de omstandigheden die de geaggregeerde status hebben geactiveerd.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

In het volgende voorbeeld wordt de status van het cluster met behulp van een aangepast toepassingsbeleid opgehaald. De resultaten worden gefilterd om alleen toepassingen en knooppunten met een fout of waarschuwing op te halen. In dit voorbeeld worden er geen knooppunten geretourneerd, omdat ze allemaal in orde zijn. Alleen de toepassing fabric:/Voting respecteert het toepassingenfilter. Omdat het aangepaste beleid specificeert om waarschuwingen te beschouwen als fouten voor de toepassing fabric:/Voting, wordt de toepassing geëvalueerd als fout en het cluster ook.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Status van knooppunten ophalen
De [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) retourneert de status van een knooppuntentiteit en bevat de statusgebeurtenissen die op het knooppunt zijn gerapporteerd. Gebruik eerst de [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) om verbinding te maken met het cluster. In het volgende voorbeeld wordt de status van een specifiek knooppunt opgehaald met behulp van standaard statusbeleid:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

In het volgende voorbeeld wordt de status van alle knooppunten in het cluster opgehaald:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Status van systeemservice ophalen 

De geaggregeerde status van de systeemservices ophalen:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Service Fabric-gebeurtenissen bekijken
> * Query's uitvoeren op EventStore-API's voor clustergebeurtenissen
> * Infrastructuur controleren/prestatiemeteritems verzamelen
> * Statusrapporten van het cluster bekijken

Ga nu verder met de volgende zelfstudie om te leren hoe u een cluster kunt schalen.
> [!div class="nextstepaction"]
> [Een cluster schalen](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json

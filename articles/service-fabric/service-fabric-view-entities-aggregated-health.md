---
title: De cumulatieve status van Azure Service Fabric-entiteiten weer geven
description: Hierin wordt beschreven hoe u de geaggregeerde status van Azure Service Fabric-entiteiten kunt opvragen, bekijken en evalueren via status query's en algemene query's.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 7127d9906cfe1ba87241bd3810a9567e77bf0391
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785495"
---
# <a name="view-service-fabric-health-reports"></a>Service Fabric status rapporten weer geven
Azure Service Fabric introduceert een [status model](service-fabric-health-introduction.md) met status entiteiten waarop systeem onderdelen en watchdog lokale voor waarden kunnen rapporteren die ze controleren. De [Health Store](service-fabric-health-introduction.md#health-store) aggregeert alle statusgegevens om te bepalen of de entiteiten in orde zijn.

Het cluster wordt automatisch ingevuld met statusrapporten die worden verzonden door de systeemonderdelen. Lees [Systeemstatusrapporten gebruiken om fouten op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) voor meer informatie.

Service Fabric biedt meerdere manieren om de geaggregeerde status van de entiteiten op te halen:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) of andere hulpprogram ma's voor visualisatie
* Status query's (via Power shell, API of REST)
* Algemene query's die een lijst met entiteiten retour neren die een status hebben als een van de eigenschappen (via Power shell, API of REST)

Voor het demonstreren van deze opties gebruiken we een lokaal cluster met vijf knoop punten en de [toepassing Fabric:/WordCount](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). De toepassing **Fabric:/WordCount** bevat twee standaard services, een stateful service van het type `WordCountServiceType` en een stateless service van het type `WordCountWebServiceType` . Ik heb de `ApplicationManifest.xml` zodanig gewijzigd dat er zeven doel replica's zijn vereist voor de stateful service en één partitie. Omdat er slechts vijf knoop punten in het cluster zijn, rapporteren de systeem onderdelen een waarschuwing op de service partitie, omdat deze lager is dan het aantal doelen.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Status in Service Fabric Explorer
Service Fabric Explorer biedt een visuele weer gave van het cluster. In de onderstaande afbeelding ziet u dat:

* De Application **Fabric:/WordCount** is rood (in fout) omdat er een fout gebeurtenis wordt gerapporteerd door **MyWatchdog** voor de **Beschik baarheid** van de eigenschap.
* Een van de services, **Fabric:/WordCount/WordCountService** is geel (in het waarschuwings bericht). De service is geconfigureerd met zeven replica's en het cluster heeft vijf knoop punten, waardoor er geen twee replica's kunnen worden geplaatst. Hoewel het hier niet wordt weer gegeven, is de service partitie geel vanwege een systeem rapport waarin wordt vermeld `System.FM` dat `Partition is below target replica or instance count` . De gele partitie activeert de service geel.
* Het cluster is rood vanwege de toepassing rood.

De evaluatie gebruikt standaard beleid uit het cluster manifest en het toepassings manifest. Ze zijn strikt beleid en zijn niet van toepassing op storingen.

Weer gave van het cluster met Service Fabric Explorer:

![Weer gave van het cluster met Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Lees meer over [service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Status query's
Service Fabric geeft statusquery's weer voor alle ondersteunde [entiteitstypes](service-fabric-health-introduction.md#health-entities-and-hierarchy). Ze kunnen worden geopend via de API met behulp van methoden op [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), PowerShell-cmdlets en REST. Deze query's retourneren gegevens over de status van de entiteit: de geaggregeerde status, de statusgebeurtenissen van de entiteit, de status van onderliggende items (indien van toepassing), de beschadigde evaluaties (wanneer de entiteit niet in orde is) en de statusstatistieken van onderliggende items (indien van toepassing).

> [!NOTE]
> Er wordt een status entiteit geretourneerd wanneer deze volledig is ingevuld in de Health Store. De entiteit moet actief zijn (niet verwijderd) en een systeem rapport hebben. De bovenliggende entiteiten in de hiërarchie keten moeten ook systeem rapporten hebben. Als aan een van deze voor waarden niet wordt voldaan, retour neren de status query's een [FabricException](/dotnet/api/system.fabric.fabricexception) met [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` die laat zien waarom de entiteit niet is geretourneerd.
>
>

De status query's moeten worden door gegeven in de entiteits-id, die afhankelijk is van het entiteits type. De query's accepteren optionele status beleids parameters. Als er geen status beleid is opgegeven, worden de [status beleidsregels](service-fabric-health-introduction.md#health-policies) uit het cluster of het toepassings manifest gebruikt voor de evaluatie. Als de manifesten geen definitie voor het status beleid bevatten, wordt het standaard status beleid gebruikt voor de evaluatie. Het standaard status beleid is niet van toepassing op eventuele fouten. De query's accepteren ook filters voor het retour neren van alleen gedeeltelijke onderliggende elementen of gebeurtenissen, die betrekking hebben op de opgegeven filters. Met een ander filter kunt u de statistieken van de onderliggende elementen uitsluiten.

> [!NOTE]
> De uitvoer filters worden toegepast aan de server zijde, zodat de antwoord grootte van het bericht wordt beperkt. We raden u aan om de uitvoer filters te gebruiken om de geretourneerde gegevens te beperken, in plaats van filters toe te passen op de client zijde.
>
>

De status van een entiteit bevat:

* De geaggregeerde status van de entiteit. Wordt berekend door de Health Store op basis van status rapporten van de entiteit, de onderliggende statussen (indien van toepassing) en het status beleid. Meer informatie over de beoordeling van de status van de [entiteit](service-fabric-health-introduction.md#health-evaluation).  
* De status gebeurtenissen voor de entiteit.
* De verzameling van de statussen van alle onderliggende entiteiten voor de entiteit die kinderen kunnen hebben. De statussen bevatten entiteit-id's en de geaggregeerde status. Als u een volledige status voor een onderliggend item wilt ontvangen, roept u de status van de query aan voor het type van de onderliggende entiteit en geeft u de onderliggende id door.
* De slechte evaluaties die verwijzen naar het rapport dat de status van de entiteit heeft geactiveerd, als de entiteit niet in orde is. De evaluaties zijn recursief, met de onderliggende status evaluaties die de huidige status hebben geactiveerd. Een watchdog heeft bijvoorbeeld een fout gerapporteerd op basis van een replica. In de status van de toepassing wordt een slechte evaluatie weer gegeven als gevolg van een slechte service; de service is beschadigd wegens een fout in de-partitie. de partitie is beschadigd vanwege een fout met de replica. de replica is beschadigd vanwege het status rapport van de watchdog-fout.
* De status statistieken voor alle onderliggende typen van de entiteiten die onderliggende items hebben. Bij cluster status wordt bijvoorbeeld het totale aantal toepassingen, services, partities, replica's en geïmplementeerde entiteiten in het cluster weer gegeven. Service status toont het totale aantal partities en replica's onder de opgegeven service.

## <a name="get-cluster-health"></a>Clusterstatus ophalen
Retourneert de status van de cluster entiteit en bevat de statussen van toepassingen en knoop punten (onderliggende items van het cluster). Invoer:

* Beschrijving Het cluster status beleid dat wordt gebruikt om de knoop punten en de cluster gebeurtenissen te evalueren.
* Beschrijving De toewijzing van het beleid voor toepassings status, met het status beleid dat wordt gebruikt om het beleid voor toepassings manifesten te onderdrukken.
* Beschrijving Filters voor gebeurtenissen, knoop punten en toepassingen die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen, knoop punten en toepassingen worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.
* Beschrijving Filter om status statistieken uit te sluiten.
* Beschrijving Filter voor het toevoegen van infrastructuur resources:/systeem status statistieken in de status statistieken. Alleen van toepassing wanneer de status statistieken niet worden uitgesloten. De status statistieken bevatten standaard alleen statistieken voor gebruikers toepassingen en niet voor de systeem toepassing.

### <a name="api"></a>API
Als u de cluster status wilt ophalen, maakt u een `FabricClient` en roept u de [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) -methode aan op de **HealthManager**.

Met de volgende aanroep wordt de cluster status opgehaald:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Met de volgende code wordt de cluster status verkregen met behulp van een aangepast cluster status beleid en filters voor knoop punten en toepassingen. Hiermee geeft u op dat de status statistieken de infra structuur:/systeem statistieken bevatten. Er wordt [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthquerydescription)gemaakt, die de invoer gegevens bevat.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth)voor het ophalen van de cluster status. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

De status van het cluster is vijf knoop punten, de systeem toepassing en Fabric:/WordCount geconfigureerd zoals beschreven.

De volgende cmdlet krijgt de cluster status met behulp van een standaard status beleid. De cumulatieve status is waarschuwing, omdat de toepassing Fabric:/WordCount een waarschuwing is. U ziet hoe de beschadigingsevaluaties details bevatten over de omstandigheden die de geaggregeerde status hebben geactiveerd.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Met de volgende Power shell-cmdlet wordt de status van het cluster met behulp van een aangepast toepassings beleid opgehaald. De resultaten worden gefilterd om alleen toepassingen en knooppunten met een fout of waarschuwing op te halen. Als gevolg hiervan worden er geen knoop punten geretourneerd, omdat ze allemaal in orde zijn. Alleen de toepassing Fabric:/WordCount respecteert het filter toepassingen. Omdat in het aangepaste beleid wordt opgegeven dat er waarschuwingen moeten worden gegeven als fouten voor de toepassing Fabric:/WordCount, wordt de toepassing geëvalueerd als fout en is dit het cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
U kunt de cluster status ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-cluster) of een post- [aanvraag](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-node-health"></a>Status van knooppunten ophalen
Retourneert de status van een knooppunt entiteit en bevat de status gebeurtenissen die op het knoop punt zijn gerapporteerd. Invoer:

* Lang De knooppunt naam waarmee het knoop punt wordt aangeduid.
* Beschrijving De instellingen voor het cluster status beleid die worden gebruikt om de status te evalueren.
* Beschrijving Filters voor gebeurtenissen waarmee wordt aangegeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de status van het knoop punt wilt ophalen via de API, maakt u een `FabricClient` en roept u de [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) -methode aan op de HealthManager.

Met de volgende code wordt de knooppunt status voor de opgegeven knooppunt naam opgehaald:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Met de volgende code wordt de knooppunt status voor de opgegeven knooppunt naam opgehaald en worden de gebeurtenissen filter en het aangepaste beleid door gegeven via [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet voor het ophalen van de status van het knoop punt is [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .
Met de volgende cmdlet wordt de status van het knoop punt opgehaald met behulp van een standaard status beleid:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Met de volgende cmdlet wordt de status van alle knoop punten in het cluster opgehaald:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
U kunt de status van het knoop punt ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-node) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-application-health"></a>Toepassings status ophalen
Hiermee wordt de status van een toepassings entiteit geretourneerd. Het bevat de statussen van de geïmplementeerde toepassing en service-onderliggende items. Invoer:

* Lang De toepassings naam (URI) waarmee de toepassing wordt geïdentificeerd.
* Beschrijving Het toepassings status beleid dat wordt gebruikt om het beleid voor toepassings manifesten te onderdrukken.
* Beschrijving Filters voor gebeurtenissen, services en geïmplementeerde toepassingen die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen, services en geïmplementeerde toepassingen worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.
* Beschrijving Filter om de status statistieken uit te sluiten. Als dat niet is opgegeven, zijn de status statistieken onder andere de opties OK, waarschuwing en aantal fouten voor alle onderliggende toepassingen: Services, partities, replica's, geïmplementeerde toepassingen en geïmplementeerde service pakketten.

### <a name="api"></a>API
Als u de status van de toepassing wilt ophalen, maakt u een `FabricClient` en roept u de [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) -methode aan op de HealthManager.

Met de volgende code wordt de toepassings status voor de opgegeven toepassings naam (URI) opgehaald:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Met de volgende code wordt de toepassings status voor de opgegeven toepassings naam (URI) opgehaald, met filters en aangepaste beleids regels die zijn opgegeven via [ApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth)om de status van de toepassing op te halen. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Met de volgende cmdlet wordt de status van de **Fabric:/WordCount-** toepassing geretourneerd:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Met de volgende Power shell-cmdlet worden aangepaste beleids regels door gegeven. Er worden ook onderliggende items en gebeurtenissen gefilterd.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
U kunt de status van een toepassing ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-an-application) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-service-health"></a>Service status ophalen
Hiermee wordt de status van een service-entiteit geretourneerd. Het bevat de statussen van de partitie. Invoer:

* Lang De service naam (URI) waarmee de service wordt geïdentificeerd.
* Beschrijving Het toepassings status beleid dat wordt gebruikt om het manifest beleid van de toepassing te onderdrukken.
* Beschrijving Filters voor gebeurtenissen en partities die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en partities worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.
* Beschrijving Filter om status statistieken uit te sluiten. Als dat niet is opgegeven, worden in de status statistieken de waarden OK, waarschuwing en aantal fouten weer gegeven voor alle partities en replica's van de service.

### <a name="api"></a>API
Als u de service status wilt ophalen via de API, maakt u een `FabricClient` en roept u de [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) -methode aan op de HealthManager.

In het volgende voor beeld wordt de status van een service met de opgegeven service naam (URI) opgehaald:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Met de volgende code wordt de service status voor de opgegeven service naam (URI) opgehaald, waarbij filters en aangepaste beleids regels worden opgegeven via [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth)voor de service status is. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Met de volgende cmdlet wordt de service status opgehaald met behulp van een standaard status beleid:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
U kunt de service status ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-service) of een post- [aanvraag](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-partition-health"></a>Status van partitie ophalen
Hiermee wordt de status van een partitie-entiteit geretourneerd. Het bevat de statussen van de replica. Invoer:

* Lang De partitie-ID (GUID) waarmee de partitie wordt geïdentificeerd.
* Beschrijving Het toepassings status beleid dat wordt gebruikt om het manifest beleid van de toepassing te onderdrukken.
* Beschrijving Filters voor gebeurtenissen en replica's die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en replica's worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.
* Beschrijving Filter om status statistieken uit te sluiten. Als u dit niet opgeeft, wordt in de status statistieken weer gegeven hoeveel replica's er in OK, waarschuwing en fout status zijn.

### <a name="api"></a>API
Als u de status van de partitie wilt ophalen via de API, maakt u een `FabricClient` en roept u de [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) -methode aan op de HealthManager. Maak [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription)om optionele para meters op te geven.

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth)voor het ophalen van de status van de partitie. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Met de volgende cmdlet krijgt u de status van alle partities van de **Fabric:/WordCount/WordCountService-** service en worden er filters uit de replica status opgehaald:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
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
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
U kunt de status van de partitie ophalen met een [Get-aanvraag](/rest/api/servicefabric/sfclient-api-getpartitionhealth) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-replica-health"></a>Replica status ophalen
Hiermee wordt de status van een stateful service replica of een stateless service exemplaar geretourneerd. Invoer:

* Lang De partitie-ID (GUID) en de replica-ID waarmee de replica wordt geïdentificeerd.
* Beschrijving De para meters voor het toepassings status beleid dat wordt gebruikt om het beleid voor toepassings manifesten te onderdrukken.
* Beschrijving Filters voor gebeurtenissen waarmee wordt aangegeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de status van de replica wilt ophalen via de API, maakt u een `FabricClient` en roept u de [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) -methode aan op de HealthManager. Gebruik [ReplicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription)om geavanceerde para meters op te geven.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth)voor het ophalen van de status van de replica. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Met de volgende cmdlet wordt de status van de primaire replica opgehaald voor alle partities van de service:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
U kunt de status van replica's ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-replica) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-deployed-application-health"></a>Geïmplementeerde toepassings status ophalen
Retourneert de status van een toepassing die is geïmplementeerd op een knooppunt entiteit. Het bevat de status van geïmplementeerde service packs. Invoer:

* Lang De toepassings naam (URI) en de naam van het knoop punt (teken reeks) waarmee de geïmplementeerde toepassing wordt geïdentificeerd.
* Beschrijving Het toepassings status beleid dat wordt gebruikt om het beleid voor toepassings manifesten te onderdrukken.
* Beschrijving Filters voor gebeurtenissen en geïmplementeerde service pakketten die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen en geïmplementeerde service pakketten worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.
* Beschrijving Filter om status statistieken uit te sluiten. Als u dit niet opgeeft, wordt in de status statistieken het aantal geïmplementeerde service pakketten weer gegeven in OK, waarschuwing en fout status.

### <a name="api"></a>API
Als u de status van een toepassing die is geïmplementeerd op een knoop punt via de API wilt ophalen, maakt u een `FabricClient` en roept u de methode [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) aan op de HealthManager. Gebruik [DeployedApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription)om optionele para meters op te geven.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth)voor het ophalen van de status van de geïmplementeerde toepassing. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Als u wilt weten waar een toepassing is geïmplementeerd, voert u [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) uit en bekijkt u de onderliggende toepassingen van de toepassing.

Met de volgende cmdlet wordt de status opgehaald van de toepassing **Fabric:/WordCount** die is geïmplementeerd op **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
U kunt de status van de geïmplementeerde toepassing ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-deployed-application) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="get-deployed-service-package-health"></a>Status van geïmplementeerd service pakket ophalen
Hiermee wordt de status van een geïmplementeerde service pakket entiteit geretourneerd. Invoer:

* Lang De naam van de toepassing (URI), de knooppunt naam (de teken reeks) en de naam van de service manifest (teken reeks) waarmee het geïmplementeerde service pakket wordt geïdentificeerd.
* Beschrijving Het toepassings status beleid dat wordt gebruikt om het manifest beleid van de toepassing te onderdrukken.
* Beschrijving Filters voor gebeurtenissen waarmee wordt aangegeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat (bijvoorbeeld alleen fouten of waarschuwingen en fouten). Alle gebeurtenissen worden gebruikt om de samengevoegde status van de entiteit te evalueren, ongeacht het filter.

### <a name="api"></a>API
Als u de status van een geïmplementeerd service pakket via de API wilt ontvangen, maakt u een `FabricClient` en roept u de [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) -methode aan op de HealthManager. Gebruik [DeployedServicePackageHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription)om optionele para meters op te geven.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth)voor het ophalen van de status van het geïmplementeerde service pakket. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Als u wilt zien waar een toepassing is geïmplementeerd, voert u [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) uit en kijkt u naar de geïmplementeerde toepassingen. Als u wilt zien welke service pakketten zich in een toepassing bevinden, bekijkt u de onderliggende items van het geïmplementeerde service pakket in de [Get-ServiceFabricDeployedApplicationHealth-](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth) uitvoer.

Met de volgende cmdlet wordt de status opgehaald van het **WordCountServicePkg** -service pakket van de toepassing **Fabric:/WordCount** die is geïmplementeerd op **_Node_2**. De entiteit heeft **System. hosting** rapporten voor een succes volle service-en ingangs punt activering en een geslaagde Service-type registratie.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
U kunt de status van het geïmplementeerde service pakket verkrijgen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-service-package) of een [post-aanvraag](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) met status beleid dat wordt beschreven in de hoofd tekst.

## <a name="health-chunk-queries"></a>Query's voor status segmenten
De status segment query's kunnen meerdere onderliggende cluster-elementen (recursief) retour neren per invoer filter. Het ondersteunt geavanceerde filters die veel flexibiliteit bieden bij het kiezen van de onderliggende items om te worden geretourneerd. De filters kunnen onderliggende items opgeven op basis van de unieke id of door andere groeps-id's en/of status waarden. Standaard zijn er geen onderliggende items opgenomen, in tegens telling tot status opdrachten die altijd onderliggende items van het hoogste niveau bevatten.

De [status query's](service-fabric-view-entities-aggregated-health.md#health-queries) retour neren alleen onderliggende items op het hoogste niveau van de opgegeven entiteit per vereiste filters. Als u de onderliggende items van de onderliggende items wilt ophalen, moet u aanvullende status-Api's aanroepen voor elke entiteit die u interesseert. Op dezelfde manier moet u voor elke gewenste entiteit een status-API aanroepen om de status van specifieke entiteiten op te halen. Met de geavanceerde filtering van segment query's kunt u meerdere items van belang in één query aanvragen, waarbij de bericht grootte en het aantal berichten worden geminimaliseerd.

De waarde van de segment query is dat u de status kunt ophalen voor meer cluster entiteiten (mogelijk alle cluster entiteiten die beginnen met de vereiste root) in één aanroep. U kunt complexe status query's uitdrukken, zoals:

* Retour neer alleen toepassingen die een fout bevatten en voor deze toepassingen zijn alle services in een waarschuwing of fout. Neem voor geretourneerde Services alle partities op.
* Retourneert alleen de status van vier toepassingen, aangeduid met hun namen.
* Alleen de status van toepassingen van een gewenst toepassings type retour neren.
* Alle geïmplementeerde entiteiten in een knoop punt retour neren. Retourneert alle toepassingen, alle geïmplementeerde toepassingen op het opgegeven knoop punt en alle geïmplementeerde service pakketten op dat knoop punt.
* Retour neer alle replica's met een fout. Hiermee worden alle toepassingen, services, partities en alleen replica's met fouten geretourneerd.
* Retour neer alle toepassingen. Neem voor een opgegeven service alle partities op.

Op dit moment wordt de status segment query alleen voor de cluster entiteit weer gegeven. Er wordt een cluster status segment geretourneerd dat de volgende bevat:

* De samengevoegde status van het cluster.
* De lijst met status afkomsten van de integriteit van de knoop punten die de invoer filters respecteren.
* De lijst met de status van het integriteits overzicht van toepassingen die de invoer filters respecteren. Elk segment status van de toepassing bevat een segment lijst met alle services die voldoen aan de invoer filters en een segment lijst met alle geïmplementeerde toepassingen die de filters respecteren. Hetzelfde voor de onderliggende services en geïmplementeerde toepassingen. Op deze manier kunnen alle entiteiten in het cluster op hiërarchische wijze als aangevraagd worden geretourneerd.

### <a name="cluster-health-chunk-query"></a>Query cluster status segment
Retourneert de status van de cluster entiteit en bevat de hiërarchische status segmenten van de vereiste onderliggende elementen. Invoer:

* Beschrijving Het cluster status beleid dat wordt gebruikt om de knoop punten en de cluster gebeurtenissen te evalueren.
* Beschrijving De toewijzing van het beleid voor toepassings status, met het status beleid dat wordt gebruikt om het beleid voor toepassings manifesten te onderdrukken.
* Beschrijving Filters voor knoop punten en toepassingen die aangeven welke vermeldingen van belang zijn en moeten worden geretourneerd in het resultaat. De filters zijn specifiek voor een entiteit/groep entiteiten of zijn van toepassing op alle entiteiten op dat niveau. De lijst met filters kan een algemeen filter en/of filters voor specifieke id's bevatten om entiteiten te verfijnen die door de query worden geretourneerd. Als deze leeg is, worden de onderliggende items niet standaard geretourneerd.
  Meer informatie over de filters vindt u op [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) en [ApplicationHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). De toepassings filters kunnen geavanceerde filters voor kinderen recursief opgeven.

Het resultaat van het segment bevat de onderliggende items die de filters respecteren.

Op dit moment worden door de segment query geen onjuiste evaluaties of entiteits gebeurtenissen geretourneerd. Deze extra informatie kan worden verkregen met behulp van de bestaande cluster status query.

### <a name="api"></a>API
Als u cluster status Chunk wilt ophalen, maakt u een `FabricClient` en roept u de [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) -methode aan op de **HealthManager**. U kunt [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) door geven om status beleid en geavanceerde filters te beschrijven.

Met de volgende code wordt het cluster status segment met geavanceerde filters opgehaald.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
De cmdlet [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk)voor het ophalen van de cluster status. Maak eerst verbinding met het cluster met behulp van de cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

De volgende code haalt alleen knoop punten op als deze een fout hebben, met uitzonde ring van een specifiek knoop punt, dat altijd moet worden geretourneerd.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Met de volgende cmdlet wordt het cluster segment met toepassings filters opgehaald.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Met de volgende cmdlet worden alle geïmplementeerde entiteiten in een knoop punt geretourneerd.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
U kunt cluster status chunk ophalen met een [Get-aanvraag](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) of een [post-aanvraag](/rest/api/servicefabric/health-of-cluster) met status beleid en geavanceerde filters die in de hoofd tekst worden beschreven.

## <a name="general-queries"></a>Algemene query's
Algemene query's retour neren een lijst met Service Fabric entiteiten van een opgegeven type. Ze worden weer gegeven via de API (via de methoden op **FabricClient. QueryManager**), Power shell-cmdlets en rest. Deze query's verzamelen subquery's van meerdere onderdelen. Een van beide is de [Health Store](service-fabric-health-introduction.md#health-store), waarmee de geaggregeerde status voor elk query resultaat wordt gevuld.  

> [!NOTE]
> Algemene query's retour neren de cumulatieve status van de entiteit en bevatten geen uitgebreide status gegevens. Als een entiteit niet in orde is, kunt u opvolgen met status query's om alle status gegevens op te halen, zoals gebeurtenissen, onderliggende statussen en slechte evaluaties.
>
>

Als algemene query's een onbekende status retour neren voor een entiteit, is het mogelijk dat de Health Store geen volledige gegevens over de entiteit bevat. Het is ook mogelijk dat een subquery naar de Health Store is mislukt (bijvoorbeeld omdat er een communicatie fout is opgetreden of omdat de Health Store is beperkt). Follow-up met een status query voor de entiteit. Als de subquery tijdelijke fouten heeft ondervonden, zoals netwerk problemen, kan deze opvolgings query slagen. Het kan u ook meer informatie geven over het Health Store waarom de entiteit niet beschikbaar is.

De query's die **HealthState** bevatten voor entiteiten zijn:

* Knooppunt lijst: retourneert de lijst knooppunten in het cluster (in pagina's).
  * API: [FabricClient. QueryClient. GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * Power shell: Get-ServiceFabricNode
* Lijst met toepassingen: Hiermee wordt de lijst met apps in het cluster (wisselbaar) geretourneerd.
  * API: [FabricClient. QueryClient. GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * Power shell: Get-ServiceFabricApplication
* Service lijst: retourneert de lijst met Services in een toepassing (wisselbaar).
  * API: [FabricClient. QueryClient. GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * Power shell: Get-ServiceFabricService
* Partitie lijst: retourneert de lijst met partities in een service (wisselt).
  * API: [FabricClient. QueryClient. GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * Power shell: Get-ServiceFabricPartition
* Replica lijst: retourneert de lijst met replica's in een partitie (wisselt).
  * API: [FabricClient. QueryClient. GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * Power shell: Get-ServiceFabricReplica
* Lijst met geïmplementeerde toepassingen: retourneert de lijst met geïmplementeerde toepassingen op een knoop punt.
  * API: [FabricClient. QueryClient. GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * Power shell: Get-ServiceFabricDeployedApplication
* Lijst met geïmplementeerde service pakketten: retourneert de lijst met Service pakketten in een geïmplementeerde toepassing.
  * API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * Power shell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Sommige query's retour neren de resultaten van de pagina. Het retour neren van deze query's is een lijst die is afgeleid van [PagedList \<T> ](/dotnet/api/system.fabric.query.pagedlist-1). Als de resultaten niet op een bericht passen, wordt er alleen een pagina geretourneerd en een ContinuationToken die een opsomming geeft van waar de inventarisatie is gestopt. Ga door met het aanroepen van dezelfde query en geef de vervolg token uit de vorige query door om de volgende resultaten op te halen.

### <a name="examples"></a>Voorbeelden
Met de volgende code worden de beschadigde toepassingen in het cluster opgehaald:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Met de volgende cmdlet worden de toepassings Details opgehaald voor de toepassing Fabric:/WordCount. U ziet dat de status op de hoogte is van de waarschuwing.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Met de volgende cmdlet worden de services opgehaald met de status fout:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Cluster-en toepassings upgrades
Tijdens een bewaakte upgrade van het cluster en de toepassing, Service Fabric de status controleren om ervoor te zorgen dat alles in orde blijft. Als een entiteit niet in orde is als geëvalueerd met het geconfigureerde status beleid, past de upgrade een upgrade-specifiek beleid toe om de volgende actie te bepalen. De upgrade kan worden onderbroken om interactie van de gebruiker toe te staan (zoals het oplossen van fout voorwaarden of het wijzigen van beleid), of het wordt mogelijk automatisch teruggezet naar de vorige versie.

Tijdens een *cluster* upgrade kunt u de upgrade status van het cluster ophalen. De upgrade status bevat onjuiste evaluaties, wat naar de slechte status in het cluster wijst. Als de upgrade wordt teruggedraaid vanwege status problemen, wordt de status van de upgrade de laatste slechte redenen onthouden. Deze informatie kan beheerders helpen bij het onderzoeken van wat er is misgegaan nadat de upgrade is teruggedraaid of gestopt.

Op dezelfde manier worden tijdens een *toepassings* upgrade alle slechte evaluaties opgenomen in de status van de toepassings upgrade.

Hieronder ziet u de upgrade status van de toepassing voor een gewijzigde Fabric:/WordCount-toepassing. Een watchdog heeft een fout bij een van de replica's gerapporteerd. De upgrade wordt teruggedraaid omdat niet wordt voldaan aan de status controles.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Meer informatie over de [service Fabric-toepassings upgrade](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Status evaluaties gebruiken om problemen op te lossen
Wanneer er een probleem is met het cluster of een toepassing, kijkt u naar het cluster of de status van de toepassing om te bepalen wat er mis is. De onjuiste evaluaties geven details over de status van de huidige slechte toestand. Als dat het geval is, kunt u inzoomen op de beschadigde onderliggende entiteiten om de hoofd oorzaak te identificeren.

Denk bijvoorbeeld aan dat de toepassing een slechte status heeft omdat er een fout rapport is over een van de replica's. Met de volgende Power shell-cmdlet worden de onjuiste evaluaties weer gegeven:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

U kunt de replica bekijken voor meer informatie:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> De onjuiste evaluaties tonen de eerste reden dat de entiteit wordt geëvalueerd naar de huidige status. Er kunnen meerdere andere gebeurtenissen zijn die deze status activeren, maar ze worden niet weer gegeven in de evaluaties. Als u meer informatie wilt, zoomt u in op de status entiteiten om alle slechte rapporten in het cluster te bepalen.
>
>

## <a name="next-steps"></a>Volgende stappen
[Systeemstatusrapporten gebruiken om fouten op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aangepaste Service Fabric status rapporten toevoegen](service-fabric-report-health.md)

[Service status rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)

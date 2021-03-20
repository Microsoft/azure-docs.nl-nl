---
title: Services en containers voor het automatisch schalen van Azure Service Fabric
description: Met Azure Service Fabric kunt u beleids regels voor automatisch schalen instellen voor services en containers.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91354799"
---
# <a name="introduction-to-auto-scaling"></a>Inleiding tot automatisch schalen
Automatisch schalen is een extra mogelijkheid van Service Fabric om uw services dynamisch te schalen op basis van de belasting die door de Services wordt gerapporteerd of op basis van het gebruik van resources. Automatisch schalen biedt een fantastische elasticiteit en maakt het inrichten van aanvullende instanties of partities van uw service op aanvraag mogelijk. Het volledige automatische schaal proces is geautomatiseerd en transparant en wanneer u uw beleid hebt ingesteld voor een service, hoeft u geen hand matige schaal bewerkingen op service niveau uit te stellen. Automatisch schalen kan worden ingeschakeld op het moment dat de service wordt gemaakt, of op elk gewenst moment door de service bij te werken.

Een veelvoorkomend scenario waarbij automatisch schalen nuttig is wanneer de belasting van een bepaalde service in de loop van de tijd verschilt. Een service zoals een gateway kan bijvoorbeeld worden geschaald op basis van de hoeveelheid resources die nodig zijn om binnenkomende aanvragen af te handelen. Laten we eens kijken naar een voor beeld van hoe deze schaal regels eruitzien:
* Als alle exemplaren van mijn gateway gemiddeld meer dan twee kernen gebruiken, kunt u de Gateway Service uitschalen door nog één exemplaar toe te voegen. Doe dit elk uur, maar u hebt nooit meer dan zeven exemplaren in totaal.
* Als alle exemplaren van mijn gateway gemiddeld minder dan 0,5 kernen gebruiken, schaalt u de service in door één exemplaar te verwijderen. Doe dit elk uur, maar nooit minder dan drie exemplaren in totaal.

Automatisch schalen wordt ondersteund voor zowel containers als normale Service Fabric Services. Als u automatisch schalen wilt gebruiken, moet u gebruikmaken van versie 6,2 of hoger van de Service Fabric runtime. 

In de rest van dit artikel wordt het schaal beleid beschreven, manieren om automatisch schalen in te scha kelen of uit te scha kelen en voor beelden van het gebruik van deze functie.

## <a name="describing-auto-scaling"></a>Een beschrijving van automatisch schalen
U kunt beleid voor automatisch schalen definiëren voor elke service in een Service Fabric cluster. Elk schaal beleid bestaat uit twee delen:
* Met de **trigger voor schalen** wordt beschreven wanneer het schalen van de service wordt uitgevoerd. De voor waarden die in de trigger zijn gedefinieerd, worden periodiek gecontroleerd om te bepalen of een service moet worden geschaald of niet.

* Het **schaal mechanisme** beschrijft hoe schalen wordt uitgevoerd wanneer deze wordt geactiveerd. Het mechanisme wordt alleen toegepast wanneer aan de voor waarden van de trigger wordt voldaan.

Alle triggers die momenteel worden ondersteund, werken met [metrische gegevens](service-fabric-cluster-resource-manager-metrics.md)over de belasting of met fysieke metrische gegevens, zoals CPU of geheugen gebruik. In beide gevallen wordt de gerapporteerde belasting voor de metrische gegevens door Service Fabric gecontroleerd en wordt de trigger regel matig geëvalueerd om te bepalen of schalen nodig is.

Er zijn twee mechanismen die momenteel worden ondersteund voor automatisch schalen. De eerste is bedoeld voor stateless Services of voor containers waarbij automatisch schalen wordt uitgevoerd door [instanties](service-fabric-concepts-replica-lifecycle.md)toe te voegen of te verwijderen. Voor stateful en stateless Services kan automatisch schalen ook worden uitgevoerd door benoemde [partities](service-fabric-concepts-partitioning.md) van de service toe te voegen of te verwijderen.

> [!NOTE]
> Momenteel wordt slechts één schaal beleid per service ondersteund, en slechts één schaalings trigger per schaal beleid.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gemiddelde partitie-load trigger met op exemplaren gebaseerd schalen
Het eerste type trigger is gebaseerd op het laden van exemplaren in een stateless service partitie. Metrische belastingen worden eerst vloeiend gemaakt om de belasting voor elk exemplaar van een partitie te verkrijgen. vervolgens worden deze waarden gemiddeld verdeeld over alle exemplaren van de partitie. Er zijn drie factoren die bepalen wanneer de service wordt geschaald:

* _Onderste belasting drempel_ is een waarde die bepaalt wanneer de service wordt **geschaald**. Als de gemiddelde belasting van alle exemplaren van de partities lager is dan deze waarde, wordt de service geschaald in.
* _Drempel_ waarde voor laden van boven is een getal dat bepaalt wanneer de service wordt **uitgeschaald**. Als de gemiddelde belasting van alle exemplaren van de partitie hoger is dan deze waarde, wordt de service uitgeschaald.
* Het interval voor het _schalen_ bepaalt hoe vaak de trigger wordt gecontroleerd. Als de trigger is ingeschakeld, wordt het mechanisme toegepast als er wordt geschaald. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt de trigger niet opnieuw gecontroleerd voordat het schaal interval opnieuw verloopt.

Deze trigger kan alleen worden gebruikt met stateless Services (stateless containers of Service Fabric Services). Als een service meerdere partities heeft, wordt de trigger voor elke partitie afzonderlijk geëvalueerd en wordt op elke partitie het opgegeven mechanisme onafhankelijk van elkaar toegepast. In dit geval is het mogelijk dat sommige partities van de service worden uitgeschaald, sommige worden geschaald en sommige niet tegelijkertijd worden geschaald, op basis van de belasting van de services.

Het enige mechanisme dat met deze trigger kan worden gebruikt, is PartitionInstanceCountScaleMechanism. Er zijn drie factoren die bepalen hoe dit mechanisme wordt toegepast:
* _Schaal verhoging_ bepaalt hoeveel instanties worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd.
* _Maximum aantal exemplaren_ definieert de bovengrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting. U kunt deze limiet overs Laan door de waarde-1 op te geven. in dat geval wordt de service zo veel mogelijk uitgeschaald (de limiet is het aantal knoop punten dat beschikbaar is in het cluster).
* _Minimum aantal exemplaren_ definieert de ondergrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Het beleid voor automatisch schalen instellen voor op instanties gebaseerd schalen

### <a name="using-application-manifest"></a>Toepassings manifest gebruiken
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C#-Api's gebruiken
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Power shell gebruiken
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gemiddelde trigger voor service belasting met schalen op basis van partities
De tweede trigger is gebaseerd op de belasting van alle partities van één service. Metrische belastingen worden eerst vloeiend gemaakt om de belasting voor elke replica of elk exemplaar van een partitie te verkrijgen. Voor stateful Services wordt de belasting van de partitie beschouwd als de belasting van de primaire replica. voor stateless Services is de belasting van de partitie de gemiddelde belasting van alle exemplaren van de partitie. Deze waarden worden gemiddeld verdeeld over alle partities van de service en deze waarde wordt gebruikt om automatisch schalen te activeren. Hetzelfde als in het vorige mechanisme zijn er drie factoren die bepalen wanneer de service wordt geschaald:

* _Onderste belasting drempel_ is een waarde die bepaalt wanneer de service wordt **geschaald**. Als de gemiddelde belasting van alle partities van de service lager is dan deze waarde, wordt de service geschaald in.
* _Drempel_ waarde voor laden van boven is een getal dat bepaalt wanneer de service wordt **uitgeschaald**. Als de gemiddelde belasting van alle partities van de service hoger is dan deze waarde, wordt de service uitgeschaald.
* Het interval voor het _schalen_ bepaalt hoe vaak de trigger wordt gecontroleerd. Als de trigger is ingeschakeld, wordt het mechanisme toegepast als er wordt geschaald. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt de trigger niet opnieuw gecontroleerd voordat het schaal interval opnieuw verloopt.

Deze trigger kan worden gebruikt met stateful en stateless Services. Het enige mechanisme dat met deze trigger kan worden gebruikt, is AddRemoveIncrementalNamedPartitionScalingMechanism. Wanneer de service is geschaald, wordt er een nieuwe partitie toegevoegd en wanneer de service wordt geschaald in een van de bestaande partities, wordt verwijderd. Er zijn beperkingen die worden gecontroleerd wanneer de service wordt gemaakt of bijgewerkt en het maken of bijwerken van de service mislukt als aan deze voor waarden niet wordt voldaan:
* Het benoemde partitie schema moet worden gebruikt voor de service.
* Partitie namen moeten opeenvolgende gehele getallen zijn, zoals ' 0 ', ' 1 ',...
* De eerste partitie naam moet ' 0 ' zijn.

Als bijvoorbeeld een service voor het eerst met drie partities wordt gemaakt, is de enige geldige mogelijkheid voor partitie namen 0, 1 en 2.

De daad werkelijke bewerking voor automatisch schalen die wordt uitgevoerd, geldt ook voor dit navolgende schema:
* Als de huidige partities van de service de naam 0, 1 en 2 hebben, wordt de partitie die wordt toegevoegd voor uitschalen, aangeduid met de naam ' 3 '.
* Als de huidige partities van de service de naam 0, 1 en 2 hebben, dan is de partitie die wordt verwijderd voor schalen in, de partitie met de naam 2.

Hetzelfde als bij het mechanisme dat gebruikmaakt van schalen door instanties toe te voegen of te verwijderen, zijn er drie para meters die bepalen hoe dit mechanisme wordt toegepast:
* _Schaal verhoging_ bepaalt hoeveel partities worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd.
* _Maximum aantal partities_ definieert de bovengrens voor schalen. Als het aantal partities van de service deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting. U kunt deze limiet overs Laan door de waarde-1 op te geven. in dat geval wordt de service zo veel mogelijk uitgeschaald (de limiet is de werkelijke capaciteit van het cluster).
* _Minimum aantal exemplaren_ definieert de ondergrens voor schalen. Als het aantal partities van de service deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

> [!WARNING] 
> Wanneer AddRemoveIncrementalNamedPartitionScalingMechanism wordt gebruikt met stateful Services, worden er door Service Fabric partities toegevoegd of verwijderd zonder dat er een **melding of waarschuwing** wordt weer gegeven. Het opnieuw partitioneren van gegevens wordt niet uitgevoerd wanneer het schaal mechanisme wordt geactiveerd. In het geval van een uitschaal bewerking worden nieuwe partities leeg en in het geval van een schaal bewerking **wordt de partitie met alle gegevens die deze bevat, verwijderd**.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Beleid voor automatisch schalen instellen voor schalen op basis van partities

### <a name="using-application-manifest"></a>Toepassings manifest gebruiken
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C#-Api's gebruiken
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Power shell gebruiken
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatisch schalen op basis van resources

Om te zorgen dat de bron monitor service kan worden geschaald op basis van de werkelijke resources

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Er zijn twee metrische gegevens die werkelijke fysieke resources vertegenwoordigen. Een van deze is servicefabric:/_CpuCores die het werkelijke CPU-gebruik vertegenwoordigen (dus 0,5 staat voor een halve kern) en de andere servicefabric:/-_MemoryInMB die het geheugen gebruik in MB aangeeft.
ResourceMonitorService is verantwoordelijk voor het bijhouden van het CPU-en geheugen gebruik van gebruikers services. Met deze service wordt gewogen zwevend gemiddelde toegepast, zodat er rekening wordt gehouden met mogelijke korte tijdige pieken. Bron bewaking wordt ondersteund voor zowel container-als niet-container toepassingen in Windows en voor containers in Linux. Automatisch schalen op resources is alleen ingeschakeld voor services die zijn geactiveerd in het [exclusieve proces model](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).

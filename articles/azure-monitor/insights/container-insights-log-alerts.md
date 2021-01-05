---
title: Waarschuwingen registreren van Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u aangepaste logboek waarschuwingen maakt voor geheugen-en CPU-gebruik van Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 131f5ebc0f72afce381b4b82d6fe50a5d5e37123
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901501"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>Logboek waarschuwingen maken op basis van Azure Monitor voor containers

Azure Monitor voor containers bewaakt de prestaties van container werkbelastingen die zijn geïmplementeerd op beheerde of zelf beheerde Kubernetes-clusters. In dit artikel wordt beschreven hoe u waarschuwingen op basis van Logboeken maakt voor de volgende situaties met AKS-clusters:

- Wanneer het CPU-of geheugen gebruik op cluster knooppunten een drempel waarde overschrijdt
- Wanneer het CPU-of geheugen gebruik in een container binnen een controller een drempel waarde overschrijdt, vergeleken met een limiet die is ingesteld voor de bijbehorende resource
- Aantal knoop punten van *het status knooppunt*
- *Aantal mislukte Pod is mislukt*, *in behandeling*, *onbekend*, *actief* of *geslaagd*
- Wanneer vrije schijf ruimte op cluster knooppunten een drempel waarde overschrijdt

Als u een waarschuwing wilt ontvangen voor hoog CPU-of geheugen gebruik, of weinig vrije schijf ruimte op cluster knooppunten, gebruikt u de query's die worden gegeven om een metrische waarschuwing of metrische meting waarschuwing te maken. Hoewel metrische waarschuwingen een lagere latentie hebben dan logboek waarschuwingen, bieden logboek waarschuwingen geavanceerde query's en grotere verfijning. Waarschuwings query's voor logboeken vergelijken een datum/tijd *met de huidige operator en* gaan één uur terug. (Azure Monitor voor containers worden alle datums opgeslagen in UTC-notatie (Coordinated Universal Time).)

Als u niet bekend bent met Azure Monitor waarschuwingen, raadpleegt u [overzicht van waarschuwingen in Microsoft Azure](../platform/alerts-overview.md) voordat u begint. Zie [waarschuwingen in Logboeken vastleggen in azure monitor](../platform/alerts-unified-log.md)voor meer informatie over waarschuwingen die gebruikmaken van logboek query's. Zie [metrische waarschuwingen in azure monitor](../platform/alerts-metric-overview.md)voor meer informatie over metrische waarschuwingen.

## <a name="resource-utilization-log-search-queries"></a>Zoek opdrachten in Logboeken van resource gebruik

De query's in deze sectie ondersteunen elk waarschuwings scenario. Deze worden gebruikt in stap 7 van de sectie [waarschuwing maken](#create-an-alert-rule) van dit artikel.

Met de volgende query wordt het gemiddelde CPU-gebruik berekend als gemiddelde van het CPU-gebruik van het knoop punt dat elke minuut wordt verbruikt.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Met de volgende query wordt het gemiddelde geheugen gebruik berekend als gemiddelde van het geheugen gebruik per minuut van een lid van het knoop punt.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>De volgende query's gebruiken de waarden voor tijdelijke aanduidingen \<your-cluster-name> en \<your-controller-name> voor het weer geven van het cluster en de controller. Vervang deze door de waarden die specifiek zijn voor uw omgeving bij het instellen van waarschuwingen.

De volgende query berekent het gemiddelde CPU-gebruik van alle containers in een controller als gemiddeld CPU-gebruik van elke container instantie in een controller per minuut. De meting is een percentage van de limiet die voor een container is ingesteld.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Met de volgende query berekent u het gemiddelde geheugen gebruik van alle containers in een controller als gemiddeld geheugen gebruik van elke container instantie in een controller per minuut. De meting is een percentage van de limiet die voor een container is ingesteld.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Met de volgende query worden alle knoop punten en aantallen geretourneerd met de status *gereed* en een *loopvlak*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Met de volgende query worden pod-fase aantallen geretourneerd op basis van alle fasen: *mislukt*, *in behandeling*, *onbekend*, *actief* of *geslaagd*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Als u een waarschuwing wilt ontvangen voor bepaalde pod-fasen, zoals *in behandeling*, *mislukt* of *onbekend*, wijzigt u de laatste regel van de query. Als u bijvoorbeeld een waarschuwing wilt ontvangen over *FailedCount* , gebruikt u: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

De volgende query retourneert cluster knooppunt schijven die meer dan 90% beschik bare ruimte gebruiken. Als u de cluster-ID wilt ophalen, voert u eerst de volgende query uit en kopieert u de waarde van de `ClusterId` eigenschap:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken

In deze sectie wordt uitgelegd hoe u een waarschuwings regel voor metrische metingen maakt met behulp van prestatie gegevens van Azure Monitor voor containers. U kunt dit basis proces met verschillende logboek query's gebruiken om te waarschuwen voor verschillende prestatie meter items. Gebruik een van de zoek query's in het logboek die eerder zijn gegeven om te beginnen met. Als u met een ARM-sjabloon wilt maken, raadpleegt u voor [beelden van het maken van logboek waarschuwingen met Azure-resource sjabloon](../platform/alerts-log-create-templates.md).

>[!NOTE]
>Met de volgende procedure voor het maken van een waarschuwings regel voor container resource gebruik moet u overschakelen naar een nieuwe API voor logboek waarschuwingen, zoals beschreven in de voor [keur voor de switch-API voor logboek waarschuwingen](../platform/alerts-log-api-switch.md).
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek in het Azure Portal naar en selecteer **log Analytics-werk ruimten**.
3. Selecteer de werk ruimte die Azure Monitor ondersteunt voor containers in de lijst met Log Analytics-werk ruimten. 
4. Selecteer in het deel venster aan de linkerkant **Logboeken** om de pagina Azure monitor logboeken te openen. U gebruikt deze pagina om Azure-logboek query's te schrijven en uit te voeren.
5. Plak op de pagina **Logboeken** een van de eerder genoemde [query's](#resource-utilization-log-search-queries) in het veld **Zoek query** en selecteer vervolgens **uitvoeren** om de resultaten te valideren. Als u deze stap niet uitvoert, is de optie **+ nieuwe waarschuwing** niet beschikbaar om te selecteren.
6. Selecteer **+ nieuwe waarschuwing** om een logboek waarschuwing te maken.
7. Selecteer in de sectie **voor waarde** de **wanneer de aangepaste logboek zoekactie vooraf \<logic undefined>** gedefinieerde logboek voorwaarde is. Het waarschuwings type voor de **aangepaste zoek opdracht voor logboeken** wordt automatisch geselecteerd omdat er rechtstreeks vanuit de pagina logboeken van Azure monitor een waarschuwing wordt gemaakt.  
8. Plak een van de [query's](#resource-utilization-log-search-queries) die eerder zijn gegeven in het veld **Zoek query** .
9. Configureer de waarschuwing als volgt:

    1. Selecteer in de vervolgkeuzelijst **Gebaseerd op** de optie **Meting van metrische gegevens**. Een metrische meting maakt een waarschuwing voor elk object in de query met een waarde boven onze opgegeven drempel.
    1. Voor **waarde**, selecteert u **groter dan** en voert u **75** in als een **drempel waarde** voor de aanvankelijke basis lijn voor de waarschuwingen voor de CPU en het geheugen gebruik. Voer **90** in voor de waarschuwing voor weinig schijf ruimte. Of voer een andere waarde in die voldoet aan uw criteria.
    1. Selecteer in het gedeelte **trigger waarschuwing op basis van** de optie **opeenvolgende inbreuken**. Selecteer in de vervolg keuzelijst **groter dan** en voer **2** in.
    1. Als u een waarschuwing voor container-CPU of geheugen gebruik wilt configureren, selecteert u onder **aggregatie op** de optie **containerName**. Selecteer **ClusterId** om te configureren voor een waarschuwing met een lage schijf van het cluster knooppunt.
    1. Stel in de **geëvalueerd op basis van** de sectie de **periode** waarde in op **60 minuten**. De regel wordt elke vijf minuten uitgevoerd en geeft als resultaat records die zijn gemaakt in het afgelopen uur van de huidige tijd. Het instellen van de tijds periode op een Wide Window-accounts voor mogelijke gegevens latentie. Het zorgt er ook voor dat de query gegevens retourneert om een onjuiste negatief te voor komen waarbij de waarschuwing nooit wordt geactiveerd.

10. Selecteer **gereed** om de waarschuwings regel te volt ooien.
11. Voer een naam in het veld **naam van waarschuwings regel** in. Geef een **Beschrijving** op die informatie over de waarschuwing bevat. En selecteer een passend Ernst niveau in de beschik bare opties.
12. Als u de waarschuwings regel onmiddellijk wilt activeren, accepteert u de standaard waarde voor het inschakelen van de **regel bij het maken**.
13. Selecteer een bestaande **actie groep** of maak een nieuwe groep. Deze stap zorgt ervoor dat dezelfde acties worden uitgevoerd telkens wanneer een waarschuwing wordt geactiveerd. Configureren op basis van de manier waarop uw IT-of DevOps Operations-team incidenten beheert.
14. Selecteer **waarschuwings regel maken** om de waarschuwings regel te volt ooien. Deze wordt onmiddellijk uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken voor het evalueren of aanpassen van waarschuwingen, het visualiseren of analyseren van uw clusters.

- Zie [Kubernetes cluster prestaties weer geven](container-insights-analyze.md) en [Kubernetes-cluster status weer geven](./container-insights-overview.md)voor meer informatie over Azure monitor en het bewaken van andere aspecten van uw Kubernetes-cluster.

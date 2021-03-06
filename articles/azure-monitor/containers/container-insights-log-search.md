---
title: Logboeken zoeken vanuit container Insights | Microsoft Docs
description: In container Insights worden metrische gegevens en logboek registraties verzameld en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201376"
---
# <a name="how-to-query-logs-from-container-insights"></a>Logboeken vanuit container Insights doorzoeken

In container Insights worden metrische gegevens over prestaties, inventaris gegevens en status informatie van container hosts en containers verzameld. De gegevens worden elke drie minuten verzameld en doorgestuurd naar de Log Analytics-werk ruimte in Azure Monitor. Deze gegevens zijn beschikbaar voor [query's](../logs/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="container-records"></a>Container records

In de volgende tabel worden de details van de records die door container Insights zijn verzameld gegeven. Zie de naslag informatie voor de tabellen [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) en [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) voor een overzicht van de kolom beschrijvingen.

| Gegevens | Gegevensbron | Gegevenstype | Velden |
|------|-------------|-----------|--------|
| Container voorraad | Kubelet | `ContainerInventory` | TimeGenerated, computer, name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, hebben, ContainerID, ImageID |
| Container logboek | Docker | `ContainerLog` | TimeGenerated, computer, afbeeldings-ID, naam, LogEntrySource, LogEntry, hebben, ContainerID |
| Container-knooppunt inventaris | Uitvoeren-API | `ContainerNodeInventory`| TimeGenerated, computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, hebben|
| Inventaris van Peul in een Kubernetes-cluster | Uitvoeren-API | `KubePodInventory` | TimeGenerated, computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, controller naam, container status, ContainerStatusReason, ContainerID, containerName, naam, PodLabel, namespace, PodStatus, clustername, PodIp, hebben |
| Inventaris van knoop punten die deel uitmaken van een Kubernetes-cluster | Uitvoeren-API | `KubeNodeInventory` | TimeGenerated, computer, clustername, ClusterId, LastTransitionTimeReady, labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, hebben | 
|Inventarisatie van permanente volumes in een Kubernetes-cluster |Uitvoeren-API |`KubePVInventory` | TimeGenerated, PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, ClusterId, clustername, _ResourceId, hebben |
| Kubernetes-gebeurtenissen | Uitvoeren-API | `KubeEvents` | TimeGenerated, computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, bericht, hebben | 
| Services in het Kubernetes-cluster | Uitvoeren-API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, hebben | 
| Prestatie gegevens voor knoop punten die deel uitmaken van het Kubernetes-cluster | Metrische gegevens over gebruik worden opgehaald uit cAdvisor en limieten van de uitvoeren-API | `Perf \| where ObjectName == "K8SNode"` | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Prestatie gegevens voor containers onderdeel van het Kubernetes-cluster | Metrische gegevens over gebruik worden opgehaald uit cAdvisor en limieten van de uitvoeren-API | `Perf \| where ObjectName == "K8SContainer"` | Tellernaam &#40;cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Aangepaste metrische gegevens ||`InsightsMetrics` | Computer, naam, naam ruimte, oorsprong, hebben, tags<sup>1</sup>, TimeGenerated, type, Va, _ResourceId | 

<sup>1</sup> de eigenschap *Tags* vertegenwoordigt [meerdere dimensies](../essentials/data-platform-metrics.md#multi-dimensional-metrics) voor de bijbehorende metriek. Zie overzicht van InsightsMetrics voor meer informatie over de metrische gegevens die in de tabel worden verzameld en opgeslagen `InsightsMetrics` en een beschrijving [](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)van de record eigenschappen.

## <a name="search-logs-to-analyze-data"></a>Zoek Logboeken om gegevens te analyseren

Met Azure Monitor Logboeken kunt u zoeken naar trends, knel punten diagnosticeren, prognoses maken of correleren van gegevens waarmee u kunt bepalen of de huidige cluster configuratie optimaal presteert. Vooraf gedefinieerde zoek opdrachten in Logboeken worden weer gegeven zodat u direct aan de slag kunt gaan met of kunt aanpassen om de informatie op de gewenste manier te retour neren.

U kunt gegevens in de werk ruimte interactief analyseren door de **gebeurtenis logboeken Kubernetes weer geven** of **container logboeken weer geven** te selecteren in het deel venster voor beeld in de vervolg keuzelijst **analyse weer** geven. De pagina **Zoeken in Logboeken** wordt weer gegeven aan de rechter kant van de Azure portal pagina waarop u zich bevond.

![Gegevens analyseren in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

De container logboeken uitvoer die wordt doorgestuurd naar uw werk ruimte zijn STDOUT en STDERR. Omdat Azure Monitor bewaakt Azure-Managed Kubernetes (AKS), wordt uitvoeren-System vandaag niet verzameld vanwege het grote volume van de gegenereerde gegevens. 

### <a name="example-log-search-queries"></a>Voor beeld van zoek opdrachten in Logboeken

Het is vaak handig om query's te bouwen die beginnen met een voor beeld of twee en deze vervolgens te wijzigen zodat ze aan uw vereisten voldoen. Om geavanceerdere query's te kunnen bouwen, kunt u experimenteren met de volgende voorbeeld query's:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Alle levenscyclus gegevens van een container weer geven

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes-gebeurtenissen

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Inventaris van installatie kopieën

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>Container-CPU

**Selecteer de weergave optie lijn diagram**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Container geheugen

**Selecteer de weergave optie lijn diagram**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Aanvragen per minuut met aangepaste metrische gegevens

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

## <a name="query-prometheus-metrics-data"></a>Prometheus metrische gegevens opvragen

Het volgende voor beeld is een Prometheus-query met metrische gegevens per schijf per knoop punt voor het lezen van de schijf.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Als u de metrische gegevens van Prometheus wilt weer geven die door Azure Monitor gefilterd op naam ruimte, geeft u Prometheus op. Hier volgt een voor beeld van een query voor het weer geven van metrische gegevens over Prometheus van de `default` kubernetes-naam ruimte.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus gegevens kunnen ook rechtstreeks worden doorzocht op naam.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Query configuratie of uitval fouten

De volgende voorbeeld query retourneert informatieve gebeurtenissen uit de tabel om eventuele configuratie-of uitval fouten te onderzoeken `KubeMonAgentEvents` .

```
KubeMonAgentEvents | where Level != "Info" 
```

De uitvoer toont resultaten die vergelijkbaar zijn met het volgende voor beeld:

![Query resultaten van informatieve gebeurtenissen van agent vastleggen in logboek](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Volgende stappen

Container Insights bevat geen vooraf gedefinieerde set met waarschuwingen. Bekijk de [prestatie waarschuwingen maken met container Insights](./container-insights-log-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor hoog CPU-en geheugen gebruik ter ondersteuning van uw DevOps-of operationele processen en procedures.
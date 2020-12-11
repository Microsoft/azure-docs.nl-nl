---
title: Logboeken van Azure Monitor voor containers opvragen | Microsoft Docs
description: Azure Monitor voor containers worden metrische gegevens en logboek registraties verzameld, en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 9bfa63a49da33289b8c811007f210e6546579d9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033558"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Logboeken van Azure Monitor voor containers opvragen

Azure Monitor voor containers verzamelt prestatie gegevens, inventaris gegevens en informatie over de status van container hosts en containers. De gegevens worden elke drie minuten verzameld en doorgestuurd naar de Log Analytics-werk ruimte in Azure Monitor. Deze gegevens zijn beschikbaar voor [query's](../log-query/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="container-records"></a>Container records

In de volgende tabel worden de details van de records die worden verzameld door Azure Monitor voor containers gegeven. Zie de naslag informatie voor de tabellen [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) en [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) voor een overzicht van de kolom beschrijvingen.

| Gegevens | Gegevensbron | Gegevenstype | Velden |
|------|-------------|-----------|--------|
| Container voorraad | Kubelet | `ContainerInventory` | TimeGenerated, computer, name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, hebben, ContainerID, ImageID |
| Container logboek | Docker | `ContainerLog` | TimeGenerated, computer, afbeeldings-ID, naam, LogEntrySource, LogEntry, hebben, ContainerID |
| Container-knooppunt inventaris | Uitvoeren-API | `ContainerNodeInventory`| TimeGenerated, computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, hebben|
| Inventaris van Peul in een Kubernetes-cluster | Uitvoeren-API | `KubePodInventory` | TimeGenerated, computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, controller naam, container status, ContainerStatusReason, ContainerID, containerName, naam, PodLabel, namespace, PodStatus, clustername, PodIp, hebben |
| Inventaris van knoop punten die deel uitmaken van een Kubernetes-cluster | Uitvoeren-API | `KubeNodeInventory` | TimeGenerated, computer, clustername, ClusterId, LastTransitionTimeReady, labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, hebben | 
| Kubernetes-gebeurtenissen | Uitvoeren-API | `KubeEvents` | TimeGenerated, computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, bericht, hebben | 
| Services in het Kubernetes-cluster | Uitvoeren-API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, hebben | 
| Prestatie gegevens voor knoop punten die deel uitmaken van het Kubernetes-cluster | Metrische gegevens over gebruik worden opgehaald uit cAdvisor en limieten van de uitvoeren-API | Perf &#124; waarbij ObjectName = = "K8SNode" | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Prestatie gegevens voor containers onderdeel van het Kubernetes-cluster | Metrische gegevens over gebruik worden opgehaald uit cAdvisor en limieten van de uitvoeren-API | Perf &#124; waarbij ObjectName = = "K8SContainer" | Tellernaam &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Aangepaste metrische gegevens ||`InsightsMetrics` | Computer, naam, naam ruimte, oorsprong, hebben, tags<sup>1</sup>, TimeGenerated, type, Va, _ResourceId | 

<sup>1</sup> de eigenschap *Tags* vertegenwoordigt [meerdere dimensies](../platform/data-platform-metrics.md#multi-dimensional-metrics) voor de bijbehorende metriek. Zie overzicht van InsightsMetrics voor meer informatie over de metrische gegevens die in de tabel worden verzameld en opgeslagen `InsightsMetrics` en een beschrijving [](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)van de record eigenschappen.

## <a name="search-logs-to-analyze-data"></a>Zoek Logboeken om gegevens te analyseren

Met Azure Monitor Logboeken kunt u zoeken naar trends, knel punten diagnosticeren, prognoses maken of correleren van gegevens waarmee u kunt bepalen of de huidige cluster configuratie optimaal presteert. Vooraf gedefinieerde zoek opdrachten in Logboeken worden weer gegeven zodat u direct aan de slag kunt gaan met of kunt aanpassen om de informatie op de gewenste manier te retour neren.

U kunt een interactieve analyse van de gegevens in de werk ruimte uitvoeren door de **gebeurtenis logboeken Kubernetes weer geven** of **container logboeken weer geven** te selecteren in het deel venster voor beeld in de vervolg keuzelijst **analyse weer geven** . De pagina **Zoeken in Logboeken** wordt weer gegeven aan de rechter kant van de Azure portal pagina waarop u zich bevond.

![Gegevens analyseren in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

De container logboeken uitvoer die wordt doorgestuurd naar uw werk ruimte zijn STDOUT en STDERR. Omdat Azure Monitor bewaakt Azure-Managed Kubernetes (AKS), wordt uitvoeren-System vandaag niet verzameld vanwege het grote volume van de gegenereerde gegevens. 

### <a name="example-log-search-queries"></a>Voor beeld van zoek opdrachten in Logboeken

Het is vaak handig om query's te bouwen die beginnen met een voor beeld of twee en deze vervolgens te wijzigen zodat ze aan uw vereisten voldoen. Om geavanceerdere query's te kunnen bouwen, kunt u experimenteren met de volgende voorbeeld query's:

| Query’s uitvoeren | Beschrijving | 
|-------|-------------|
| ContainerInventory<br> &#124; project computer, name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; weergave tabel | Alle levenscyclus gegevens van een container weer geven| 
| KubeEvents_CL<br> &#124; waar niet (IsEmpty (Namespace_s))<br> &#124; sorteren op TimeGenerated desc<br> &#124; weergave tabel | Kubernetes-gebeurtenissen|
| ContainerImageInventory<br> &#124; samenvatte AggregatedValue = Count () per afbeelding, ImageTag, uitgevoerd | Inventaris van installatie kopieën | 
| **Selecteer de weergave optie lijn diagram**:<br> Prestaties<br> &#124; waarbij ObjectName = = "K8SContainer" en CounterName = = "cpuUsageNanoCores" &#124; samenvatten AvgCPUUsageNanoCores = AVG (CounterValue) by bin (TimeGenerated, 30m), INSTANCENAME | Container-CPU | 
| **Selecteer de weergave optie lijn diagram**:<br> Prestaties<br> &#124; waarbij ObjectName = = "K8SContainer" en CounterName = = "memoryRssBytes" &#124; samenvatten AvgUsedRssMemoryBytes = AVG (CounterValue) by bin (TimeGenerated, 30m), INSTANCENAME | Container geheugen |
| InsightsMetrics<br> &#124; waarbij name = = "requests_count"<br> &#124;s overzicht van Val = any (val) by TimeGenerated = bin (TimeGenerated, 1M)<br> &#124; sorteren op TimeGenerated ASC<br> &#124; project RequestsPerMinute = val-vorig (val), TimeGenerated <br> Barchart weer geven &#124;  | Aanvragen per minuut met aangepaste metrische gegevens |

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

Azure Monitor voor containers bevat geen vooraf gedefinieerde set met waarschuwingen. Raadpleeg de procedures [voor het maken van prestatie waarschuwingen met Azure monitor voor containers](./container-insights-log-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor hoog CPU-en geheugen gebruik ter ondersteuning van uw DevOps-of operationele processen en procedures.

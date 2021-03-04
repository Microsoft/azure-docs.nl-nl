---
title: Waarschuwingen van VM Insights
description: Hierin wordt beschreven hoe u waarschuwings regels maakt op basis van prestatie gegevens die worden verzameld door VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046802"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Waarschuwingen maken op basis van VM Insights
[Waarschuwingen in azure monitor](../alerts/alerts-overview.md) proactief u op de hoogte stellen van interessante gegevens en patronen in uw bewakings gegevens. VM Insights bevat geen vooraf geconfigureerde waarschuwings regels, maar u kunt uw eigen waarschuwing maken op basis van de gegevens die worden verzameld. Dit artikel bevat richt lijnen voor het maken van waarschuwings regels, met inbegrip van een aantal voorbeeld query's.

> [!IMPORTANT]
> De waarschuwingen die in dit artikel worden beschreven, zijn gebaseerd op logboek query's van gegevens verzamelde VM Insights. Dit wijkt af van de waarschuwingen die zijn gemaakt door [Azure monitor voor VM-gast status](vminsights-health-overview.md) , een functie die momenteel beschikbaar is als open bare preview. Als deze functie in de buurt van algemene Beschik baarheid wordt uitgelegd, worden richt lijnen voor waarschuwingen geconsolideerd.


## <a name="alert-rule-types"></a>Typen waarschuwings regels
Azure Monitor heeft [verschillende typen waarschuwings regels](../alerts/alerts-overview.md#what-you-can-alert-on) op basis van de gegevens die worden gebruikt om de waarschuwing te maken. Alle gegevens die worden verzameld door VM Insights, worden opgeslagen in Azure Monitor logboeken die [logboek waarschuwingen](../alerts/alerts-log.md)ondersteunt. U kunt momenteel geen [metrische waarschuwingen](../alerts/alerts-log.md) gebruiken met prestatie gegevens die zijn verzameld uit VM Insights, omdat de gegevens niet worden verzameld in azure monitor metrieken. Als u gegevens voor metrische waarschuwingen wilt verzamelen, installeert u de [Diagnostische uitbrei ding](../agents/diagnostics-extension-overview.md) voor Windows-vm's of de [telegrafa-agent](../essentials/collect-custom-metrics-linux-telegraf.md) voor Linux-vm's om prestatie gegevens te verzamelen in meet waarden.

Er zijn twee typen logboek waarschuwingen in Azure Monitor:

- [Met het aantal resultaten waarschuwingen](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) wordt een enkele waarschuwing gemaakt wanneer een query ten minste een opgegeven aantal records retourneert. Deze zijn ideaal voor niet-numerieke gegevens, zoals Windows-en syslog-gebeurtenissen die worden verzameld door de [log Analytics agent](../agents/log-analytics-agent.md) of voor het analyseren van prestatie trends op meerdere computers.
- Met [metrische metings waarschuwingen](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) wordt een afzonderlijke waarschuwing gemaakt voor elke record in een query met een waarde die hoger is dan een drempelwaarde die is gedefinieerd in de waarschuwings regel. Deze waarschuwings regels zijn ideaal voor prestatie gegevens die worden verzameld door VM Insights, omdat deze afzonderlijke waarschuwingen voor elke computer kunnen maken.


## <a name="alert-rule-walkthrough"></a>Scenario voor waarschuwings regels
In deze sectie wordt uitgelegd hoe u een waarschuwings regel voor metrische metingen maakt met behulp van prestatie gegevens uit VM Insights. U kunt dit basis proces met verschillende logboek query's gebruiken om te waarschuwen voor verschillende prestatie meter items.

Begin met het maken van een nieuwe waarschuwings regel volgens de procedure in [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../alerts/alerts-log.md). Selecteer voor de **resource** de log Analytics-werk ruimte die Azure monitor vm's gebruikt in uw abonnement. Omdat de doel resource voor regels voor logboek waarschuwingen altijd een Log Analytics werk ruimte is, moet de logboek query een filter voor bepaalde virtuele machines of virtuele-machine schaal sets bevatten. 

Voor de **voor waarde** van de waarschuwings regel gebruikt u een van de query's in de [volgende sectie](#sample-alert-queries) als de **Zoek query**. De query moet een numerieke eigenschap met de naam *AggregatedValue* retour neren. De gegevens moeten worden samenvatten op computer zodat u een afzonderlijke waarschuwing kunt maken voor elke virtuele machine die de drempel waarde overschrijdt.

Selecteer **metrische meting** in de **waarschuwings logica** en geef vervolgens een **drempel waarde** op. Geef bij **trigger waarschuwing op basis van op** hoe vaak de drempel waarde moet worden overschreden voordat een waarschuwing wordt gemaakt. U kunt er waarschijnlijk niet voor zorgen dat de processor eenmaal een drempel waarde overschrijdt en vervolgens terugkeert naar normaal, maar u kunt er wel voor zorgen dat de drempel waarde ten opzichte van meerdere opeenvolgende metingen wordt overschreden.

Het **geëvalueerd op basis van** sectie bepaalt hoe vaak de query wordt uitgevoerd en het tijd venster voor de query. In het onderstaande voor beeld wordt de query elke 15 minuten uitgevoerd en worden de prestatie waarden geëvalueerd die in de afgelopen 15 minuten zijn verzameld.


![Waarschuwings regel voor metrische meting](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Voorbeeld waarschuwings query's
De volgende query's kunnen worden gebruikt met een waarschuwings regel voor metrische metingen met behulp van prestatie gegevens die worden verzameld door VM Insights. Elk bevat een overzicht van gegevens per computer, zodat er een waarschuwing wordt gemaakt voor elke computer met een waarde die de drempel overschrijdt.

### <a name="cpu-utilization"></a>CPU-gebruik

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Beschikbaar geheugen in MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Beschikbaar geheugen in percentage

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Gebruikte logische schijf-alle schijven op elke computer

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Gebruikt logische schijf-afzonderlijke schijven

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS van logische schijf

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Gegevens verhouding van logische schijf

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Netwerk interfaces ontvangen bytes-alle interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Ontvangen netwerk interfaces-bytes-afzonderlijke interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Verzonden network interfaces-bytes-alle interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Verzonden network interfaces-bytes-afzonderlijke interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Schaalset voor virtuele machines
Wijzig met de abonnements-ID, resource groep en naam van de virtuele-machine schaalset.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Specifieke virtuele machine
Wijzig met uw abonnements-ID, resource groep en naam van de virtuele machine.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>CPU-gebruik voor alle reken resources in een abonnement
Wijzig met uw abonnements-ID.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>CPU-gebruik voor alle reken resources in een resource groep
Wijzig met uw abonnements-ID en resource groep.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [waarschuwingen vindt u in azure monitor](../alerts/alerts-overview.md).
- Meer informatie over [logboek query's met behulp van gegevens uit VM Insights](vminsights-log-search.md).

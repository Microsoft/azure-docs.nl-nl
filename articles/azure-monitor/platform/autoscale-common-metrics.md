---
title: Algemene metrische gegevens automatisch schalen
description: Meer informatie over de metrische gegevens die vaak worden gebruikt voor het automatisch schalen van uw Cloud Services, Virtual Machines en Web Apps.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: e1a77fc1b40faca0a339c5e1aaceb71dec8de8bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004633"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor automatisch schalen van algemene metrische gegevens

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor automatisch schalen, kunt u het aantal actieve instanties omhoog of omlaag schalen op basis van telemetriegegevens (metrische gegevens). In dit document worden algemene metrische gegevens beschreven die u mogelijk wilt gebruiken. In het Azure Portal kunt u de metriek kiezen van de resource die u wilt schalen. U kunt echter ook een wille keurige metriek kiezen van een andere resource om door te schalen.

Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md). Andere Azure-Services gebruiken verschillende schaal methoden.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Reken gegevens voor virtuele machines op basis van Resource Manager
Standaard worden metrische gegevens op basis van Resource Manager Virtual Machines en Virtual Machine Scale Sets basis waarden (op het niveau van de host). Wanneer u de diagnostische gegevens verzameling voor een Azure-VM en VMSS configureert, worden de diagnostische Azure-extensie bovendien ook prestatie meter items van het gast besturingssysteem (meestal ' metrische gegevens van het besturings systeem ' genoemd).  U gebruikt al deze metrische gegevens in regels voor automatisch schalen.

U kunt de `Get MetricDefinitions` API/PoSH/CLI gebruiken om de metrische gegevens weer te geven die beschikbaar zijn voor uw VMSS-resource.

Als u VM-schaal sets gebruikt en er geen specifieke metriek wordt weer gegeven, is deze waarschijnlijk *uitgeschakeld* in de uitbrei ding voor diagnostische gegevens.

Als een bepaalde metrische waarde niet wordt bemonsterd of overgedragen op de gewenste frequentie, kunt u de diagnostische configuratie bijwerken.

Als een van beide voor waarden waar is, raadpleegt u [Power shell gebruiken om Azure Diagnostics in te scha kelen op een virtuele machine met Windows](../../virtual-machines/extensions/diagnostics-windows.md) over Power shell om uw Azure VM Diagnostics-extensie te configureren en bij te werken om de metriek in te scha kelen. Dit artikel bevat ook een voor beeld van een configuratie bestand voor diagnostische gegevens.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metrische gegevens van de host voor Windows-en Linux-Vm's op basis van Resource Manager
De volgende metrische gegevens op hostniveau worden standaard verzonden voor Azure VM en VMSS in zowel Windows-als Linux-exemplaren. Deze metrische gegevens beschrijven uw Azure VM, maar worden verzameld van de Azure VM-host, in plaats van via een agent die is geïnstalleerd op de gast-VM. U kunt deze metrische gegevens gebruiken in regels voor automatisch schalen.

- [Metrische gegevens van de host voor Windows-en Linux-Vm's op basis van Resource Manager](./metrics-supported.md#microsoftcomputevirtualmachines)
- [Metrische gegevens van de host voor Windows-en Linux-VM Scale Sets op basis van Resource Manager](./metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Metrische gegevens van het gast besturingssysteem voor Windows-Vm's op basis van Resource Manager
Wanneer u een virtuele machine in azure maakt, wordt Diagnostische gegevens ingeschakeld met behulp van de diagnostische extensie. Met de uitbrei ding voor diagnostische gegevens wordt een set metrische gegevens verzonden die zijn gemaakt van binnen van de virtuele machine. Dit betekent dat u automatisch de metrische gegevens die niet standaard worden verzonden, kunt uitschalen.

U kunt een lijst met metrische gegevens genereren met behulp van de volgende opdracht in Power shell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

U kunt een waarschuwing maken voor de volgende metrische gegevens:

| Naam meetwaarde | Eenheid |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentage |
| \Processor (_Total) \% beschermde tijd |Percentage |
| Gebruikers tijd van \Processor (_Total) \% |Percentage |
| \Processor-frequentie \Processor Information (_Total) |Aantal |
| \System\Processes |Aantal |
| \Process (_Total) \Thread aantal |Aantal |
| \Process (_Total) \Handle aantal |Aantal |
| \Memory \% toegewezen bytes in gebruik |Percentage |
| \Memory\Available Bytes |Bytes |
| \Memory\Committed bytes |Bytes |
| Limiet voor \Memory\Commit |Bytes |
| \Memory\Pool bytes in wissel geheugen |Bytes |
| Niet-wisselbaar \Memory\Pool bytes |Bytes |
| \PhysicalDisk (_Total) \% schijf tijd |Percentage |
| Lees tijd schijf \PhysicalDisk (_Total) \% |Percentage |
| \PhysicalDisk (_Total) \% Schrijf tijd schijf |Percentage |
| \PhysicalDisk (_Total) \Gelezen-overdrachten per seconde |CountPerSecond |
| \PhysicalDisk (_Total) \Gelezen-Lees bewerkingen per seconde |CountPerSecond |
| \PhysicalDisk (_Total) \Gelezen schrijf bewerkingen per seconde |CountPerSecond |
| \PhysicalDisk (_Total) \Gelezen bytes per seconde |BytesPerSecond |
| \PhysicalDisk (_Total) \Gelezen Lees bewerkingen in bytes per seconde |BytesPerSecond |
| \PhysicalDisk (_Total) \Gelezen geschreven bytes per seconde |BytesPerSecond |
| \PhysicalDisk (_Total) \Avg.-wachtrij lengte voor de schijf |Aantal |
| \PhysicalDisk (_Total) \Avg.-wachtrij lengte voor lezen van schijf |Aantal |
| \PhysicalDisk (_Total) \Avg. schijf lengte schrijf wachtrij |Aantal |
| \LogicalDisk (_Total) \% beschik bare ruimte |Percentage |
| \LogicalDisk (_Total) \Resterende MB |Aantal |

### <a name="guest-os-metrics-linux-vms"></a>Gegevens van virtuele machines van het gast besturingssysteem Linux-Vm's
Wanneer u een virtuele machine in azure maakt, wordt de diagnostische gegevens standaard ingeschakeld met behulp van de diagnostische extensie.

U kunt een lijst met metrische gegevens genereren met behulp van de volgende opdracht in Power shell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 U kunt een waarschuwing maken voor de volgende metrische gegevens:

| Naam meetwaarde | Eenheid |
| --- | --- |
| \Memory\AvailableMemory |Bytes |
| \Memory\PercentAvailableMemory |Percentage |
| \Memory\UsedMemory |Bytes |
| \Memory\PercentUsedMemory |Percentage |
| \Memory\PercentUsedByCache |Percentage |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percentage |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percentage |
| \Processor\PercentIdleTime |Percentage |
| \Processor\PercentUserTime |Percentage |
| \Processor\PercentNiceTime |Percentage |
| \Processor\PercentPrivilegedTime |Percentage |
| \Processor\PercentInterruptTime |Percentage |
| \Processor\PercentDPCTime |Percentage |
| \Processor\PercentProcessorTime |Percentage |
| \Processor\PercentIOWaitTime |Percentage |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Seconden |
| \PhysicalDisk\AverageWriteTime |Seconden |
| \PhysicalDisk\AverageTransferTime |Seconden |
| \PhysicalDisk\AverageDiskQueueLength |Aantal |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |Aantal |
| \NetworkInterface\PacketsReceived |Aantal |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Aantal |
| \NetworkInterface\TotalTxErrors |Aantal |
| \NetworkInterface\TotalCollisions |Aantal |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Gegevens die vaak worden gebruikt App Service (server farm)
U kunt ook automatisch schalen uitvoeren op basis van algemene gegevens van webservers, zoals de lengte van de http-wachtrij. De naam van de metrische waarde is **HttpQueueLength**.  In de volgende sectie worden metrische gegevens van de beschik bare server farm (App Service) weer gegeven.

### <a name="web-apps-metrics"></a>Web Apps metrische gegevens
U kunt een lijst met Web Apps metrische gegevens genereren met behulp van de volgende opdracht in Power shell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

U kunt een melding ontvangen over of schalen op basis van deze metrische gegevens.

| Naam meetwaarde | Eenheid |
| --- | --- |
| CpuPercentage |Percentage |
| MemoryPercentage |Percentage |
| DiskQueueLength |Aantal |
| HttpQueueLength |Aantal |
| BytesReceived |Bytes |
| Bytes sent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Veelgebruikte metrische opslag gegevens
U kunt schalen op de lengte van de opslag wachtrij. Dit is het aantal berichten in de opslag wachtrij. De lengte van de opslag wachtrij is een speciale metrische waarde en de drempel waarde is het aantal berichten per exemplaar. Als er bijvoorbeeld twee exemplaren zijn en als de drempel waarde is ingesteld op 100, wordt er geschaald wanneer het totale aantal berichten in de wachtrij 200 is. Dit kan 100 berichten per instantie, 120 en 80 of een andere combi natie van Maxi maal 200 of meer zijn.

Configureer deze instelling in de Azure Portal op de Blade **instellingen** . Voor VM-schaal sets kunt u de instelling voor automatisch schalen in het Resource Manager-sjabloon bijwerken om *metrische waarde* als *ApproximateMessageCount* te gebruiken en de id van de opslag wachtrij als *metricResourceUri* door te geven.

Met een klassiek opslag account kunt u bijvoorbeeld de instelling voor automatisch schalen metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Voor een (niet-klassiek) opslag account zou het metricTrigger het volgende omvatten:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Veelgebruikte Service Bus metrische gegevens
U kunt schalen op Service Bus wachtrij lengte, het aantal berichten in de Service Bus wachtrij. Service Bus wachtrij lengte is een speciale metrische waarde en de drempel waarde is het aantal berichten per exemplaar. Als er bijvoorbeeld twee exemplaren zijn en als de drempel waarde is ingesteld op 100, wordt er geschaald wanneer het totale aantal berichten in de wachtrij 200 is. Dit kan 100 berichten per instantie, 120 en 80 of een andere combi natie van Maxi maal 200 of meer zijn.

Voor VM-schaal sets kunt u de instelling voor automatisch schalen in het Resource Manager-sjabloon bijwerken om *metrische waarde* als *ApproximateMessageCount* te gebruiken en de id van de opslag wachtrij als *metricResourceUri* door te geven.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Voor Service Bus bestaat het concept van de resource groep niet, maar Azure Resource Manager maakt een standaard resource groep per regio. De resource groep bevindt zich doorgaans in de indeling ' default-ServiceBus-[regio] '. Bijvoorbeeld ' default-ServiceBus-oostelijke ', ' default-ServiceBus-Westus ', ' default-ServiceBus-AustraliaEast ' enzovoort.
>
>


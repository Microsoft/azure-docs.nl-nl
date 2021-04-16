---
title: NSG-stroomlogboeken beheren - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u stroomlogboeken van netwerkbeveiligingsgroep in Azure Network Watcher met PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 29340852cabcc77b7488f734a4677697b4a9b972
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535230"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Stroomlogboeken van netwerkbeveiligingsgroep configureren met PowerShell

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroomlogboeken van netwerkbeveiligingsgroep zijn een functie van Network Watcher waarmee u informatie over in- en uit te gaan IP-verkeer via een netwerkbeveiligingsgroep kunt weergeven. Deze stroomlogboeken zijn geschreven in json-indeling en geven uitgaande en binnenkomende stromen per regel weer, de NIC waar de stroom op van toepassing is, informatie met vijf tuples over de stroom (bron-/doel-IP, bron-/doelpoort, protocol) en of het verkeer is toegestaan of geweigerd.

De gedetailleerde specificatie van alle opdrachten voor NSG-stroomlogboeken voor verschillende versies van AzPowerShell vindt u [hier](https://docs.microsoft.com/powershell/module/az.network/#network-watcher)

## <a name="register-insights-provider"></a>Insights-provider registreren

De **Microsoft.Insights-provider** moet zijn geregistreerd om stroomlogregistratie goed te laten werken. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, moet u het volgende script uitvoeren.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen en Traffic Analytics

De opdracht voor het inschakelen van stroomlogboeken wordt weergegeven in het volgende voorbeeld:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Voor het opslagaccount dat u opgeeft, kunnen geen netwerkregels worden geconfigureerd die de netwerktoegang beperken tot alleen Microsoft-services of specifieke virtuele netwerken. Het opslagaccount kan hetzelfde of een ander Azure-abonnement hebben dan de NSG waarmee u het stroomlogboek inschakelen. Als u verschillende abonnementen gebruikt, moeten ze beide worden gekoppeld aan dezelfde Azure Active Directory tenant. Het account dat u voor elk abonnement gebruikt, moet de [benodigde machtigingen hebben.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Stroomlogboeken Traffic Analytics en netwerkbeveiligingsgroep uitschakelen

Gebruik het volgende voorbeeld om verkeersanalyses en stroomlogboeken uit te schakelen:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpprogramma voor toegang tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount is Microsoft Azure Storage Explorer, die u hier kunt downloaden:  https://storageexplorer.com/

Als er een opslagaccount is opgegeven, worden stroomlogboekbestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Ga naar Overzicht van stroomlogboek voor netwerkbeveiligingsgroep voor informatie over de structuur [van het logboek](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

---
title: NSG-stroom logboeken beheren-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u stroom logboeken voor netwerk beveiligings groepen in azure Network Watcher beheert met Power shell
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
ms.openlocfilehash: 771b4ce2999357d729c3ffe557b778cf62a5c0f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98010979"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Stroom logboeken voor netwerk beveiligings groepen configureren met Power shell

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroom logboeken van netwerk beveiligings groepen zijn een functie van Network Watcher waarmee u informatie kunt bekijken over binnenkomend en IP-verkeer via een netwerk beveiligings groep. Deze stroom logboeken worden geschreven in JSON-indeling en uitgaande en inkomende stromen per regel weer gegeven, de NIC waarop de stroom van toepassing is, 5-tuple informatie over de stroom (bron/doel-IP, bron/doel poort, Protocol) en of het verkeer is toegestaan of geweigerd.

## <a name="register-insights-provider"></a>Insights-provider registreren

Voor een goede werking van de stroom registratie moet de **micro soft. Insights** -provider zijn geregistreerd. Als u niet zeker weet of de provider van **micro soft. Insights** is geregistreerd, voert u het volgende script uit.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Stroom logboeken van netwerk beveiligings groep en Traffic Analytics inschakelen

De opdracht om stroom Logboeken in te scha kelen wordt weer gegeven in het volgende voor beeld:

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

Voor het opslag account dat u opgeeft, kunnen geen netwerk regels worden geconfigureerd die de netwerk toegang beperken tot alleen micro soft-Services of specifieke virtuele netwerken. Het opslag account kan zich in hetzelfde of een ander Azure-abonnement bevindt dan de NSG waarvoor u het stroom logboek inschakelt. Als u verschillende abonnementen gebruikt, moeten deze beide zijn gekoppeld aan dezelfde Azure Active Directory Tenant. Het account dat u voor elk abonnement gebruikt, moet de [benodigde machtigingen](required-rbac-permissions.md)hebben.

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Stroom logboeken van Traffic Analytics en netwerk beveiligings groep uitschakelen

Gebruik het volgende voor beeld om Traffic Analytics en stroom Logboeken uit te scha kelen:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Een stroom logboek downloaden

De opslag locatie van een stroom logboek wordt gedefinieerd bij het maken. Een handig hulp middel om toegang te krijgen tot deze stroom logboeken die zijn opgeslagen in een opslag account, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:  https://storageexplorer.com/

Als er een opslag account is opgegeven, worden flow-logboek bestanden opgeslagen in een opslag account op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Voor informatie over de structuur van het logboek gaat u naar [overzicht stroom logboek netwerk beveiligings groep](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het visualiseren van uw NSG-stroom logboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over hoe u [uw NSG-stroom logboeken visualiseren met open-source-hulpprogram ma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

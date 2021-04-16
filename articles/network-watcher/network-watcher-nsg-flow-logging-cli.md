---
title: NSG-stroomlogboeken beheren - Azure CLI
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u logboeken voor netwerkbeveiligingsgroepstromen in Azure Network Watcher met Azure CLI
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
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535264"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Stroomlogboeken van netwerkbeveiligingsgroep configureren met Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroomlogboeken van netwerkbeveiligingsgroep zijn een functie van Network Watcher waarmee u informatie over in- en uit te gaan IP-verkeer via een netwerkbeveiligingsgroep kunt weergeven. Deze stroomlogboeken zijn geschreven in json-indeling en geven uitgaande en binnenkomende stromen per regel weer, de NIC waar de stroom op van toepassing is, informatie met vijf tuples over de stroom (bron-/doel-IP, bron-/doelpoort, protocol) en of het verkeer is toegestaan of geweigerd.

Als u de stappen in dit artikel wilt uitvoeren, moet u de Azure-opdrachtregelinterface voor Mac, Linux en [Windows (CLI) installeren.](/cli/azure/install-azure-cli) De gedetailleerde specificatie van alle opdrachten voor stroomlogboeken vindt u [hier](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest)

## <a name="register-insights-provider"></a>Insights-provider registreren

De **Microsoft.Insights-provider** moet zijn geregistreerd om stroomlogregistratie goed te laten werken. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, moet u het volgende script uitvoeren.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen

De opdracht voor het inschakelen van stroomlogboeken wordt weergegeven in het volgende voorbeeld:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Voor het opslagaccount dat u opgeeft, kunnen geen netwerkregels zijn geconfigureerd die de netwerktoegang beperken tot alleen Microsoft-services of specifieke virtuele netwerken. Het opslagaccount kan hetzelfde of een ander Azure-abonnement hebben dan de NSG waarmee u het stroomlogboek inschakelen. Als u verschillende abonnementen gebruikt, moeten ze beide worden gekoppeld aan dezelfde Azure Active Directory tenant. Het account dat u voor elk abonnement gebruikt, moet de [benodigde machtigingen hebben.](required-rbac-permissions.md) 

Als het opslagaccount zich in een andere resourcegroep of een ander abonnement dan de netwerkbeveiligingsgroep begeeft, geeft u de volledige id van het opslagaccount op in plaats van de naam. Als het opslagaccount zich bijvoorbeeld in een resourcegroep met de naam *RG-Storage* in plaats van *storageAccountName* op te geven in de vorige opdracht, geeft u */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName op.*

## <a name="disable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiligingsgroep uitschakelen

Gebruik het volgende voorbeeld om stroomlogboeken uit te schakelen:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpprogramma voor toegang tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount is Microsoft Azure Storage Explorer, dat u hier kunt downloaden:  https://storageexplorer.com/

Als er een opslagaccount is opgegeven, worden stroomlogboekbestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

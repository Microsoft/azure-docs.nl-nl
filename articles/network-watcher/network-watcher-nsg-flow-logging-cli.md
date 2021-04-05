---
title: NSG-stroom logboeken beheren-Azure CLI
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u stroom logboeken voor netwerk beveiligings groepen in azure Network Watcher beheert met Azure CLI
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
ms.openlocfilehash: 46d12db413fdf01995bc84ae018065e877afb15e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017813"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Stroom logboeken voor netwerk beveiligings groepen configureren met Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroom logboeken van netwerk beveiligings groepen zijn een functie van Network Watcher waarmee u informatie kunt bekijken over binnenkomend en IP-verkeer via een netwerk beveiligings groep. Deze stroom logboeken worden geschreven in JSON-indeling en uitgaande en inkomende stromen per regel weer gegeven, de NIC waarop de stroom van toepassing is, 5-tuple informatie over de stroom (bron/doel-IP, bron/doel poort, Protocol) en of het verkeer is toegestaan of geweigerd.

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure-opdracht regel interface voor Mac, Linux en Windows (CLI) installeren](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Insights-provider registreren

Voor een goede werking van de stroom registratie moet de **micro soft. Insights** -provider zijn geregistreerd. Als u niet zeker weet of de provider van **micro soft. Insights** is geregistreerd, voert u het volgende script uit.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Stroom logboeken van netwerk beveiligings groepen inschakelen

De opdracht om stroom Logboeken in te scha kelen wordt weer gegeven in het volgende voor beeld:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Voor het opslag account dat u opgeeft, kunnen geen netwerk regels worden geconfigureerd die de netwerk toegang beperken tot alleen micro soft-Services of specifieke virtuele netwerken. Het opslag account kan zich in hetzelfde of een ander Azure-abonnement bevindt dan de NSG waarvoor u het stroom logboek inschakelt. Als u verschillende abonnementen gebruikt, moeten deze beide zijn gekoppeld aan dezelfde Azure Active Directory Tenant. Het account dat u voor elk abonnement gebruikt, moet de [benodigde machtigingen](required-rbac-permissions.md)hebben. 

Als het opslag account zich in een andere resource groep of een ander abonnement bevindt dan de netwerk beveiligings groep, geeft u de volledige ID van het opslag account op in plaats van de naam. Bijvoorbeeld, als het opslag account zich in een resource groep bevindt met de naam *RG-opslag*, in plaats van *storageAccountName* op te geven in de vorige opdracht, geeft u */Subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName* op.

## <a name="disable-network-security-group-flow-logs"></a>Stroom logboeken van netwerk beveiligings groep uitschakelen

Gebruik het volgende voor beeld om stroom Logboeken uit te scha kelen:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Een stroom logboek downloaden

De opslag locatie van een stroom logboek wordt gedefinieerd bij het maken. Een handig hulp middel om toegang te krijgen tot deze stroom logboeken die zijn opgeslagen in een opslag account, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:  https://storageexplorer.com/

Als er een opslag account is opgegeven, worden flow-logboek bestanden opgeslagen in een opslag account op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het visualiseren van uw NSG-stroom logboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over hoe u [uw NSG-stroom logboeken visualiseren met open-source-hulpprogram ma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

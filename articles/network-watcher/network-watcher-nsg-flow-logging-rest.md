---
title: NSG-stroomlogboeken beheren - Azure REST API
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u stroomlogboeken van netwerkbeveiligingsgroep beheert in Azure Network Watcher met REST API
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
ms.openlocfilehash: b45d066d0996aaba2a25500f8134085f5e9b6ffb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535197"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Stroomlogboeken van netwerkbeveiligingsgroep configureren met REST API

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroomlogboeken van netwerkbeveiligingsgroep zijn een functie van Network Watcher waarmee u informatie kunt weergeven over in- en uitstromend IP-verkeer via een netwerkbeveiligingsgroep. Deze stroomlogboeken worden geschreven in json-indeling en tonen uitgaande en binnenkomende stromen per regel, de NIC op wie de stroom van toepassing is, informatie met vijf tuples over de stroom (bron-/doel-IP, bron-/doelpoort, protocol) en of het verkeer is toegestaan of geweigerd.

## <a name="before-you-begin"></a>Voordat u begint

ARMclient wordt gebruikt om de REST API aan te roepen met behulp van PowerShell. ARMClient vindt u op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient). De gedetailleerde specificaties van NSG-stroomlogboeken REST API vindt u [hier](https://docs.microsoft.com/rest/api/network-watcher/flowlogs) 

In dit scenario wordt ervan uitgenomen dat u de stappen in Een Network Watcher hebt [gevolgd om](network-watcher-create.md) een Network Watcher.

> [!Important]
> Voor Network Watcher REST API de naam van de resourcegroep in de aanvraag-URI is de resourcegroep die de Network Watcher bevat, niet de resources waar u de diagnostische acties op wilt uitvoeren.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, ziet u hoe u stroomlogboeken kunt inschakelen, uitschakelen en er query's op kunt uitvoeren met behulp van de REST API. Ga naar Stroomlogregistratie voor netwerkbeveiligingsgroep - Overzicht voor meer informatie over stroomlogregistraties voor [netwerkbeveiligingsgroep.](network-watcher-nsg-flow-logging-overview.md)

In dit scenario gaat u het volgende doen:

* Stroomlogboeken inschakelen (versie 2)
* Stroomlogboeken uitschakelen
* Status van stroomlogboeken opvragen

## <a name="log-in-with-armclient"></a>Aanmelden met ARMClient

Meld u aan bij armclient met uw Azure-referenties.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-provider registreren

Als u wilt dat stroomlogregistratie werkt, moet de **Microsoft.Insights-provider** zijn geregistreerd. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, moet u het volgende script uitvoeren.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen

De opdracht voor het inschakelen van stroomlogboeken versie 2 wordt weergegeven in het volgende voorbeeld. Vervang voor versie 1 het veld 'versie' door '1':

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Het antwoord dat uit het vorige voorbeeld is geretourneerd, is als volgt:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiligingsgroep uitschakelen

Gebruik het volgende voorbeeld om stroomlogboeken uit te schakelen. De aanroep is hetzelfde als het inschakelen van stroomlogboeken, behalve **false** is ingesteld voor de ingeschakelde eigenschap.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Het antwoord dat uit het vorige voorbeeld is geretourneerd, is als volgt:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Stroomlogboeken opvragen

Met de volgende REST-aanroep wordt de status van stroomlogboeken in een netwerkbeveiligingsgroep opgevraagd.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Hier volgt een voorbeeld van het antwoord dat wordt geretourneerd:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpprogramma voor toegang tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount is Microsoft Azure Storage Explorer, die u hier kunt downloaden:  https://storageexplorer.com/

Als een opslagaccount is opgegeven, worden pakketopnamebestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

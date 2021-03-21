---
title: Problemen met verbindingen oplossen-Azure CLI
titleSuffix: Azure Network Watcher
description: Meer informatie over het gebruik van de functie verbinding oplossen van Azure Network Watcher met behulp van de Azure CLI.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e93a9b7ba2c7f801761d33ccacfa3ecdb3c48503
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019785"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Verbindings problemen met Azure-Network Watcher met Azure CLI oplossen

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure-CLI](network-watcher-connectivity-cli.md)
> - [Azure REST-API](network-watcher-connectivity-rest.md)

Meer informatie over het gebruik van verbindings problemen oplossen om te controleren of een directe TCP-verbinding van een virtuele machine naar een bepaald eind punt tot stand kan worden gebracht.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de volgende resources hebt:

* Een exemplaar van Network Watcher in de regio waarvoor u problemen met een verbinding wilt oplossen.
* Virtuele machines voor het oplossen van verbindingen met.

> [!IMPORTANT]
> Verbindings problemen oplossen vereist dat de VM-extensie is geïnstalleerd op de VM die u wilt oplossen `AzureNetworkWatcherExtension` . Voor het installeren van de uitbrei ding op een Windows-VM gaat u naar [azure Network Watcher agent-extensie voor virtuele machines voor Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en voor Linux VM gaat u naar de [Azure Network Watcher agent-extensie voor virtuele machines voor Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). De uitbrei ding is niet vereist voor het eind punt van de bestemming.

## <a name="check-connectivity-to-a-virtual-machine"></a>Controleer de verbinding met een virtuele machine

In dit voor beeld wordt de verbinding met een virtuele doel machine via poort 80 gecontroleerd.

### <a name="example"></a>Voorbeeld

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Antwoord

Het volgende antwoord is afkomstig uit het vorige voor beeld.  In dit antwoord is de `ConnectionStatus` **onbereikbaar**. U kunt zien dat alle probe-verzen ding is mislukt. De connectiviteit op het virtuele apparaat is mislukt vanwege een door de gebruiker geconfigureerde `NetworkSecurityRule` **UserRule_Port80**, geconfigureerd om inkomend verkeer op poort 80 te blok keren. Deze informatie kan worden gebruikt om verbindings problemen te onderzoeken.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Routerings problemen valideren

In dit voor beeld wordt de verbinding tussen een virtuele machine en een extern eind punt gecontroleerd.

### <a name="example"></a>Voorbeeld

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Antwoord

In het volgende voor beeld `connectionStatus` wordt de wordt weer gegeven als **onbereikbaar**. In de `hops` Details kunt u zien `issues` dat het verkeer is geblokkeerd vanwege een `UserDefinedRoute` .

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Website latentie controleren

In het volgende voor beeld wordt de verbinding met een website gecontroleerd.

### <a name="example"></a>Voorbeeld

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Antwoord

In het volgende antwoord ziet u `connectionStatus` dat de weer gave kan worden weer gegeven als **bereikbaar**. Wanneer een verbinding tot stand is gebracht, worden er latentie waarden gegeven.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Connectiviteit met een opslag eindpunt controleren

In het volgende voor beeld wordt de connectiviteit van een virtuele machine naar een blog Storage-account gecontroleerd.

### <a name="example"></a>Voorbeeld

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Antwoord

De volgende JSON is het voorbeeld antwoord van het uitvoeren van de vorige cmdlet. Als de controle is geslaagd, `connectionStatus` wordt de eigenschap weer gegeven als **bereikbaar**.  U vindt de details over het aantal hops dat is vereist om de opslag-Blob en latentie te bereiken.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatiseren van pakket opnames met waarschuwingen voor virtuele machines door het weer geven van [een waarschuwing gegenereerde pakket opname maken](network-watcher-alert-triggered-packet-capture.md)

Controleren of bepaalde verkeer is toegestaan in of buiten uw virtuele machine door te kijken naar controle van de [IP-stroom](diagnose-vm-network-traffic-filtering-problem.md)
---
title: Pakket opnames beheren met Azure Network Watcher-Azure CLI | Microsoft Docs
description: Op deze pagina wordt uitgelegd hoe u de functie voor het vastleggen van pakketten van Network Watcher beheert met behulp van de Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 838a5255c013b530aa3bfdb857a2ba34b7dbeeed
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98010962"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Pakket opnames beheren met Azure Network Watcher met behulp van de Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure-CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Met Network Watcher-pakket opname kunt u opname sessies maken om verkeer van en naar een virtuele machine bij te houden. Er worden filters voor de opname sessie gegeven om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Met pakket opname kunt u netwerk afwijkingen zowel reactief als proactief vaststellen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het verkrijgen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en nog veel meer. Doordat pakket opnames op afstand kunnen worden geactiveerd, vereenvoudigt deze mogelijkheid de belasting van het hand matig uitvoeren van een pakket opname en op de gewenste computer, waardoor kost bare tijd wordt bespaard.

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure Command-Line-interface voor Mac, Linux en Windows (Azure CLI) installeren](/cli/azure/install-azure-cli).

Dit artikel begeleidt u door de verschillende beheer taken die momenteel beschikbaar zijn voor pakket opname.

- [**Een pakket opname starten**](#start-a-packet-capture)
- [**Een pakket opname stoppen**](#stop-a-packet-capture)
- [**Een pakket opname verwijderen**](#delete-a-packet-capture)
- [**Pakket opname downloaden**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de volgende resources hebt:

- Een exemplaar van Network Watcher in de regio waarin u een pakket opname wilt maken
- Een virtuele machine waarop de pakket Capture-extensie is ingeschakeld.

> [!IMPORTANT]
> Voor het vastleggen van pakketten moet een agent op de virtuele machine worden uitgevoerd. De agent wordt geïnstalleerd als een uitbrei ding. Ga naar [extensies en functies van virtuele machines](../virtual-machines/extensions/features-windows.md)voor instructies voor VM-extensies.

## <a name="install-vm-extension"></a>VM-extensie installeren

### <a name="step-1"></a>Stap 1

Voer de `az vm extension set` opdracht uit om de pakket Capture-agent te installeren op de virtuele gast machine.

Voor virtuele Windows-machines:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Voor virtuele Linux-machines:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Stap 2

Om ervoor te zorgen dat de agent is geïnstalleerd, voert u de `vm extension show` opdracht uit en geeft u de naam van de resource groep en de virtuele machine door. Controleer de resulterende lijst om te controleren of de agent is geïnstalleerd.

Voor virtuele Windows-machines:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Voor virtuele Linux-machines:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Het volgende voor beeld is een voor beeld van het resultaat van het uitvoeren van `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Een pakket opname starten

Zodra de voor gaande stappen zijn voltooid, wordt de pakket Capture-agent geïnstalleerd op de virtuele machine.

### <a name="step-1"></a>Stap 1

Een opslag account ophalen. Dit opslag account wordt gebruikt om het pakket opname bestand op te slaan.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Stap 2

U bent nu klaar om een pakket opname te maken.  Eerst gaan we de para meters bekijken die u wilt configureren. Filters zijn een van deze para meters die kunnen worden gebruikt om de gegevens te beperken die worden opgeslagen door de pakket opname. In het volgende voor beeld wordt een pakket opname met verschillende filters ingesteld.  Met de eerste drie filters worden uitgaande TCP-verkeer alleen verzameld van lokale IP-10.0.0.3 naar doel poorten 20, 80 en 443.  Met het laatste filter wordt alleen UDP-verkeer verzameld.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Het volgende voor beeld is de verwachte uitvoer van het uitvoeren van de `az network watcher packet-capture create` opdracht.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Pakket opname ophalen

Als u de `az network watcher packet-capture show-status` opdracht uitvoert, wordt de status opgehaald van een actieve of voltooide pakket opname.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Het volgende voor beeld is de uitvoer van de `az network watcher packet-capture show-status` opdracht. Het volgende voor beeld is wanneer de opname wordt gestopt met een StopReason van TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Een pakket opname stoppen

Door de `az network watcher packet-capture stop` opdracht uit te voeren als een opname sessie wordt uitgevoerd, wordt deze gestopt.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> De opdracht retourneert geen reactie wanneer deze wordt uitgevoerd op een actieve opname sessie of een bestaande sessie die al is gestopt.

## <a name="delete-a-packet-capture"></a>Een pakket opname verwijderen

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Als u een pakket opname verwijdert, wordt het bestand in het opslag account niet verwijderd.

## <a name="download-a-packet-capture"></a>Pakket opname downloaden

Zodra de pakket opname sessie is voltooid, kan het opname bestand worden geüpload naar de Blob-opslag of naar een lokaal bestand op de virtuele machine. De opslag locatie van de pakket opname wordt gedefinieerd tijdens het maken van de sessie. Een handig hulp middel om toegang te krijgen tot deze opname bestanden die zijn opgeslagen in een opslag account, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:  https://storageexplorer.com/

Als er een opslag account is opgegeven, worden pakket opname bestanden opgeslagen in een opslag account op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatiseren van pakket opnames met waarschuwingen voor virtuele machines door het weer geven van [een waarschuwing gegenereerde pakket opname maken](network-watcher-alert-triggered-packet-capture.md)

Controleren of bepaalde verkeer is toegestaan in of buiten uw virtuele machine door te kijken naar controle van de [IP-stroom](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
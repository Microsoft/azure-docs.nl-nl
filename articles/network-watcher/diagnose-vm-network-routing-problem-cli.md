---
title: Een probleem met VM-netwerkroutering vaststellen - Azure CLI
titleSuffix: Azure Network Watcher
description: In dit artikel leert u hoe u Azure CLI gebruikt om een probleem met netwerkroutering van virtuele machines te diagnosticeren met behulp van de functie volgende hop van Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 2ca7a3b25b1355e21782c1d9f736d20a14cbd4ac
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785447"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnose uitvoeren voor een probleem met netwerkroutering van virtuele machines - Azure CLI

In dit artikel implementeert u een virtuele machine (VM) en controleert u de communicatie naar een IP-adres en URL. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

- De Azure CLI-opdrachten in dit artikel zijn zo opgemaakt dat ze worden uitgevoerd in een Bash-shell.

## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm#az_vm_create). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door met de resterende stappen totdat de VM is gemaakt en uitvoer wordt geretourneerd door de Azure CLI.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerkcommunicatie met Network Watcher wilt testen, moet u eerst een netwerk-watcher inschakelen in de regio waarin de VM zich wilt testen, en vervolgens de functie volgende hop van Network Watcher gebruiken om de communicatie te testen.

### <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een netwerk-watcher hebt ingeschakeld in de regio VS - oost, gaat u verder [met Volgende hop gebruiken.](#use-next-hop) Gebruik de [opdracht az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) om een netwerk-watcher te maken in de regio VS - oost:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Volgende hop gebruiken

Azure maakt automatisch routes naar standaardbestemmingen. U kunt uw eigen, aangepaste routes maken om die standaardroutes te overschrijven. Soms hebben aangepaste routes tot gevolg dat de communicatie mislukt. Als u de routering vanaf een VM wilt testen, gebruikt u [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) om de volgende routeringshop te bepalen wanneer verkeer is bestemd voor een specifiek adres.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Na enkele seconden ziet u in de uitvoer dat **nextHopType** **Internet** is en dat **routeTableId** **System Route** is. Dit resultaat laat u weten dat er een geldige route naar de bestemming is.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

De geretourneerde uitvoer laat u weten **dat Geen** **het nextHopType** is en dat **routeTableId** ook Systeemroute **is.** Hieruit kunt u afleiden dat er wel een geldige systeemroute is naar de bestemming, maar dat er geen volgende hop is voor het routeren van het verkeer naar de bestemming.

## <a name="view-details-of-a-route"></a>Details van een route weergeven

Als u de routering verder wilt analyseren, controleert u de effectieve routes voor de netwerkinterface met de [opdracht az network nic show-effective-route-table:](/cli/azure/network/nic#az_network_nic_show_effective_route_table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

De volgende tekst is opgenomen in de geretourneerde uitvoer:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Toen u de opdracht hebt gebruikt om uitgaande communicatie naar `az network watcher show-next-hop` 13.107.21.200 te testen in Volgende [hop gebruiken,](#use-next-hop)werd de route met **het adresPrefix** 0.0.0.0/0** gebruikt om verkeer naar het adres te sturen, omdat geen enkele andere route in de uitvoer het adres bevat. De standaardinstelling is dat alle adressen die niet zijn opgegeven in het adresvoorvoegsel van een andere route, worden doorgestuurd naar internet.

Toen u de opdracht hebt gebruikt om uitgaande communicatie naar `az network watcher show-next-hop` 172.31.0.100 te testen, bleek uit het resultaat dat er geen volgend hoptype was. In de geretourneerde uitvoer ziet u ook de volgende tekst:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Zoals u in de uitvoer van de opdracht kunt zien, is er wel een standaardroute naar het voorvoegsel `az network watcher nic show-effective-route-table` 172.16.0.0/12, dat het adres 172.31.0.100 bevat, maar **nextHopType** is **Geen.** Azure maakt een standaardroute naar 172.16.0.0/12, maar stelt geen volgend hoptype in, tenzij daar een reden voor is. Als u bijvoorbeeld het adresbereik 172.16.0.0/12 hebt toegevoegd aan de adresruimte van het virtuele netwerk, wijzigt Azure **het nextHopType** in **Virtueel** netwerk voor de route. Met een controle wordt vervolgens **Virtueel netwerk als** **nextHopType weer geven.**

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt en netwerkroutering van de VM vastgesteld. U hebt geleerd dat Azure verschillende standaardroutes maakt en u hebt de routering naar twee verschillende bestemmingen getest. Lees hier meer over [routering in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en hoe u [aangepaste routes maakt](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Voor uitgaande VM-verbindingen kunt u ook de latentie en toegestane en geweigerde netwerkverkeer tussen de VM en een eindpunt bepalen met behulp van de probleemoplossingsfunctie van Network Watcher voor [verbindingen.](network-watcher-connectivity-cli.md) U kunt de communicatie tussen een VM en een eindpunt, zoals een IP-adres of URL, in de Network Watcher bewaken. Zie Een netwerkverbinding [bewaken voor meer informatie.](connection-monitor.md)

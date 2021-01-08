---
title: Een VM-netwerk routerings probleem vaststellen-Azure CLI
titleSuffix: Azure Network Watcher
description: In dit artikel leert u hoe u met Azure CLI een probleem met de netwerk routering van een virtuele machine kunt vaststellen met behulp van de volgende hop-functionaliteit van Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 42174ce094242a6e7412deea0bf1f0eed0f3b6ea
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013172"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Een probleem met de netwerk routering van een virtuele machine diagnosticeren-Azure CLI

In dit artikel implementeert u een virtuele machine (VM) en controleert u de communicatie met een IP-adres en een URL. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

- De Azure CLI-opdrachten in dit artikel zijn ingedeeld om te worden uitgevoerd in een bash-shell.

## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm#az-vm-create). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door met de resterende stappen totdat de VM is gemaakt en uitvoer wordt geretourneerd door de Azure CLI.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerk communicatie met Network Watcher wilt testen, moet u eerst een Network Watcher inschakelen in de regio waarin de virtuele machine zich bevindt die u wilt testen en vervolgens de volgende hop-mogelijkheid Network Watcher gebruiken om de communicatie te testen.

### <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een Network Watcher hebt ingeschakeld in de regio VS-Oost, gaat u verder met de [volgende hop](#use-next-hop). Gebruik de opdracht [AZ Network Watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) om een netwerk-Watcher te maken in de regio VS-Oost:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Volgende hop gebruiken

Azure maakt automatisch routes naar standaardbestemmingen. U kunt uw eigen, aangepaste routes maken om die standaardroutes te overschrijven. Soms hebben aangepaste routes tot gevolg dat de communicatie mislukt. Als u de route ring van een virtuele machine wilt testen, gebruikt u [AZ Network Watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) om de volgende routerings-hop te bepalen wanneer het verkeer voor een specifiek adres bestemd is.

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

Na een paar seconden is de uitvoer informeert dat de **NextHopType** **Internet** is en dat de **routeTableId** **systeem route** is. Dit leidt ertoe dat u weet dat er een geldige route naar de bestemming is.

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

De uitvoer heeft informeert dat **geen** **nextHopType** is en dat de **routeTableId** ook **systeem routes** zijn. Hieruit kunt u afleiden dat er wel een geldige systeemroute is naar de bestemming, maar dat er geen volgende hop is voor het routeren van het verkeer naar de bestemming.

## <a name="view-details-of-a-route"></a>Details van een route weergeven

Als u de route ring verder wilt analyseren, controleert u de meest efficiënte routes voor de netwerk interface met de opdracht [AZ Network NIC show-ingangsdatum-route tabel](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

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

Wanneer u de opdracht hebt gebruikt `az network watcher show-next-hop` voor het testen van uitgaande communicatie naar 13.107.21.200 in de [volgende hop](#use-next-hop), is de route met de **addressPrefix** 0.0.0.0/0 * * gebruikt voor het routeren van verkeer naar het adres, omdat geen enkele andere route in de uitvoer het adres bevat. De standaardinstelling is dat alle adressen die niet zijn opgegeven in het adresvoorvoegsel van een andere route, worden doorgestuurd naar internet.

Wanneer u de opdracht hebt gebruikt `az network watcher show-next-hop` voor het testen van uitgaande communicatie naar 172.31.0.100, heeft het resultaat gemeld dat er geen type voor de volgende hop is. In de geretourneerde uitvoer ziet u ook de volgende tekst:

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

Zoals u kunt zien in de uitvoer van de `az network watcher nic show-effective-route-table` opdracht, is er een standaard route naar het voor voegsel 172.16.0.0/12, dat het 172.31.0.100-adres bevat, de **NextHopType** is **geen**. Azure maakt een standaardroute naar 172.16.0.0/12, maar stelt geen volgend hoptype in, tenzij daar een reden voor is. Als u bijvoorbeeld het adres bereik 172.16.0.0/12 hebt toegevoegd aan de adres ruimte van het virtuele netwerk, wijzigt Azure de **nextHopType** in het **virtuele netwerk** voor de route. Een controle wordt vervolgens het **virtuele netwerk** weer gegeven als de **nextHopType**.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een virtuele machine gemaakt en een diagnose van de netwerk routering van de virtuele machine. U hebt geleerd dat Azure verschillende standaardroutes maakt en u hebt de routering naar twee verschillende bestemmingen getest. Lees hier meer over [routering in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en hoe u [aangepaste routes maakt](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Voor uitgaande VM-verbindingen kunt u ook de latentie en het toegestane en geweigerde netwerk verkeer tussen de virtuele machine en een eind punt bepalen met behulp van de [verbindings problemen](network-watcher-connectivity-cli.md) met de verbinding van Network Watcher. U kunt de communicatie tussen een virtuele machine en een eind punt, zoals een IP-adres of URL, na verloop van tijd bewaken met behulp van de Network Watcher-functie verbindings monitor. Zie [een netwerk verbinding bewaken](connection-monitor.md)voor meer informatie.

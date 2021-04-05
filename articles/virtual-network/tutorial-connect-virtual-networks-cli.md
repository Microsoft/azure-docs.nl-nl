---
title: Verbinding maken met virtuele netwerken met VNet-peering-Azure CLI
description: In dit artikel leert u hoe u virtuele netwerken kunt verbinden met virtuele netwerk peering, met behulp van Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741770"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuele netwerken verbinden met virtuele netwerk peering met behulp van Azure CLI

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In dit artikel leert u het volgende:

* Twee virtuele netwerken maken
* Twee virtuele netwerken koppelen met virtueel-netwerkpeering
* Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
* Communiceren tussen VM's

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

Voordat u een virtueel netwerk maakt, moet u een resource groep maken voor het virtuele netwerk en alle andere resources die in dit artikel zijn gemaakt. Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork1* gemaakt met het adres voorvoegsel *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Maak een virtueel netwerk met de naam *myVirtualNetwork2* met het adres voorvoegsel *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

Peerings worden tot stand gebracht tussen virtuele netwerk-Id's, dus u moet eerst de ID van elk virtueel netwerk ophalen met [AZ Network vnet show](/cli/azure/network/vnet) en de id opslaan in een variabele.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Maak een peering van *myVirtualNetwork1* naar *myVirtualNetwork2* met [AZ Network vnet peering Create](/cli/azure/network/vnet/peering). Als de `--allow-vnet-access` para meter niet is opgegeven, wordt er een peering tot stand gebracht, maar kan deze niet door de communicatie stromen.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **peeringState** is *gestart*. De peering blijft in de *geïnitieerde* status totdat u de peering van *myVirtualNetwork2* naar *myVirtualNetwork1* maakt. Maak een peering van *myVirtualNetwork2* naar *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **peeringState** is *verbonden*. Azure heeft ook de peering-status van de *myVirtualNetwork1-myVirtualNetwork2-* peering gewijzigd in *verbonden*. Bevestig dat de peering-status voor de *myVirtualNetwork1-myVirtualNetwork2-* peering *is* gewijzigd in verbonden met [AZ Network vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Resources in één virtueel netwerk kunnen niet communiceren met resources in het andere virtuele netwerk totdat de **peeringState** voor de peerings in beide virtuele netwerken is *verbonden*. 

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voor beeld wordt een VM gemaakt met de naam *myVm1* in het virtuele netwerk *myVirtualNetwork1* . Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. Met deze `--no-wait` optie wordt de virtuele machine op de achtergrond gemaakt, zodat u verder kunt gaan met de volgende stap.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>De tweede VM maken

Maak een VM in het virtuele netwerk *myVirtualNetwork2* .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die lijkt op het volgende voor beeld: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt voor toegang tot de virtuele machine via internet in een latere stap.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

Gebruik de volgende opdracht om een SSH-sessie te maken met de *myVm2* -VM. Vervang door `<publicIpAddress>` het open bare IP-adres van uw virtuele machine. In het vorige voor beeld is het open bare IP-adres *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Ping de virtuele machine in *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

U ontvangt vier antwoorden. 

Sluit de SSH-sessie naar de *myVm2* -VM. 

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik [AZ Group delete](/cli/azure/group) om de resource groep en alle resources die deze bevat te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u twee netwerken in dezelfde Azure-regio kunt verbinden met virtuele netwerk peering. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region) en in [ verschillende Azure-abonnementen ](create-peering-different-subscriptions.md#cli) 'peeren', en peering gebruiken om [netwerkontwerpen met een stertopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) te maken. Zie voor meer informatie over virtueel-netwerkpeering [Peering op virtueel netwerk](virtual-network-peering-overview.md) en [Virtueel-netwerkpeerings beheren](virtual-network-manage-peering.md).

U kunt [uw eigen computer verbinden met een virtueel netwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN en communiceren met resources in een virtueel netwerk of in gekoppelde virtuele netwerken. Zie [script voorbeelden](cli-samples.md)voor herbruikbare scripts voor het volt ooien van veel van de taken die worden behandeld in de artikelen in het virtuele netwerk.

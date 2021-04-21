---
title: 'Een virtueel netwerk maken: quickstart - Azure CLI'
titlesuffix: Azure Virtual Network
description: In deze quickstart leert u hoe u een virtueel netwerk maakt met Azure CLI. Met een virtueel netwerk kunnen Azure-resources met elkaar en met internet communiceren.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776749"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Quickstart: Een virtueel netwerk maken met Azure CLI

Met een virtueel netwerk kunnen Azure-resources, zoals virtuele machines, privé met elkaar en met internet communiceren. 

In deze snelstart leert u hoe u een virtueel netwerk maakt. Nadat u een virtueel netwerk hebt gemaakt, implementeert u twee virtuele machines in het virtuele netwerk. Vervolgens maakt u verbinding met de virtuele machines via internet en is er privécommunicatie via het nieuwe virtuele netwerk mogelijk.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Een resourcegroep en een virtueel netwerk maken

Voordat u een virtueel netwerk kunt maken, moet u een resourcegroep maken die het virtuele netwerk host. Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). In dit voorbeeld wordt een resourcegroep met **de naam CreateVNetQS-rg** gemaakt op de **locatie Eastus:**

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). In dit voorbeeld wordt een standaard virtueel netwerk met de **naam myVNet gemaakt** met één subnet met de **naam default**:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM’s in het virtuele netwerk.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [az vm create](/cli/azure/vm#az_vm_create). 

Als SSH-sleutels niet al bestaan op de standaardsleutellocatie, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. 

Met de optie `--no-wait` wordt de virtuele machine op de achtergrond gemaakt. U kunt doorgaan met de volgende stap. 

In dit voorbeeld wordt een VM met de **naam myVM1 gemaakt:**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>De tweede VM maken

U hebt de `--no-wait` optie in de vorige stap gebruikt. U kunt verder gaan en de tweede VM maken met de **naam myVM2.**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Azure CLI-uitvoerbericht

Het maken van de VM's duurt enkele minuten. Nadat Azure de virtuele machines heeft gemaakt, wordt met Azure CLI als volgt uitvoer geretourneerd:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>Openbaar IP-adres van VM

Gebruik [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)om het openbare IP-adres **myVM2** op te halen:

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Vervang in deze opdracht door `<publicIpAddress>` het openbare IP-adres van uw **VM myVM2:**

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

Voer de volgende opdracht in om **privécommunicatie tussen de VM's myVM2** en **myVM1** te bevestigen:

```bash
ping myVM1 -c 4
```

U ontvangt vier reacties van *10.0.0.4*.

Sluit de SSH-sessie met de **VM myVM2** af.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Volgende stappen

Voor deze snelstart geldt het volgende: 

* U hebt een standaard virtueel netwerk en twee virtuele machines gemaakt. 
* U hebt met één virtuele machine verbinding gemaakt via internet en er is er privécommunicatie tussen de twee virtuele machines geweest.

Privécommunicatie tussen VM's is onbeperkt in een virtueel netwerk. 

Ga verder naar het volgende artikel voor meer informatie over het configureren van verschillende typen VM-netwerkcommunicatie:
> [!div class="nextstepaction"]
> [Netwerkverkeer filteren](tutorial-filter-network-traffic.md)

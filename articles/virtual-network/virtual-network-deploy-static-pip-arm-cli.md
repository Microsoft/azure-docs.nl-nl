---
title: Een VM maken met een statisch openbaar IP-adres - Azure CLI-| Microsoft Docs
description: Meer informatie over het maken van een VM met een statisch openbaar IP-adres met behulp van de Azure-opdrachtregelinterface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790127"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Een virtuele machine met een statisch openbaar IP-adres maken met de Azure CLI

U kunt een virtuele machine met een statisch openbaar IP-adres maken. Met een openbaar IP-adres kunt u vanaf internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres toe in plaats van een dynamisch adres om ervoor te zorgen dat het adres nooit verandert. Meer informatie over [statische openbare IP-adressen.](./public-ip-addresses.md#allocation-method) Zie IP-adressen toevoegen, wijzigen of verwijderen om een openbaar IP-adres dat is toegewezen aan een bestaande virtuele machine te wijzigen van dynamisch in statisch, of om te werken met [privé-IP-adressen.](virtual-network-network-interface-addresses.md) Openbare IP-adressen hebben een [nominaal bedrag](https://azure.microsoft.com/pricing/details/ip-addresses)en er is een limiet voor het aantal openbare IP-adressen dat u per abonnement kunt gebruiken. [](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U kunt de volgende stappen voltooien vanaf uw lokale computer of met behulp van de Azure Cloud Shell. Als u uw lokale computer wilt gebruiken, moet u ervoor zorgen dat [de Azure CLI is geïnstalleerd.](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de Azure Cloud Shell, **selecteert u Proberen** in de rechterbovenhoek van een opdrachtvak dat volgt. De Cloud Shell meldt u aan bij Azure.

1. Als u de Cloud Shell, gaat u verder met stap 2. Open een opdrachtsessie en meld u aan bij Azure met `az login` .
2. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep gemaakt in de Azure-regio VS - oost:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm#az_vm_create). Met `--public-ip-address-allocation=static` de optie wordt een statisch openbaar IP-adres toegewezen aan de virtuele machine. In het volgende voorbeeld wordt een virtuele Ubuntu-machine gemaakt met een statisch openbaar IP-adres van de basis-SKU met de *naam myPublicIpAddress:*

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Als het openbare IP-adres een standaard-SKU moet zijn, voegt u `--public-ip-sku Standard` toe aan de vorige opdracht. Meer informatie over openbare [IP-adres-SKU's.](./public-ip-addresses.md#sku) Als de virtuele machine wordt toegevoegd aan de back-endpool van een openbare Azure Load Balancer, moet de SKU van het openbare IP-adres van de virtuele machine overeenkomen met de SKU van het openbare IP-adres van de load balancer. Zie voor meer [informatie Azure Load Balancer.](../load-balancer/skus.md)

4. Bekijk het toegewezen openbare IP-adres en bevestig dat het is gemaakt als een statisch, basis-SKU-adres met [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure heeft een openbaar IP-adres toegewezen van adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Wijzig de IP-adresinstellingen in het besturingssysteem van de virtuele machine niet. Het besturingssysteem is niet op de hoogte van openbare IP-adressen van Azure. Hoewel u instellingen voor privé-IP-adressen aan het besturingssysteem kunt toevoegen, wordt u aangeraden dit niet te doen tenzij dit nodig is en pas nadat u Een privé-IP-adres toevoegen aan een [besturingssysteem hebt gelezen.](virtual-network-network-interface-addresses.md#private)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure
- Meer informatie over alle instellingen [voor openbare IP-adressen](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen en](./private-ip-addresses.md) het toewijzen van een statisch [privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over het maken [van virtuele Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [en Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

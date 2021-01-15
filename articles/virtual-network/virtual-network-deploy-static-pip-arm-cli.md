---
title: Een virtuele machine met een statisch openbaar IP-adres maken-Azure CLI | Microsoft Docs
description: Meer informatie over het maken van een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure-opdracht regel interface (CLI).
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
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221339"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Een virtuele machine met een statisch openbaar IP-adres maken met behulp van de Azure CLI

U kunt een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres kunt u via internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres in plaats van een dynamisch adres toe om ervoor te zorgen dat het adres nooit wordt gewijzigd. Meer informatie over [statische open bare IP-adressen](./public-ip-addresses.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over het wijzigen van een openbaar IP-adres dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch of voor gebruik van privé-IP-adressen. Open bare IP-adressen hebben een [nominale belasting](https://azure.microsoft.com/pricing/details/ip-addresses)en er geldt een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor het aantal open bare IP-adressen dat u per abonnement kunt gebruiken.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U kunt de volgende stappen uitvoeren vanaf uw lokale computer of met behulp van de Azure Cloud Shell. Als u uw lokale computer wilt gebruiken, controleert u of de [Azure cli is geïnstalleerd](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u de Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van een wille keurig opdracht venster. De Cloud Shell meldt u aan bij Azure.

1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2. Open een opdracht sessie en meld u aan bij Azure met `az login` .
2. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep gemaakt in de regio VS Oost Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static`Met deze optie wordt een statisch openbaar IP-adres toegewezen aan de virtuele machine. In het volgende voor beeld wordt een Ubuntu-virtuele machine gemaakt met een statisch IP-adres voor de basis-SKU met de naam *myPublicIpAddress*:

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

   Als het open bare IP-adres een standaard-SKU moet zijn, voegt `--public-ip-sku Standard` u toe aan de vorige opdracht. Meer informatie over [open bare IP-adres-sku's](./public-ip-addresses.md#sku). Als de virtuele machine wordt toegevoegd aan de back-end-groep van een openbaar Azure Load Balancer, moet de SKU van het open bare IP-adres van de virtuele machine overeenkomen met de SKU van het open bare IP-adres van de load balancer. Zie [Azure Load Balancer](../load-balancer/skus.md)voor meer informatie.

4. Bekijk het toegewezen open bare IP-adres en bevestig dat het is gemaakt als een statisch, basis-SKU-adres met [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure heeft een openbaar IP-adres toegewezen op basis van adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Wijzig de IP-adres instellingen in het besturings systeem van de virtuele machine niet. Het besturings systeem is niet op de hoogte van open bare IP-adressen van Azure. Hoewel u persoonlijke IP-adres instellingen kunt toevoegen aan het besturings systeem, wordt aangeraden dit niet te doen, tenzij dit nodig is, en pas na het lezen van [een privé-IP-adres toevoegen aan een besturings systeem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in azure
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](./private-ip-addresses.md) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over het maken van virtuele [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machines

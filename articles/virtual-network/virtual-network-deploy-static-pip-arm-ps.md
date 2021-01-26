---
title: Een virtuele machine maken met een statisch openbaar IP-adres-Power shell | Microsoft Docs
description: Maak een virtuele machine (VM) met een statisch openbaar IP-adres met behulp van Power shell. Statische open bare IP-adressen zijn adressen die nooit veranderen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791253"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Een virtuele machine met een statisch openbaar IP-adres maken met behulp van Power shell


U kunt een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres kunt u via internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres in plaats van een dynamisch adres toe om ervoor te zorgen dat het adres nooit wordt gewijzigd. Meer informatie over [statische open bare IP-adressen](./public-ip-addresses.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over het wijzigen van een openbaar IP-adres dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch of voor gebruik van privé-IP-adressen. Open bare IP-adressen hebben een [nominale belasting](https://azure.microsoft.com/pricing/details/ip-addresses)en er geldt een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor het aantal open bare IP-adressen dat u per abonnement kunt gebruiken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U kunt de volgende stappen uitvoeren vanaf uw lokale computer of met behulp van de Azure Cloud Shell. Als u de lokale computer wilt gebruiken, controleert u of de [Azure PowerShell zijn geïnstalleerd](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u de Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van een wille keurig opdracht venster. De Cloud Shell meldt u aan bij Azure.

1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2. Open een opdracht sessie en meld u aan bij Azure met `Connect-AzAccount` .
2. Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep gemaakt in de regio VS Oost Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Maak een virtuele machine met de opdracht [New-AzVM](/powershell/module/az.Compute/New-azVM) . `-AllocationMethod "Static"`Met deze optie wordt een statisch openbaar IP-adres toegewezen aan de virtuele machine. In het volgende voor beeld wordt een virtuele Windows Server-machine gemaakt met een statisch, een openbaar IP-adres met de naam *myPublicIpAddress*. Wanneer u hierom wordt gevraagd, geeft u een gebruikers naam en wacht woord op die moeten worden gebruikt als aanmeldings referenties voor de virtuele machine:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Als het open bare IP-adres een standaard-SKU moet zijn, dient u [een openbaar IP-adres te maken](virtual-network-public-ip-address.md#create-a-public-ip-address), [een netwerk interface te maken](virtual-network-network-interface.md#create-a-network-interface), [het open bare IP-adres toe te wijzen aan de netwerk interface](virtual-network-network-interface-addresses.md#add-ip-addresses)en vervolgens [een virtuele machine te maken met de netwerk interface](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), in afzonderlijke stappen. Meer informatie over [open bare IP-adres-sku's](./public-ip-addresses.md#sku). Als de virtuele machine wordt toegevoegd aan de back-end-groep van een openbaar Azure Load Balancer, moet de SKU van het open bare IP-adres van de virtuele machine overeenkomen met de SKU van het open bare IP-adres van de load balancer. Zie [Azure Load Balancer](../load-balancer/skus.md)voor meer informatie.

4. Bekijk het toegewezen open bare IP-adres en bevestig dat het is gemaakt als een statisch adres, met [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure heeft een openbaar IP-adres toegewezen op basis van adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Wijzig de IP-adres instellingen in het besturings systeem van de virtuele machine niet. Het besturings systeem is niet op de hoogte van open bare IP-adressen van Azure. Hoewel u persoonlijke IP-adres instellingen kunt toevoegen aan het besturings systeem, wordt aangeraden dit niet te doen, tenzij dit nodig is, en pas na het lezen van [een privé-IP-adres toevoegen aan een besturings systeem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in azure
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](./private-ip-addresses.md) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over het maken van virtuele [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machines

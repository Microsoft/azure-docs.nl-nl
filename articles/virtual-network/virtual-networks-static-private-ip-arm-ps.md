---
title: Een virtuele machine maken met een statisch privé-IP-adres-Azure PowerShell
description: Meer informatie over het maken van een virtuele machine met een privé-IP-adres met behulp van Power shell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 2825d8d9f8be7c56d7beea767a1afb7a290eafa2
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222595"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Een virtuele machine met een statisch privé-IP-adres maken met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt een virtuele machine (VM) met een statisch privé-IP-adres maken. Wijs een statisch privé-IP-adres toe in plaats van een dynamisch adres als u wilt selecteren welk adres van een subnet aan een virtuele machine wordt toegewezen. Meer informatie over [statische privé-IP-adressen](./public-ip-addresses.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over het wijzigen van een privé-IP-adres dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch of voor het werken met open bare IP-adressen.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U kunt de volgende stappen uitvoeren vanaf uw lokale computer of met behulp van de Azure Cloud Shell. Als u de lokale computer wilt gebruiken, controleert u of de [Azure PowerShell zijn geïnstalleerd](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u de Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van een wille keurig opdracht venster. De Cloud Shell meldt u aan bij Azure.

1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2. Open een opdracht sessie en meld u aan bij Azure met `Connect-AzAccount` .
2. Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep gemaakt in de regio VS Oost Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Maak een subnet-configuratie en een virtueel netwerk met de opdrachten [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) en [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Maak een netwerk interface in het virtuele netwerk en wijs een persoonlijk IP-adres uit het subnet toe aan de netwerk interface met de opdrachten [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) en [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Maak een VM-configuratie met [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)en maak de virtuele machine met [New-AzVM](/powershell/module/az.Compute/New-azVM). Wanneer u hierom wordt gevraagd, geeft u een gebruikers naam en wacht woord op die moeten worden gebruikt als aanmeldings referenties voor de virtuele machine:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Hoewel u persoonlijke IP-adres instellingen kunt toevoegen aan het besturings systeem, raden we u aan dit pas te doen nadat [een persoonlijk IP-adres aan een besturings systeem is toegevoegd](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Als u toegang wilt krijgen tot de virtuele machine via internet, moet u een openbaar IP-adres toewijzen aan de VM. U kunt ook een dynamisch persoonlijk IP-adres toewijzen aan een statische toewijzing. Zie [IP-adressen toevoegen of wijzigen](virtual-network-network-interface-addresses.md)voor meer informatie. Daarnaast is het raadzaam om het netwerk verkeer naar uw virtuele machine te beperken door een netwerk beveiligings groep te koppelen aan de netwerk interface, het subnet dat u hebt gemaakt voor de netwerk interface in of beide. Zie [netwerk beveiligings groepen beheren](manage-network-security-group.md)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [privé-IP-adressen](./private-ip-addresses.md) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine.
- Meer informatie over het maken van virtuele [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machines.

---
title: Een openbaar IP-adres ontkoppelen van een Azure-VM
titlesuffix: Azure Virtual Network
description: Meer informatie over het ontkoppelen van een openbaar IP-adres van een virtuele machine
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934198"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Een openbaar IP-adres ontkoppelen van een Azure-VM 

In dit artikel leert u hoe u een openbaar IP-adres loskoppelt van een virtuele Azure-machine (VM).

U kunt de [Azure Portal](#azure-portal), de Azure [-opdracht regel interface](#azure-cli) (CLI) of [Power shell](#powershell) gebruiken om een openbaar IP-adres van een virtuele machine te ontkoppelen.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine die u wilt loskoppelen van het open bare IP-adres en selecteer deze.
3. Selecteer op de pagina VM de optie **overzicht**, selecteer het open bare IP-adres, zoals wordt weer gegeven in de volgende afbeelding:

   ![Open bare IP selecteren](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Selecteer op de pagina openbaar IP-adres **overzicht** en selecteer vervolgens ontkoppelen **, zoals** wordt weer gegeven in de volgende afbeelding:

    ![Openbaar IP ontkoppelen](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. In **openbaar IP-adres** ontkoppelen, selecteert u **Ja**.

## <a name="azure-cli"></a>Azure CLI

Installeer de [Azure cli](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in de CLI-opdrachten die volgen. Als u probeert te selecteren **, wordt** een Cloud shell aangeroepen waarmee u zich kunt aanmelden bij uw Azure-account met.

1. Als u de CLI lokaal gebruikt in bash, meldt u zich aan bij Azure met `az login` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerk interface die is gekoppeld aan een virtuele machine. Gebruik de opdracht [AZ Network NIC-IP-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) om een openbaar IP-adres te ontkoppelen van een IP-configuratie. In het volgende voor beeld wordt een openbaar IP-adres met de naam *myVMPublicIP* uit de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerk interface met de naam *myVMVMNic* dat is gekoppeld aan een virtuele machine genaamd *myVM* in een resource groep met de naam *myResourceGroup* ontkoppeld.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Als u de naam van een netwerk interface die is gekoppeld aan uw virtuele machine niet weet, gebruikt u de opdracht [AZ VM NIC List](/cli/azure/vm/nic#az-vm-nic-list) om ze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen weer gegeven van de netwerk interfaces die zijn gekoppeld aan een virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voor beeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voor beeld is *myVMVMNic* de naam van de netwerk interface.

   - Als u de naam van een IP-configuratie voor een netwerk interface niet weet, gebruikt u de opdracht [AZ Network NIC IP-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) om deze op te halen. De volgende opdracht toont bijvoorbeeld de namen van de open bare IP-configuraties voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Als u de naam van een open bare IP-configuratie voor een netwerk interface niet weet, gebruikt u de opdracht [AZ Network NIC IP-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) om deze op te halen. De volgende opdracht toont bijvoorbeeld de namen van de open bare IP-configuraties voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installeer [Power shell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. Hiervoor is Power shell vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in de Power shell-opdrachten die volgen. Als u probeert te selecteren **, wordt** een Cloud shell aangeroepen waarmee u zich kunt aanmelden bij uw Azure-account met.

1. Als u Power shell lokaal gebruikt, meldt u zich aan bij Azure met `Connect-AzAccount` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerk interface die is gekoppeld aan een virtuele machine. Gebruik de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) voor het ophalen van een netwerk interface. Stel de waarde voor het open bare IP-adres in op null en gebruik vervolgens de opdracht [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) om de nieuwe IP-configuratie te schrijven naar de netwerk interface.

   In het volgende voor beeld wordt een openbaar IP-adres met de naam *myVMPublicIP* van een netwerk interface met de naam *myVMVMNic* die is gekoppeld aan een virtuele machine met de naam *myVM*. Alle resources bevinden zich in een resource groep met de naam *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Als u de naam van een netwerk interface die aan uw virtuele machine is gekoppeld, niet weet, gebruikt u de opdracht [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om ze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen weer gegeven van de netwerk interfaces die zijn gekoppeld aan een virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *myVMVMNic* de naam van de netwerk interface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van een IP-configuratie voor een netwerk interface niet weet, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. De volgende opdracht toont bijvoorbeeld de namen van de IP-configuraties voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *ipconfigmyVM* de naam van een IP-configuratie.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [koppelen van een openbaar IP-adres aan een virtuele machine](associate-public-ip-address-vm.md).

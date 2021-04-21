---
title: Een openbaar IP-adres loskoppelen van een Azure-VM
titlesuffix: Azure Virtual Network
description: Meer informatie over het ontkoppelen van een openbaar IP-adres van een VM
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
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776536"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Een openbaar IP-adres loskoppelen van een Azure-VM 

In dit artikel leert u hoe u een openbaar IP-adres kunt loskoppelen van een virtuele Azure-machine (VM).

U kunt de [Azure Portal,](#azure-portal)de Azure-opdrachtregelinterface (CLI) of [PowerShell](#powershell) gebruiken om een openbaar [IP-adres](#azure-cli) te ontkoppelen van een VM.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine waar u het openbare IP-adres van wilt loskoppelen en selecteer het.
3. Selecteer overzicht op de VM-pagina, selecteer het openbare IP-adres zoals wordt weergegeven in de volgende afbeelding:

   ![Selecteer Openbaar IP-adres](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Selecteer op de pagina Openbaar IP-adres **de** optie Overzicht en selecteer vervolgens Loskoppelen, zoals wordt weergegeven in de volgende afbeelding:

    ![Openbaar IP-adres ontkoppelen](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Selecteer **ja in Openbaar IP-adres ontkoppelen.** 

## <a name="azure-cli"></a>Azure CLI

Installeer de [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de **knop Uitproberen** in de VOLGENDE CLI-opdrachten. Als **u Proberen selecteert,** wordt een Cloud Shell u zich kunt aanmelden bij uw Azure-account.

1. Als u de CLI lokaal in Bash gebruikt, meld u zich dan aan bij Azure met `az login` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een VM. Gebruik de [opdracht az network nic-ip-config update om](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) een openbaar IP-adres te ontkoppelen van een IP-configuratie. In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myVMPublicIP* losgekoppeld van de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerkinterface met de naam *myVMVMNic* die is gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Als u de naam van een netwerkinterface die is gekoppeld aan uw VM niet weet, gebruikt u de [opdracht az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) om deze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen van de netwerkinterfaces vermeld die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voorbeeld is *myVMVMNic* de naam van de netwerkinterface.

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet weet, gebruikt u de opdracht [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) om deze op te halen. Met de volgende opdracht worden bijvoorbeeld de namen van de openbare IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup vermeld:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Als u de naam van een openbare IP-configuratie voor een netwerkinterface niet weet, gebruikt u de opdracht [az network nic ip-config show om](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) deze op te halen. Met de volgende opdracht worden bijvoorbeeld de namen van de openbare IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup vermeld:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installeer [PowerShell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. PowerShell is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de **knop Uitproberen** in de Volgende PowerShell-opdrachten. Als **u Proberen selecteert,** wordt een Cloud Shell u zich kunt aanmelden bij uw Azure-account.

1. Als u PowerShell lokaal gebruikt, meld u zich dan aan bij Azure met `Connect-AzAccount` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een VM. Gebruik de [opdracht Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om een netwerkinterface op te halen. Stel de waarde voor Openbaar IP-adres in op null en gebruik vervolgens de opdracht [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) om de nieuwe IP-configuratie naar de netwerkinterface te schrijven.

   In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myVMPublicIP* losgekoppeld van een netwerkinterface met de naam *myVMVMNic* die is gekoppeld aan een VM met de naam *myVM*. Alle resources maken deel uit van een resourcegroep met de *naam myResourceGroup.*
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Als u de naam van een netwerkinterface die is gekoppeld aan uw VM niet weet, gebruikt u de [opdracht Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om deze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen van de netwerkinterfaces vermeld die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld. In de voorbeelduitvoer is *myVMVMNic* de naam van de netwerkinterface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet weet, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. Met de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup vermeld:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld. In de voorbeelduitvoer *is ipconfigmyVM* de naam van een IP-configuratie.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [koppelen van een openbaar IP-adres aan een VM.](associate-public-ip-address-vm.md)

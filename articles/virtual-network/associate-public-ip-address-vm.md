---
title: Een openbaar IP-adres koppelen aan een virtuele machine
titlesuffix: Azure Virtual Network
description: Leer hoe u een openbaar IP-adres koppelt aan een virtuele machine (VM) met behulp van de Azure Portal of de Azure CLI.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767705"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Een openbaar IP-adres koppelen aan een virtuele machine

In dit artikel leert u hoe u een openbaar IP-adres koppelt aan een bestaande virtuele machine (VM). Als u vanaf internet verbinding wilt maken met een VM, moet er een openbaar IP-adres aan de VM zijn gekoppeld. Als u een nieuwe VM met een openbaar IP-adres wilt maken, kunt u dit doen met behulp van [de Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), de Azure-opdrachtregelinterface [(CLI)](virtual-network-deploy-static-pip-arm-cli.md)of [PowerShell.](virtual-network-deploy-static-pip-arm-ps.md) Openbare IP-adressen hebben een nominaal bedrag. Zie prijzen voor [meer informatie.](https://azure.microsoft.com/pricing/details/ip-addresses/) Er geldt een limiet voor het aantal openbare IP-adressen dat u per abonnement kunt gebruiken. Zie limieten voor [meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

U kunt de [Azure Portal,](#azure-portal)de [Azure-opdrachtregelinterface](#azure-cli) (CLI) of [PowerShell](#powershell) gebruiken om een openbaar IP-adres te koppelen aan een VM.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine waar u het openbare IP-adres aan wilt toevoegen en selecteer het.
3. Selecteer **onder** Instellingen de **optie Netwerken** en selecteer vervolgens de netwerkinterface waar u het openbare IP-adres aan wilt toevoegen, zoals wordt weergegeven in de volgende afbeelding:

   ![Netwerkinterface selecteren](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Openbare IP-adressen zijn gekoppeld aan netwerkinterfaces die zijn gekoppeld aan een VM. In de vorige afbeelding heeft de VM slechts één netwerkinterface. Als de VM meerdere netwerkinterfaces had, zouden deze allemaal worden weergegeven en selecteert u de netwerkinterface waar u het openbare IP-adres aan wilt koppelen.

4. Selecteer **IP-configuraties** en selecteer vervolgens een IP-configuratie, zoals wordt weergegeven in de volgende afbeelding:

   ![IP-configuratie selecteren](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Openbare IP-adressen zijn gekoppeld aan IP-configuraties voor een netwerkinterface. In de vorige afbeelding heeft de netwerkinterface één IP-configuratie. Als de netwerkinterface meerdere IP-configuraties heeft, worden deze allemaal weergegeven in de lijst en selecteert u de IP-configuratie waar u het openbare IP-adres aan wilt koppelen.

5. Selecteer **Ingeschakeld en** selecteer vervolgens **IP-adres (*Vereiste instellingen configureren*)**. Kies een bestaand openbaar IP-adres, waarmee het vak Openbaar **IP-adres** kiezen automatisch wordt gesloten. Als er geen beschikbare openbare IP-adressen worden vermeld, moet u er een maken. Zie Een openbaar IP-adres maken [voor meer informatie.](virtual-network-public-ip-address.md#create-a-public-ip-address) Selecteer **Opslaan,** zoals wordt weergegeven in de volgende afbeelding, en sluit vervolgens het vak voor de IP-configuratie.

   ![Openbaar IP-adres inschakelen](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De openbare IP-adressen die worden weergegeven, zijn de ip-adressen die zich in dezelfde regio als de VM hebben. Als u meerdere openbare IP-adressen hebt gemaakt in de regio, wordt alles hier weergegeven. Als een van de twee grijs wordt weergegeven, komt dit doordat het adres al is gekoppeld aan een andere resource.

6. Bekijk het openbare IP-adres dat is toegewezen aan de IP-configuratie, zoals wordt weergegeven in de volgende afbeelding. Het kan enkele seconden duren voordat een IP-adres wordt weergegeven.

   ![Toegewezen openbaar IP-adres weergeven](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie Voor een lijst met adresgroepen die in elke regio worden gebruikt, [Microsoft Azure Datacenter IP-adresbereiken.](https://www.microsoft.com/download/details.aspx?id=41653) Het toegewezen adres kan elk adres zijn in de pools die worden gebruikt voor de regio. Als u wilt dat het adres wordt toegewezen vanuit een specifieke groep in de regio, gebruikt u het voorvoegsel [openbaar IP-adres](public-ip-address-prefix.md).

7. [Netwerkverkeer naar de VM toestaan met](#allow-network-traffic-to-the-vm) beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="azure-cli"></a>Azure CLI

Installeer de [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de **knop Uitproberen** in de VOLGENDE CLI-opdrachten. Als **u Proberen selecteert,** wordt een Cloud Shell u zich kunt aanmelden bij uw Azure-account.

1. Als u de CLI lokaal in Bash gebruikt, meld u zich dan aan bij Azure met `az login` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een VM. Gebruik de [opdracht az network nic-ip-config update om](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) een openbaar IP-adres te koppelen aan een IP-configuratie. In het volgende voorbeeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* aan de IP-configuratie met de naam *ipconfigmyVM van* een bestaande netwerkinterface met de naam *myVMVMNic,* die bestaat in een resourcegroep met de *naam myResourceGroup.*
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [az network public-ip create om](/cli/azure/network/public-ip#az_network_public_ip_create) er een te maken. Met de volgende opdracht maakt u bijvoorbeeld een openbaar IP-adres met de *naam myVMPublicIP* in een resourcegroep met de *naam myResourceGroup.*
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Met de vorige opdracht maakt u een openbaar IP-adres met standaardwaarden voor verschillende instellingen die u mogelijk wilt aanpassen. Zie Een openbaar IP-adres maken voor meer informatie over alle instellingen [voor openbare IP-adressen.](virtual-network-public-ip-address.md#create-a-public-ip-address) Het adres wordt toegewezen vanuit een groep openbare IP-adressen die worden gebruikt voor elke Azure-regio. Zie Ip-adresbereiken voor datacenters voor een lijst met adresgroepen die in [Microsoft Azure regio worden gebruikt.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Als u de naam van een netwerkinterface die is gekoppeld aan uw VM niet weet, gebruikt u de [opdracht az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) om deze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen van de netwerkinterfaces vermeld die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voorbeeld is *myVMVMNic* de naam van de netwerkinterface.

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet weet, gebruikt u de opdracht [az network nic ip-config list om](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) deze op te halen. Met de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup vermeld:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Bekijk het openbare IP-adres dat is toegewezen aan de [IP-configuratie met de opdracht az vm list-ip-addresses.](/cli/azure/vm#az_vm_list_ip_addresses) In het volgende voorbeeld ziet u de IP-adressen die zijn toegewezen aan een bestaande VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup.*

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie Voor een lijst met adresgroepen die in elke regio worden gebruikt, [Microsoft Azure Datacenter IP-adresbereiken.](https://www.microsoft.com/download/details.aspx?id=41653) Het toegewezen adres kan elk adres zijn in de pools die worden gebruikt voor de regio. Als u wilt dat het adres wordt toegewezen vanuit een specifieke groep in de regio, gebruikt u het voorvoegsel [openbaar IP-adres](public-ip-address-prefix.md).

4. [Netwerkverkeer naar de VM toestaan met](#allow-network-traffic-to-the-vm) beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="powershell"></a>PowerShell

Installeer [PowerShell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. PowerShell is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de **knop Uitproberen** in de Volgende PowerShell-opdrachten. Als **u Proberen selecteert,** wordt een Cloud Shell u zich kunt aanmelden bij uw Azure-account.

1. Als u PowerShell lokaal gebruikt, meld u zich dan aan bij Azure met `Connect-AzAccount` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een VM. Gebruik de opdrachten [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) en [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) om het virtuele netwerk en subnet op te halen waarin de netwerkinterface zich in zich. Gebruik vervolgens de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om een netwerkinterface en de opdracht [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) op te halen om een bestaand openbaar IP-adres op te halen. Gebruik vervolgens de opdracht [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) om het openbare IP-adres te koppelen aan de IP-configuratie en de [opdracht Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) om de nieuwe IP-configuratie naar de netwerkinterface te schrijven.

   In het volgende voorbeeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* aan de IP-configuratie met de naam *ipconfigmyVM van* een bestaande netwerkinterface met de naam *myVMVMNic,* die bestaat in een subnet met de naam *myVMSubnet* in een virtueel netwerk met de naam *myVMVNet.* Alle resources maken deel uit van een resourcegroep met de *naam myResourceGroup.*
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) om er een te maken. Met de volgende opdracht maakt u bijvoorbeeld een dynamisch *openbaar* IP-adres met de naam *myVMPublicIP* in een resourcegroep met de *naam myResourceGroup* in de *regio eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Met de vorige opdracht maakt u een openbaar IP-adres met standaardwaarden voor verschillende instellingen die u mogelijk wilt aanpassen. Zie Een openbaar IP-adres maken voor meer informatie over alle instellingen [voor openbare IP-adressen.](virtual-network-public-ip-address.md#create-a-public-ip-address) Het adres wordt toegewezen vanuit een groep openbare IP-adressen die worden gebruikt voor elke Azure-regio. Zie Ip-adresbereiken voor datacenters voor een lijst met adresgroepen die in [Microsoft Azure regio worden gebruikt.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Als u de naam van een netwerkinterface die is gekoppeld aan uw VM niet weet, gebruikt u de [opdracht Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om deze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen van de netwerkinterfaces vermeld die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de *naam myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld. In de voorbeelduitvoer is *myVMVMNic* de naam van de netwerkinterface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van het virtuele netwerk of subnet waarin de netwerkinterface zich in zich, niet weet, gebruikt u de opdracht om `Get-AzNetworkInterface` de informatie weer te geven. Met de volgende opdracht haalt u bijvoorbeeld de gegevens van het virtuele netwerk en het subnet op voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld. In de voorbeelduitvoer is *myVMVNET* de naam van het virtuele netwerk en *myVMSubnet* de naam van het subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet weet, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. Met de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de *naam myResourceGroup vermeld:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In de voorbeelduitvoer *is ipconfigmyVM* de naam van een IP-configuratie.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Bekijk het openbare IP-adres dat is toegewezen aan de IP-configuratie met de [opdracht Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) In het volgende voorbeeld ziet u het adres dat is toegewezen aan een openbaar IP-adres met de naam *myVMPublicIP* in een resourcegroep met de *naam myResourceGroup.*

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Als u de naam van het openbare IP-adres dat is toegewezen aan een IP-configuratie niet weet, voert u de volgende opdrachten uit om deze op te halen:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In de voorbeelduitvoer is *myVMPublicIP* de naam van het openbare IP-adres dat is toegewezen aan de IP-configuratie.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie Ip-adresbereiken voor datacenters voor een lijst met adresgroepen die in [Microsoft Azure regio worden gebruikt.](https://www.microsoft.com/download/details.aspx?id=41653) Het toegewezen adres kan elk adres zijn in de pools die worden gebruikt voor de regio. Als u wilt dat het adres wordt toegewezen vanuit een specifieke groep in de regio, gebruikt u het voorvoegsel [openbaar IP-adres](public-ip-address-prefix.md).

4. [Netwerkverkeer naar de VM toestaan met](#allow-network-traffic-to-the-vm) beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="allow-network-traffic-to-the-vm"></a>Netwerkverkeer naar de VM toestaan

Voordat u vanaf internet verbinding kunt maken met het openbare IP-adres, moet u ervoor zorgen dat de benodigde poorten zijn geopend in een netwerkbeveiligingsgroep die u mogelijk hebt gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich of beide heeft geplaatst. Hoewel beveiligingsgroepen verkeer filteren op het privé-IP-adres van de netwerkinterface, wordt het openbare adres in Azure omgezet in het privé-IP-adres zodra binnenkomend internetverkeer binnenkomt. Als een netwerkbeveiligingsgroep de verkeersstroom voorkomt, mislukt de communicatie met het openbare IP-adres. U kunt de effectieve beveiligingsregels voor een netwerkinterface en het subnet ervan weergeven met behulp van [portal,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)of [PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Volgende stappen

Sta inkomende internetverkeer naar uw VM toe met een netwerkbeveiligingsgroep. Zie Werken met netwerkbeveiligingsgroepen voor meer informatie over het maken [van een netwerkbeveiligingsgroep.](manage-network-security-group.md#work-with-network-security-groups) Zie Beveiligingsgroepen voor meer informatie over [netwerkbeveiligingsgroepen.](./network-security-groups-overview.md)

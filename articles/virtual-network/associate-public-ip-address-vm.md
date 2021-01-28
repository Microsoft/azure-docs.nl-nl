---
title: Een openbaar IP-adres koppelen aan een virtuele machine
titlesuffix: Azure Virtual Network
description: Meer informatie over het koppelen van een openbaar IP-adres aan een virtuele machine (VM) met behulp van de Azure Portal of de Azure CLI.
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
ms.openlocfilehash: 6ea16da3844b8098d87d65e1016f92c69ae34067
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945157"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Een openbaar IP-adres koppelen aan een virtuele machine

In dit artikel leert u hoe u een openbaar IP-adres kunt koppelen aan een bestaande virtuele machine (VM). Als u verbinding wilt maken met een virtuele machine via internet, moet aan de virtuele machine een openbaar IP-adres zijn gekoppeld. Als u een nieuwe virtuele machine met een openbaar IP-adres wilt maken, kunt u dit doen met behulp van de [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), de [Azure-opdracht regel interface (CLI)](virtual-network-deploy-static-pip-arm-cli.md)of [Power shell](virtual-network-deploy-static-pip-arm-ps.md). Open bare IP-adressen hebben een nominale vergoeding. Zie [prijzen](https://azure.microsoft.com/pricing/details/ip-addresses/)voor meer informatie. Er geldt een limiet voor het aantal open bare IP-adressen dat u per abonnement kunt gebruiken. Zie [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)voor meer informatie.

U kunt de [Azure Portal](#azure-portal), de Azure- [opdracht regel interface](#azure-cli) (CLI) of [Power shell](#powershell) gebruiken om een openbaar IP-adres te koppelen aan een virtuele machine.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine waaraan u het open bare IP-adres wilt toevoegen en selecteer deze.
3. Onder **instellingen** selecteert u **netwerken** en selecteert u vervolgens de netwerk interface waaraan u het open bare IP-adres wilt toevoegen, zoals wordt weer gegeven in de volgende afbeelding:

   ![Netwerk interface selecteren](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Open bare IP-adressen zijn gekoppeld aan netwerk interfaces die zijn gekoppeld aan een virtuele machine. In de vorige afbeelding heeft de VM slechts één netwerk interface. Als de virtuele machine meerdere netwerk interfaces heeft, worden deze allemaal weer gegeven en selecteert u de netwerk interface waaraan u het open bare IP-adres wilt koppelen.

4. Selecteer **IP-configuraties** en selecteer vervolgens een IP-configuratie, zoals wordt weer gegeven in de volgende afbeelding:

   ![IP-configuratie selecteren](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Open bare IP-adressen zijn gekoppeld aan IP-configuraties voor een netwerk interface. In de vorige afbeelding heeft de netwerk interface één IP-configuratie. Als de netwerk interface meerdere IP-configuraties heeft, worden deze allemaal weer gegeven in de lijst en selecteert u de IP-configuratie waaraan u het open bare IP-adres wilt koppelen.

5. Selecteer **ingeschakeld** en selecteer vervolgens **IP-adres (*vereiste instellingen configureren*)**. Kies een bestaand openbaar IP-adres, waarmee automatisch het vak **openbaar IP-adres kiezen** wordt gesloten. Als er geen beschik bare open bare IP-adressen worden weer gegeven, moet u er een maken. Zie [een openbaar IP-adres maken](virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie. Selecteer **Opslaan**, zoals wordt weer gegeven in de volgende afbeelding en sluit het vak voor de IP-configuratie.

   ![Openbaar IP-adres inschakelen](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De open bare IP-adressen die zich in dezelfde regio als de virtuele machine bevinden, worden weer gegeven. Als er meerdere open bare IP-adressen in de regio zijn gemaakt, worden deze hier weer gegeven. Als deze grijs worden weer gegeven, is het adres al aan een andere resource gekoppeld.

6. Bekijk het open bare IP-adres dat is toegewezen aan de IP-configuratie, zoals wordt weer gegeven in de volgende afbeelding. Het kan een paar seconden duren voordat een IP-adres wordt weer gegeven.

   ![Toegewezen openbaar IP-adres weer geven](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio wordt gebruikt. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een overzicht van de adres groepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die worden gebruikt voor de regio. Als u het adres moet worden toegewezen uit een specifieke groep in de regio, gebruikt u een [openbaar IP-adres voorvoegsel](public-ip-address-prefix.md).

7. [Netwerk verkeer naar de virtuele machine toestaan](#allow-network-traffic-to-the-vm) met beveiligings regels in een netwerk beveiligings groep.

## <a name="azure-cli"></a>Azure CLI

Installeer de [Azure cli](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in de CLI-opdrachten die volgen. Als u probeert te selecteren **, wordt** een Cloud shell aangeroepen waarmee u zich kunt aanmelden bij uw Azure-account met.

1. Als u de CLI lokaal gebruikt in bash, meldt u zich aan bij Azure met `az login` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerk interface die is gekoppeld aan een virtuele machine. Gebruik de opdracht [AZ Network NIC-IP-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) om een openbaar IP-adres te koppelen aan een IP-configuratie. In het volgende voor beeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* gekoppeld aan de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerk interface met de naam *myVMVMNic* die bestaat in een resource groep met de naam *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) om er een te maken. Met de volgende opdracht maakt u bijvoorbeeld een openbaar IP-adres met de naam *myVMPublicIP* in een resource groep met de naam *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Met de vorige opdracht maakt u een openbaar IP-adres met standaard waarden voor verschillende instellingen die u wilt aanpassen. Zie [een openbaar IP-adres maken](virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie over alle instellingen voor het open bare IP-adres. Het adres wordt toegewezen vanuit een groep open bare IP-adressen die worden gebruikt voor elke Azure-regio. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een overzicht van de adres groepen die in elke regio worden gebruikt.

   - Als u de naam van een netwerk interface die is gekoppeld aan uw virtuele machine niet weet, gebruikt u de opdracht [AZ VM NIC List](/cli/azure/vm/nic#az-vm-nic-list) om ze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen weer gegeven van de netwerk interfaces die zijn gekoppeld aan een virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voor beeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voor beeld is *myVMVMNic* de naam van de netwerk interface.

   - Als u de naam van een IP-configuratie voor een netwerk interface niet weet, gebruikt u de opdracht [AZ Network NIC IP-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) om deze op te halen. De volgende opdracht toont bijvoorbeeld de namen van de IP-configuraties voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Bekijk het open bare IP-adres dat is toegewezen aan de IP-configuratie met de opdracht [AZ VM List-IP-addresses](/cli/azure/vm#az-vm-list-ip-addresses) . In het volgende voor beeld ziet u de IP-adressen die zijn toegewezen aan een bestaande virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio wordt gebruikt. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een overzicht van de adres groepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die worden gebruikt voor de regio. Als u het adres moet worden toegewezen uit een specifieke groep in de regio, gebruikt u een [openbaar IP-adres voorvoegsel](public-ip-address-prefix.md).

4. [Netwerk verkeer naar de virtuele machine toestaan](#allow-network-traffic-to-the-vm) met beveiligings regels in een netwerk beveiligings groep.

## <a name="powershell"></a>PowerShell

Installeer [Power shell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. Hiervoor is Power shell vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in de Power shell-opdrachten die volgen. Als u probeert te selecteren **, wordt** een Cloud shell aangeroepen waarmee u zich kunt aanmelden bij uw Azure-account met.

1. Als u Power shell lokaal gebruikt, meldt u zich aan bij Azure met `Connect-AzAccount` .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerk interface die is gekoppeld aan een virtuele machine. Gebruik de opdrachten [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) en [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) voor het virtuele netwerk en het subnet waarin de netwerk interface zich bevindt. Gebruik vervolgens de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) voor het ophalen van een netwerk interface en de opdracht [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om een bestaand openbaar IP-adres op te halen. Gebruik vervolgens de opdracht [set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) om het open bare IP-adres te koppelen aan de IP-configuratie en de [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) -opdracht om de nieuwe IP-configuratie te schrijven naar de netwerk interface.

   In het volgende voor beeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* gekoppeld aan de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerk interface met de naam *myVMVMNic* die zich in een subnet met de naam *myVMSubnet* in een virtueel netwerk met de naam *myVMVNet* bevindt. Alle resources bevinden zich in een resource groep met de naam *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) om er een te maken. Met de volgende opdracht maakt u bijvoorbeeld een *dynamisch* openbaar IP-adres met de naam *myVMPublicIP* in een resource groep met de naam *myResourceGroup* in de regio *eastus* .
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Met de vorige opdracht maakt u een openbaar IP-adres met standaard waarden voor verschillende instellingen die u wilt aanpassen. Zie [een openbaar IP-adres maken](virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie over alle instellingen voor het open bare IP-adres. Het adres wordt toegewezen vanuit een groep open bare IP-adressen die worden gebruikt voor elke Azure-regio. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een overzicht van de adres groepen die in elke regio worden gebruikt.

   - Als u de naam van een netwerk interface die aan uw virtuele machine is gekoppeld, niet weet, gebruikt u de opdracht [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om ze weer te geven. Met de volgende opdracht worden bijvoorbeeld de namen weer gegeven van de netwerk interfaces die zijn gekoppeld aan een virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *myVMVMNic* de naam van de netwerk interface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van het virtuele netwerk of subnet waarin de netwerk interface zich bevindt, niet weet, gebruikt u de `Get-AzNetworkInterface` opdracht om de gegevens weer te geven. De volgende opdracht haalt bijvoorbeeld het virtuele netwerk en de subnetgegevens op voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *myVMVNET* de naam van het virtuele netwerk en *myVMSubnet* de naam van het subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Als u de naam van een IP-configuratie voor een netwerk interface niet weet, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. De volgende opdracht toont bijvoorbeeld de namen van de IP-configuraties voor een netwerk interface met de naam *myVMVMNic* in een resource groep met de naam *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *ipconfigmyVM* de naam van een IP-configuratie.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Bekijk het open bare IP-adres dat is toegewezen aan de IP-configuratie met de opdracht [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . In het volgende voor beeld ziet u het adres dat is toegewezen aan een openbaar IP-adres met de naam *myVMPublicIP* in een resource groep met de naam *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Als u de naam van het open bare IP-adres dat is toegewezen aan een IP-configuratie niet weet, voert u de volgende opdrachten uit om deze op te halen:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voor beeld dat volgt. In de voorbeeld uitvoer is *myVMPublicIP* de naam van het open bare IP-adres dat is toegewezen aan de IP-configuratie.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio wordt gebruikt. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een overzicht van de adres groepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die worden gebruikt voor de regio. Als u het adres moet worden toegewezen uit een specifieke groep in de regio, gebruikt u een [openbaar IP-adres voorvoegsel](public-ip-address-prefix.md).

4. [Netwerk verkeer naar de virtuele machine toestaan](#allow-network-traffic-to-the-vm) met beveiligings regels in een netwerk beveiligings groep.

## <a name="allow-network-traffic-to-the-vm"></a>Netwerk verkeer naar de VM toestaan

Voordat u verbinding kunt maken met het open bare IP-adres van Internet, moet u ervoor zorgen dat de benodigde poorten zijn geopend in elke netwerk beveiligings groep die u mogelijk hebt gekoppeld aan de netwerk interface, het subnet waarin de netwerk interface zich bevindt of beide. Hoewel in beveiligings groepen verkeer naar het privé-IP-adres van de netwerk interface wordt gefilterd, wordt het open bare IP-adres door Azure omgezet naar het privé-IP-adres, dus als een netwerk beveiligings groep de verkeers stroom voor komt, mislukt de communicatie met het open bare IP-adres. U kunt de juiste beveiligings regels voor een netwerk interface en het bijbehorende subnet weer geven met behulp van de [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [cli](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)of [Power shell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Volgende stappen

Sta binnenkomend Internet verkeer naar uw virtuele machine toe met een netwerk beveiligings groep. Zie [werken met netwerk beveiligings groepen](manage-network-security-group.md#work-with-network-security-groups)voor meer informatie over het maken van een netwerk beveiligings groep. Zie [beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.

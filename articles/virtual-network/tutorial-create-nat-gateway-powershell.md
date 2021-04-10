---
title: 'Zelf studie: een NAT-gateway maken-Power shell'
titlesuffix: Azure Virtual Network NAT
description: Aan de slag met het maken van een NAT-gateway met Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620229"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Zelfstudie: Een NAT-gateway maken met Azure PowerShell

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een virtueel netwerk.
> * Hiermee maakt u een virtuele machine.
> * Maak een NAT-gateway en koppel deze aan het virtuele netwerk.
> * Maak verbinding met de virtuele machine en controleer IP-adres van NAT.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

In deze sectie maken we de NAT-gateway en ondersteunende bronnen.

* Voor toegang tot het internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIP**. 

* Maak een globale Azure NAT-gateway met [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Als gevolg van deze opdracht maakt u een gateway resource met de naam **myNATgateway** die gebruikmaakt van het open bare IP-adres **myPublicIP**. De time-out voor inactiviteit is ingesteld op 10 minuten.  

* Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) in de **myResourceGroup** met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). De IP-adres ruimte voor het virtuele netwerk is **10.1.0.0/16**. Het subnet in het virtuele netwerk is **10.1.0.0/24**.

* Maak een Azure bastion-host met de naam **myBastionHost** om toegang te krijgen tot de virtuele machine. Gebruik [New-AzBastion](/powershell/module/az.network/new-azbastion) om de bastion-host te maken. Maak een openbaar IP-adres voor de bastion-host met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Virtuele machine

In deze sectie maakt u een virtuele machine om de NAT-gateway te testen en controleert u het open bare IP-adres van de uitgaande verbinding.

* Maak een netwerk interface met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Stel een beheerders naam en-wacht woord in voor de virtuele machine met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Maak de virtuele machine met:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Wacht tot het maken van de virtuele machine is voltooid voordat u verdergaat met de volgende sectie.

## <a name="test-nat-gateway"></a>NAT-gateway testen

In deze sectie testen we de NAT-gateway. Eerst wordt het open bare IP-adres van de NAT-gateway gedetecteerd. Vervolgens maakt u verbinding met de virtuele test machine en controleert u de uitgaande verbinding via de NAT-gateway.
    
1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Zoek het open bare IP-adres voor de NAT-gateway op het scherm **overzicht** . Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Noteer het open bare IP-adres:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Openbaar IP-adres van NAT-gateway detecteren" border="true":::

3. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resource groep **myResourceGroupNAT** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer **https://whatsmyip.com** in de adres balk in.

9. Controleer of het weer gegeven IP-adres overeenkomt met het NAT-gateway adres dat u in de vorige stap hebt genoteerd:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer met het externe uitgaande IP-adres" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het virtuele netwerk, de virtuele machine en de NAT-gateway door de volgende stappen uit te voeren:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure Virtual Network NAT:
> [!div class="nextstepaction"]
> [Overzicht van Virtual Network NAT](nat-overview.md)

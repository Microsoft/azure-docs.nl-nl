---
title: Routerings voorkeur configureren voor een virtuele machine-Azure PowerShell
description: Meer informatie over het maken van een virtuele machine met een openbaar IP-adres met een routerings voorkeurs keuze met behulp van de Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672966"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Routerings voorkeur configureren voor een virtuele machine met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u routerings voorkeur configureert voor een virtuele machine. Het Internet gebonden verkeer van de virtuele machine wordt doorgestuurd via het ISP-netwerk wanneer u kiest voor **Internet** als de voorkeurs methode voor route ring. De standaard routering is via het wereld wijde netwerk van micro soft.

In dit artikel wordt beschreven hoe u een virtuele machine maakt met een openbaar IP-adres dat is ingesteld voor het routeren van verkeer via het ISP-netwerk met behulp van Azure PowerShell.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2. Open een opdracht sessie en meld u aan bij Azure met `Connect-AzAccount` .
2. Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep gemaakt in de regio VS Oost Azure:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

U hebt een openbaar IP-adres nodig om toegang te krijgen tot uw virtuele machines via internet. Open bare IP-adressen maken met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). In het volgende voor beeld wordt een openbaar IP-adres voor IPv4 gemaakt met de naam *MyPublicIP* -route voorkeurs type *Internet* in de resource groep *MyResourceGroup* in de regio *VS Oost* :

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Voordat u een virtuele machine implementeert, moet u ondersteunende netwerk bronnen maken, netwerk beveiligings groep, virtueel netwerk en virtuele NIC.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). In het volgende voor beeld wordt een NSG met de naam *mijnnbg* gemaakt

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myVNET* met *mySubNet*:

### <a name="create-a-subnet"></a>Een subnet maken

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Een NIC maken

Virtuele Nic's maken met [New-AzNetworkInterface] (/PowerShell/module/AZ.Network/New-aznetworkinterface. In het volgende voor beeld wordt een virtuele NIC gemaakt.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

U kunt de virtuele machine nu maken met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Netwerk verkeer naar de VM toestaan

Voordat u verbinding kunt maken met het open bare IP-adres van Internet, moet u ervoor zorgen dat de benodigde poorten zijn geopend in elke netwerk beveiligings groep die u mogelijk hebt gekoppeld aan de netwerk interface, het subnet waarin de netwerk interface zich bevindt of beide. U kunt de juiste beveiligings regels voor een netwerk interface en het bijbehorende subnet weer geven met behulp van de [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [cli](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)of [Power shell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [routerings voorkeur in open bare IP-adressen](routing-preference-overview.md).
* Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
* Meer informatie over [instellingen voor open bare IP-adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
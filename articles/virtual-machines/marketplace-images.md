---
title: Informatie over het aankoop plan voor Marketplace opgeven met behulp van Azure PowerShell
description: Meer informatie over het opgeven van Details van een Azure Marketplace-aankoop plan bij het maken van installatie kopieën in een galerie met gedeelde afbeeldingen.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562534"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Informatie over het aankoop plan van Azure Marketplace leveren bij het maken van installatie kopieën

Als u in een gedeelde Galerie een installatie kopie maakt met behulp van een bron die oorspronkelijk is gemaakt op basis van een Azure Marketplace-installatie kopie, moet u mogelijk informatie over het aankoop plan bijhouden. In dit artikel wordt beschreven hoe u informatie over het aankoop plan kunt vinden voor een virtuele machine en hoe u die informatie gebruikt bij het maken van een definitie van een installatie kopie. We hebben ook betrekking op het gebruik van de informatie uit de definitie van de installatie kopie om de informatie van het aankoop plan te vereenvoudigen bij het maken van een virtuele machine voor een installatie kopie.

Zie [Azure Marketplace-installatie kopieën zoeken en gebruiken](./windows/cli-ps-findimage.md)voor meer informatie over het zoeken naar en het gebruik van Marketplace-installatie kopieën.


## <a name="get-the-source-vm-information"></a>De gegevens van de bron-VM ophalen
Als u nog steeds de oorspronkelijke VM hebt, kunt u de naam van het abonnement, de uitgever en de product informatie ophalen met behulp van Get-AzVM. In dit voor beeld wordt een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup* opgehaald en worden vervolgens de informatie over het aankoop plan voor de virtuele machine weer gegeven.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>De definitie van de installatie kopie maken

Down load de galerie met installatie kopieën die u wilt gebruiken om de installatie kopie op te slaan. U kunt eerst alle galerieën weer geven.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Maak vervolgens variabelen voor de galerie die u wilt gebruiken. In dit voor beeld maken we een variabele `$gallery` met de naam *myGallery* in de resource groep *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

De definitie van de installatie kopie maken met behulp van de  `-PurchasePlanPublisher` `-PurchasePlanProduct` `-PurchasePlanName` para meters, en.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Maak vervolgens een installatie kopie versie met behulp van [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). U kunt een installatie kopie versie maken op basis van een [virtuele machine](image-version-vm-powershell.md#create-an-image-version), een [beheerde installatie kopie](image-version-managed-image-powershell.md#create-an-image-version), een [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)of [een andere versie van de installatie kopie](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>De VM maken

Wanneer u een virtuele machine maakt op basis van de installatie kopie, kunt u de informatie uit de definitie van de installatie kopie gebruiken om de gegevens van de uitgever door te geven met behulp van [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Marketplace-installatie kopieën zoeken en gebruiken](./windows/cli-ps-findimage.md)voor meer informatie over het zoeken naar en het gebruik van Marketplace-installatie kopieën.

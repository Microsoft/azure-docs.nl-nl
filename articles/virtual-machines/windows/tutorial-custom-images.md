---
title: Zelfstudie - Aangepaste VM-installatiekopieën maken met Azure PowerShell
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een aangepaste installatiekopie van een virtuele Windows-machine te maken in Azure Shared Image Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555819"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Zelfstudie: Windows VM-installatiekopieën maken met Azure PowerShell

Installatiekopieën kunnen worden gebruikt als een bootstrap voor implementaties en zorgen voor consistentie tussen verschillende virtuele machines. In deze zelfstudie maakt u uw eigen gespecialiseerde installatiekopie van een virtuele Azure-machine met behulp van PowerShell en bewaart u deze in een Shared Image Gallery. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie voor de installatiekopie maken
> * De versie van een installatiekopie maken
> * Een VM vanuit de installatiekopie maken 
> * Een galerie met installatiekopieën delen



## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Indien nodig raadpleegt u de [PowerShell-snelstartgids](quick-create-powershell.md) om een VM te maken voor deze zelfstudie. Vervang tijdens het volgen van de zelfstudie de resourcenamen waar nodig.

## <a name="overview"></a>Overzicht

Met een [Shared Image Gallery](../shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de Shared Image Gallery kunt u aangepaste VM-installatiekopieën met anderen delen. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. 

De functie Shared Image Gallery heeft meerdere resourcetypen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="get-the-vm"></a>De VM ophalen

U met behulp van [Get-AzVM](/powershell/module/az.compute/get-azvm) een lijst met VM's weergeven die beschikbaar zijn in een resourcegroep. Wanneer u de naam van de virtuele machine en de resourcegroep weet, kunt u `Get-AzVM` opnieuw gebruiken om het VM-object op te halen en op te slaan in een variabele voor later gebruik. In dit voorbeeld wordt een virtuele machine met de naam *sourceVM* opgehaald uit de resourcegroep myResourceGroup en toegewezen aan de variabele *$sourceVM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myGalleryRG* gemaakt in de regio *EastUS*:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Een galerie met installatiekopieën maken 

Een galerie met installatiekopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van installatiekopieën. De naam van de galerie kan bestaan uit hoofdletters en kleine letters, cijfers en punten. De naam van de galerie kan geen liggende streepjes bevatten. De naam van de galerie moet uniek zijn binnen uw abonnement. 

Maak een galerie met installatiekopieën met de opdracht [New-AzGallery](/powershell/module/az.compute/new-azgallery). In het volgende voorbeeld wordt een galerie met de naam *myGallery* gemaakt in de resourcegroep *myGalleryRG*.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken 

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Die worden gebruikt voor het beheren van informatie over de installatiekopieversies die daarbinnen worden gemaakt. Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. Zie [Installatiekopiedefinities](../shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Maak de definitie van de installatiekopie met behulp van [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In dit voorbeeld heeft de installatiekopie in de galerie de naam *myGalleryImage* en wordt deze gemaakt voor een gespecialiseerde installatiekopie. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Maak een installatiekopieversie van een virtuele machine met [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de versie van de installatiekopie *1.0.0* en wordt deze gerepliceerd naar datacenters in de regio's *East US* en *South Central US*. Bij het kiezen van doelregio's voor replicatie moet u de *source*-regio opnemen als doel voor replicatie.

Als u een installatiekopieversie wilt maken op basis van de virtuele machine, gebruikt u `$vm.Id.ToString()` voor de `-Source`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Het kan even duren om de installatiekopie naar alle doelregio's te repliceren.


## <a name="create-a-vm"></a>Een virtuele machine maken 

Zodra u een gespecialiseerde installatiekopie hebt gemaakt, kunt u een of meer nieuwe VM's maken. De cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) gebruiken. Als u de installatiekopie wilt gebruiken, gebruikt u `Set-AzVMSourceImage` en stelt u de/het `-Id` in op de definitie-id van de installatiekopie (in dit geval $galleryImage.id) om altijd de nieuwste versie van de installatiekopie te gebruiken. 

Vervang resourcenamen naar behoefte in dit voorbeeld. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>De galerie delen

We raden aan om te delen op galerieniveau. Gebruik een e-mailadres en de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om de object-id voor gebruikers op te halen en gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om hen toegang te geven tot de galerie. Vervang het e-mailadres, alinne_montes@contoso.com in dit voorbeeld, door uw eigen gegevens.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Resources opschonen

U kunt de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure biedt ook de service [Azure VM Image Builder](../image-builder-overview.md), die is gebouwd op Packer. U hoeft slechts uw aanpassingen in een sjabloon te beschrijven en de service handelt het maken van de installatiekopie af. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een gespecialiseerde installatiekopie voor een virtuele machine gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie voor de installatiekopie maken
> * De versie van een installatiekopie maken
> * Een VM vanuit de installatiekopie maken 
> * Een galerie met installatiekopieën delen

Ga door met de volgende zelfstudie voor meer informatie over het maken van virtuele machines met hoge beschikbaarheid.

> [!div class="nextstepaction"]
> [Virtuele machines met hoge beschikbaarheid maken](tutorial-availability-sets.md)
---
title: Opslag van Managed disks tussen verschillende schijf typen converteren met behulp van Azure PowerShell
description: Het converteren van Azure Managed disks tussen de verschillende typen schijven met behulp van Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: e2541607b116159e4f6ec4028c83ecc9a45eded8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550736"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Het opslag type van een beheerde schijf bijwerken

Er zijn vier schijf typen van Azure Managed disks: Azure Ultra disks, Premium SSD, Standard SSD en standaard HDD. U kunt scha kelen tussen Premium SSD, Standard SSD en standaard HDD op basis van uw prestatie behoeften. U kunt niet overstappen van of naar een ultra schijf. u moet een nieuwe implementeren.

Deze functionaliteit wordt niet ondersteund voor niet-beheerde schijven. U kunt echter eenvoudig een niet-beheerde [schijf converteren naar een Managed Disk](convert-unmanaged-to-managed-disks.md) om tussen schijf typen te scha kelen.



## <a name="before-you-begin"></a>Voordat u begint

* Omdat voor de conversie van de virtuele machine (VM) opnieuw moet worden opgestart, moet u de migratie van de schijf opslag plannen tijdens een reeds bestaand onderhouds venster.
* Als uw schijf niet-beheerd is, [moet u deze eerst converteren naar een beheerde schijf](convert-unmanaged-to-managed-disks.md) , zodat u kunt scha kelen tussen opslag opties.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Alle Managed disks van een virtuele machine wisselen tussen accounts naar een andere account

In dit voor beeld ziet u hoe u alle schijven van een virtuele machine converteert naar Premium Storage. Als u echter de variabele $storageType in dit voor beeld wijzigt, kunt u het schijf type van de virtuele machine converteren naar Standard SSD of standaard HDD. Als u Premium Managed disks wilt gebruiken, moet uw virtuele machine gebruikmaken van een [VM-grootte](../sizes.md) die Premium-opslag ondersteunt. In dit voor beeld wordt ook overgeschakeld naar een grootte die ondersteuning biedt voor Premium Storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Afzonderlijke beheerde schijven wisselen tussen Standard en Premium

Voor uw werk belasting voor ontwikkelen en testen wilt u mogelijk een combi natie van standaard-en Premium-schijven om uw kosten te verlagen. U kunt ervoor kiezen om alleen die schijven bij te werken waarvoor betere prestaties nodig zijn. In dit voor beeld ziet u hoe u één VM-schijf converteert van Standard naar Premium Storage. Als u echter de variabele $storageType in dit voor beeld wijzigt, kunt u het schijf type van de virtuele machine converteren naar Standard SSD of standaard HDD. Als u Premium Managed disks wilt gebruiken, moet uw virtuele machine gebruikmaken van een [VM-grootte](../sizes.md) die Premium-opslag ondersteunt. In dit voor beeld ziet u ook hoe u kunt overschakelen naar een grootte die Premium-opslag ondersteunt:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Managed disks overschakelen van het ene schijf type naar het andere

Volg deze stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de VM in de lijst met **virtuele machines**.
3. Als de virtuele machine niet is gestopt, selecteert u **stoppen** boven in het deel venster VM- **overzicht** en wacht u totdat de virtuele machine is gestopt.
4. Selecteer in het deel venster voor de VM de optie **schijven** in het menu.
5. Selecteer de schijf die u wilt converteren.
6. Selecteer **configuratie** in het menu.
7. Wijzig het **account type** van het oorspronkelijke schijf type naar het gewenste schijf type.
8. Selecteer **Opslaan** en sluit het deel venster schijf.

De schijf type conversie is onmiddellijk. U kunt de virtuele machine na de conversie starten.

## <a name="next-steps"></a>Volgende stappen

Een alleen-lezen kopie van een virtuele machine maken met behulp van een [moment opname](snapshot-copy-managed-disk.md).
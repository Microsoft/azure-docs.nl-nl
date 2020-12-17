---
title: Een beschikbaarheidsset met Vm's wijzigen
description: Meer informatie over het wijzigen van de beschikbaarheidsset voor uw virtuele machine met behulp van Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 8c0694bd1dc2fefed644dc91a0d649dd1a480428
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654537"
---
# <a name="change-the-availability-set-for-a-vm"></a>De beschikbaarheidsset voor een VM wijzigen
In de volgende stappen wordt beschreven hoe u de beschikbaarheidsset van een virtuele machine wijzigt met behulp van Azure PowerShell. Een virtuele machine kan alleen worden toegevoegd aan een beschikbaarheidsset wanneer deze wordt gemaakt. Als u de beschikbaarheidsset wilt wijzigen, moet u de virtuele machine verwijderen en vervolgens opnieuw maken. 

Dit artikel is van toepassing op zowel Linux-als Windows-Vm's.

Dit artikel is voor het laatst getest op 2/12/2019 met behulp van de [Azure Cloud shell](https://shell.azure.com/powershell) en de [AZ Power shell-module](/powershell/azure/install-az-ps) versie 1.2.0.

In dit voor beeld wordt niet gecontroleerd of de virtuele machine is gekoppeld aan een load balancer. Als uw virtuele machine is gekoppeld aan een load balancer, moet u het script bijwerken om die aanvraag af te handelen. 


## <a name="change-the-availability-set"></a>De beschikbaarheidsset wijzigen 

Het volgende script geeft een voor beeld van het verzamelen van de vereiste informatie, het verwijderen van de oorspronkelijke VM en het opnieuw maken van de gegevens in een nieuwe beschikbaarheidsset.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Volgende stappen

Voeg extra opslag ruimte toe aan uw virtuele machine door een extra [gegevens schijf](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)toe te voegen.

---
title: De besturingssysteem schijf voor een Azure-VM wisselen met Power shell
description: Wijzig de besturingssysteem schijf die wordt gebruikt door een virtuele machine van Azure met behulp van Power shell.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 8e928944a7508cc2a0ed35e89189fa2dd8c50665
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550379"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>De besturingssysteem schijf wijzigen die wordt gebruikt door een Azure VM met behulp van Power shell

Als u een bestaande VM hebt, maar u de schijf wilt wisselen voor een back-upschijf of een andere besturingssysteem schijf, kunt u Azure PowerShell gebruiken om de besturingssysteem schijven te vervangen. U hoeft de virtuele machine niet te verwijderen en opnieuw te maken. U kunt zelfs een beheerde schijf in een andere resource groep gebruiken, zolang deze nog niet in gebruik is.

 

De VM moet stopped\deallocated zijn. vervolgens kan de bron-ID van de beheerde schijf worden vervangen door de resource-ID van een andere beheerde schijf.

Zorg ervoor dat de VM-grootte en het opslag type compatibel zijn met de schijf die u wilt koppelen. Als de schijf die u wilt gebruiken zich bijvoorbeeld in Premium Storage bevindt, moet de virtuele machine Premium Storage kunnen zijn (zoals een grootte van de DS-serie). Beide schijven moeten ook dezelfde grootte hebben.
En zorg ervoor dat u geen niet-versleutelde virtuele machine mengt met een versleutelde besturingssysteem schijf. dit wordt niet ondersteund. Als de virtuele machine geen gebruik maakt van Azure Disk Encryption, moet de besturingssysteem schijf die wordt omgewisseld, niet gebruikmaken van Azure Disk Encryption.

Een lijst met schijven in een resource groep ophalen met behulp van [Get-AzDisk](/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Wanneer u de naam van de schijf die u wilt gebruiken hebt, stelt u deze in als de besturingssysteem schijf voor de virtuele machine. In dit voor beeld stop\deallocates de virtuele machine met de naam *myVM* en wordt de schijf met de naam *newDisk* toegewezen als de nieuwe besturingssysteem schijf. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Volgende stappen**

Als u een kopie van een schijf wilt maken, raadpleegt u [een moment opname van een schijf](snapshot-copy-managed-disk.md).

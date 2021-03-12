---
title: Een moment opname van een virtuele harde schijf maken met behulp van de portal of Power shell
description: Meer informatie over het maken van een kopie van een Azure-VM die u kunt gebruiken als back-up of voor het oplossen van problemen met behulp van de portal of Power shell.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556057"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Een moment opname maken met behulp van de portal of Power shell

Een moment opname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD). U kunt een moment opname maken van een besturings systeem of gegevens schijf VHD om te gebruiken als back-up, of om problemen met virtuele machine (VM) op te lossen.

Als u de moment opname wilt gebruiken om een nieuwe virtuele machine te maken, kunt u het beste de virtuele machine op een schone manier afsluiten voordat u een moment opname maakt, zodat alle processen die worden uitgevoerd, worden gewist.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken 

Voer de volgende stappen uit om een moment opname te maken: 
1.  Selecteer **een resource maken** op de [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer de **moment opname**.
3. Selecteer in het venster **moment opname** **maken**. Het venster **moment opname maken** wordt weer gegeven.
4. Voer een **naam** in voor de moment opname.
5. Selecteer een bestaande [resource groep](../../azure-resource-manager/management/overview.md#resource-groups) of voer de naam van een nieuwe in. 
6. Selecteer de **Locatie** van een Azure-datacenter.  
7. Voor de **bron schijf** selecteert u de beheerde schijf voor de moment opname.
8. Het **account type** selecteren dat moet worden gebruikt voor het opslaan van de moment opname. Selecteer **Standard_HDD**, tenzij u wilt dat de moment opname wordt opgeslagen op een hoogwaardige schijf.
9. Selecteer **Maken**.

## <a name="use-powershell"></a>PowerShell gebruiken

De volgende stappen laten zien hoe u de VHD-schijf kopieert en de configuratie van de moment opname maakt. U kunt vervolgens een moment opname van de schijf maken met behulp van de cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Stel een aantal para meters in: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. De VM ophalen:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. De configuratie van de moment opname maken. Voor dit voor beeld is de moment opname van de besturingssysteem schijf:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Als u uw moment opname wilt opslaan in zone-flexibele opslag, maakt u deze in een regio die [beschikbaarheids zones](../../availability-zones/az-overview.md) ondersteunt en de `-SkuName Standard_ZRS` para meter bevat.   
   
4. De moment opname maken:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Volgende stappen

Een virtuele machine maken op basis van een moment opname door een beheerde schijf te maken op basis van een moment opname en vervolgens de nieuwe beheerde schijf als de besturingssysteem schijf te koppelen. Zie het voor beeld in [een VM maken van een moment opname met Power shell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot)voor meer informatie.
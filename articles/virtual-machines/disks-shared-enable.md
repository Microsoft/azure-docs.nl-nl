---
title: Gedeelde schijven voor Azure Managed disks inschakelen
description: Configureer een Azure Managed disk met gedeelde schijven, zodat u deze kunt delen op meerdere Vm's
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 661d4ba575eafa4e261a1c92c1112a259b95eac7
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683588"
---
# <a name="enable-shared-disk"></a>Gedeelde schijf inschakelen

In dit artikel wordt beschreven hoe u de functie gedeelde schijven voor Azure Managed disks inschakelt. Gedeelde Azure-schijven is een nieuwe functie voor Azure Managed disks waarmee u gelijktijdig een beheerde schijf kunt koppelen aan meerdere virtuele machines (Vm's). Als u een beheerde schijf aan meerdere Vm's koppelt, kunt u nieuwe, geclusterde toepassingen implementeren of migreren naar Azure. 

Als u op zoek bent naar algemene informatie over beheerde schijven waarop gedeelde schijven zijn ingeschakeld, raadpleegt u [gedeelde Azure-schijven](disks-shared.md).

## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Gedeelde schijven bieden ondersteuning voor verschillende besturings systemen. Zie de secties [Windows](windows/disks-shared.md#windows) en [Linux](linux/disks-shared.md#linux) van het conceptuele artikel voor de ondersteunde besturings systemen.

## <a name="disk-sizes"></a>Schijf grootten

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Gedeelde schijven implementeren

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Een Premium SSD implementeren als een gedeelde schijf

Voor het implementeren van een beheerde schijf waarop de functie gedeelde schijf is ingeschakeld, gebruikt u de nieuwe eigenschap `maxShares` en definieert u een waarde die groter is dan 1. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf van alle virtuele machines wordt ontkoppeld. Zie de [schijf grootten](#disk-sizes) voor de toegestane waarden voor `maxShares` .

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

Vervang `[parameters('dataDiskName')]` ,, `[resourceGroup().location]` en door `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` uw eigen waarden voordat u de volgende sjabloon gebruikt.

[Sjabloon voor gedeelde schijf Premium-SSD](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Een ultra schijf implementeren als een gedeelde schijf

Als u een beheerde schijf met de functie gedeelde schijf wilt implementeren, wijzigt `maxShares` u de para meter in een waarde die groter is dan 1. Dit maakt de schijf deelbaar op meerdere Vm's.

> [!IMPORTANT]
> De waarde van `maxShares` kan alleen worden ingesteld of gewijzigd wanneer een schijf van alle virtuele machines wordt ontkoppeld. Zie de [schijf grootten](#disk-sizes) voor de toegestane waarden voor `maxShares` .


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Voor beeld van regionale schijf

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Voor beeld van zonegebonden-schijf

Dit voor beeld is bijna hetzelfde als het vorige, behalve als er een schijf wordt gemaakt in beschikbaarheids zone 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Voor beeld van regionale schijf

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Voor beeld van zonegebonden-schijf

Dit voor beeld is bijna hetzelfde als het vorige, behalve als er een schijf wordt gemaakt in beschikbaarheids zone 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Voor beeld van regionale schijf

Vervang,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` en `[parameters('diskMBpsReadOnly')]` door uw eigen waarden voordat u de volgende sjabloon gebruikt.

[Sjabloon voor regionale, gedeelde Ultra schijven](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Voor beeld van zonegebonden-schijf

Vervang,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` en `[parameters('diskMBpsReadOnly')]` door uw eigen waarden voordat u de volgende sjabloon gebruikt.

[Sjabloon voor gedeelde Ultra schijven zonegebonden](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Gedeelde Azure-schijven gebruiken met uw Vm's

Wanneer u een gedeelde schijf met hebt geïmplementeerd `maxShares>1` , kunt u de schijf koppelen aan een of meer van uw virtuele machines.

> [!NOTE]
> Als u een ultra schijf implementeert, moet u ervoor zorgen dat deze overeenkomt met de benodigde vereisten. Zie [Azure Ultra Disk gebruiken](disks-enable-ultra-ssd.md) voor meer informatie.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Ondersteunde SCSI PR-opdrachten

Zodra u de gedeelde schijf hebt gekoppeld aan uw virtuele machines in uw cluster, kunt u quorum en lees-/schrijftoegang tot stand brengen met behulp van SCSI-PR. De volgende PR-opdrachten zijn beschikbaar wanneer u gedeelde Azure-schijven gebruikt:

Als u wilt communiceren met de schijf, begint u met de lijst met permanente reserve ringen:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Wanneer u PR_RESERVE, PR_PREEMPT_RESERVATION of PR_RELEASE_RESERVATION gebruikt, geeft u een van de volgende permanente reserve ring-type op:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

U moet ook een permanente reserve ring-sleutel opgeven wanneer u PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION of PR_RELEASE-reserve ring gebruikt.


## <a name="next-steps"></a>Volgende stappen

Als u liever Azure Resource Manager sjablonen gebruikt voor het implementeren van uw schijf, zijn de volgende voorbeeld sjablonen beschikbaar:
- [Premium - SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Regionale Ultra schijven](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Zonegebonden Ultra schijven](https://aka.ms/SharedUltraDiskARMtemplateZonal)

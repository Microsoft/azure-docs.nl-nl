---
title: Het station met het besturings systeem van een virtuele Windows-machine in een Azure uitbreiden
description: Breid de grootte van het OS-station van een virtuele machine uit met behulp van Azure PowerShell in het Resource Manager-implementatie model.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 2f991dd93549cf73005127569af496df541eecde
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549597"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>De besturingssysteemschijf van een virtuele machine uitbreiden

Wanneer u een nieuwe virtuele machine (VM) maakt in een resource groep door een installatie kopie te implementeren vanuit [Azure Marketplace](https://azure.microsoft.com/marketplace/), is het standaard station van het besturings systeem vaak 127 GB (sommige installatie kopieën hebben standaard kleinere besturingssysteem schijf grootten). Hoewel het mogelijk is om gegevens schijven toe te voegen aan de virtuele machine (het nummer is afhankelijk van de SKU die u hebt gekozen) en wij raden u aan om toepassingen en CPU-intensieve workloads te installeren op deze addendum schijven, maar klanten moeten het station van het besturings systeem uitbreiden voor de ondersteuning van specifieke scenario's:

- Voor de ondersteuning van oudere toepassingen die onderdelen installeren op het station van het besturings systeem.
- Een fysieke computer of virtuele machine van on-premises migreren met een grotere OS-schijf.

> [!IMPORTANT]
> Voor het wijzigen van het formaat van een besturings systeem of gegevens schijf van een virtuele Azure-machine moet de toewijzing van de virtuele machine ongedaan worden gemaakt.
>
> Het verkleinen van een bestaande schijf wordt niet ondersteund en kan mogelijk leiden tot verlies van gegevens.
> 
> Nadat u de schijven hebt uitgebreid, moet u [het volume in het besturings systeem uitbreiden](#expand-the-volume-within-the-os) om te kunnen profiteren van de grotere schijf.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Het formaat van een beheerde schijf in het Azure Portal wijzigen

1. Ga in het [Azure Portal](https://portal.azure.com)naar de virtuele machine waarin u de schijf wilt uitbreiden. Selecteer **stoppen** om de toewijzing van de virtuele machine ongedaan te maken.
2. Wanneer de virtuele machine is gestopt, selecteert u in het menu links onder **instellingen** de optie **schijven**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Scherm afbeelding met de optie schijven die is geselecteerd in de sectie instellingen van het menu.":::

 
3. Selecteer bij **schijf naam** de schijf waarvan u de grootte wilt wijzigen.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Scherm opname van het deel venster schijven waarin een schijf naam is geselecteerd.":::

4. Selecteer in het linkermenu onder **instellingen** de optie **configuratie**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Scherm opname van de configuratie optie die is geselecteerd in de sectie instellingen van het menu.":::

5. Selecteer in **grootte (GIB)** de gewenste schijf grootte.
   
   > [!WARNING]
   > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het Maxi maal toegestane aantal is 2.048 GB voor besturingssysteem schijven. (Het is mogelijk om de VHD-BLOB buiten die grootte uit te breiden, maar het besturings systeem werkt alleen met de eerste 2.048 GB schijf ruimte.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Scherm opname van het configuratie deel venster waarin de schijf grootte is geselecteerd.":::

6. Selecteer **Opslaan**.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Scherm opname van het configuratie deel venster waarin de knop Opslaan is geselecteerd.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Het formaat van een beheerde schijf wijzigen met behulp van Power shell

Open uw Power shell ISE of Power shell-venster in de beheer modus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resource beheer en selecteer uw abonnement:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Stel de naam van de resource groep en de VM-naam in:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Een verwijzing naar uw virtuele machine verkrijgen:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Stop de virtuele machine voordat u het formaat van de schijf wijzigt:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Verkrijg een verwijzing naar de beheerde besturingssysteem schijf. Stel de grootte van de beheerde besturingssysteem schijf in op de gewenste waarde en werk de schijf bij:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het Maxi maal toegestane aantal is 2.048 GB voor besturingssysteem schijven. (Het is mogelijk om de VHD-BLOB buiten die grootte uit te breiden, maar het besturings systeem werkt alleen met de eerste 2.048 GB schijf ruimte.)
    > 
         
6. Het bijwerken van de virtuele machine kan enkele seconden duren. Wanneer de opdracht is uitgevoerd, start u de VM opnieuw op:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

Dat is alles. Ga nu met RDP naar de VM, open Computerbeheer (of Schijfbeheer) en vouw het station met de zojuist toegewezen ruimte uit.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Het formaat van een onbeheerde schijf wijzigen met behulp van Power shell

Open uw Power shell ISE of Power shell-venster in de beheer modus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resource beheer en selecteer uw abonnement:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. De naam van de resource groep en VM-namen instellen:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Een verwijzing naar uw virtuele machine verkrijgen:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Stop de virtuele machine voordat u het formaat van de schijf wijzigt:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Stel de grootte van de niet-beheerde besturingssysteem schijf in op de gewenste waarde en werk de virtuele machine bij:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het Maxi maal toegestane aantal is 2.048 GB voor besturingssysteem schijven. (Het is mogelijk om de VHD-BLOB buiten die grootte uit te breiden, maar het besturings systeem kan alleen met de eerste 2.048 GB ruimte werken.)
    > 
    > 
   
6. Het bijwerken van de virtuele machine kan enkele seconden duren. Wanneer de opdracht is uitgevoerd, start u de VM opnieuw op:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Scripts voor besturingssysteem schijf

Hieronder ziet u het volledige script voor uw referentie voor zowel beheerde als onbeheerde schijven:


**Beheerde schijven**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Niet-beheerde schijven**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Grootte van gegevens schijven wijzigen

Dit artikel is voornamelijk gericht op het uitbreiden van de besturingssysteem schijf van de virtuele machine, maar het script kan ook worden gebruikt voor het uitbreiden van de gegevens schijven die zijn gekoppeld aan de virtuele machine. Als u bijvoorbeeld de eerste gegevensschijf die is gekoppeld aan de VM wilt uitbreiden, vervangt u het object `OSDisk` van `StorageProfile` door de matrix `DataDisks` en gebruikt u een numerieke index om een verwijzing naar de eerste gekoppelde schijf te verkrijgen, zoals hieronder wordt weergegeven:

**Beheerde schijf**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Niet-beheerde schijf**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Op dezelfde manier kunt u verwijzen naar andere gegevens schijven die zijn gekoppeld aan de VM, hetzij door gebruik te maken van een index zoals hierboven wordt weer gegeven, of de eigenschap **name** van de schijf:


**Beheerde schijf**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Niet-beheerde schijf**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Het volume binnen het besturings systeem uitbreiden

Wanneer u de schijf voor de virtuele machine hebt uitgebreid, moet u naar het besturings systeem gaan en het volume uitbreiden om de nieuwe ruimte te maken. Er zijn verschillende methoden voor het uitbreiden van een partitie. In deze sectie wordt beschreven hoe u de virtuele machine verbindt met een RDP-verbinding om de partitie uit te breiden met **Disk Part**.

1. Open een RDP-verbinding met uw VM.

2. Open een opdracht prompt en typ **Disk Part**.

3. Typ bij de **Disk Part** -prompt `list volume` . Noteer het volume dat u wilt uitbreiden.

4. Typ bij de **Disk Part** -prompt `select volume <volumenumber>` . Hiermee wordt het volume *volumenumber* geselecteerd dat u wilt uitbreiden naar aaneengesloten lege ruimte op dezelfde schijf.

5. Typ bij de **Disk Part** -prompt `extend [size=<size>]` . Hiermee wordt het geselecteerde volume uitgebreid naar *grootte* in mega bytes (MB).


## <a name="next-steps"></a>Volgende stappen

U kunt ook schijven koppelen met behulp van de [Azure Portal](attach-managed-disk-portal.md).

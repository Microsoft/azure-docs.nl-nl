---
title: Een Windows-probleemoplossings-VM gebruiken met Azure PowerShell | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Windows-VM'S in azure door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: fbdccc2efb72626d1974f4cd81be9d2ef4c522d4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201583"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van Azure PowerShell
Als op uw virtuele Windows-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk de stappen voor het oplossen van problemen op de schijf zelf uitvoeren. Een veelvoorkomend voor beeld hiervan is een mislukte toepassings update waarmee wordt voor komen dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om de schijf te verbinden met een andere Windows-VM om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM te herstellen. 

> [!Important]
> De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [beheerde schijven](../managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
We kunnen nu Azure PowerShell gebruiken om de besturingssysteem schijf voor een virtuele machine te wijzigen. Het is niet meer nodig om de virtuele machine te verwijderen en opnieuw te maken.

Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
2. Maak een moment opname van de besturingssysteem schijf van de virtuele machine.
3. Maak een schijf van de moment opname van de besturingssysteem schijf.
4. Koppel de schijf als een gegevens schijf aan een herstel-VM.
5. Maak verbinding met de Recovery-VM. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de gekopieerde besturingssysteem schijf op te lossen.
6. De schijf ontkoppelen en loskoppelen van de herstel-VM.
7. Wijzig de besturingssysteem schijf voor de betrokken VM.

U kunt de opdrachten voor het herstellen van de virtuele machine gebruiken om stap 1, 2, 3, 4, 6 en 7 te automatiseren. Zie [een Windows-VM herstellen met behulp van de opdrachten voor het herstellen van virtuele Azure-machines](repair-windows-vm-using-azure-virtual-machine-repair-commands.md)voor meer documentatie en instructies.

Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/) hebt geïnstalleerd en bent aangemeld bij uw abonnement:

```powershell
Connect-AzAccount
```

Vervang in de volgende voor beelden de parameter namen door uw eigen waarden. 

## <a name="determine-boot-issues"></a>Opstart problemen vaststellen
U kunt een scherm opname van uw virtuele machine in azure bekijken om opstart problemen op te lossen. Deze scherm afbeelding kan u helpen identificeren waarom een virtuele machine niet kan worden opgestart. In het volgende voor beeld wordt de scherm opname opgehaald van de Windows-VM met `myVM` de naam in de resource groep met de naam `myResourceGroup` :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Controleer de scherm afbeelding om te bepalen waarom de virtuele machine niet kan worden opgestart. Noteer alle specifieke fout berichten of fout codes die worden weer gegeven.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

In het volgende voor beeld wordt de VM gestopt met de naam `myVM` van de resource groep met de naam `myResourceGroup` :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Wacht totdat de virtuele machine is verwijderd voordat u de volgende stap verwerkt.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Een moment opname maken van de besturingssysteem schijf van de virtuele machine

In het volgende voor beeld wordt een moment opname gemaakt met `mySnapshot` de naam van de besturingssysteem schijf van de virtuele machine met de naam ' myVM '. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Deze kan niet worden gekoppeld aan een virtuele machine. In de volgende stap maakt u een schijf van deze moment opname.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname

Met dit script maakt u een beheerde schijf met een naam `newOSDisk` uit de moment opname met de naam `mysnapshot` .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
U hebt nu een kopie van de oorspronkelijke besturingssysteem schijf. U kunt deze schijf koppelen aan een andere Windows-VM voor het oplossen van problemen.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>De schijf koppelen aan een andere Windows-VM voor probleem oplossing

Nu koppelen we de kopie van de oorspronkelijke besturingssysteem schijf als een gegevens schijf aan een virtuele machine. Met dit proces kunt u configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden op de schijf controleren. In het volgende voor beeld wordt de schijf met de naam van `newOSDisk` de virtuele machine gekoppeld `RecoveryVM` .

> [!NOTE]
> Als u de schijf wilt koppelen, moet het exemplaar van de oorspronkelijke besturingssysteem schijf en de herstel-VM zich op dezelfde locatie bevindt.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Verbinding maken met de herstel-VM en problemen oplossen op de gekoppelde schijf

1. RDP naar de herstel-VM met de juiste referenties. In het volgende voor beeld wordt het RDP-verbindings bestand gedownload voor de virtuele machine `RecoveryVM` met de naam in de resource groep `myResourceGroup` , en wordt deze gedownload naar `C:\Users\ops\Documents`

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. De gegevens schijf moet automatisch worden gedetecteerd en gekoppeld. Bekijk de lijst met gekoppelde volumes om de stationsletter als volgt te bepalen:

    ```powershell
    Get-Disk
    ```

    In de volgende voorbeeld uitvoer ziet u de schijf die is verbonden met een schijf **2**. (U kunt ook gebruiken `Get-Volume` om de stationsletter weer te geven):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Nadat de kopie van de oorspronkelijke besturingssysteem schijf is gekoppeld, kunt u zo nodig onderhouds-en probleemoplossings stappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.

## <a name="unmount-and-detach-original-os-disk"></a>Oorspronkelijke besturingssysteem schijf ontkoppelen en loskoppelen
Wanneer de fouten zijn opgelost, ontkoppelt u de bestaande schijf en koppelt u deze los van de herstel-VM. U kunt uw schijf met geen enkele andere virtuele machine gebruiken totdat de lease die de schijf aan de herstel-VM koppelt, wordt vrijgegeven.

1. Ontkoppel de gegevens schijf uit uw RDP-sessie op de virtuele machine voor herstel. U hebt het schijf nummer nodig van de vorige `Get-Disk` cmdlet. Ga vervolgens als `Set-Disk` volgt te werkt om de schijf als offline in te stellen:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bevestig dat de schijf nu opnieuw is ingesteld als offline `Get-Disk` . In de volgende voorbeeld uitvoer ziet u dat de schijf nu is ingesteld als offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Sluit uw RDP-sessie af. Verwijder vanuit uw Azure PowerShell-sessie de schijf met de naam `newOSDisk` uit de virtuele machine met de naam ' RecoveryVM '.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>De besturingssysteem schijf voor de betrokken VM wijzigen

U kunt Azure PowerShell gebruiken om de schijven van het besturings systeem te wisselen. U hoeft de virtuele machine niet te verwijderen en opnieuw te maken.

In dit voor beeld wordt de virtuele machine met de naam gestopt `myVM` en wordt de schijf toegewezen `newOSDisk` als de nieuwe besturingssysteem schijf. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten controleren en inschakelen

In het volgende voor beeld wordt de diagnostische uitbrei ding ingeschakeld op de virtuele machine `myVMDeployed` met de naam in de resource groep met de naam `myResourceGroup` :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met RDP-verbindingen met een Azure VM oplossen](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Windows-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Resource Manager.

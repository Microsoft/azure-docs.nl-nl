---
title: Azure VM-schijven uitsluiten van replicatie met Azure Site Recovery en Azure PowerShell
description: Meer informatie over het uitsluiten van schijven van virtuele Azure-machines tijdens het Azure Site Recovery met behulp van Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135800"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Schijven uitsluiten van de Power shell-replicatie van virtuele Azure-machines

In dit artikel wordt beschreven hoe u schijven uitsluit wanneer u virtuele Azure-machines repliceert. U kunt schijven uitsluiten voor het optimaliseren van de verbruikte replicatie bandbreedte of de bronnen op de doel zijde die door die schijven worden gebruikt. Deze mogelijkheid is momenteel alleen beschikbaar via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

- Zorg ervoor dat u bekend bent met de [nood herstel architectuur en-onderdelen](azure-to-azure-architecture.md).
- Raadpleeg de [ondersteuningsvereisten](azure-to-azure-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat u de AzureRm-module AZ van Power shell hebt. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om Power shell te installeren of bij te werken.
- Zorg ervoor dat u ten minste één keer een Recovery Services-kluis en beveiligde virtuele machines hebt gemaakt. Als u deze dingen nog niet hebt gedaan, volgt u het proces [voor het instellen van herstel na nood geval voor virtuele Azure-machines met behulp van Azure PowerShell](azure-to-azure-powershell.md).
- [Lees dit artikel](azure-to-azure-enable-replication-added-disk.md)als u informatie zoekt over het toevoegen van schijven aan een Azure VM die is ingeschakeld voor replicatie.

## <a name="why-exclude-disks-from-replication"></a>Waarom schijven uitsluiten van replicatie
Mogelijk moet u schijven uitsluiten van replicatie omdat:

- De virtuele machine heeft [Azure site Recovery limieten bereikt voor het repliceren van gegevens wijzigings tarieven](./azure-to-azure-support-matrix.md).

- De gegevens die op de uitgesloten schijf zijn achtergelaten, zijn niet belang rijk of hoeven niet te worden gerepliceerd.

- U wilt opslag-en netwerk bronnen opslaan door de gegevens niet te repliceren.

## <a name="how-to-exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

In ons voor beeld repliceert u een virtuele machine met één besturings systeem en drie gegevens schijven in de regio VS-Oost naar de regio vs-West 2. De naam van de virtuele machine is *AzureDemoVM*. We sluiten schijf 1 uit en houden schijven 2 en 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Details ophalen van de virtuele machines die moeten worden gerepliceerd

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Details over de schijven van de virtuele machine ophalen. Deze informatie wordt later gebruikt bij het starten van de replicatie van de virtuele machine.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Een virtuele machine van Azure repliceren

In het volgende voor beeld wordt ervan uitgegaan dat u al beschikt over een cache-opslag account, een replicatie beleid en toewijzingen. Als u deze dingen niet hebt, volgt u het proces bij het [instellen van herstel na nood geval voor virtuele Azure-machines met behulp van Azure PowerShell](azure-to-azure-powershell.md).

Een virtuele machine van Azure repliceren met *Managed disks*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Wanneer de bewerking voor het starten van de replicatie slaagt, worden de VM-gegevens gerepliceerd naar de herstel regio.

U kunt naar de Azure Portal gaan en de gerepliceerde Vm's bekijken onder gerepliceerde items.

Het replicatie proces begint met het seeden van een kopie van de replicerende schijven van de virtuele machine in de herstel regio. Deze fase wordt de initiële replicatie fase genoemd.

Wanneer de initiële replicatie is voltooid, wordt de replicatie verplaatst naar de fase differentiële synchronisatie. Op dit moment wordt de virtuele machine beveiligd. Selecteer de beveiligde virtuele machine om te zien of er schijven zijn uitgesloten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het uitvoeren van een testfailover](site-recovery-test-failover-to-azure.md).

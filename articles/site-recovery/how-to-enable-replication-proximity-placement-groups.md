---
title: Virtuele Azure-machines repliceren die worden uitgevoerd in proximity-plaatsings groepen
description: Meer informatie over het repliceren van virtuele Azure-machines die worden uitgevoerd in proximity placement groups met behulp van Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 7ac836992db33c6212fd009b914b30b7221249d8
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745580"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Virtuele Azure-machines repliceren die worden uitgevoerd in nabijheidsplaatsingsgroepen naar een andere regio

In dit artikel wordt beschreven hoe u virtuele machines die worden uitgevoerd in een proximity-plaatsings groep, repliceert naar een secundaire regio.

[Proximity placement groups](../virtual-machines/windows/proximity-placement-groups-portal.md) is een logische groepering van virtuele machines van Azure, die u kunt gebruiken om de netwerk LATENTIE tussen vm's die aan uw toepassingen is gekoppeld, te verlagen. Wanneer de Vm's binnen dezelfde plaatsings groep worden geïmplementeerd, bevinden ze zich fysiek zo dicht mogelijk bij elkaar. Proximity-plaatsings groepen zijn bijzonder nuttig voor het oplossen van de vereisten van latentie gevoelige werk belastingen.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Herstel na nood geval met proximity-plaatsings groepen

In een typisch scenario is het mogelijk dat uw virtuele machines worden uitgevoerd in een proximity-plaatsings groep om te voor komen dat de netwerk latentie tussen de verschillende lagen van uw toepassing. Hoewel dit kan leiden tot een optimale netwerk latentie van uw toepassing, kunt u deze toepassingen beveiligen met Site Recovery voor een storing op regio niveau. Site Recovery repliceert de gegevens van de ene regio naar een andere Azure-regio en brengt de computers in een herstel regio voor nood gevallen in een gebeurtenis van de failover.

## <a name="considerations"></a>Overwegingen

- De beste poging is om de virtuele machines te failoveren/failback uit te stellen in een proximity-plaatsings groep. Als de virtuele machine tijdens de failover/failback echter niet in de nabijheid kan worden gebracht, worden er nog steeds failover/failback uitgevoerd en worden de virtuele machines gemaakt buiten een proximity-plaatsings groep.
-  Als een Beschikbaarheidsset is vastgemaakt aan een proximity-plaatsings groep en tijdens failover/failback-Vm's in de beschikbaarheidsset een toewijzings beperking hebben, worden de virtuele machines gemaakt buiten de beschikbaarheidsset en proximity-plaatsings groep.
-  Site Recovery voor proximity-plaatsings groepen wordt niet ondersteund voor niet-beheerde schijven.

> [!NOTE]
> Azure Site Recovery biedt geen ondersteuning voor failback van beheerde schijven voor Hyper-V naar Azure-scenario's. Daarom wordt failback van proximity-plaatsings groep in azure naar Hyper-V niet ondersteund.

## <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat u beschikt over de Azure PowerShell AZ-module. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [hand leiding voor het installeren en configureren van Azure PowerShell](/powershell/azure/install-az-ps).
2. De minimale Azure PowerShell AZ-versie moet 4.1.0 zijn. Als u de huidige versie wilt controleren, gebruikt u de onderstaande opdracht:
    ```
    Get-InstalledModule -Name Az
    ```

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Site Recovery instellen voor Virtual Machines in proximity-plaatsings groep

> [!NOTE]
> Zorg ervoor dat u beschikt over de unieke ID van de plaatsings groep voor doel gerichte nabijheid. Als u een nieuwe proximity-plaatsings groep wilt maken, controleert u de opdracht [hier](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) en als u een bestaande proximity-plaatsings groep gebruikt, gebruikt u de opdracht [hier](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure naar Azure

1. [Meld](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) u aan bij uw account en stel uw abonnement in.
2. Bekijk de details van de virtuele machine die u van plan bent te repliceren, zoals [hier](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)wordt beschreven.
3. [Maak](./azure-to-azure-powershell.md#create-a-recovery-services-vault) de Recovery Services-kluis en [Stel](./azure-to-azure-powershell.md#set-the-vault-context) de kluis context in.
4. Bereid de kluis voor op het starten van de virtuele replicatie machine. Hiervoor moet u een [service Fabric-object](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) maken voor zowel primaire als herstel regio's.
5. [Maak](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) een site Recovery-beveiligings container voor zowel de primaire als de herstel infrastructuur.
6. Een replicatie beleid [maken](./azure-to-azure-powershell.md#create-a-replication-policy) .
7. Maak een beveiligings container toewijzing tussen de primaire en herstel beveiligings container met behulp van [deze](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) stappen en een beveiligings container toewijzing voor failback, zoals [hier](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)wordt beschreven.
8. Maak een cache-opslag account door [de](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) volgende stappen uit te voeren.
9. Maak de vereiste netwerk toewijzingen zoals [hier](./azure-to-azure-powershell.md#create-network-mappings)wordt vermeld.
10. Als u de virtuele machine van Azure wilt repliceren met Managed disks, gebruikt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Wanneer u replicatie inschakelt voor meerdere gegevens schijven, gebruikt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs.Add($OSDiskReplicationConfig)

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs.Add($DataDisk1ReplicationConfig)
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Wanneer zone-replicatie met PPG wordt ingeschakeld, wordt de opdracht voor het starten van de replicatie met de Power shell-cmdlet uitgewisseld.

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Zodra de bewerking voor het starten van de replicatie is voltooid, worden de gegevens van de virtuele machine gerepliceerd naar de herstel regio.

Het replicatie proces wordt gestart door eerst een kopie van de replicerende schijven van de virtuele machine in het herstel gebied te seeden. Deze fase wordt de initiële replicatie fase genoemd.

Wanneer de initiële replicatie is voltooid, wordt de replicatie verplaatst naar de differentiële synchronisatie fase. Op dit moment wordt de virtuele machine beveiligd en kan er een testfailover worden uitgevoerd. De replicatie status van het gerepliceerde item dat de virtuele machine vertegenwoordigt, wordt naar de beveiligde status verplaatst nadat de initiële replicatie is voltooid.

Controleer de replicatie status en de replicatie status voor de virtuele machine door Details van het met replicatie beveiligde item corresponderende items op te halen.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Als u een testfailover wilt uitvoeren, voert u [de volgende](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) stappen uit om de testfailover te valideren en op te schonen.
12. Volg de stappen die [hier](./azure-to-azure-powershell.md#fail-over-to-azure)worden beschreven om failover uit te voeren.
13. Als u de bron regio opnieuw wilt beveiligen en failback, gebruikt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Volg [de stappen om](./azure-to-azure-powershell.md#disable-replication)replicatie uit te scha kelen.

### <a name="vmware-to-azure"></a>VMware naar Azure

1. Zorg ervoor dat u [de on-premises VMware-servers voorbereidt](./vmware-azure-tutorial-prepare-on-premises.md) op herstel na nood geval naar Azure.
2. Meld u aan bij uw account en stel uw abonnement in zoals [hier](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)is opgegeven.
3. [Stel](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) een Recovery Services kluis in en [Stel de kluis context](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)in.
4. [Valideer](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) uw kluis registratie.
5. Een replicatie beleid [maken](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) .
6. [Voeg](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) een vCenter-Server toe en Detecteer virtuele machines en [Maak](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) opslag accounts voor replicatie.
7. Als u VMware Virtual Machines wilt repliceren, controleert u de details hier en volgt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. U kunt de replicatie status en replicatie status van de virtuele machine controleren met de cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Configureer de failover-instellingen door de stappen [hier](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)te volgen.
10. [Voer](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) een testfailover uit.
11. Failover naar Azure met behulp van [deze](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) stappen.

### <a name="hyper-v-to-azure"></a>Hyper-V naar Azure

1. Zorg ervoor dat u [de on-premises Hyper-V-servers voorbereidt](./hyper-v-prepare-on-premises-tutorial.md) op herstel na nood geval naar Azure.
2. [Meld](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) u aan bij Azure.
3. [Stel](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) uw kluis in en [Stel](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) de Recovery Services kluis context in.
4. [Maak](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) een Hyper-V-site.
5. [Installeer](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) de provider en de agent.
6. Een replicatie beleid [maken](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) .
7. Schakel replicatie in met behulp van de onderstaande stappen: 
    
    a. Haal als volgt het Beveilig bare item op dat overeenkomt met de virtuele machine die u wilt beveiligen:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Beveilig de virtuele machine. Als er meer dan één schijf aan de virtuele machine is gekoppeld, geeft u de besturingssysteem schijf op met behulp van de para meter OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Wacht totdat de virtuele machines een beveiligde status hebben bereikt na de initiële replicatie. Dit kan enige tijd duren, afhankelijk van factoren zoals de hoeveelheid gegevens die moeten worden gerepliceerd en de beschik bare upstream-band breedte naar Azure. Wanneer een beveiligde status is ingesteld, worden de taak status en StateDescription als volgt bijgewerkt: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Herstel eigenschappen van de virtuele machine (zoals de grootte van de VM-rol) en het Azure-netwerk waarmee de VM-NIC na een failover moet worden gekoppeld.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Voer [een testfailover uit.](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)


## <a name="next-steps"></a>Volgende stappen

Als u het opnieuw beveiligen en failback voor VMware naar Azure wilt uitvoeren, volgt u de stappen die [hier](./vmware-azure-prepare-failback.md)worden beschreven.

Als u een failover wilt uitvoeren voor Hyper-V naar Azure, volgt u de stappen die [hier](./site-recovery-failover.md) worden beschreven en voert u de volgende stappen uit om een failback uit te [voeren.](./hyper-v-azure-failback.md)

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.
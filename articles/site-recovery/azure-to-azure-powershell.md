---
title: Herstel na nood gevallen voor Azure-Vm's met Azure PowerShell en Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor virtuele Azure-machines met Azure Site Recovery met behulp van Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 1570bd9dfa62caa749d5a3983b93c2555be058ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93348726"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Herstel na noodgevallen van virtuele Azure-machines instellen met Azure PowerShell

In dit artikel ziet u hoe u herstel na nood gevallen instelt en test voor virtuele Azure-machines met behulp van Azure PowerShell.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> - Maak een Recovery Services-kluis.
> - Stel de kluis context in voor de Power shell-sessie.
> - Bereid de kluis voor op het repliceren van virtuele Azure-machines.
> - Netwerk toewijzingen maken.
> - Maak opslag accounts om virtuele machines naar te repliceren.
> - Virtuele Azure-machines repliceren naar een herstel regio voor herstel na nood gevallen.
> - Een testfailover uitvoeren, de testfailover valideren en de testfailover opschonen.
> - Failover naar het herstel gebied.

> [!NOTE]
> Niet alle scenario mogelijkheden die beschikbaar zijn via de portal, zijn mogelijk beschikbaar via Azure PowerShell. Enkele van de scenario mogelijkheden die momenteel niet worden ondersteund via Azure PowerShell zijn:
> - De mogelijkheid om op te geven dat alle schijven in een virtuele machine moeten worden gerepliceerd zonder dat elke schijf van de virtuele machine expliciet hoeft te worden opgegeven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Raadpleeg de [ondersteuningsvereisten](azure-to-azure-support-matrix.md) voor alle onderdelen.
- U hebt de Azure PowerShell-module `Az`. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [hand leiding voor het installeren en configureren van Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Meld u aan bij uw Azure-abonnement met de cmdlet `Connect-AzAccount`.

```azurepowershell
Connect-AzAccount
```

Selecteer uw Azure-abonnement. Gebruik de cmdlet `Get-AzSubscription` om de lijst met Azure-abonnementen weer te geven waartoe u toegang hebt. Selecteer het Azure-abonnement waarmee u wilt werken met de `Set-AzContext` cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Details ophalen van de virtuele machine die moet worden gerepliceerd

In dit artikel wordt een virtuele machine in de regio VS-Oost gerepliceerd naar en hersteld in de regio vs West 2. De virtuele machine die wordt gerepliceerd, heeft een besturingssysteem schijf en één gegevens schijf. De naam van de virtuele machine die in het voor beeld wordt gebruikt, is `AzureDemoVM` .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Details van de schijf voor de schijven van de virtuele machine ophalen. Schijf Details worden later gebruikt bij het starten van de replicatie voor de virtuele machine.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak een resource groep waarin u de Recovery Services kluis wilt maken.

> [!IMPORTANT]
> * De Recovery Services-kluis en de virtuele machines die worden beveiligd, moeten zich op verschillende Azure-locaties bevinden.
> * De resource groep van de Recovery Services-kluis en de virtuele machines die worden beveiligd, moeten zich op verschillende Azure-locaties bevinden.
> * De Recovery Services-kluis en de resource groep waartoe deze behoort, kunnen zich op dezelfde Azure-locatie bevindt.

In het voor beeld in dit artikel bevindt de virtuele machine die wordt beveiligd, zich in de regio VS-Oost. De geselecteerde herstel regio voor herstel na nood gevallen is de regio vs-West 2. De Recovery Services-kluis en de resource groep van de kluis bevinden zich in de herstel regio, VS-West 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Maak een Recovery Services-kluis. In dit voor beeld wordt een Recovery Services kluis met `a2aDemoRecoveryVault` de naam gemaakt in de regio vs West 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>De kluis context instellen

Stel de kluis context in voor gebruik in de Power shell-sessie. Nadat de kluis context is ingesteld, worden Azure Site Recovery bewerkingen in de Power shell-sessie uitgevoerd in de context van de geselecteerde kluis.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Voor een migratie van Azure naar Azure kunt u de kluis context instellen op de zojuist gemaakte kluis:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>De kluis voorbereiden om virtuele Azure-machines te repliceren

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Een Site Recovery Fabric-object maken om de primaire (bron) regio aan te duiden

Het Fabric-object in de kluis vertegenwoordigt een Azure-regio. Het primaire Fabric-object wordt gemaakt om de Azure-regio weer te geven waartoe de virtuele machines behoren die met de kluis worden beveiligd. In het voor beeld in dit artikel bevindt de virtuele machine die wordt beveiligd, zich in de regio VS-Oost.

- Er kan slechts één Fabric-object per regio worden gemaakt.
- Als u eerder Site Recovery replicatie voor een virtuele machine in de Azure Portal hebt ingeschakeld, maakt Site Recovery automatisch een Fabric-object. Als er voor een regio een Fabric-object bestaat, kunt u geen nieuwe maken.

Voordat u begint, moet u weten dat Site Recovery bewerkingen asynchroon worden uitgevoerd. Wanneer u een bewerking initieert, wordt een Azure Site Recovery taak verzonden en wordt een taak tracking object geretourneerd. Gebruik het object voor taak tracering om de meest recente status voor de taak () op te halen `Get-AzRecoveryServicesAsrJob` en om de status van de bewerking te controleren.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Als virtuele machines uit meerdere Azure-regio's worden beveiligd met dezelfde kluis, maakt u één Fabric-object voor elke Azure-bron regio.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Een Site Recovery Fabric-object maken om de herstel regio aan te duiden

Het Recovery Fabric-object vertegenwoordigt de Azure-herstel locatie. Als er een failover is, worden virtuele machines gerepliceerd en hersteld naar de herstel regio die wordt vertegenwoordigd door de herstel infrastructuur. De Azure-regio voor herstel die in dit voor beeld wordt gebruikt, is vs-West 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Een Site Recovery-beveiligings container maken in de primaire infra structuur

De beveiligings container is een container die wordt gebruikt om gerepliceerde items binnen een infra structuur te groeperen.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Een Site Recovery-beveiligings container maken in de herstel infrastructuur

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

#### <a name="fabric-and-container-creation-when-enabling-zone-to-zone-replication"></a>Infra structuur en container maken bij het inschakelen van zone replicatie

Wanneer zone-replicatie wordt ingeschakeld, wordt er slechts één infra structuur gemaakt. Maar er zijn twee containers. Ervan uitgaande dat de regio Europa-west is, gebruikt u de volgende opdrachten om de primaire en beveiligings containers op te halen:

```azurepowershell
$primaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-container"
$recoveryPprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-t-container"
```

### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Een toewijzing van een beveiligings container maken tussen de primaire en herstel beveiligings container

Een toewijzing van een beveiligings container wijst de primaire beveiligings container toe aan een herstel beveiligings container en een replicatie beleid. Maak een toewijzing voor elk replicatie beleid dat u gebruikt voor het repliceren van virtuele machines tussen een beveiligings container paar.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

#### <a name="protection-container-mapping-creation-when-enabling-zone-to-zone-replication"></a>Toewijzing van beveiligings container maken wanneer zone replicatie wordt ingeschakeld

Wanneer zone replicatie inschakelt, gebruikt u de onderstaande opdracht om de toewijzing van de beveiligings container te maken. Ervan uitgaande dat de regio is Europa-west, wordt de opdracht

```azurepowershell
$protContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimprotectionContainer -Name "westeurope-westeurope-24-hour-retention-policy-s"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Een beveiligings container toewijzing maken voor failback (omgekeerde replicatie na een failover)

Wanneer u klaar bent om de virtuele machine waarvoor een failover is uitgevoerd, toe te voegen aan de oorspronkelijke Azure-regio, voert u een failback uit. Als u een failback wilt uitvoeren, wordt de virtuele machine waarvoor een failover is uitgevoerd, omgekeerd gerepliceerd van de regio voor een failover naar de oorspronkelijke regio. Voor omgekeerde replicatie, de rollen van de oorspronkelijke regio en de herstel regio-switch. De oorspronkelijke regio wordt nu de nieuwe herstel regio en de aanvankelijke herstel regio wordt nu de primaire regio. De toewijzing van de beveiligings container voor omgekeerde replicatie vertegenwoordigt de geschakelde rollen van de oorspronkelijke en herstel regio's.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Cache-opslag account en doel opslag account maken

Een cache-opslag account is een standaard opslag account in dezelfde Azure-regio als de virtuele machine die wordt gerepliceerd. Het cache-opslag account wordt gebruikt voor het tijdelijk bewaren van replicatie wijzigingen voordat de wijzigingen worden verplaatst naar de Azure-regio voor herstel. U kunt ervoor kiezen om andere cache opslag accounts voor de verschillende schijven van een virtuele machine op te geven, maar dit is niet nodig.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Voor virtuele machines die **geen beheerde schijven gebruiken**, is het doel opslag account het opslag account in de herstel regio waarnaar schijven van de virtuele machine worden gerepliceerd. Het doel opslag account kan een Standard-opslag account of een Premium Storage-account zijn. Selecteer het type opslag account dat is vereist op basis van de waarde voor het wijzigen van de gegevens (i/o-schrijf snelheden) voor de schijven en de Azure Site Recovery ondersteunde verloop limieten voor het opslag type.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Netwerk toewijzingen maken

Een netwerk toewijzing wijst virtuele netwerken in de primaire regio toe aan virtuele netwerken in de herstel regio. De netwerk toewijzing specificeert het virtuele Azure-netwerk in de herstel regio, waarmee een virtuele machine in het primaire virtuele netwerk failover moet uitvoeren. Een virtueel Azure-netwerk kan worden toegewezen aan slechts één virtueel Azure-netwerk in een herstel regio.

- Maak een virtueel Azure-netwerk in de herstel regio zodat failover kan worden uitgevoerd naar:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Haal het primaire virtuele netwerk op. Het VNet waarmee de virtuele machine is verbonden:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Netwerk toewijzing maken tussen het primaire virtuele netwerk en het virtuele netwerk voor herstel:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Netwerk toewijzing maken voor de omgekeerde richting (failback):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Virtuele Azure-machine repliceren

Repliceer de virtuele Azure-machine met **Managed disks**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Repliceer de virtuele Azure-machine met niet- **beheerde schijven**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Zodra de bewerking voor het starten van de replicatie is voltooid, worden de gegevens van de virtuele machine gerepliceerd naar de herstel regio.

Het replicatie proces wordt gestart door eerst een kopie van de replicerende schijven van de virtuele machine in het herstel gebied te seeden. Deze fase wordt de initiële replicatie fase genoemd.

Wanneer de initiële replicatie is voltooid, wordt de replicatie verplaatst naar de differentiële synchronisatie fase. Op dit moment wordt de virtuele machine beveiligd en kan er een testfailover worden uitgevoerd. De replicatie status van het gerepliceerde item dat de virtuele machine vertegenwoordigt, wordt naar de **beveiligde** status verplaatst nadat de initiële replicatie is voltooid.

Controleer de replicatie status en de replicatie status voor de virtuele machine door Details van het met replicatie beveiligde item corresponderende items op te halen.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Een testfailover uitvoeren, de testfailover valideren en de testfailover opschonen

Nadat de replicatie voor de virtuele machine een beveiligde status heeft bereikt, kan een testfailover worden uitgevoerd op de virtuele machine (op het voor replicatie beveiligde item van de virtuele machine).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Voer een testfailover uit.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Wacht tot de bewerking testfailover is voltooid.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Nadat de taak testfailover is voltooid, kunt u verbinding maken met de virtuele machine waarvoor de test is mislukt en de testfailover valideren.

Wanneer het testen is voltooid voor de virtuele machine waarvoor de test is mislukt, moet u de test kopie opschonen door de bewerking voor het opschonen van de failovertest te starten. Met deze bewerking wordt de test kopie van de virtuele machine verwijderd die is gemaakt door de testfailover.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Failover naar Azure

Failover van de virtuele machine naar een specifiek herstel punt.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Wanneer de failover-taak is voltooid, kunt u de failover-bewerking door voeren.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Opnieuw beveiligen en failback naar de bron regio

Wanneer u klaar bent om terug te gaan naar de oorspronkelijke regio, start u na een failover de omgekeerde replicatie voor het beveiligde replicatie-item met behulp van de- `Update-AzRecoveryServicesAsrProtectionDirection` cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Nadat de beveiliging is voltooid, kunt u een failover uitvoeren in de omgekeerde richting, VS-West naar VS-Oost en failback naar de bron regio.

## <a name="disable-replication"></a>Replicatie uitschakelen

U kunt replicatie met de `Remove-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet uitschakelen.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [Azure site Recovery Power shell-referentie](/powershell/module/az.RecoveryServices) voor meer informatie over hoe u andere taken kunt uitvoeren, zoals het maken van herstel plannen en het testen van de failover van herstel plannen met Power shell.

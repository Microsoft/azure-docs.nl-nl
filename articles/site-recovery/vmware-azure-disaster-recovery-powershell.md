---
title: VMware-nood herstel instellen met behulp van Power shell in azure site Revoery
description: Meer informatie over het instellen van replicatie en failover naar Azure voor herstel na nood gevallen van virtuele VMware-machines met Power shell in Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129907"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Herstel na noodgevallen van virtuele VMware-machines naar Azure instellen met PowerShell

In dit artikel ziet u hoe u virtuele VMware-machines repliceert naar Azure met behulp van Azure PowerShell.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> - Een Recovery Services-kluis maken en de kluiscontext instellen.
> - Valideer de registratie van de server in de kluis.
> - Stel de replicatie in, met inbegrip van een replicatie beleid. Voeg uw vCenter-Server toe en ontdek Vm's.
> - Een vCenter-Server toevoegen en detecteren
> - Maak opslag accounts voor het opslaan van replicatie Logboeken of-gegevens en repliceer de Vm's.
> - Een failover uitvoeren. Failover-instellingen configureren, een instellingen uitvoeren voor het repliceren van virtuele machines.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](vmware-azure-architecture.md).
- Raadpleeg de [ondersteuningsvereisten](./vmware-physical-azure-support-matrix.md) voor alle onderdelen.
- U hebt de `Az`  module Azure PowerShell. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [hand leiding voor het installeren en configureren van Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met behulp van de cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Selecteer het Azure-abonnement waarnaar u uw virtuele VMware-machines wilt repliceren. Gebruik de cmdlet Get-AzSubscription om de lijst met Azure-abonnementen weer te geven waartoe u toegang hebt. Selecteer het Azure-abonnement waarmee u wilt werken met de cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Een Recovery Services-kluis instellen

1. Maak een resource groep waarin u de Recovery Services kluis wilt maken. In het onderstaande voor beeld heeft de resource groep de naam VMwareDRtoAzurePS en wordt deze gemaakt in de regio Azië-oost.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Maak een Recovery Services-kluis. In het onderstaande voor beeld heeft de Recovery Services-kluis de naam VMwareDRToAzurePs en wordt deze gemaakt in de Azië-oost regio en in de resource groep die u in de vorige stap hebt gemaakt.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Down load de kluis registratie sleutel voor de kluis. De kluis registratie sleutel wordt gebruikt om de on-premises configuratie server te registreren bij deze kluis. Registratie maakt deel uit van het installatie proces van de configuratie server software.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Gebruik de gedownloade kluis registratie sleutel en volg de stappen in de onderstaande artikelen om de installatie en registratie van de configuratie server te volt ooien.
   - [Uw beveiligings doelen kiezen](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [De bronomgeving instellen](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>De kluis context instellen

Stel de kluis context in met behulp van de cmdlet Set-ASRVaultContext. Eenmaal ingesteld, worden volgende Azure Site Recovery bewerkingen in de Power shell-sessie uitgevoerd in de context van de geselecteerde kluis.

> [!TIP]
> De Azure Site Recovery Power shell-module (AZ. Recovery Services module) wordt geleverd met eenvoudig te gebruiken aliassen voor de meeste cmdlets. De cmdlets in de module hebben de vorm *\<Operation> - **AzRecoveryServicesAsr** \<Object>* en hebben gelijkwaardige aliassen die het formulier *\<Operation> - **ASR** \<Object>* gebruiken. U kunt de cmdlet-aliassen voor gebruiks gemak vervangen.

In het onderstaande voor beeld wordt de kluis Details van de variabele $vault gebruikt om de kluis context voor de Power shell-sessie op te geven.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Als alternatief voor de Set-ASRVaultContext cmdlet kan een ook de Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet gebruiken om de kluis context in te stellen. Geef het pad op waar het bestand met de kluis registratie sleutel zich bevindt als de para meter-Path voor de cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Bijvoorbeeld:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
In de volgende secties van dit artikel wordt ervan uitgegaan dat de kluis context voor Azure Site Recovery bewerkingen is ingesteld.

## <a name="validate-vault-registration"></a>Kluis registratie valideren

Voor dit voor beeld hebben we het volgende:

- Er is een configuratie server (**ConfigurationServer**) geregistreerd bij deze kluis.
- Een extra proces server (**uitschalen-ProcessServer**) is geregistreerd bij *ConfigurationServer*
- Accounts (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) zijn ingesteld op de configuratie server. Deze accounts worden gebruikt om de vCenter-Server toe te voegen, om virtuele machines te detecteren en de Mobility service-software te installeren op Windows-en Linux-servers die moeten worden gerepliceerd.

1. Geregistreerde configuratie servers worden vertegenwoordigd door een Fabric-object in Site Recovery. De lijst met Fabric-objecten in de kluis ophalen en de configuratie Server identificeren.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identificeer de proces servers die kunnen worden gebruikt om machines te repliceren.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Uit de bovenstaande uitvoer ***$ProcessServers [0]** _ correspondeert met _uitschalen-ProcessServer * en ***$ProcessServers [1]**_ overeenkomt met de rol van proces server op _ConfigurationServer *

3. Identificeer accounts die op de configuratie server zijn ingesteld.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Uit de bovenstaande uitvoer ***$AccountHandles [0]** _ correspondeert met het account _vCenter_account *, ***$AccountHandles [1]**_ naar account _WindowsAccount *, en ***$AccountHandles [2]**_ naar account _LinuxAccount *

## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

In deze stap worden er twee replicatie beleidsregels gemaakt. Eén beleid voor het repliceren van virtuele VMware-machines naar Azure en de andere voor het repliceren van virtuele machines die in Azure worden uitgevoerd, terug naar de on-premises VMware-site.

> [!NOTE]
> De meeste Azure Site Recovery bewerkingen worden asynchroon uitgevoerd. Wanneer u een bewerking initieert, wordt een Azure Site Recovery taak verzonden en wordt een taak tracking object geretourneerd. Dit object voor taak tracking kan worden gebruikt om de status van de bewerking te bewaken.

1. Maak een replicatie beleid met de naam *ReplicationPolicy* om virtuele VMware-machines te repliceren naar Azure met de opgegeven eigenschappen.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Een replicatie beleid maken dat moet worden gebruikt voor failback van Azure naar de on-premises VMware-site.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Gebruik de taak Details in *$Job _FailbackPolicyCreate* om te controleren of de bewerking is voltooid.

   * Een toewijzing van een beveiligings container maken om het replicatie beleid toe te wijzen aan de configuratie server.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Een vCenter-Server toevoegen en Vm's detecteren

Voeg een vCenter Server toe op IP-adres of hostnaam. De para meter **-Port** specificeert de poort op de vCenter-Server waarmee verbinding moet worden gemaakt. in de para meter-name wordt een beschrijvende naam voor de vCenter **-** server opgegeven en de para meter  **-account** geeft de account ingang op de configuratie server op die moet worden gebruikt om virtuele machines te detecteren die worden beheerd door de vCenter-Server.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Opslag accounts voor replicatie maken

**Als u naar een beheerde schijf wilt schrijven, gebruikt u [Power shell AZ. Recovery Services module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) en hoger.** U hoeft alleen maar een logboek opslag account te maken. Het is raadzaam om een standaard account type en LRS-redundantie te gebruiken omdat het wordt gebruikt om alleen tijdelijke logboeken op te slaan. Zorg ervoor dat het opslag account is gemaakt in dezelfde Azure-regio als de kluis.

Als u een versie van AZ. Recovery Services-module gebruikt die ouder is dan 2.0.0, gebruikt u de volgende stappen om opslag accounts te maken. Deze opslag accounts worden later gebruikt voor het repliceren van virtuele machines. Zorg ervoor dat de opslag accounts worden gemaakt in dezelfde Azure-regio als de kluis. U kunt deze stap overs Laan als u van plan bent om een bestaand opslag account te gebruiken voor replicatie.

> [!NOTE]
> Bij het repliceren van on-premises virtuele machines naar een Premium Storage-account, moet u een extra Standard-opslag account (logboek opslag account) opgeven. Het account voor logboek opslag bevat replicatie Logboeken als een tussenliggende opslag, totdat de logboeken kunnen worden toegepast op het Premium-opslag doel.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Virtuele VMware-machines repliceren

Het duurt ongeveer 15-20 minuten om virtuele machines te detecteren van de vCenter-Server. Zodra de detectie is uitgevoerd, wordt er een beveiligbaar item object gemaakt in Azure Site Recovery voor elke gedetecteerde virtuele machine. In deze stap worden drie van de gedetecteerde virtuele machines gerepliceerd naar de Azure Storage accounts die u in de vorige stap hebt gemaakt.

U hebt de volgende gegevens nodig om een gedetecteerde virtuele machine te beveiligen:

* Het Beveilig bare item dat moet worden gerepliceerd.
* Het opslag account waarnaar de virtuele machine moet worden gerepliceerd (alleen als u naar het opslag account repliceert). 
* Er is een logboek opslag vereist om virtuele machines te beveiligen met een Premium Storage-account of een beheerde schijf.
* De proces server die moet worden gebruikt voor replicatie. De lijst met beschik bare proces servers is opgehaald en opgeslagen in de variabelen ***$ProcessServers [0]** _ _(uitschalen-ProcessServer) * en ***$ProcessServers [1]**_ _ (ConfigurationServer) *.
* Het account dat moet worden gebruikt voor de push-installatie van de Mobility service-software op de computers. De lijst met beschik bare accounts is opgehaald en opgeslagen in de variabele ***$AccountHandles*** .
* De toewijzing van de beveiligings container voor het replicatie beleid dat moet worden gebruikt voor replicatie.
* De resource groep waarin de virtuele machines moeten worden gemaakt voor failover.
* Optioneel, het virtuele netwerk van Azure en het subnet waarmee de virtuele machine waarvoor een failover is uitgevoerd, moet worden verbonden.

Repliceer nu de volgende virtuele machines met de instellingen die zijn opgegeven in deze tabel


|Virtuele machine  |Proces server        |Opslagaccount              |Opslag account voor logboek  |Beleid           |Account voor de installatie van de Mobility-service|Doelresourcegroep  | Virtueel doelnetwerk  |Doel-subnet  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N.v.t.| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N.v.t.                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Zodra de taak replicatie inschakelen is voltooid, wordt de initiële replicatie voor de virtuele machines gestart. De initiële replicatie kan enige tijd duren, afhankelijk van de hoeveelheid gegevens die moet worden gerepliceerd en de band breedte die beschikbaar is voor replicatie. Nadat de initiële replicatie is voltooid, wordt de virtuele machine verplaatst naar een beveiligde status. Zodra de virtuele machine een beveiligde status heeft bereikt, kunt u een testfailover voor de virtuele machine uitvoeren, deze toevoegen aan herstel plannen, enzovoort.

U kunt de replicatie status en replicatie status van de virtuele machine controleren met de cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Failover-instellingen configureren

Failover-instellingen voor beveiligde computers kunnen worden bijgewerkt met behulp van de cmdlet Set-ASRReplicationProtectedItem. Enkele van de instellingen die kunnen worden bijgewerkt met deze cmdlet zijn:
* De naam van de virtuele machine die moet worden gemaakt voor de failover
* VM-grootte van de virtuele machine die moet worden gemaakt op failover
* Virtueel Azure-netwerk en subnet waarmee de Nic's van de virtuele machine moeten worden verbonden bij failover
* Failover naar Managed disks
* Azure Hybrid Use Benefit Toep assen
* Wijs een statisch IP-adres uit het virtuele doel netwerk toe dat aan de virtuele machine moet worden toegewezen bij een failover.

In dit voor beeld updaten we de VM-grootte van de virtuele machine die moet worden gemaakt voor de virtuele machine *Win2K12VM1* en geeft u op dat de virtuele machine beheerde schijven gebruikt bij failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Voer een DR-analyse (testfailover) als volgt uit:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Zodra de testfailover is voltooid, ziet u dat er een virtuele machine met de naam *'-test '* (in dit geval Win2K12VM1-test) is gemaakt in Azure.
3. U kunt nu verbinding maken met de virtuele machine waarvoor de test is mislukt en de testfailover valideren.
4. Ruim de testfailover op met behulp van de cmdlet Start-ASRTestFailoverCleanupJob. Met deze bewerking wordt de virtuele machine verwijderd die is gemaakt als onderdeel van de testfailover.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Failover naar Azure

In deze stap hebben we failover van de virtuele machine Win2K12VM1 naar een specifiek herstel punt.

1. Een lijst met beschik bare herstel punten ophalen die voor de failover moeten worden gebruikt:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Als failover is uitgevoerd, kunt u de failoverbewerking door voeren en omgekeerde replicatie van Azure naar de on-premises VMware-site instellen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het automatiseren van meer taken met behulp van de [Azure site Recovery Power shell-referentie](/powershell/module/Az.RecoveryServices).

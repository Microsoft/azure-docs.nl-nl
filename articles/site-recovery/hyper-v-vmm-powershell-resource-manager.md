---
title: Herstel na nood gevallen voor Hyper-V (met VMM) instellen op een secundaire site met Azure Site Recovery/Power shell
description: Hierin wordt beschreven hoe u herstel na nood gevallen instelt voor virtuele Hyper-V-machines in VMM-Clouds naar een secundaire VMM-site met behulp van Azure Site Recovery en Power shell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086214"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Herstel na nood geval instellen voor virtuele Hyper-V-machines naar een secundaire site met behulp van Power shell (Resource Manager)

In dit artikel wordt beschreven hoe u de stappen voor replicatie van virtuele Hyper-V-machines in System Center Virtual Machine Manager Clouds kunt automatiseren met een Virtual Machine Manager-Cloud in een secundaire on-premises site met behulp van [Azure site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- De [architectuur en onderdelen voor dit scenario](hyper-v-vmm-architecture.md) doornemen.
- Raadpleeg de [ondersteuningsvereisten](./vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat Virtual Machine Manager-servers en Hyper-V-hosts voldoen aan de [ondersteunings vereisten](./vmware-physical-secondary-support-matrix.md).
- Controleer of de virtuele machines die u wilt repliceren, voldoen aan de ondersteuning van de [gerepliceerde machine](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>Voorbereiden op netwerktoewijzing

[Netwerk toewijzing](hyper-v-vmm-network-mapping.md) wordt toegewezen tussen on-premises VM-netwerken van Virtual Machine Manager in bron-en doel-Clouds. Bij toewijzing gebeurt het volgende:

- Virtuele machines worden verbonden met de juiste VM-doelnetwerken na een failover.
- Gerepliceerde VM's worden optimaal op Hyper-V-doelhostservers geplaatst.
- Als u geen netwerk toewijzing configureert, worden replica-Vm's na een failover niet verbonden met een VM-netwerk.

Bereid Virtual Machine Manager als volgt voor:

- Zorg ervoor dat u [Virtual Machine Manager logische netwerken](/system-center/vmm/network-logical) op de bron-en doel-Virtual Machine Manager-servers hebt:
  - Het logische netwerk op de bronserver moet worden gekoppeld aan de broncloud waarin de Hyper-V-hosts zich bevinden.
  - Het logische netwerk op de doelserver moet worden gekoppeld aan de doelcloud.
- Zorg ervoor dat u [VM-netwerken](/system-center/vmm/network-virtual) hebt op de bron-en doel-Virtual Machine Manager-servers. VM-netwerken moeten zijn gekoppeld aan het logische netwerk op elke locatie.
- Verbind VM's op de Hyper-V-bronhosts met het VM-bronnetwerk.

## <a name="prepare-for-powershell"></a>Voorbereiden voor Power shell

Zorg ervoor dat u beschikt over Azure PowerShell Ready to go:

- Als u Power shell al gebruikt, moet u een upgrade uitvoeren naar versie 0.8.10 of hoger. Meer [informatie](/powershell/azure/) over het instellen van Power shell.
- Nadat u Power shell hebt ingesteld en geconfigureerd, controleert u de [service-cmdlets](/powershell/azure/).
- Lees de aan de [slag](/powershell/azure/get-started-azureps) -hand leiding voor meer informatie over het gebruik van parameter waarden, invoer en uitvoer in Power shell.

## <a name="set-up-a-subscription"></a>Een abonnement instellen

1. Meld u aan bij uw Azure-account vanuit Power shell.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Haal een lijst met uw abonnementen op met de abonnements-Id's. Noteer de ID van het abonnement waarin u de Recovery Services kluis wilt maken.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Stel het abonnement voor de kluis in.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Maak een Azure Resource Manager resource groep als u er nog geen hebt.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Maak een nieuwe Recovery Services kluis. Sla het kluis object op in een variabele die u later wilt gebruiken.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   U kunt het kluis object ophalen nadat u het hebt gemaakt met behulp van de- `Get-AzRecoveryServicesVault` cmdlet.

## <a name="set-the-vault-context"></a>De kluis context instellen

1. Een bestaande kluis ophalen.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Stel de kluis context in.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>De Site Recovery provider installeren

1. Maak een map op de Virtual Machine Manager-computer door de volgende opdracht uit te voeren:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Pak de bestanden uit met behulp van het gedownloade provider installatie bestand.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Installeer de provider en wacht totdat de installatie is voltooid.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registreer de server in de kluis.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Een replicatie beleid maken en koppelen

1. Maak een replicatie beleid, in dit geval voor Hyper-V 2012 R2, als volgt:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > De Virtual Machine Manager-Cloud kan Hyper-V-hosts bevatten waarop verschillende versies van Windows Server worden uitgevoerd, maar het replicatie beleid is voor een specifieke versie van een besturings systeem. Als u verschillende hosts hebt die worden uitgevoerd op verschillende besturings systemen, moet u voor elk systeem een afzonderlijk replicatie beleid maken. Als u bijvoorbeeld vijf hosts hebt die worden uitgevoerd op Windows Server 2012 en drie hosts waarop Windows Server 2012 R2 wordt uitgevoerd, maakt u twee replicatie beleidsregels. U maakt een voor elk type besturings systeem.

1. Haal de primaire beveiligings container (primaire Virtual Machine Manager-Cloud) en de Recovery Protection-container (Virtual Machine Manager-Cloud) op.

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Haal het replicatie beleid op dat u hebt gemaakt met behulp van de beschrijvende naam.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Start de koppeling van de beveiligings container (Virtual Machine Manager-Cloud) met het replicatie beleid.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Wacht totdat de taak voor de beleids koppeling is voltooid. Als u wilt controleren of de taak is voltooid, gebruikt u het volgende Power shell-fragment:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Nadat de verwerking is voltooid, voert u de volgende opdracht uit:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Volg de stappen in [activiteit controleren](#monitor-activity)om de voltooiing van de bewerking te controleren.

##  <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. Gebruik deze opdracht om servers voor de huidige kluis op te halen. Met de opdracht worden de Site Recovery servers in de `$Servers` matrix variabele opgeslagen.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Voer deze opdracht uit om de netwerken op te halen voor de virtuele bron machine Manager-server en de doel-Virtual Machine Manager-server.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > De bron-Virtual Machine Manager-server kan het eerste of het tweede in de server matrix zijn. Controleer de namen van de Virtual Machine Manager-servers en haal de netwerken op de juiste wijze op.

1. Met deze cmdlet wordt een toewijzing gemaakt tussen het primaire netwerk en het herstel netwerk. Hiermee wordt het primaire netwerk opgegeven als het eerste element van `$PrimaryNetworks` . Hiermee wordt het herstel netwerk opgegeven als het eerste element van `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Beveiliging voor Vm's inschakelen

Nadat de servers, Clouds en netwerken op de juiste wijze zijn geconfigureerd, schakelt u de beveiliging voor virtuele machines in de cloud in.

1. Als u beveiliging wilt inschakelen, voert u de volgende opdracht uit om de beveiligings container op te halen:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. U kunt als volgt de beveiligings entiteit (VM) ophalen:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Schakel replicatie in voor de virtuele machine.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Als u wilt repliceren naar CMK ingeschakelde beheerde schijven in azure, voert u de volgende stappen uit met AZ Power Shell 3.3.0:
>
> 1. Failover naar beheerde schijven inschakelen door de VM-eigenschappen bij te werken
> 1. De `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet gebruiken om de schijf-id op te halen voor elke schijf van het beveiligde item
> 1. Maak een Dictionary-object met behulp `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` van de cmdlet om de toewijzing van de schijf-id aan de schijf versleuteling in te stellen. Deze schijf versleutelings sets moeten vooraf door u worden gemaakt in de doel regio.
> 1. Werk de VM-eigenschappen bij met behulp `Set-AzRecoveryServicesAsrReplicationProtectedItem` van de cmdlet door het object Dictionary in de para meter **DiskIdToDiskEncryptionSetMap** door te geven.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

Als u uw implementatie wilt testen, voert u een testfailover uit voor één virtuele machine. U kunt ook een herstel plan maken dat meerdere Vm's bevat en een testfailover voor het plan uitvoeren. Met een testfailover wordt uw failover- en herstelmechanisme in een geïsoleerd netwerk gesimuleerd.

1. Haal de virtuele machine op waarin failover wordt uitgevoerd voor Vm's.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Voer een testfailover uit.

   Voor één VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Voor een herstel plan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Volg de stappen in [activiteit controleren](#monitor-activity)om de voltooiing van de bewerking te controleren.

## <a name="run-planned-and-unplanned-failovers"></a>Geplande en niet-geplande failovers uitvoeren

1. Een geplande failover uitvoeren.

   Voor één VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Voor een herstel plan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Een niet-geplande failover uitvoeren.

   Voor één VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Voor een herstel plan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Activiteit bewaken

Gebruik de volgende opdrachten om failover-activiteiten te bewaken. Wacht totdat de verwerking tussen taken is voltooid.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](/powershell/module/az.recoveryservices) over site Recovery met Resource Manager Power shell-cmdlets.

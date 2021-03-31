---
title: Herstel na nood geval voor Hyper-V-VM'S met Azure Site Recovery en Power shell
description: Automatiseer herstel na nood gevallen van virtuele Hyper-V-machines naar Azure met de Azure Site Recovery-service met behulp van Power shell en Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132517"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Herstel na noodgeval instellen naar Azure voor Hyper-V-VM's met behulp van PowerShell en Azure Resource Manager

[Azure site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) door de replicatie, failover en het herstel van virtuele machines van Azure (vm's) en on-premises vm's en fysieke servers te organiseren.

In dit artikel wordt beschreven hoe u Windows Power shell gebruikt, samen met Azure Resource Manager, om virtuele Hyper-V-machines te repliceren naar Azure. In het voor beeld in dit artikel wordt beschreven hoe u één VM die op een Hyper-V-host wordt uitgevoerd, naar Azure repliceert.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell biedt cmdlets voor het beheren van Azure met behulp van Windows Power shell. Site Recovery Power shell-cmdlets, die beschikbaar zijn in Azure PowerShell voor Azure Resource Manager, helpen u bij het beveiligen en herstellen van uw servers in Azure.

U hoeft geen Power shell-expert te zijn om dit artikel te kunnen gebruiken, maar u moet wel inzicht krijgen in basis concepten, zoals modules, cmdlets en sessies. Zie de [Power shell-documentatie](/powershell) en [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)voor meer informatie.

> [!NOTE]
> Micro soft-partners in het programma Cloud Solution Provider (CSP) kunnen de beveiliging van klanten servers configureren en beheren voor hun respectieve CSP-abonnementen (Tenant abonnementen).

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat deze vereisten aanwezig zijn in de volgende plaats:

- Een [Microsoft Azure](https://azure.microsoft.com/) -account. U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). Daarnaast kunt u meer lezen over de [prijzen van Azure site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Zie [install Azure PowerShell](/powershell/azure/install-az-ps)voor meer informatie over deze versie en hoe u deze installeert.

Daarnaast bevat het specifieke voor beeld dat in dit artikel wordt beschreven, de volgende vereisten:

- Een Hyper-V-host met Windows Server 2012 R2 of Microsoft Hyper-V Server 2012 R2 met een of meer virtuele machines. Hyper-V-servers moeten zijn verbonden met internet, hetzij rechtstreeks, hetzij via een proxy.
- De Vm's die u wilt repliceren, moeten voldoen aan [deze vereisten](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Stap 1: Meld u aan bij uw Azure-account

1. Open een Power shell-console en voer deze opdracht uit om u aan te melden bij uw Azure-account. Met de cmdlet wordt u gevraagd om uw account referenties op te vragen: `Connect-AzAccount` .
   - U kunt ook uw account referenties als een para meter in de cmdlet toevoegen `Connect-AzAccount` met behulp van de para meter **Credential** .
   - Als u een CSP-partner bent die namens een Tenant werkt, geeft u de klant op als een Tenant met behulp van de naam van een tenantID of Tenant primaire domein. Bijvoorbeeld: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account meerdere abonnementen kan hebben:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Controleer of uw abonnement is geregistreerd voor het gebruik van de Azure-providers voor Recovery Services en Site Recovery met behulp van de volgende opdrachten:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Controleer of in de uitvoer van de opdracht de **RegistrationState** is ingesteld op **geregistreerd**, u kunt door gaan met stap 2. Als dat niet het geval is, moet u de ontbrekende provider in uw abonnement registreren door de volgende opdrachten uit te voeren:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Controleer of de providers zijn geregistreerd met behulp van de volgende opdrachten:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Stap 2: de kluis instellen

1. Maak een Azure Resource Manager resource groep waarin u de kluis wilt maken of gebruik een bestaande resource groep. Maak als volgt een nieuwe resource groep. De `$ResourceGroupName` variabele bevat de naam van de resource groep die u wilt maken en de $geo variabele bevat de Azure-regio waarin de resource groep moet worden gemaakt (bijvoorbeeld ' Brazilië-Zuid ').

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Voer de cmdlet uit om een lijst met resource groepen in uw abonnement te verkrijgen `Get-AzResourceGroup` .
1. Maak als volgt een nieuwe Azure Recovery Services-kluis:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

U kunt een lijst met bestaande kluizen ophalen met de `Get-AzRecoveryServicesVault` cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Stap 3: de Recovery Services kluis context instellen

Stel de context van de kluis als volgt in:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Stap 4: een Hyper-V-site maken

1. Maak als volgt een nieuwe Hyper-V-site:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Met deze cmdlet wordt een Site Recovery taak gestart om de site te maken en wordt een Site Recovery taak object geretourneerd. Wacht tot de taak is voltooid en controleer of de taak is voltooid.
1. Gebruik de `Get-AzRecoveryServicesAsrJob` cmdlet om het taak object op te halen en de huidige status van de taak te controleren.
1. Genereer en down load een registratie sleutel voor de site, als volgt:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Kopieer de gedownloade sleutel naar de Hyper-V-host. U hebt de sleutel nodig om de Hyper-V-host te registreren bij de-site.

## <a name="step-5-install-the-provider-and-agent"></a>Stap 5: de provider en de agent installeren

1. Down load het installatie programma voor de nieuwste versie van de provider van [micro soft](https://aka.ms/downloaddra).
1. Voer het installatie programma uit op de Hyper-V-host.
1. Aan het einde van de installatie gaat u verder met de registratie stap.
1. Wanneer u hierom wordt gevraagd, geeft u de gedownloade sleutel op en voltooit u de registratie van de Hyper-V-host.
1. Controleer als volgt of de Hyper-V-host is geregistreerd bij de site:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Als u een Hyper-V-kernserver gebruikt, downloadt u het installatiebestand en voert u de volgende stappen uit:

1. Pak de bestanden uit _AzureSiteRecoveryProvider.exe_ naar een lokale map door deze opdracht uit te voeren:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Voer de volgende opdracht uit:

   ```console
   .\setupdr.exe /i
   ```

   Resultaten worden vastgelegd in _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registreer de server door de volgende opdracht uit te voeren:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Stap 6: een replicatie beleid maken

Voordat u begint, moet het opgegeven opslag account zich in dezelfde Azure-regio bevinden als de kluis en moet geo-replicatie zijn ingeschakeld.

1. Maak als volgt een replicatie beleid:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Controleer de geretourneerde taak om te controleren of het maken van het replicatie beleid slaagt.

1. Haal als volgt de beveiligings container op die overeenkomt met de site:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Koppel de beveiligings container als volgt aan het replicatie beleid:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Wacht totdat de koppelings taak is voltooid.

1. Haal de toewijzing van de beveiligings container op.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Stap 7: VM-beveiliging inschakelen

1. Haal als volgt het Beveilig bare item op dat overeenkomt met de virtuele machine die u wilt beveiligen:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Beveilig de virtuele machine. Als er meer dan één schijf aan de virtuele machine is gekoppeld, geeft u de besturingssysteem schijf op met behulp van de para meter **OSDiskName** .

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Wacht totdat de virtuele machines een beveiligde status hebben bereikt na de initiële replicatie. Dit kan enige tijd duren, afhankelijk van factoren zoals de hoeveelheid gegevens die moeten worden gerepliceerd en de beschik bare upstream-band breedte naar Azure. Wanneer een beveiligde status is ingesteld, worden de taak status en StateDescription als volgt bijgewerkt:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Herstel eigenschappen van de virtuele machine (zoals de grootte van de VM-rol) en het Azure-netwerk waarmee de VM-NIC na een failover moet worden gekoppeld.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Als u wilt repliceren naar CMK ingeschakelde beheerde schijven in azure, voert u de volgende stappen uit met AZ Power Shell 3.3.0:
>
> 1. Failover naar beheerde schijven inschakelen door de VM-eigenschappen bij te werken
> 1. De `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet gebruiken om de schijf-id op te halen voor elke schijf van het beveiligde item
> 1. Maak een Dictionary-object met behulp `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` van de cmdlet om de toewijzing van de schijf-id aan de schijf versleuteling in te stellen. Deze schijf versleutelings sets moeten vooraf door u worden gemaakt in de doel regio.
> 1. Werk de VM-eigenschappen bij met behulp `Set-AzRecoveryServicesAsrReplicationProtectedItem` van de cmdlet door het object Dictionary in de para meter **DiskIdToDiskEncryptionSetMap** door te geven.

## <a name="step-8-run-a-test-failover"></a>Stap 8: een testfailover uitvoeren

1. Voer een testfailover als volgt uit:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Controleer of de test-VM is gemaakt in Azure. De taak testfailover is onderbroken na het maken van de test-VM in Azure.
1. Voer de volgende handelingen uit om de testfailover op te schonen en te volt ooien:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](/powershell/module/az.recoveryservices) over Azure Site Recovery met Azure Resource Manager Power shell-cmdlets.

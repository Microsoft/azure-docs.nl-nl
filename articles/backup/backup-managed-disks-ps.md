---
title: Back-ups maken van Azure Managed Disks met behulp van Azure PowerShell
description: Meer informatie over het maken van een back-up van Azure Managed Disks met behulp van Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630399"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Back-ups maken van Azure Managed Disks met behulp van Azure PowerShell

In dit artikel wordt uitgelegd hoe u een back-up van [Azure Managed Disk](../virtual-machines/managed-disks-overview.md) maakt met behulp van Azure PowerShell.

In dit artikel leert u het volgende:

- Een back-upkluis maken

- Maak een back-upbeleid

- Een back-up van een Azure-schijf configureren

- Een back-uptaak op aanvraag uitvoeren

Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie over de beschik baarheid van Azure schijf back-upregio, ondersteunde scenario's en beperkingen.

## <a name="create-a-backup-vault"></a>Een back-upkluis maken

Een back-upkluis is een opslag entiteit in azure die back-upgegevens bevat voor verschillende nieuwere werk belastingen die Azure Backup ondersteunt, zoals Azure Database for PostgreSQL servers en Azure-schijven. Back-upkluizen maken het eenvoudig om uw back-upgegevens te organiseren en zo de beheer overhead te minimaliseren. Back-upkluizen zijn gebaseerd op het Azure Resource Manager model van Azure, dat uitgebreide mogelijkheden biedt voor het beveiligen van back-upgegevens.

Voordat u een back-upkluis maakt, kiest u de opslag redundantie van de gegevens in de kluis. Ga vervolgens door met het maken van de back-upkluis met de opslag redundantie en de locatie. In dit artikel maken we een back-upkluis ' TestBkpVault ' in de regio ' westus ' onder de resource groep ' testBkpVaultRG '. Gebruik de opdracht [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) voor het maken van een back-upkluis. Meer informatie over het [maken van een back-upkluis](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Nadat u een kluis hebt gemaakt, maakt u een back-upbeleid voor het beveiligen van Azure-schijven.

## <a name="create-a-backup-policy"></a>Een back-upbeleid maken

Als u inzicht wilt krijgen in de interne onderdelen van een back-upbeleid voor Azure Disk Backup, haalt u de beleids sjabloon op met behulp van de opdracht [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Met deze opdracht wordt een standaard beleids sjabloon voor een opgegeven gegevens bron type geretourneerd. Gebruik deze beleids sjabloon om een nieuw beleid te maken.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

De beleids sjabloon bestaat uit een trigger (waarmee wordt bepaald wat de back-up activeert) en een levens cyclus (die bepaalt wanneer de back-up moet worden verwijderd/gekopieerd/verplaatst). In azure schijf back-up is de standaard waarde voor trigger een geplande trigger voor elke 4 uur (PT4H) en om elke back-up gedurende 7 dagen te bewaren.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure Disk Backup biedt meerdere back-ups per dag. Als u vaker back-ups nodig hebt, kiest u de frequentie van de back-up **per uur** met de mogelijkheid om back-ups te maken met intervallen van elke 4, 6, 8 of 12 uur. De back-ups worden gepland op basis van het geselecteerde **tijds** interval. Als u bijvoorbeeld **elke vier uur** selecteert, worden de back-ups op ongeveer de interval van elke 4 uur genomen, zodat de back-ups gelijkmatig over de hele dag worden gedistribueerd. Als een back-up van één dag voldoende is, kiest u de frequentie van **dagelijkse** back-ups. In de dagelijkse back-upfrequentie kunt u het tijdstip van de dag opgeven wanneer uw back-ups worden gemaakt. Het is belang rijk te weten dat de tijd van de dag de begin tijd van de back-up aangeeft en niet de tijd waarop de back-up is voltooid. De tijd die nodig is voor het volt ooien van de back-upbewerking is afhankelijk van verschillende factoren, zoals de grootte van de schijf en het verloop frequentie tussen opeenvolgende back-ups. Azure Disk Backup is echter een back-up zonder agent die gebruikmaakt van [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md), wat geen invloed heeft op de prestaties van de productie-toepassing.

   >[!NOTE]
   > Hoewel de geselecteerde kluis de instelling voor globale redundantie kan hebben, ondersteunt momenteel alleen de gegevens opslag van moment opnamen van Azure-schijven. Alle back-ups worden opgeslagen in een resource groep in uw abonnement en worden niet gekopieerd naar backup-kluis opslag.

Raadpleeg het document over het [back-upbeleid van Azure](backup-managed-disks.md#create-backup-policy) voor meer informatie over het maken van een beleid.

Als u de frequentie van het uurtarief of de Bewaar periode wilt wijzigen, gebruikt u de opdrachten [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) en/of [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . Zodra het beleids object alle gewenste waarden heeft, kunt u door gaan met het maken van een nieuw beleid van het object Policy met behulp van de [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Back-up configureren

Zodra de kluis en het beleid zijn gemaakt, zijn er drie kritieke punten die de gebruiker moet overwegen om een Azure-schijf te beveiligen.

### <a name="key-entities-involved"></a>Betrokken belangrijkste entiteiten

#### <a name="disk-to-be-protected"></a>Te beveiligen schijf

Haal de ARM-ID op van de schijf die moet worden beveiligd. Dit zal fungeren als de id van de schijf. We gebruiken een voor beeld van een schijf met de naam ' PSTestDisk ' onder een resource groep ' diskrg ' onder een ander abonnement.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Resource groep voor moment opnamen

De moment opnamen van de schijf worden opgeslagen in een resource groep in uw abonnement. Als richt lijn is het raadzaam om een speciale resource groep te maken als een gegevens Archief voor moment opnamen dat door de Azure Backup-service moet worden gebruikt. Als u een speciale resource groep hebt, kunt u toegangs machtigingen voor de resource groep beperken, waardoor de back-upgegevens veilig en eenvoudig kunnen worden beheerd. Noteer de ARM-ID voor de resource groep waar u de moment opnamen van de schijf wilt plaatsen

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Back-upkluis

Voor de back-upkluizen zijn machtigingen vereist op de schijf en de resource groep voor moment opnamen om moment opnamen te kunnen activeren en hun levens cyclus te beheren. De door het systeem toegewezen beheerde identiteit van de kluis wordt gebruikt voor het toewijzen van dergelijke machtigingen. Gebruik de opdracht [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) om door het systeem toegewezen beheerde identiteit voor de Recovery Services-kluis in te scha kelen.

### <a name="assign-permissions"></a>Machtigingen toewijzen

De gebruiker moet enkele machtigingen toewijzen via RBAC in de kluis (vertegenwoordigd door een kluis-MSI) en de relevante schijf en/of de schijf RG. Deze kunnen worden uitgevoerd via de portal of Power shell. Alle gerelateerde machtigingen worden beschreven in de punten 1, 2, 3 in [deze sectie](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>De aanvraag voorbereiden

Zodra alle relevante machtigingen zijn ingesteld, wordt de configuratie van de back-up uitgevoerd in twee stappen. Eerst bereiden we de relevante aanvraag voor met behulp van de relevante kluis, het beleid, de schijf en de moment opname van de resource groep met de opdracht [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Vervolgens verzenden we de aanvraag om de schijf te beveiligen met de opdracht [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Haal het relevante back-upexemplaar op waarop de gebruiker een back-up wil activeren met behulp van de [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

U kunt een Bewaar regel opgeven tijdens het activeren van de back-up. Als u de Bewaar regels in het beleid wilt bekijken, gaat u door het beleids object voor Bewaar regels. In het onderstaande voor beeld wordt de regel met de naam default weer gegeven. deze regel wordt gebruikt voor de back-up op aanvraag

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Activeer een back-up op aanvraag met behulp van de [back-up-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) opdracht.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Taken bijhouden

Volg alle taken met behulp van de opdracht [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . U kunt alle taken weer geven en een bepaald taak detail ophalen.

U kunt ook AZ. ResourceGraph gebruiken om alle taken in alle back-upkluizen bij te houden. Gebruik de opdracht [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) om de relevante taak op te halen die in elke back-upkluis kan worden uitgevoerd.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Managed Disks herstellen met behulp van Azure PowerShell](restore-managed-disks-ps.md)

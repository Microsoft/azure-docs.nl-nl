---
title: Azure-Managed Disks herstellen via Azure PowerShell
description: Meer informatie over het herstellen van Azure-Managed Disks met Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520034"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure-Managed Disks herstellen met Azure PowerShell

In dit artikel wordt uitgelegd hoe u [Azure-Managed Disks](../virtual-machines/managed-disks-overview.md) herstellen vanaf een herstelpunt dat is gemaakt door Azure Backup.

Momenteel wordt de optie Original-Location Recovery (OLR) voor herstel door de bestaande bronschijf te vervangen van waar de back-ups zijn gemaakt, niet ondersteund. U kunt vanaf een herstelpunt herstellen om een nieuwe schijf te maken in dezelfde resourcegroep als die van de bronschijf van waar de back-ups zijn gemaakt of in een andere resourcegroep. Dit staat bekend als Alternate-Location Recovery (ALR) en hiermee kunt u zowel de bronschijf als de herstelde (nieuwe) schijf behouden.

In dit artikel leert u het volgende:

- Herstellen om een nieuwe schijf te maken

- De status van de herstelbewerking bijhouden

In de voorbeelden verwijzen we naar een bestaande back-upkluis 'TestBkpVault' onder de resourcegroep testBkpVaultRG

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Herstellen om een nieuwe schijf te maken

### <a name="setting-up-permissions"></a>Rechten instellen

Backup Vault maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Als u wilt herstellen vanuit een back-up, heeft de beheerde identiteit van de Backup-kluis een set machtigingen nodig voor de resourcegroep waarin de schijf moet worden hersteld.

Back-upkluis maakt gebruik van een door het systeem toegewezen beheerde identiteit, die is beperkt tot één per resource en is gekoppeld aan de levenscyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type die alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten.](../active-directory/managed-identities-azure-resources/overview.md)

Wijs de relevante machtigingen toe voor de door het systeem toegewezen beheerde identiteit van de kluis in de doelresourcegroep waar de schijven worden hersteld/gemaakt, zoals hier [wordt vermeld.](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetching-the-relevant-recovery-point"></a>Het relevante herstelpunt ophalen

Haal alle exemplaren op met behulp van de [opdracht Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) en identificeer het relevante exemplaar.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

U kunt ook **Az.Resourcegraph** en de opdracht [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) gebruiken om te zoeken in exemplaren in veel kluizen en abonnementen.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Zodra het exemplaar is geïdentificeerd, haalt u het relevante herstelpunt op.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>De herstelaanvraag voorbereiden

Bouw de ARM-id van de nieuwe schijf die moet worden gemaakt met [](#setting-up-permissions)de doelresourcegroep waaraan machtigingen zijn toegewezen zoals hierboven is beschreven, en de vereiste schijfnaam. Een schijf kan bijvoorbeeld de naam **PSTestDisk2** hebben onder een resourcegroep **targetrg** met een ander abonnement.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Gebruik de [opdracht Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) om de herstelaanvraag voor te bereiden met alle relevante details.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Herstel activeren

Gebruik de [opdracht Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) om het herstel te activeren met de aanvraag die hierboven is voorbereid.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Tracerings job

Volg alle taken met behulp van [de opdracht Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) U kunt alle taken bekijken en een bepaalde taakdetails ophalen.

U kunt **Az.ResourceGraph ook gebruiken om** alle taken in alle back-upkluizen bij te houden. Gebruik de [opdracht Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) om de relevante taak op te halen, die zich in elke back-upkluis kan vinden.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Azure Disk Backup](disk-backup-faq.yml)

---
title: Azure Managed Disks herstellen via Azure PowerShell
description: Meer informatie over het herstellen van Azure Managed Disks met behulp van Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630379"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure Managed Disks herstellen met behulp van Azure PowerShell

In dit artikel wordt uitgelegd hoe u [Azure Managed disks](../virtual-machines/managed-disks-overview.md) kunt herstellen vanaf een herstel punt dat is gemaakt door Azure backup.

Op dit moment wordt de optie voor het Original-Location herstellen (herstellen) van het herstellen door de bestaande bron schijf te vervangen van waar de back-ups werden gemaakt, niet ondersteund. U kunt herstellen vanaf een herstel punt om een nieuwe schijf te maken in dezelfde resource groep als die van de bron schijf waar de back-ups zijn gemaakt of in een andere resource groep. Dit staat bekend als Alternate-Location herstel (ALR) en dit helpt de bron schijf en de herstelde (nieuwe) schijf te blijven gebruiken.

In dit artikel leert u het volgende:

- Herstellen om een nieuwe schijf te maken

- De status van de herstel bewerking bijhouden

In de voor beelden wordt naar een bestaande back-upkluis "TestBkpVault" in de resource groep "testBkpVaultRG" verwezen

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Herstellen om een nieuwe schijf te maken

### <a name="setting-up-permissions"></a>Rechten instellen

Backup-kluis maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Als u wilt herstellen vanuit een back-up, moet de beheerde identiteit van de back-upkluis een set machtigingen hebben voor de resource groep waarin de schijf moet worden hersteld.

Back-upkluis maakt gebruik van een door het systeem toegewezen beheerde identiteit die is beperkt tot één per resource en is verbonden met de levens cyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type dat alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md).

Wijs de relevante machtigingen voor de door het systeem toegewezen beheerde identiteit van de kluis toe aan de doel resource groep waar de schijven worden teruggezet/gemaakt, zoals [hier](restore-managed-disks.md#restore-to-create-a-new-disk)wordt beschreven.

### <a name="fetching-the-relevant-recovery-point"></a>Het relevante herstel punt wordt opgehaald

Haal alle exemplaren op met de opdracht [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) en Identificeer het relevante exemplaar.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

U kunt ook **AZ. Resourcegraph** en de opdracht [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) gebruiken om in verschillende kluizen en abonnementen naar instanties te zoeken.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Zodra het exemplaar is geïdentificeerd, haalt u het relevante herstel punt op.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>De herstel aanvraag voorbereiden

Maak de ARM-ID van de nieuwe schijf die moet worden gemaakt met de doel resource groep, waaraan machtigingen zijn toegewezen zoals [hierboven](#setting-up-permissions)beschreven en de vereiste schijf naam. Een schijf kan bijvoorbeeld de naam **PSTestDisk2** onder een resource groep **targetrg** met een ander abonnement zijn.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Gebruik de opdracht [initialiseren-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) om de herstel aanvraag met alle relevante gegevens voor te bereiden.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Herstel activeren

Gebruik de opdracht [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) om de herstel bewerking te activeren met de hierboven voor bereide aanvraag.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Taak bijhouden

Volg alle taken met behulp van de opdracht [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . U kunt alle taken weer geven en een bepaald taak detail ophalen.

U kunt ook **AZ. ResourceGraph** gebruiken om alle taken in alle back-upkluizen bij te houden. Gebruik de opdracht [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) om de relevante taak te verkrijgen. deze kan zich in elke back-upkluis bevinden.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Azure Disk Backup](disk-backup-faq.md)

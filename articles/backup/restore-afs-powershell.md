---
title: Azure Files herstellen met Power shell
description: In dit artikel leert u hoe u Azure Files kunt herstellen met behulp van de Azure Backup-service en Power shell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021381"
---
# <a name="restore-azure-files-with-powershell"></a>Azure Files herstellen met Power shell

In dit artikel wordt uitgelegd hoe u een volledige bestands share of specifieke bestanden herstelt vanaf een herstel punt dat is gemaakt door de [Azure backup](backup-overview.md) -service met behulp van Azure PowerShell.

U kunt een volledige bestands share of specifieke bestanden op de share herstellen. U kunt herstellen naar de oorspronkelijke locatie of naar een andere locatie.

> [!WARNING]
> Controleer of de Power shell-versie is bijgewerkt naar de minimale versie van AZ. Recovery Services 2.6.0 voor AFS-back-ups. Zie de [sectie](backup-azure-afs-automation.md#important-notice-backup-item-identification) overzicht van de vereiste voor deze wijziging voor meer informatie.

>[!NOTE]
>Azure Backup ondersteunt nu het terugzetten van meerdere bestanden of mappen naar de oorspronkelijke of alternatieve locatie met behulp van Power shell. Raadpleeg [deze sectie](#restore-multiple-files-or-folders-to-original-or-alternate-location) van het document voor meer informatie.

## <a name="fetch-recovery-points"></a>Herstel punten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om alle herstel punten voor het back-upitem weer te geven.

In het volgende script:

* De variabele **$RP** is een matrix met herstel punten voor het geselecteerde back-upitem van de afgelopen zeven dagen.
* De matrix wordt in omgekeerde volg orde gesorteerd met het laatste herstel punt bij index **0**.
* Gebruik standaard-Power shell-matrix indexie om het herstel punt te kiezen.
* In het voor beeld selecteert **$RP [0]** het meest recente herstel punt.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Nadat het relevante herstel punt is geselecteerd, herstelt u de bestands share of het bestand naar de oorspronkelijke locatie of naar een andere locatie.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Een Azure-bestands share herstellen naar een alternatieve locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie te identificeren:

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

Voer de cmdlet als volgt uit met de para meters:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Een Azure-bestand terugzetten op een andere locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie aan te duiden en het bestand dat u wilt herstellen, uniek te identificeren.

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **SourceFilePath**: het absolute pad van het bestand dat in de bestands share moet worden hersteld, als een teken reeks. Dit pad is hetzelfde pad dat wordt gebruikt in de Power shell **-cmdlet Get-AzStorageFile** .
* **SourceFileType**: Hiermee wordt aangegeven of een map of een bestand is geselecteerd. De **map** of het **bestand** wordt geaccepteerd.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

De aanvullende para meters (SourceFilePath en SourceFileType) zijn alleen gerelateerd aan het afzonderlijke bestand dat u wilt herstellen.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Met deze opdracht wordt een taak geretourneerd met een ID die moet worden bijgehouden, zoals wordt weer gegeven in de vorige sectie.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure-bestands shares en-bestanden naar de oorspronkelijke locatie herstellen

Wanneer u naar een oorspronkelijke locatie herstelt, hoeft u geen doel-en doel-gerelateerde para meters op te geven. Alleen **ResolveConflict** moet worden gegeven.

### <a name="overwrite-an-azure-file-share"></a>Een Azure-bestands share overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Een Azure-bestand overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Meerdere bestanden of mappen terugzetten op de oorspronkelijke of alternatieve locatie

Gebruik de opdracht [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) door het pad door te geven van alle bestanden of mappen die u wilt herstellen als waarde voor de para meter **MultipleSourceFilePath** .

### <a name="restore-multiple-files"></a>Meerdere bestanden terugzetten

In het volgende script proberen we de *FileSharePage.png* -en *MyTestFile.txt* -bestanden te herstellen.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Meerdere mappen herstellen

In het volgende script proberen we de *zrs1_restore* -en *herstel* mappen te herstellen.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

De uitvoer ziet er ongeveer als volgt uit:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Als u meerdere bestanden of mappen naar een alternatieve locatie wilt herstellen, gebruikt u de bovenstaande scripts door de parameter waarden voor de doel locatie op te geven, zoals hierboven beschreven in [een Azure-bestand herstellen naar een andere locatie](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](restore-afs.md) het herstellen van Azure files in de Azure Portal.

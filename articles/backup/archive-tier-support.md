---
title: Ondersteuning voor archief lagen (preview-versie)
description: Meer informatie over de ondersteuning van de archiefmap voor Azure Backup
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 322bc9d7e2160cc9156c793859b9fda833b3df09
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563970"
---
# <a name="archive-tier-support-preview"></a>Ondersteuning voor archief lagen (preview-versie)

Klanten zijn afhankelijk van Azure Backup voor het opslaan van back-upgegevens, inclusief de gegevens over de back-up van de Long-Term retentie (LTR), waarbij de vereisten voor het bewaren worden gedefinieerd door de nalevings regels van de organisatie. In de meeste gevallen worden de oudere back-upgegevens zelden gebruikt en worden ze alleen opgeslagen voor nalevings behoeften.

Azure Backup ondersteunt back-ups voor lange termijn retentie punten in de archief laag, naast moment opnamen en de Standard-laag.

## <a name="scope-for-preview"></a>Bereik voor preview

Ondersteunde werk belastingen:

- Azure-VM's
  - Alleen maandelijkse en jaarlijkse herstel punten. Dagelijkse en wekelijkse herstel punten worden niet ondersteund.
  - Leeftijd >= 3 maanden in de Vault-Standard-laag
  - Retentie links >= 6 maanden
  - Geen actieve dagelijkse en wekelijkse afhankelijkheden
- SQL Server op virtuele machines van Azure
  - Alleen volledige herstel punten. Logboeken en verschillen worden niet ondersteund.
  - >leeftijd = 45 dagen in Vault-Standard laag
  - Retentie links >= 6 maanden
  - Geen afhankelijkheden

Ondersteunde clients:

- De mogelijkheid wordt gegeven met behulp van Power shell

>[!NOTE]
>De ondersteuning voor de archief tier voor Azure-Vm's en SQL Server in azure Vm's is beperkt tot een open bare Preview met beperkte aanmeldingen. Gebruik deze [koppeling](https://aka.ms/ArchivePreviewInterestForm)om u aan te melden voor archief ondersteuning.

## <a name="get-started-with-powershell"></a>Aan de slag met PowerShell

1. Voer de volgende opdracht uit in PowerShell:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Maak verbinding met Azure met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Meld u aan bij uw abonnement:

   `Set-AzContext -Subscription "SubscriptionName"`

1. De kluis ophalen:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. De lijst met back-upitems ophalen:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Het back-upitem ophalen.

    - Voor virtuele machines van Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Voor SQL Server in azure virtual machines:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>PowerShell gebruiken

### <a name="check-archivable-recovery-points"></a>Herstel punten voor archivering controleren

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Hiermee worden alle herstel punten weer geven die zijn gekoppeld aan een bepaald back-upitem dat gereed is om te worden verplaatst naar het archief.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Controleren waarom een herstel punt niet kan worden verplaatst naar het archief

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Waar `$rp[0]` is het herstel punt waarvoor u wilt controleren waarom het niet kan worden gearchiveerd.

Voorbeelduitvoer:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Aanbevolen set bezorgings punten controleren (alleen voor virtuele Azure-machines)

De herstel punten die zijn gekoppeld aan een virtuele machine zijn incrementeel. Wanneer een bepaald herstel punt naar een archief wordt verplaatst, wordt dit geconverteerd naar een volledige back-up en vervolgens verplaatst naar het archief. De kosten besparingen die zijn gekoppeld aan de overstap naar Archive zijn dus afhankelijk van het verloop van de gegevens bron.

Azure Backup heeft dus een aanbevolen set herstel punten die kunnen leiden tot kosten besparingen, als ze samen worden verplaatst.

>[!NOTE]
>De kosten besparingen zijn afhankelijk van een aantal redenen en zijn mogelijk niet hetzelfde voor twee exemplaren.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Verplaatsen naar archief

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Met deze opdracht wordt een archiefbaar herstel punt verplaatst naar Archive. Er wordt een taak geretourneerd die kan worden gebruikt om de verplaatsings bewerking bij te houden van de portal en met Power shell.

### <a name="view-archived-recovery-points"></a>Gearchiveerde herstel punten weer geven

Met deze opdracht worden alle gearchiveerde herstel punten geretourneerd.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Herstellen met Power shell

Azure Backup biedt een geïntegreerde herstel methodologie voor herstel punten in archief.

De geïntegreerde herstel bewerking is een proces dat uit twee stappen bestaat. De eerste stap omvat het reactiveren van de herstel punten die zijn opgeslagen in het archief en deze tijdelijk op te slaan in de standaardlaag voor een duur (ook wel bekend als de rehydratatie duur), variërend van een periode van 10 tot 30 dagen. De standaard waarde is 15 dagen. Er zijn twee verschillende prioriteiten voor rehydratatie: standaard en hoge prioriteit. Meer informatie over de [rehydratatie-prioriteit](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- Als de rehydratatie-duur eenmaal is geselecteerd, kan deze niet worden gewijzigd en blijven de opnieuw gehydrateerde herstel punten in de standaard laag voor de rehydratatie duur.
>- De extra stap voor rehydratatie kosten.

Zie [een Azure VM herstellen met Power shell](backup-azure-vms-automation.md#restore-an-azure-vm)voor meer informatie over de verschillende herstel methoden voor virtuele Azure-machines.

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Voer [de volgende stappen uit](backup-azure-sql-automation.md#restore-sql-dbs)om SQL Server te herstellen. De vereiste aanvullende para meters zijn **RehydrationPriority** en **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Taken vanuit Power shell weer geven

Gebruik de volgende Power shell-cmdlet om de verplaatsings-en herstel taken weer te geven:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Gebruik de portal

### <a name="check-archived-recovery-point"></a>Gearchiveerd herstel punt controleren

U kunt nu alle herstel punten weer geven die zijn verplaatst naar het archief.

![Alle herstel punten.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Herstellen in de portal

Voor herstel punten die zijn verplaatst naar Archive, moet u de para meters voor de rehydratatie duration-en rehydratatie-prioriteit toevoegen.

![Herstellen in de portal.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Taken in de portal weer geven

![Taken weer geven in de portal.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Beveiliging wijzigen

Er zijn twee manieren waarop u de beveiliging voor een gegevens bron kunt wijzigen:

- Een bestaand beleid wijzigen
- De gegevens bron beveiligen met een nieuw beleid

In beide gevallen wordt het nieuwe beleid toegepast op alle oudere herstel punten, die zich in de laag standaard en in de laag Archive bevinden. Oudere herstel punten kunnen dus worden verwijderd als het beleid wordt gewijzigd.

Wanneer de herstel punten worden verplaatst naar het archief, worden er een vroege verwijderings periode van 180 dagen in rekening gebracht. De kosten worden in rekening gebracht. Als een herstel punt dat gedurende 180 dagen niet in het archief is beslag, wordt verwijderd, worden kosten in rekening gebracht die gelijk zijn aan 180 min het aantal dagen dat het heeft gespendeerd in de laag standaard.

Herstel punten die gedurende een periode van Mini maal zes maanden niet in het archief zijn opgelopen, zullen de kosten voor vroegtijdige verwijdering van de verwijdering oplopen.

## <a name="stop-protection-and-delete-data"></a>Beveiliging stoppen en gegevens verwijderen

Als u de gegevens niet meer wilt beveiligen en verwijderen, worden alle herstel punten verwijderd. Voor herstel punten in archief die gedurende een periode van 180 dagen in de Archive-laag niet hebben verbleven, worden de herstel punten verwijderd tot de kosten voor vroegtijdige verwijdering.

## <a name="error-codes-and-troubleshooting-steps"></a>Fout codes en stappen voor probleem oplossing

Er zijn verschillende fout codes die beschikbaar zijn wanneer een herstel punt niet kan worden verplaatst naar het archief.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Fout bericht** -Recovery-Point type komt niet in aanmerking voor archief verplaatsen

**Beschrijving** : deze fout code wordt weer gegeven wanneer het geselecteerde type herstel punt niet in aanmerking komt voor verplaatsing naar het archief.

**Aanbevolen actie** : Controleer [hier](#scope-for-preview) de geschiktheid van het herstel punt

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Fout bericht** -Recovery-Point die actieve afhankelijkheden hebben voor Restore, komt niet in aanmerking voor archief verplaatsen

**Beschrijving:** Het geselecteerde herstel punt heeft actieve afhankelijkheden en kan daarom niet worden verplaatst naar het archief.

**Aanbevolen actie** : Controleer [hier](#scope-for-preview) de geschiktheid van het herstel punt

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Fout bericht** -Recovery-Point komt niet in aanmerking voor archief verplaatsing omdat de levens duur die is uitgegeven in de kluis-Standard-laag lager is dan het vereiste minimum

**Beschrijving** : het herstel punt moet in de standaard-laag blijven gedurende mini maal drie maanden voor Azure virtual machines en 45 dagen voor SQL Server in azure virtual machines

**Aanbevolen actie** : Controleer [hier](#scope-for-preview) de geschiktheid van het herstel punt

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Fout bericht** -Recovery-Point resterende levens duur is lager dan het vereiste minimum.

**Beschrijving** : de minimale levens duur die is vereist voor een herstel punt voor de mogelijkheid om een archief te verplaatsen, is zes maanden.

**Aanbevolen actie** : Controleer [hier](#scope-for-preview) de geschiktheid van het herstel punt

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Fout bericht** -Recovery-Point komt niet in aanmerking voor archief verplaatsing omdat het al is verplaatst naar de archief laag

**Beschrijving** : het geselecteerde herstel punt bevindt zich al in het archief. Het is dus niet in aanmerking komen om te worden verplaatst naar het archief.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Fout bericht** : Data Source-Type komt niet in aanmerking voor aanbevelings-API.

**Beschrijving** : de aanbevelings-API is alleen van toepassing op virtuele machines van Azure. Het is niet van toepassing op het geselecteerde gegevens bron type.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Fout bericht** : het herstel punt is al opnieuw gehydrateerd. Rehydratatie is niet toegestaan voor deze RP.

**Beschrijving** : het geselecteerde herstel punt is al opnieuw gehydrateerd.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Fout bericht** : herstel punt komt niet in aanmerking voor het verplaatsen van het archief.

**Beschrijving** : het geselecteerde herstel punt komt niet in aanmerking voor het verplaatsen van het archief.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Fout** **bericht** : het archief herstel punt is niet opnieuw gehydrateerd. Herstel opnieuw proberen nadat rehydratatie is voltooid op archief RP.

**Beschrijving** : het herstel punt is niet opnieuw gehydrateerd. Probeer terug te zetten nadat u het herstel punt reactiveren.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Fout** **bericht**: rehydratatie wordt alleen ondersteund voor archief herstel punten-rehydratatie wordt alleen ondersteund voor archief herstel punten

**Beschrijving** – rehydratatie is niet toegestaan voor het geselecteerde herstel punt.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Fout bericht** : rehydratatie is al In-Progress voor archief herstel punt.

**Beschrijving** : de rehydratatie voor het geselecteerde herstel punt wordt al uitgevoerd.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Fout bericht** : herstel punt kan niet worden verplaatst naar de archief laag vanwege onvoldoende Bewaar duur opgegeven in het beleid

**Aanbevolen actie** -beleid bijwerken op het beveiligde item met de juiste Bewaar instelling en probeer het opnieuw.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Fout bericht** : er wordt nog steeds bepaald of dit herstel punt kan worden verplaatst.

**Beschrijving** : de gereedheid voor het verplaatsen van het herstel punt is nog te bepalen.

**Aanbevolen actie** -controleren na enige tijd.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Wat gebeurt er met het archiveren van herstel punten als ik de beveiliging stop en gegevens bewaart?

Het herstel punt blijft permanent in het archief. Zie [impact van stop beveiliging op herstel punten](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Prijzen van Azure Backup](azure-backup-pricing.md)
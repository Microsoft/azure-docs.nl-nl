---
title: Problemen met back-ups van Azure-bestands share oplossen
description: Dit artikel gaat over het oplossen van problemen die optreden bij het beveiligen van uw Azure-bestandsshares.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376975"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Problemen oplossen bij het maken van back-ups van Azure-bestands shares

In dit artikel vindt u informatie over het oplossen van problemen bij het configureren van back-ups of het herstellen van Azure-bestands shares met behulp van de Azure Backup-service.

## <a name="common-configuration-issues"></a>Veelvoorkomende configuratie problemen

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Kan mijn opslag account niet vinden voor het configureren van de back-up voor de Azure-bestands share

- Wacht totdat de detectie is voltooid.
- Controleer of een bestands share onder het opslag account al is beveiligd met een andere Recovery Services kluis.

  >[!NOTE]
  >Alle bestandsshares in een opslagaccount kunnen in maar één Recovery Services-kluis worden beveiligd. U kunt [Dit script](scripts/backup-powershell-script-find-recovery-services-vault.md) gebruiken om de Recovery Services kluis te vinden waar uw opslag account is geregistreerd.

- Zorg ervoor dat de bestands share niet aanwezig is in een van de niet-ondersteunde opslag accounts. Raadpleeg de [ondersteunings matrix voor Azure file share backup](azure-file-share-support-matrix.md) om ondersteunde opslag accounts te vinden.
- Zorg ervoor dat de gecombineerde lengte van de naam van het opslag account en de naam van de resource groep niet langer zijn dan 84 tekens voor nieuwe opslag accounts en 77 tekens in het geval van klassieke opslag accounts.
- Controleer de firewall-instellingen van het opslag account om ervoor te zorgen dat de optie voor het toestaan van vertrouwde micro soft-Services voor toegang tot het opslag account is ingeschakeld.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Fout in de portal geeft aan dat de detectie van opslagaccounts is mislukt

Als u een partner abonnement hebt (CSP-enabled), negeert u de fout. Neem contact op met de ondersteuning als uw abonnement geen CSP-functionaliteit heeft en uw opslag accounts niet kunnen worden gedetecteerd.

### <a name="selected-storage-account-validation-or-registration-failed"></a>De geselecteerde opslag account is niet gevalideerd of de registratie is mislukt

Voer de registratie opnieuw uit. Neem contact op met de ondersteuning als het probleem zich blijft voordoen.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Kan de bestands shares in het geselecteerde opslag account niet weer geven of vinden

- Zorg ervoor dat het opslag account bestaat in de resource groep en niet is verwijderd of verplaatst na de laatste validatie of registratie in de kluis.
- Zorg ervoor dat de bestands share die u wilt beveiligen, niet is verwijderd.
- Zorg ervoor dat het opslag account een ondersteund opslag account is voor het maken van een back-up van bestands shares. Raadpleeg de [ondersteunings matrix voor Azure file share backup](azure-file-share-support-matrix.md) om ondersteunde opslag accounts te vinden.
- Controleer of de bestands share al in dezelfde Recovery Services kluis wordt beveiligd.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>De configuratie van de back-upbestands share (of de configuratie van het beveiligings beleid) is mislukt

- Voer de configuratie opnieuw uit om te zien of het probleem zich blijft voordoen.
- Zorg ervoor dat de bestands share die u wilt beveiligen, niet is verwijderd.
- Als u meerdere bestands shares tegelijk wilt beveiligen en een aantal van de bestands shares mislukt, kunt u proberen om de back-up van de mislukte bestands shares opnieuw te configureren.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Kan de Recovery Services kluis niet verwijderen na het ongedaan maken van de beveiliging van een bestands share

Open in de Azure Portal uw   >  opslag accounts voor **back-upinfrastructuur** van de kluis  >  . Selecteer **registratie ongedaan maken** om de opslag accounts te verwijderen uit de Recovery Services kluis.

>[!NOTE]
>Een Recovery Services kluis kan alleen worden verwijderd na het ongedaan maken van de registratie van alle opslag accounts die zijn geregistreerd bij de kluis.

## <a name="common-backup-or-restore-errors"></a>Veelvoorkomende back-up-of herstel fouten

>[!NOTE]
>Raadpleeg [dit document](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) om te controleren of u voldoende machtigingen hebt voor het uitvoeren van back-up-en herstel bewerkingen.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound: de bewerking is mislukt omdat de bestands share niet is gevonden

Fout code: FileShareNotFound

Fout bericht: de bewerking is mislukt omdat de bestands share niet is gevonden

Zorg ervoor dat de bestands share die u wilt beveiligen, niet is verwijderd.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-opslag account niet gevonden of wordt niet ondersteund

Fout code: UserErrorFileShareEndpointUnreachable

Fout bericht: het opslag account is niet gevonden of wordt niet ondersteund

- Zorg ervoor dat het opslag account bestaat in de resource groep en niet is verwijderd of verwijderd uit de resource groep na de laatste validatie.

- Zorg ervoor dat het opslag account een ondersteund opslag account is voor het maken van een back-up van bestands shares.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-u hebt de maximum limiet voor moment opnamen voor deze bestands share bereikt. u kunt meer doen als de oudere versies verlopen

Fout code: AFSMaxSnapshotReached

Fout bericht: de maximum limiet van moment opnamen voor deze bestands share is bereikt. u kunt meer doen als de oudere versies verlopen.

- Deze fout kan optreden wanneer u meerdere back-ups op aanvraag voor een bestands share maakt.
- Er is een limiet van 200 moment opnamen per bestands share, met inbegrip van de Azure Backup. Oudere geplande back-ups (of momentopnamen) worden automatisch opgeschoond. Back-ups (of momentopnamen) op aanvraag moeten worden verwijderd als de limiet is bereikt.

Verwijder de back-ups op aanvraag (momentopnamen van Azure-bestandsshares) uit de Azure Files-portal.

>[!NOTE]
> als u momentopnamen verwijdert die zijn gemaakt door Azure Backup, gaan de herstelpunten verloren.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound: de bewerking is mislukt omdat het opgegeven opslag account niet meer bestaat

Fout code: UserErrorStorageAccountNotFound

Fout bericht: de bewerking is mislukt omdat het opgegeven opslag account niet meer bestaat.

Zorg ervoor dat het opslag account nog bestaat en niet is verwijderd.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-de gegevens van het opslag account zijn onjuist

Fout code: UserErrorDTSStorageAccountNotFound

Fout bericht: de ingevoerde gegevens van het opslag account zijn onjuist.

Zorg ervoor dat het opslag account nog bestaat en niet is verwijderd.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-resource groep bestaat niet

Fout code: UserErrorResourceGroupNotFound

Fout bericht: de resource groep bestaat niet

Selecteer een bestaande resourcegroep of maak een nieuwe.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest: er wordt al een back-uptaak uitgevoerd voor deze bestands share

Fout code: ParallelSnapshotRequest

Fout bericht: er wordt al een back-uptaak uitgevoerd voor deze bestands share.

- Back-up van bestands shares biedt geen ondersteuning voor aanvragen voor parallelle moment opnamen voor dezelfde bestands share.

- Wacht tot de bestaande back-uptaak is voltooid en probeer het opnieuw. Als u geen back-uptaak kunt vinden in de Recovery Services kluis, raadpleegt u de andere Recovery Services kluizen in hetzelfde abonnement.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-back-up van bestands share of herstellen is mislukt vanwege beperking van de opslag service. Dit komt mogelijk doordat de opslag service bezig is met het verwerken van andere aanvragen voor het opgegeven opslag account

Fout code: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Fout bericht: het maken van een back-up of herstel van de bestands share is mislukt vanwege een beperking van de opslag service. Dit komt mogelijk doordat de opslagservice bezig is met het verwerken van andere aanvragen voor het betreffende opslagaccount.

Probeer de back-up-of herstel bewerking op een later tijdstip.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-doel bestands share niet gevonden

Fout code: TargetFileShareNotFound

Fout bericht: kan de doel bestands share niet vinden.

- Zorg ervoor dat het geselecteerde opslag account bestaat en dat de bestands share van het doel niet wordt verwijderd.

- Zorg ervoor dat het opslag account een ondersteund opslag account is voor het maken van een back-up van bestands shares.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-back-up-of herstel taken zijn mislukt omdat het opslag account is vergrendeld

Fout code: UserErrorStorageAccountIsLocked

Fout bericht: back-up-of herstel taken zijn mislukt omdat het opslag account is vergrendeld.

Verwijder de vergren deling van het opslag account of gebruik **verwijderings vergrendeling** in plaats van **Lees vergrendeling** en voer de back-up-of herstel bewerking opnieuw uit.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached-herstel is mislukt omdat het aantal mislukte bestanden groter is dan de drempel waarde

Fout code: DataTransferServiceCoFLimitReached

Fout bericht: herstel is mislukt omdat het aantal mislukte bestanden groter is dan de drempel waarde.

- Oorzaken voor herstel fouten worden weer gegeven in een bestand (pad dat is opgegeven in de taak gegevens). Los de fouten op en voer de herstel bewerking alleen opnieuw uit voor de mislukte bestanden.

- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, zijn momenteel in gebruik
  - Er bestaat al een map met dezelfde naam als het mislukte bestand in de bovenliggende map.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-herstel is mislukt omdat er geen bestand kan worden hersteld

Fout code: DataTransferServiceAllFilesFailedToRecover

Fout bericht: het herstellen is mislukt omdat er geen bestand kan worden hersteld.

- Oorzaken voor herstel fouten worden weer gegeven in een bestand (pad dat is opgegeven in de taak gegevens). Los de fouten op en voer de herstelbewerkingen alleen opnieuw uit voor de mislukte bestanden.

- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, zijn momenteel in gebruik
  - Er bestaat al een map met dezelfde naam als het mislukte bestand in de bovenliggende map.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid-herstel mislukt omdat een van de bestanden in de bron niet bestaat

Fout code: DataTransferServiceSourceUriNotValid

Fout bericht: het herstellen is mislukt omdat een van de bestanden in de bron niet bestaat.

- De geselecteerde items zijn niet aanwezig in de herstelpuntgegevens. Geef de juiste bestandslijst op om de bestanden te herstellen.
- De momentopname van de bestandsshare die overeenkomt met het herstelpunt wordt handmatig verwijderd. Selecteer een ander herstelpunt en voer de herstelbewerking opnieuw uit.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked: een herstel taak wordt uitgevoerd op hetzelfde doel

Fout code: UserErrorDTSDestLocked

Fout bericht: een herstel taak wordt uitgevoerd op hetzelfde doel.

- Back-up van bestands shares biedt geen ondersteuning voor parallelle herstel naar dezelfde doel bestands share.

- Wacht tot de bestaande hersteltaak is voltooid en probeer het opnieuw. Als u een herstel taak niet kunt vinden in de Recovery Services kluis, raadpleegt u de andere Recovery Services kluizen in hetzelfde abonnement.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull: de herstel bewerking is mislukt omdat de doel bestands share vol is

Fout code: UserErrorTargetFileShareFull

Fout bericht: de herstel bewerking is mislukt omdat de doel bestands share vol is.

Verhoog het quotum voor de grootte van de doel bestands share om ruimte te maken voor de herstel gegevens en voer de herstel bewerking opnieuw uit.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-doel bestands share heeft onvoldoende opslag quotum grootte voor herstellen

Fout code: UserErrorTargetFileShareQuotaNotSufficient

Fout bericht: de doel bestands share heeft niet voldoende opslag quotum grootte voor herstellen

Verhoog het quotum voor de grootte van de doel bestands share om ruimte te maken voor de herstel gegevens en voer de bewerking opnieuw uit

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed: de herstel bewerking is mislukt omdat er een fout is opgetreden tijdens het uitvoeren van pre-herstel bewerkingen op File Sync Service resources die zijn gekoppeld aan de doel bestands share

Fout code: File Sync PreRestoreFailed

Fout bericht: de herstel bewerking is mislukt omdat er een fout is opgetreden bij het uitvoeren van voor bereide bewerkingen op File Sync Service resources die zijn gekoppeld aan de doel bestands share.

Probeer de gegevens op een later tijdstip terug te zetten. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-Azure File Sync Service wijzigingen worden gedetecteerd voor de doel bestands share. De wijzigings detectie is geactiveerd door een eerdere herstel bewerking naar de doel bestands share

Fout code: AzureFileSyncChangeDetectionInProgress

Fout bericht: detectie van Azure File Sync Service wijzigingen wordt uitgevoerd voor de doel bestands share. De wijzigings detectie is geactiveerd door een eerdere herstel bewerking naar de doel bestands share.

Gebruik een andere doel bestands share. U kunt ook wachten tot de detectie van Azure File Sync Service-wijzigingen is voltooid voor de doel bestands share voordat u de herstel bewerking opnieuw uitvoert.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored: een of meer bestanden kunnen niet worden hersteld. Raadpleeg de lijst met mislukte bestanden in het pad dat hierboven wordt vermeld voor meer informatie

Fout code: UserErrorAFSRecoverySomeFilesNotRestored

Fout bericht: een of meer bestanden kunnen niet worden hersteld. Controleer de lijst met mislukte bestanden in het pad hierboven voor meer informatie.

- Oorzaken voor herstel fouten worden weer gegeven in het bestand (pad dat is opgegeven in de taak gegevens). Los de redenen op en voer de herstel bewerking alleen voor de mislukte bestanden opnieuw uit.
- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, zijn momenteel in gebruik
  - Er bestaat al een map met dezelfde naam als het mislukte bestand in de bovenliggende map.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound: de moment opname van de Azure-bestands share die overeenkomt met het herstel punt is niet gevonden

Fout code: UserErrorAFSSourceSnapshotNotFound

Fout bericht: kan de moment opname van de Azure-bestands share die overeenkomt met het herstel punt niet vinden

- Zorg ervoor dat de moment opname van de bestands share, die overeenkomt met het herstel punt dat u voor herstel wilt gebruiken, nog bestaat.

  >[!NOTE]
  >Als u een moment opname van een bestands share verwijdert die is gemaakt door Azure Backup, worden de bijbehorende herstel punten onbruikbaar. Het is raadzaam moment opnamen niet te verwijderen om gegarandeerd herstel te garanderen.

- Selecteer een ander herstel punt om uw gegevens te herstellen.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget: een andere herstel taak wordt uitgevoerd op dezelfde doel bestands share

Fout code: UserErrorAnotherRestoreInProgressOnSameTarget

Fout bericht: er wordt een andere herstel taak uitgevoerd op dezelfde doel bestands share

Gebruik een andere doel bestands share. U kunt de bewerking ook annuleren of wachten tot de andere herstelbewerking is voltooid.

## <a name="common-modify-policy-errors"></a>Veelvoorkomende beleids fouten voor wijzigingen

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-er wordt een andere beveiligings bewerking voor het configureren van dit item uitgevoerd

Fout code: BMSUserErrorConflictingProtectionOperation

Fout bericht: er wordt een andere configure Protection-bewerking uitgevoerd voor dit item.

Wacht tot de vorige wijzigings beleids bewerking is voltooid en probeer het later opnieuw.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked: er wordt een andere bewerking uitgevoerd voor het geselecteerde item

Fout code: BMSUserErrorObjectLocked

Fout bericht: er wordt een andere bewerking uitgevoerd voor het geselecteerde item.

Wacht totdat de andere bewerking in uitvoering is voltooid en probeer het later opnieuw.

## <a name="common-soft-delete-related-errors"></a>Veelvoorkomende problemen bij zacht verwijderen

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState: dit herstel punt is niet beschikbaar omdat de moment opname die is gekoppeld aan dit punt zich in een bestands share bevindt die de status zacht verwijderd heeft

Fout code: UserErrorRestoreAFSInSoftDeleteState

Fout bericht: dit herstel punt is niet beschikbaar omdat de moment opname die is gekoppeld aan dit punt zich in een bestands share bevindt die de status zacht verwijderd heeft.

U kunt geen herstel bewerking uitvoeren wanneer de bestands share de status zacht verwijderd heeft. Verwijder de bestands share uit de bestanden portal of gebruik het [script verwijderen](scripts/backup-powershell-script-undelete-file-share.md) en probeer vervolgens te herstellen.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState-weer gegeven herstel punten zijn niet beschikbaar omdat de bijbehorende bestands share die de moment opnamen van het herstel punt bevat permanent is verwijderd

Fout code: UserErrorRestoreAFSInDeleteState

Fout bericht: de vermelde herstel punten zijn niet beschikbaar omdat de bijbehorende bestands share die de moment opnamen van herstel punten bevat permanent is verwijderd.

Controleer of de back-up van de bestands share is verwijderd. Als de status zacht verwijderd was, controleert u of de tijdelijke verwijdering van de Bewaar periode is afgelopen en niet is hersteld. In een van deze gevallen gaan alle moment opnamen permanent verloren en kunnen de gegevens niet worden hersteld.

>[!NOTE]
> U wordt aangeraden de back-ups van de bestands share niet te verwijderen, of als deze de status zacht verwijderd heeft, de verwijdering ongedaan te maken voordat de tijdelijke verwijderings periode eindigt, om te voor komen dat alle herstel punten verloren gaan.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState-back-up is mislukt omdat de Azure-bestands share de status zacht verwijderd heeft

Fout code: UserErrorBackupAFSInSoftDeleteState

Fout bericht: de back-up is mislukt omdat de Azure-bestands share de status zacht verwijderd heeft

Verwijder de bestands share uit de **bestanden Portal** of gebruik het [script verwijderen](scripts/backup-powershell-script-undelete-file-share.md) om door te gaan met de back-up en permanente verwijdering van gegevens te voor komen.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState-back-up is mislukt omdat de gekoppelde Azure-bestands share permanent is verwijderd

Fout code: UserErrorBackupAFSInDeleteState

Fout bericht: de back-up is mislukt omdat de gekoppelde Azure-bestands share permanent is verwijderd

Controleer of de back-up van de bestands share permanent is verwijderd. Zo ja, stop de back-up voor de bestands share om herhaalde back-upfouten te voor komen. Zie [stoppen met beveiliging voor Azure-bestands share voor](./manage-afs-backup.md#stop-protection-on-a-file-share) meer informatie over het stoppen van de beveiliging

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het maken van back-ups van Azure-bestands shares:

- [Een back-up maken van Azure-bestandsshares](backup-afs.md)
- [Veelgestelde vragen over back-ups van Azure file share](backup-azure-files-faq.md)

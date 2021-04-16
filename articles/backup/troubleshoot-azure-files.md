---
title: Problemen met back-ups van Azure-bestands share oplossen
description: Dit artikel gaat over het oplossen van problemen die optreden bij het beveiligen van uw Azure-bestandsshares.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515070"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Problemen oplossen tijdens het maken van back-up van Azure-bestands shares

Dit artikel bevat informatie over probleemoplossing voor het oplossen van problemen die u tegenkomt bij het configureren van back-ups of het herstellen van Azure-bestands shares met behulp van de Azure Backup Service.

## <a name="common-configuration-issues"></a>Veelvoorkomende configuratieproblemen

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Kan mijn opslagaccount niet vinden voor het configureren van back-ups voor de Azure-bestands share

- Wacht totdat de detectie is voltooid.
- Controleer of een bestands share onder het opslagaccount al is beveiligd met een andere Recovery Services-kluis.

  >[!NOTE]
  >Alle bestandsshares in een opslagaccount kunnen in maar één Recovery Services-kluis worden beveiligd. U kunt dit [script gebruiken om](scripts/backup-powershell-script-find-recovery-services-vault.md) de Recovery Services-kluis te vinden waar uw opslagaccount is geregistreerd.

- Zorg ervoor dat de bestands share niet aanwezig is in een van de niet-ondersteunde opslagaccounts. Raadpleeg de ondersteuningsmatrix voor [back-ups van Azure-bestands share om](azure-file-share-support-matrix.md) ondersteunde opslagaccounts te vinden.
- Zorg ervoor dat de gecombineerde lengte van de naam van het opslagaccount en de naam van de resourcegroep niet langer is dan 84 tekens in het geval van nieuwe Opslagaccounts en 77 tekens in het geval van klassieke opslagaccounts.
- Controleer de firewallinstellingen van het opslagaccount om ervoor te zorgen dat de optie om vertrouwde Microsoft-services toegang te geven tot het opslagaccount is ingeschakeld.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Fout in de portal geeft aan dat de detectie van opslagaccounts is mislukt

Als u een partnerabonnement hebt (CSP ingeschakeld), negeert u de fout. Als uw abonnement niet is ingeschakeld voor CSP en uw opslagaccounts niet kunnen worden gevonden, neemt u contact op met de ondersteuning.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Validatie of registratie van geselecteerde opslagaccount mislukt

De registratie opnieuw proberen. Als het probleem zich blijft voordoen, neem dan contact op met de ondersteuning.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Kan geen bestands shares in het geselecteerde opslagaccount vinden of vinden

- Zorg ervoor dat het opslagaccount bestaat in de resourcegroep en niet is verwijderd of verplaatst na de laatste validatie of registratie in de kluis.
- Zorg ervoor dat de bestands share die u wilt beveiligen, niet is verwijderd.
- Zorg ervoor dat het opslagaccount een ondersteund opslagaccount is voor het maken van back-ups van bestands delen. Raadpleeg de ondersteuningsmatrix voor [back-ups van Azure-bestands share om](azure-file-share-support-matrix.md) ondersteunde opslagaccounts te vinden.
- Controleer of de bestands share al is beveiligd in dezelfde Recovery Services-kluis.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Configuratie van back-upbestands share (of de configuratie van het beveiligingsbeleid) mislukt

- De configuratie opnieuw proberen om te zien of het probleem zich blijft voordoen.
- Zorg ervoor dat de bestands share die u wilt beveiligen, niet is verwijderd.
- Als u meerdere bestands shares tegelijk probeert te beveiligen en sommige bestands shares mislukken, configureert u opnieuw een back-up voor de mislukte bestands shares.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Kan de Recovery Services-kluis niet verwijderen na het opheffen van de beveiliging van een bestands share

Open in Azure Portal Uw **Vault**  >  **Backup Infrastructure**  >  **Storage-accounts.** Selecteer **Registratie ongedaan maken** om de opslagaccounts uit de Recovery Services-kluis te verwijderen.

>[!NOTE]
>Een Recovery Services-kluis kan alleen worden verwijderd nadat de registratie van alle opslagaccounts die bij de kluis zijn geregistreerd, is verwijderd.

## <a name="common-backup-or-restore-errors"></a>Veelvoorkomende back-up- of herstelfouten

>[!NOTE]
>Raadpleeg dit [document om ervoor](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) te zorgen dat u voldoende machtigingen hebt voor het uitvoeren van back-up- of herstelbewerkingen.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound: bewerking is mislukt omdat de bestandsshare niet is gevonden

Foutcode: FileShareNotFound

Foutbericht: Bewerking is mislukt omdat de bestands share niet is gevonden

Zorg ervoor dat de bestands share die u probeert te beveiligen, niet is verwijderd.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable: opslagaccount niet gevonden of niet ondersteund

Foutcode: UserErrorFileShareEndpointUnreachable

Foutbericht: Opslagaccount niet gevonden of niet ondersteund

- Zorg ervoor dat het opslagaccount bestaat in de resourcegroep en niet is verwijderd of verwijderd uit de resourcegroep na de laatste validatie.

- Zorg ervoor dat het opslagaccount een ondersteund opslagaccount is voor het maken van back-ups van bestands delen.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached: u hebt de maximale limiet voor het aantal momentopnamen voor deze bestands share bereikt; u kunt meer nemen zodra de oudere verlopen

Foutcode: AFSMaxSnapshotReached

Foutbericht: U hebt de maximale limiet voor momentopnamen voor deze bestands share bereikt; u kunt er meer van maken zodra de oudere verlopen.

- Deze fout kan optreden wanneer u meerdere back-ups op aanvraag maakt voor een bestands share.
- Er is een limiet van 200 momentopnamen per bestands share, inclusief de momentopnamen die zijn gemaakt door Azure Backup. Oudere geplande back-ups (of momentopnamen) worden automatisch opgeschoond. Back-ups (of momentopnamen) op aanvraag moeten worden verwijderd als de limiet is bereikt.

Verwijder de back-ups op aanvraag (momentopnamen van Azure-bestandsshares) uit de Azure Files-portal.

>[!NOTE]
> als u momentopnamen verwijdert die zijn gemaakt door Azure Backup, gaan de herstelpunten verloren.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound- Bewerking mislukt omdat het opgegeven opslagaccount niet meer bestaat

Foutcode: UserErrorStorageAccountNotFound

Foutbericht: De bewerking is mislukt omdat het opgegeven opslagaccount niet meer bestaat.

Zorg ervoor dat het opslagaccount nog bestaat en niet wordt verwijderd.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound: de opgegeven opslagaccountgegevens zijn onjuist

Foutcode: UserErrorDTSStorageAccountNotFound

Foutbericht: De opgegeven opslagaccountgegevens zijn onjuist.

Zorg ervoor dat het opslagaccount nog bestaat en niet wordt verwijderd.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound: resourcegroep bestaat niet

Foutcode: UserErrorResourceGroupNotFound

Foutbericht: Resourcegroep bestaat niet

Selecteer een bestaande resourcegroep of maak een nieuwe.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest: er wordt al een back-up job uitgevoerd voor deze bestands share

Foutcode: ParallelSnapshotRequest

Foutbericht: Er wordt al een back-up job uitgevoerd voor deze bestands share.

- Back-up van bestands share biedt geen ondersteuning voor parallelle momentopnameaanvragen voor dezelfde bestands share.

- Wacht tot de bestaande back-up job is gemaakt en probeer het vervolgens opnieuw. Als u geen back-up taak kunt vinden in de Recovery Services-kluis, controleert u andere Recovery Services-kluizen in hetzelfde abonnement.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling: back-up of herstel van bestandsshare is mislukt vanwege beperking van opslagservice. Dit kan komen doordat de opslagservice bezig is met het verwerken van andere aanvragen voor het opgegeven opslagaccount

Foutcode: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Foutbericht: Back-up of herstel van bestands share is mislukt vanwege beperking van opslagservice. Dit komt mogelijk doordat de opslagservice bezig is met het verwerken van andere aanvragen voor het betreffende opslagaccount.

Probeer de back-up-/herstelbewerking op een later tijdstip.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound: doelbestandsshare niet gevonden

Foutcode: TargetFileShareNotFound

Foutbericht: Doelbestands share niet gevonden.

- Zorg ervoor dat het geselecteerde opslagaccount bestaat en dat de doelbestands share niet wordt verwijderd.

- Zorg ervoor dat het opslagaccount een ondersteund opslagaccount is voor het maken van back-ups van bestands delen.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked: back-up- of hersteltaken zijn mislukt omdat het opslagaccount de vergrendelde status heeft

Foutcode: UserErrorStorageAccountIsLocked

Foutbericht: Back-up- of hersteltaken zijn mislukt omdat het opslagaccount de vergrendelde status heeft.

Verwijder de vergrendeling van het opslagaccount of gebruik **verwijderenvergrendeling** in plaats van leesvergrendeling en **open** de back-up- of herstelbewerking opnieuw.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached: herstel is mislukt omdat het aantal mislukte bestanden hoger is dan de drempelwaarde

Foutcode: DataTransferServiceCoFLimitReached

Foutbericht: Het herstel is mislukt omdat het aantal mislukte bestanden hoger is dan de drempelwaarde.

- Oorzaken van herstelstoringen worden vermeld in een bestand (pad in de taakdetails). De fouten aanpakken en alleen de herstelbewerking voor de mislukte bestanden opnieuw uitvoeren.

- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, worden momenteel gebruikt
  - een map met dezelfde naam als het mislukte bestand bestaat in de bovenliggende map.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover: herstel is mislukt omdat er geen bestand kan worden hersteld

Foutcode: DataTransferServiceAllFilesFailedToRecover

Foutbericht: Herstel is mislukt omdat er geen bestand kan worden hersteld.

- Oorzaken van herstelstoringen worden vermeld in een bestand (pad in de taakdetails). Los de fouten op en voer de herstelbewerkingen alleen opnieuw uit voor de mislukte bestanden.

- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, worden momenteel gebruikt
  - een map met dezelfde naam als het mislukte bestand bestaat in de bovenliggende map.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - Herstellen mislukt omdat een van de bestanden in de bron niet bestaat

Foutcode: DataTransferServiceSourceUriNotValid

Foutbericht: Herstellen mislukt omdat een van de bestanden in de bron niet bestaat.

- De geselecteerde items zijn niet aanwezig in de herstelpuntgegevens. Geef de juiste bestandslijst op om de bestanden te herstellen.
- De momentopname van de bestandsshare die overeenkomt met het herstelpunt wordt handmatig verwijderd. Selecteer een ander herstelpunt en voer de herstelbewerking opnieuw uit.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked: er wordt een herstelklus naar dezelfde bestemming verwerkt

Foutcode: UserErrorDTSDestLocked

Foutbericht: Er wordt een herstelklus naar dezelfde bestemming verwerkt.

- Back-up van bestands delen biedt geen ondersteuning voor parallel herstel naar dezelfde doelbestands share.

- Wacht tot de bestaande hersteltaak is voltooid en probeer het opnieuw. Als u geen herstelkluis kunt vinden in de Recovery Services-kluis, controleert u andere Recovery Services-kluizen in hetzelfde abonnement.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull: herstelbewerking is mislukt omdat de doelbestandsshare vol is

Foutcode: UserErrorTargetFileShareFull

Foutbericht: De herstelbewerking is mislukt omdat de doelbestands share vol is.

Verhoog het quotum voor de doelbestands sharegrootte om ruimte te bieden aan de herstelgegevens en de herstelbewerking opnieuw uit te voeren.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient: doelbestandsshare heeft onvoldoende opslaggroottequotum voor herstel

Foutcode: UserErrorTargetFileShareQuotaNotSufficient

Foutbericht: Doelbestands share heeft onvoldoende opslaggroottequotum voor herstel

Verhoog het quotum voor de doelbestands share om ruimte te bieden aan de herstelgegevens en de bewerking opnieuw uit te voeren

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed- Herstelbewerking is mislukt omdat er een fout is opgetreden tijdens het uitvoeren van pre-herstelbewerkingen op File Sync Service-resources die zijn gekoppeld aan de doelbestands share

Foutcode: File Sync PreRestoreFailed

Foutbericht: De herstelbewerking is mislukt omdat er een fout is opgetreden tijdens het uitvoeren van pre-herstelbewerkingen op File Sync Service-resources die zijn gekoppeld aan de doelbestands share.

Probeer de gegevens op een later tijdstip te herstellen. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress- Azure File Sync Service wijzigingsdetectie wordt uitgevoerd voor de doelbestands share. De wijzigingsdetectie is geactiveerd door een eerdere herstel naar de doelbestands share

Foutcode: AzureFileSyncChangeDetectionInProgress

Foutbericht: Azure File Sync servicewijzigingsdetectie wordt uitgevoerd voor de doelbestands share. De wijzigingsdetectie is geactiveerd door een eerdere herstel naar de doelbestands share.

Gebruik een andere doelbestands share. U kunt ook wachten tot Azure File Sync servicewijzigingsdetectie is voltooid voor de doelbestands share voordat u het herstel opnieuw gaat proberen.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored: een of meer bestanden kunnen niet worden hersteld. Raadpleeg de lijst met mislukte bestanden in het bovenstaande pad voor meer informatie

Foutcode: UserErrorAFSRecoverySomeFilesNotRestored

Foutbericht: Een of meer bestanden kunnen niet worden hersteld. Controleer de lijst met mislukte bestanden in het pad hierboven voor meer informatie.

- Redenen voor het mislukken van het herstel worden vermeld in het bestand (pad in de taakdetails). De redenen aanpakken en alleen de herstelbewerking voor de mislukte bestanden opnieuw proberen.
- Veelvoorkomende redenen voor fouten bij het herstellen van bestanden:

  - bestanden die zijn mislukt, worden momenteel gebruikt
  - een map met dezelfde naam als het mislukte bestand bestaat in de bovenliggende map.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound: momentopname van Azure-bestands share die overeenkomt met herstelpunt kan niet worden gevonden

Foutcode: UserErrorAFSSourceSnapshotNotFound

Foutbericht: Momentopname van Azure-bestands share die overeenkomt met herstelpunt kan niet worden gevonden

- Zorg ervoor dat de momentopname van de bestands share, die overeenkomt met het herstelpunt dat u probeert te gebruiken voor herstel, nog steeds bestaat.

  >[!NOTE]
  >Als u een momentopname van een bestands share verwijdert die is gemaakt door Azure Backup, worden de bijbehorende herstelpunten onbruikbaar. We raden u aan geen momentopnamen te verwijderen om gegarandeerd herstel te garanderen.

- Selecteer een ander herstelpunt om uw gegevens te herstellen.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget: er wordt een andere herstel job uitgevoerd op dezelfde doelbestands share

Foutcode: UserErrorAnotherRestoreInProgressOnSameTarget

Foutbericht: Er wordt een andere herstel job uitgevoerd op dezelfde doelbestands share

Gebruik een andere doelbestands share. U kunt de bewerking ook annuleren of wachten tot de andere herstelbewerking is voltooid.

## <a name="common-modify-policy-errors"></a>Veelvoorkomende beleidsfouten wijzigen

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation: er wordt een andere beveiligingsbewerking voor configureren uitgevoerd voor dit item

Foutcode: BMSUserErrorConflictingProtectionOperation

Foutbericht: Er wordt een andere beveiligingsbewerking voor configureren uitgevoerd voor dit item.

Wacht tot de vorige bewerking voor het wijzigen van het beleid is uitgevoerd en het opnieuw proberen op een later tijdstip.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked: er wordt een andere bewerking uitgevoerd op het geselecteerde item

Foutcode: BMSUserErrorObjectLocked

Foutbericht: Er wordt een andere bewerking uitgevoerd op het geselecteerde item.

Wacht tot de andere bewerking is voltooid en het op een later tijdstip opnieuw proberen.

## <a name="common-soft-delete-related-errors"></a>Veelvoorkomende fouten met betrekking tot soft delete

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState: dit herstelpunt is niet beschikbaar omdat de momentopname die aan dit punt is gekoppeld, zich in een bestands share met de status 'soft-deleted' heeft

Foutcode: UserErrorRestoreAFSInSoftDeleteState

Foutbericht: Dit herstelpunt is niet beschikbaar omdat de momentopname die aan dit punt is gekoppeld, zich in een bestands share met de status 'soft-leted' heeft.

U kunt geen herstelbewerking uitvoeren wanneer de bestands share de status 'soft deleted' heeft. De bestands share verwijderen uit de bestandenportal of met behulp van [het script verwijderen](scripts/backup-powershell-script-undelete-file-share.md) verwijderen en probeer vervolgens te herstellen.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState: vermelde herstelpunten zijn niet beschikbaar omdat de gekoppelde bestands share met de momentopnamen van het herstelpunt permanent is verwijderd

Foutcode: UserErrorRestoreAFSInDeleteState

Foutbericht: Vermelde herstelpunten zijn niet beschikbaar omdat de bijbehorende bestands share met de momentopnamen van het herstelpunt permanent is verwijderd.

Controleer of de back-up van de bestands share is verwijderd. Als deze de status 'softleted' heeft, controleert u of de bewaarperiode voor het verwijderen van de gegevens is afgelopen en niet is hersteld. In beide gevallen verliest u al uw momentopnamen permanent en kunt u de gegevens niet herstellen.

>[!NOTE]
> U wordt aangeraden de back-up van de bestands share niet te verwijderen, of als deze de status 'soft deleted' heeft, verwijderen te verwijderen voordat de retentieperiode voor soft delete is beëindigd, om te voorkomen dat al uw herstelpunten verloren gaan.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - Back-up is mislukt omdat de Azure-bestands share de status 'soft-leted' heeft

Foutcode: UserErrorBackupAFSInSoftDeleteState

Foutbericht: Back-up is mislukt omdat de Azure-bestands share de status 'soft-deleted' heeft

Verwijder de bestands share  niet meer uit de bestandenportal of gebruik het script Verwijderen verwijderen [om](scripts/backup-powershell-script-undelete-file-share.md) door te gaan met de back-up en permanente verwijdering van gegevens te voorkomen.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState: back-up is mislukt omdat de bijbehorende Azure-bestands share permanent wordt verwijderd

Foutcode: UserErrorBackupAFSInDeleteState

Foutbericht: Back-up is mislukt omdat de bijbehorende Azure-bestands share permanent wordt verwijderd

Controleer of de bestands share met een back-up permanent is verwijderd. Zo ja, stop dan de back-up voor de bestands share om herhaalde back-upfouten te voorkomen. Zie Stop [Protection for Azure file share](./manage-afs-backup.md#stop-protection-on-a-file-share) (Beveiliging voor Azure-bestands delen stoppen) voor meer informatie over het stoppen van de beveiliging

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het maken van back-up van Azure-bestands shares:

- [Een back-up maken van Azure-bestandsshares](backup-afs.md)
- [Veelgestelde vragen over back-up van Azure-bestands delen](backup-azure-files-faq.yml)

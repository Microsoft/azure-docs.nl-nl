---
title: Back-upfouten in azure Disk Backup oplossen
description: Meer informatie over het oplossen van back-upfouten in azure Disk Backup
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737724"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Back-upfouten in azure Disk Backup oplossen (in preview-versie)

>[!IMPORTANT]
>Azure Disk Backup is in Preview zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor de beschik baarheid van regio's.
>
>[Vul dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) in om u aan te melden voor de preview-versie.

In dit artikel vindt u informatie over het oplossen van problemen met back-ups en herstel met Azure disk. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie over de beschik baarheid van [Azure schijf back-](disk-backup-overview.md) upregio, ondersteunde scenario's en beperkingen.

## <a name="common-issues-faced-with-azure-disk-backup"></a>Veelvoorkomende problemen met Azure Disk Backup

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Fout code: UserErrorSnapshotRGSubscriptionMismatch

Fout bericht: er is een ongeldig abonnement geselecteerd voor het momentopname gegevens archief

Aanbevolen actie: schijven en moment opnamen van schijven worden opgeslagen in hetzelfde abonnement. U kunt elke resource groep kiezen voor het opslaan van de moment opnamen van de schijf in het abonnement. Selecteer hetzelfde abonnement als dat van de bron schijf. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie.

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Fout code: UserErrorSnapshotRGNotFound

Fout bericht: kan de bewerking niet uitvoeren omdat de resource groep voor het momentopname gegevens archief niet bestaat.

Aanbevolen actie: Maak de resource groep en geef de vereiste machtigingen op. Zie [back-up configureren](backup-managed-disks.md#configure-backup)voor meer informatie.

### <a name="error-code-usererrormanageddisknotfound"></a>Fout code: UserErrorManagedDiskNotFound

Fout bericht: kan de bewerking niet uitvoeren omdat de beheerde schijf niet meer bestaat.

Aanbevolen actie: de back-ups blijven mislukken, omdat de bron schijf kan worden verwijderd of verplaatst naar een andere locatie. Gebruik het bestaande herstel punt om de schijf te herstellen als deze per ongeluk wordt verwijderd. Als de schijf wordt verplaatst naar een andere locatie, configureert u een back-up voor de schijf.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Fout code: UserErrorNotEnoughPermissionOnDisk

Fout bericht: voor de Azure Backup-service zijn aanvullende machtigingen voor de schijf vereist om deze bewerking uit te voeren.

Aanbevolen actie: verleen de beheerde identiteit van de back-upkluis de juiste machtigingen op de schijf. Raadpleeg [de documentatie](backup-managed-disks.md) om te begrijpen welke machtigingen zijn vereist voor de door de back-upkluis beheerde identiteit en hoe u deze kunt opgeven.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Fout code: UserErrorNotEnoughPermissionOnSnapshotRG

Fout bericht: voor de Azure Backup-service zijn extra machtigingen vereist voor de resource groep voor momentopname gegevens opslag om deze bewerking uit te voeren.

Aanbevolen actie: verleen de beheerde identiteit van de back-upkluis de juiste machtigingen voor de resource groep gegevens opslag voor moment opnamen. De resource groep voor momentopname gegevens opslag is de locatie waar de moment opnamen van de schijf worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md) voor meer informatie over de resource groep en de machtigingen die zijn vereist voor de door de back-upkluis beheerde identiteit en hoe u deze kunt opgeven.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Fout code: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Fout bericht: ongeldige schijf of Azure Backup service vereist extra machtigingen op de schijf om deze bewerking uit te voeren

Aanbevolen actie: de back-ups blijven mislukken, omdat de bron schijf kan worden verwijderd of verplaatst naar een andere locatie. Gebruik het bestaande herstel punt om de schijf te herstellen als deze per ongeluk wordt verwijderd. Als de schijf wordt verplaatst naar een andere locatie, configureert u een back-up voor de schijf. Als de schijf niet wordt verwijderd of verplaatst, verleent u de beheerde identiteit van de back-upkluis de juiste machtigingen op de schijf. Raadpleeg [de documentatie](backup-managed-disks.md) om te begrijpen welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Fout code: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Fout bericht: kan de bewerking niet uitvoeren omdat de resource groep voor momentopname gegevens opslag niet meer bestaat. Of de Azure Backup-service vereist extra machtigingen voor de resource groep momentopname gegevens opslag om deze bewerking uit te voeren.

Aanbevolen actie: Maak een resource groep en verleen de beheerde identiteit van de back-upkluis de juiste machtigingen voor de resource groep voor de gegevens opslag van de moment opname. De resource groep voor momentopname gegevens opslag is de locatie waar de moment opnamen van de schijf worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md) voor informatie over wat de resource groep is, welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Fout code: UserErrorDiskBackupAuthorizationFailed

Fout bericht: de beheerde identiteit van de back-upkluis mist de benodigde machtigingen om deze bewerking uit te voeren.

Aanbevolen actie: verleen de beheerde identiteit van de back-upkluis de juiste machtigingen op de schijf om een back-up te maken van de gegevens opslag en de resource groep voor moment opnamen waarin de moment opnamen worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md) om te begrijpen welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Fout code: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Fout bericht: kan de bewerking niet uitvoeren omdat de resource groep voor momentopname gegevens opslag niet meer bestaat. Of de Azure Backup-service vereist extra machtigingen voor de resource groep gegevens opslag voor moment opnamen om deze bewerking uit te voeren.

Aanbevolen actie: Maak de resource groep en verleen de beheerde identiteit van de back-upkluis de juiste machtigingen voor de resource groep voor de gegevens opslag van de moment opname. De resource groep voor momentopname gegevens opslag is de locatie waar de moment opnamen worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md) om te begrijpen wat de resource groep is, welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Fout code: UserErrorOperationalStoreParametersNotProvided

Fout bericht: kan de bewerking niet uitvoeren omdat de para meter voor de resource groep voor momentopname gegevens opslag niet is ingesteld

Aanbevolen actie: Geef de para meter voor de resource groep voor momentopname gegevens opslag op. De resource groep voor momentopname gegevens opslag is de locatie waar de moment opnamen van de schijf worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md)voor meer informatie.

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Fout code: UserErrorInvalidOperationalStoreResourceGroup

Fout bericht: de ingevoerde resource groep voor moment opnamen van gegevens opslag is ongeldig

Aanbevolen actie: Geef een geldige resource groep op voor de gegevens opslag van de moment opname. De resource groep voor momentopname gegevens opslag is de locatie waar de moment opnamen van de schijf worden opgeslagen. Raadpleeg [de documentatie](backup-managed-disks.md)voor meer informatie.

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Fout code: UserErrorDiskBackupDiskTypeNotSupported

Fout bericht: niet-ondersteund schijf type

Aanbevolen actie: Raadpleeg [de ondersteunings matrix over niet](disk-backup-support-matrix.md) -ondersteunde scenario's en beperkingen.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Fout code: UserErrorSameNameDiskAlreadyExists

Fout bericht: kan niet herstellen omdat er al een schijf met dezelfde naam bestaat in de geselecteerde doel resource groep

Aanbevolen actie: Geef een andere schijf naam op voor de herstel bewerking. Zie [Azure Managed disks herstellen](restore-managed-disks.md)voor meer informatie.

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Fout code: UserErrorRestoreTargetRGNotFound

Fout bericht: de bewerking is mislukt omdat de doel resource groep niet bestaat.

Aanbevolen actie: Geef een geldige resource groep op om te herstellen. Zie [Azure Managed disks herstellen](restore-managed-disks.md)voor meer informatie.

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Fout code: UserErrorNotEnoughPermissionOnTargetRG

Fout bericht: de Azure Backup-service vereist extra machtigingen voor de doel resource groep om deze bewerking uit te voeren.

Aanbevolen actie: verleen de beheerde identiteit van de back-upkluis de juiste machtigingen voor de doel resource groep. De doel resource groep is de geselecteerde locatie waar de schijf moet worden hersteld. Raadpleeg de documentatie bij het [herstellen](restore-managed-disks.md) om te begrijpen welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Fout code: UserErrorSubscriptionDiskQuotaLimitReached

Fout bericht: de bewerking is mislukt omdat de maximum limiet voor het schijf quotum voor het abonnement is bereikt.

Aanbevolen actie: Raadpleeg het [Azure-abonnement en de service limieten en de quota documentatie](../azure-resource-manager/management/azure-subscription-service-limits.md) of neem contact op met Microsoft ondersteuning voor meer informatie.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Fout code: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Fout bericht: de bewerking is mislukt omdat de doel resource groep niet bestaat. Of de Azure Backup-service vereist extra machtigingen voor de doel resource groep om deze bewerking uit te voeren.

Aanbevolen actie: Geef een geldige resource groep op om te herstellen en verleen de beheerde identiteit van de back-upkluis de juiste machtigingen voor de doel resource groep. De doel resource groep is de geselecteerde locatie waar de schijf moet worden hersteld. Raadpleeg de documentatie bij het [herstellen](restore-managed-disks.md) om te begrijpen welke machtigingen vereist zijn voor de beheerde identiteit van de back-upkluis en hoe u deze kunt opgeven.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Fout code: UserErrorDESKeyVaultKeyDisabled

Fout bericht: de sleutel kluis sleutel die wordt gebruikt voor de schijf versleuteling is niet ingeschakeld.

Aanbevolen actie: Schakel de sleutel kluis sleutel in die wordt gebruikt voor de schijf versleutelings. Raadpleeg de beperkingen in de [ondersteunings matrix](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Fout code: UserErrorMSIPermissionsNotPresentOnDES

Fout bericht: de Azure Backup-service heeft toestemming nodig om toegang te krijgen tot de schijf versleuteling die wordt gebruikt voor de schijf.

Aanbevolen actie: Zorg dat lezers toegang hebben tot de beheerde identiteit van de back-upkluis naar de schijf Encryption set (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Fout code: UserErrorDESKeyVaultKeyNotAvailable

Fout bericht: de sleutel kluis sleutel die wordt gebruikt voor de schijf versleutelings is niet beschikbaar.

Aanbevolen actie: Zorg ervoor dat de sleutel kluis sleutel die wordt gebruikt voor de schijf versleutelings, niet is uitgeschakeld of verwijderd.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Fout code: UserErrorDiskSnapshotNotFound

Fout bericht: de moment opname van de schijf voor dit herstel punt is verwijderd.

Aanbevolen actie: moment opnamen worden opgeslagen in de resource groep van het momentopname gegevens archief binnen uw abonnement. Het is mogelijk dat de moment opname die is gerelateerd aan het geselecteerde herstel punt, is verwijderd of verplaatst van deze resource groep. Overweeg het gebruik van een ander herstel punt om te herstellen. Volg ook de aanbevolen richt lijnen voor het kiezen van een resource groep voor de moment opname die wordt vermeld in de [Restore-documentatie](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Fout code: UserErrorSnapshotMetadataNotFound

Fout bericht: de meta gegevens van de schijf momentopname voor dit herstel punt zijn verwijderd

Aanbevolen actie: overweeg het gebruik van een ander herstel punt dat u wilt herstellen. Zie de [documentatie over herstel](restore-managed-disks.md)voor meer informatie.

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Fout code: BackupAgentPluginHostValidateProtectionError

Fout bericht: schijf back-up is nog niet beschikbaar in de regio van de back-upkluis, waaronder het configureren van de beveiliging wordt uitgevoerd.

Aanbevolen actie: de back-upkluis moet zich in een voor beeld van een ondersteunde regio bevinden. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor de beschik baarheid van regio's.

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Fout code: UserErrorDppDatasourceAlreadyHasBackupInstance

Fout bericht: de schijf die u wilt configureren van de back-up wordt al beveiligd. De schijf is al gekoppeld aan een back-upexemplaar in een back-upkluis.

Aanbevolen actie: deze schijf is al gekoppeld aan een back-upexemplaar in een back-upkluis. Als u deze schijf opnieuw wilt beveiligen, verwijdert u het back-upexemplaar van de back-upkluis waar het momenteel wordt beveiligd en moet u de schijf opnieuw beveiligen in een andere kluis.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Fout code: UserErrorDppDatasourceAlreadyProtected

Fout bericht: de schijf die u wilt configureren van de back-up wordt al beveiligd. De schijf is al gekoppeld aan een back-upexemplaar in een back-upkluis.

Aanbevolen actie: deze schijf is al gekoppeld aan een back-upexemplaar in een back-upkluis. Als u deze schijf opnieuw wilt beveiligen, verwijdert u het back-upexemplaar van de back-upkluis waar het momenteel wordt beveiligd en moet u de schijf opnieuw beveiligen in een andere kluis.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Fout code: UserErrorMaxConcurrentOperationLimitReached

Fout bericht: kan de bewerking niet starten omdat het maximale aantal gelijktijdige back-ups is bereikt.

Aanbevolen actie: wacht tot de vorige uitvoering van de back-up is voltooid.

## <a name="next-steps"></a>Volgende stappen

- [Azure Disck Backup-ondersteuningsmatrix](disk-backup-support-matrix.md)
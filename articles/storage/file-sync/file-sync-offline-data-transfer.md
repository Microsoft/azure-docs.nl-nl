---
title: Gegevens migreren naar Azure File Sync met Azure Data Box
description: Bulkgegevens offline migreren die compatibel zijn met Azure File Sync. Vermijd bestandsconflicten en behoudt de ACL's en tijdstempels van bestanden en mappen nadat u synchronisatie hebt ingeschakeld.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796695"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Bulkgegevens migreren in Azure File Sync met Azure DataBox
U kunt bulkgegevens op twee Azure File Sync migreren:

* **Upload uw bestanden met behulp van Azure File Sync.** Dit is de eenvoudigste methode. Verplaats uw bestanden lokaal naar Windows Server 2012 R2 of hoger en installeer Azure File Sync agent. Nadat u de synchronisatie hebt ingesteld, worden uw bestanden geüpload van de server. (Onze klanten ervaren momenteel ongeveer elke twee dagen een gemiddelde uploadsnelheid van 1 TiB.) Om ervoor te zorgen dat uw server niet te veel bandbreedte gebruikt voor uw datacenter, kunt u een bandbreedtebeperkingsschema [instellen.](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Uw bestanden offline overdragen.** Als u niet voldoende bandbreedte hebt, kunt u bestanden mogelijk niet binnen een redelijke tijd uploaden naar Azure. De uitdaging is de initiële synchronisatie van de hele set bestanden. Gebruik offline hulpprogramma's voor bulkmigratie, zoals de Azure Data Box [om deze uitdaging te ondervangen.](https://azure.microsoft.com/services/storage/databox) 

In dit artikel wordt uitgelegd hoe u bestanden offline migreert op een manier die compatibel is met Azure File Sync. Volg deze instructies om bestandsconflicten te voorkomen en om toegangsbeheerlijsten (ACL's) en tijdstempels voor bestanden en mappen te behouden nadat u de synchronisatie hebt ingeschakeld.

## <a name="migration-tools"></a>Hulpprogramma's voor migratie
Het proces dat we in dit artikel beschrijven, werkt niet alleen voor Data Box maar ook voor andere hulpprogramma's voor offlinemigratie. Het werkt ook voor hulpprogramma's zoals AzCopy, Robocopy of partnerhulpprogramma's en -services die rechtstreeks via internet werken. Als u de eerste upload-uitdaging wilt overwinnen, volgt u de stappen in dit artikel om deze hulpprogramma's te gebruiken op een manier die compatibel is met Azure File Sync.

In sommige gevallen moet u van de ene Windows-server naar een andere Windows-server gaan voordat u Azure File Sync. [Storage Migration Service](/windows-server/storage/storage-migration-service/overview) (SMS) kan daarbij helpen. Of u nu wilt migreren naar een serverversie van het besturingssysteem die wordt ondersteund door Azure File Sync (Windows Server 2012R2 en hoger), of u hoeft alleen maar te migreren omdat u een nieuw systeem voor Azure File Sync koopt, sms heeft talloze functies en voordelen die uw migratie soepel laten verlopen.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Voordelen van het gebruik van een hulpprogramma om gegevens offline over te dragen
Dit zijn de belangrijkste voordelen van het gebruik van een overdrachtshulpprogramma, Data Box voor offlinemigratie:

- U hoeft niet al uw bestanden via het netwerk te uploaden. Voor grote naamruimten kan dit hulpprogramma aanzienlijke netwerkbandbreedte en -tijd besparen.
- Wanneer u Azure File Sync gebruikt, ongeacht welk overdrachtshulpprogramma u gebruikt (Data Box, Azure Import/Export-service, en meer), uploadt uw liveserver alleen de bestanden die veranderen nadat u de gegevens naar Azure hebt verplaatst.
- Azure File Sync synchroniseert de ACL's voor bestanden en mappen, zelfs als het offlinehulpprogramma voor bulkmigratie geen ACL's transporteert.
- Data Box en Azure File Sync geen downtime nodig. Wanneer u Data Box gegevens naar Azure over te dragen, gebruikt u efficiënt de netwerkbandbreedte en behoudt u de betrouwbaarheid van bestanden. U houdt uw naamruimte ook up-to-date door alleen de bestanden te uploaden die veranderen nadat u de gegevens naar Azure hebt verplaatst.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Vereisten voor de offlinegegevensoverdracht
U moet synchronisatie niet inschakelen op de server die u migreert voordat u de offline gegevensoverdracht voltooit. Andere zaken die u moet overwegen voordat u begint, zijn als volgt:

- Als u van plan bent om Data Box gebruiken voor uw bulkmigratie: controleer de implementatievoorwaarden [voor Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Uw uiteindelijke Azure File Sync plannen: [Een implementatie Azure File Sync plannen](file-sync-planning.md)
- Selecteer Een of meer Azure-opslagaccounts die de bestands shares zullen opslaan die u wilt synchroniseren. Zorg ervoor dat uw bulkmigratie wordt gebruikt voor tijdelijke faserings shares in hetzelfde opslagaccount(s). Bulkmigratie kan alleen worden ingeschakeld met behulp van een definitieve en een faserings-share die zich in hetzelfde opslagaccount bevindt.
- Een bulkmigratie kan alleen worden gebruikt wanneer u een nieuwe synchronisatierelatie met een serverlocatie maakt. U kunt een bulkmigratie met een bestaande synchronisatierelatie niet inschakelen.


## <a name="process-for-offline-data-transfer"></a>Proces voor offline gegevensoverdracht
U kunt de Azure File Sync instellen op een manier die compatibel is met hulpprogramma's voor bulkmigratie, zoals Azure Data Box:

![Diagram waarin wordt getoond hoe u een Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Stap | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Stap 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Bestel uw Data Box](../../databox/data-box-deploy-ordered.md). De Data Box biedt [verschillende producten om aan](https://azure.microsoft.com/services/storage/databox/data) uw behoeften te voldoen. Wanneer u uw Data Box ontvangt, [](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) volgt u de documentatie om uw gegevens te kopiëren naar dit UNC-pad op de Data Box: *\\<\> \<StorageAccountName_AzFile\> \<ShareName\> DeviceIPAddres*. Hier is *ShareName* de naam van de faserings share. Verzend de Data Box terug naar Azure. |
| ![Stap 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Wacht totdat uw bestanden worden weer geven in de Azure-bestands shares die u hebt gekozen als tijdelijke faserings shares. *Synchronisatie met deze shares niet inschakelen.* |
| ![Stap 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Maak een nieuwe lege share voor elke bestands share die Data Box voor u hebt gemaakt. Deze nieuwe share moet zich in hetzelfde opslagaccount als de Data Box share. [Een nieuwe Azure-bestands share maken.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)</li><li>[Maak een synchronisatiegroep](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in een opslagsynchronisatieservice. Verwijs naar de lege share als een cloud-eindpunt. Herhaal deze stap voor elke Data Box bestands share. [Stel Azure File Sync](file-sync-deployment-guide.md)in.</li></ul> |
| ![Stap 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Voeg uw liveservermap toe als een server-eindpunt.](file-sync-deployment-guide.md#create-a-server-endpoint) Geef in het proces op dat u de bestanden naar Azure hebt verplaatst en verwijst naar de faserings shares. U kunt opslag in cloudlagen in- of uitschakelen als dat nodig is. Tijdens het maken van een server-eindpunt op uw liveserver, verwijst u naar de faserings share. Selecteer op de blade **Server-eindpunt** toevoegen onder **Offlinegegevensoverdracht** de optie Ingeschakeld en selecteer vervolgens de faserings share die zich in hetzelfde opslagaccount als het cloud-eindpunt moet hebben. Hier wordt de lijst met beschikbare shares gefilterd op opslagaccount en shares die nog niet zijn gesynchroniseerd. In de schermopname na deze tabel ziet u hoe u kunt verwijzen naar de DataBox-share tijdens het maken van het server Azure Portal. |
| ![Stap 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | Nadat u het server-eindpunt in de vorige stap hebt toegevoegd, worden de gegevens automatisch vanuit de juiste bron gestroomd. In [de sectie De share synchroniseren](#syncing-the-share) wordt uitgelegd wanneer gegevens stromen vanuit de DataBox-share of vanaf de Windows Server |
| |

![Schermopname van Azure Portal gebruikersinterface, waarin wordt weergegeven hoe u offline gegevensoverdracht kunt inschakelen tijdens het maken van een nieuw server-eindpunt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>De share synchroniseren
Nadat u het server-eindpunt hebt ingesteld, wordt de synchronisatie uitgevoerd. Het synchronisatieproces bepaalt of elk bestand op de server ook aanwezig is in de faserings share waar Data Box de bestanden zijn opgeslagen. Als het bestand daar bestaat, kopieert het synchronisatieproces het bestand uit de faserings share in plaats van het te uploaden vanaf de server. Als het bestand niet bestaat in de staging-share of als er een nieuwere versie beschikbaar is op de lokale server, uploadt het synchronisatieproces het bestand van de lokale server.

Bij het synchroniseren van de share worden ontbrekende bestandskenmerken, machtigingen of tijdstempels van de bestandsvarianten op de lokale server samengevoegd, waarbij deze worden gecombineerd met de bestands tegenhangers van de DataBox-share. Dit zorgt ervoor dat elk bestand en elke map met alle mogelijke bestandstrouwheid in de Azure-bestands share binnenkomen.

> [!IMPORTANT]
> U kunt de modus voor bulkmigratie alleen inschakelen wanneer u een server-eindpunt maakt. Nadat u een server-eindpunt hebt ingesteld, kunt u niet meer bulksgewijs gemigreerde gegevens van een al gesynchroniseerde server integreren in de naamruimte.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL's en tijdstempels voor bestanden en mappen
Azure File Sync zorgt ervoor dat ACL's voor bestanden en mappen worden gesynchroniseerd vanaf de liveserver, zelfs als het hulpprogramma voor bulkmigratie dat u hebt gebruikt, in eerste instantie geen ACL's heeft getransporteerd. Daarom hoeft de faserings share geen ACL's voor bestanden en mappen te bevatten. Wanneer u de functie voor offlinegegevensmigratie inschakelen wanneer u een nieuw server-eindpunt maakt, worden alle bestands-ACL's gesynchroniseerd op de server. Nieuw gemaakte en gewijzigde tijdstempels worden ook gesynchroniseerd.

## <a name="shape-of-the-namespace"></a>Vorm van de naamruimte
Wanneer u de synchronisatie inschakelen, bepaalt de inhoud van de server de vorm van de naamruimte. Als bestanden van de lokale server worden verwijderd nadat de Data Box en migratie zijn uitgevoerd, worden deze bestanden niet verplaatst naar de live, synchronisatie van de naamruimte. Ze blijven in de faserings share, maar ze worden niet gekopieerd. Dit is nodig omdat de synchronisatie de naamruimte volgens de liveserver houdt. De Data Box *momentopname* is slechts een faseringsplaats voor het efficiënt kopiëren van bestanden. Het is niet de autoriteit voor de vorm van de live-naamruimte.

## <a name="cleaning-up-after-bulk-migration"></a>Opsruiming na bulkmigratie 
Wanneer de server de initiële synchronisatie van de naamruimte voltooit, gebruiken Data Box gemigreerde bestanden de faseringsbestands share. Op de **blade Eigenschappen van server Azure Portal** in de sectie **Offlinegegevensoverdracht** wordt de status gewijzigd van **Wordt** uitgevoerd in **Voltooid.** 

![Schermopname van de blade Eigenschappen van server-eindpunt, waar de besturingselementen voor status en uitschakelen voor offline gegevensoverdracht zich bevinden](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

U kunt nu de faserings share ops schonen om kosten te besparen:

1. Selecteer op de blade **Eigenschappen van** server-eindpunt, wanneer de status **Voltooid** is, de **optie Offlinegegevensoverdracht uitschakelen.**
2. Overweeg de faserings share te verwijderen om kosten te besparen. De staging-share bevat waarschijnlijk geen ACL's voor bestanden en mappen, dus het is onwaarschijnlijk dat deze nuttig is. Maak voor back-uppunt in tijd een echte momentopname van [de synchronisatie van de Azure-bestands share](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json). U kunt [de Azure Backup momentopnamen volgens een]( ../../backup/backup-afs.md) schema maken.

Schakel de offlinemodus voor gegevensoverdracht alleen uit wanneer de status **Voltooid** is of wanneer u wilt annuleren vanwege een onjuiste configuratie. Als u de modus uitzet tijdens een implementatie, worden bestanden vanaf de server geüpload, zelfs als uw faserings share nog steeds beschikbaar is.

> [!IMPORTANT]
> Nadat u de offlinemodus voor gegevensoverdracht hebt uitgeschakeld, kunt u deze niet opnieuw inschakelen, zelfs niet als de faserings share van de bulkmigratie nog steeds beschikbaar is.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync en vooraf geseede bestanden in de cloud

Als u bestanden in een Azure-bestands share op een andere manier hebt geseed dan DataBox , bijvoorbeeld via AzCopy, RoboCopy vanuit een cloudback-up of een andere methode, moet u nog steeds het [offline](#process-for-offline-data-transfer) gegevensoverdrachtproces volgen dat in dit artikel wordt beschreven. U hoeft DataBox alleen te negeren als de methode waarmee uw bestanden naar de cloud worden verplaatst. Het is echter cruciaal om ervoor te zorgen dat u  nog steeds het proces van het seeden van de bestanden in een faserings share en niet de laatste, Azure File Sync verbonden share volgt.

> [!WARNING]
> **Volg het proces van het seeden van bestanden in een faserings share en niet** de laatste , Azure File Sync verbonden share. Als u dit niet doet, kunnen er bestandsconflicten optreden (beide bestandsversies worden opgeslagen) en kunnen bestanden die op de liveserver zijn verwijderd, worden teruggehaald als ze nog steeds bestaan in uw oudere, geseede set bestanden. Daarnaast worden mapwijzigingen samengevoegd met elkaar, waardoor het lastig is om de naamruimte te scheiden na een dergelijke fout.

## <a name="next-steps"></a>Volgende stappen
- [Een implementatie van Azure File Sync plannen](file-sync-planning.md)
- [Azure Files SYNC implementeren](file-sync-deployment-guide.md)
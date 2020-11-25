---
title: Gegevens migreren naar Azure File Sync met Azure Data Box
description: Migreer bulk gegevens die compatibel zijn met Azure File Sync. Voorkom bestands conflicten en behoud de Acl's en tijds tempels van bestanden en mappen nadat u synchronisatie hebt ingeschakeld.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017624"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Bulkgegevens migreren in Azure File Sync met Azure DataBox
U kunt op twee manieren bulk gegevens migreren naar Azure File Sync:

* **Upload uw bestanden met behulp van Azure file sync.** Dit is de eenvoudigste methode. Verplaats uw bestanden lokaal naar Windows Server 2012 R2 of later en installeer de Azure File Sync-agent. Nadat u de synchronisatie hebt ingesteld, worden uw bestanden van de server geüpload. (Onze klanten hebben momenteel een gemiddelde upload snelheid van 1 TiB ongeveer elke twee dagen.) Als u er zeker van wilt zijn dat uw server niet te veel band breedte voor uw Data Center gebruikt, kunt u een [planning voor bandbreedte beperking](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)instellen.
* **De bestanden offline zetten.** Als u onvoldoende band breedte hebt, is het mogelijk dat u in een redelijke tijd geen bestanden naar Azure kunt uploaden. De uitdaging is de initiële synchronisatie van de hele set bestanden. Gebruik voor het oplossen van deze uitdaging offline hulpprogram ma's voor bulk migratie, zoals de [Azure data Box serie](https://azure.microsoft.com/services/storage/databox). 

In dit artikel wordt uitgelegd hoe u bestanden offline kunt migreren op een manier die compatibel is met Azure File Sync. Volg deze instructies om bestands conflicten te voor komen en de toegangs beheer lijsten (Acl's) en tijds tempels van het bestand en de map te bewaren nadat de synchronisatie is ingeschakeld.

## <a name="migration-tools"></a>Hulpprogramma's voor migratie
Het proces dat in dit artikel wordt beschreven, werkt niet alleen voor Data Box, maar ook voor andere hulpprogram ma's voor offline migratie. Het werkt ook voor hulpprogram ma's zoals AzCopy, Robocopy of partner tools en-services die rechtstreeks op Internet werken. U kunt de eerste upload vraag echter oplossen door de stappen in dit artikel te volgen om deze hulpprogram ma's te gebruiken op een manier die compatibel is met Azure File Sync.

In sommige gevallen moet u overstappen van de ene Windows-Server naar een andere Windows-Server voordat u Azure File Sync doorneemt. U kunt met behulp van de [opslag migratie service](/windows-server/storage/storage-migration-service/overview) (SMS). Of u moet migreren naar een versie van het besturings systeem van de server die wordt ondersteund door Azure File Sync (Windows Server 2012R2 en up) of u hoeft alleen maar te migreren omdat u een nieuw systeem voor Azure File Sync koopt, heeft SMS talrijke functies en voor delen waarmee u uw migratie probleemloos kunt uitvoeren.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Voor delen van het gebruik van een hulp programma om gegevens offline over te dragen
Dit zijn de belangrijkste voor delen van het gebruik van een hulp programma voor het overzetten van een overdracht, zoals Data Box voor offline migratie:

- U hoeft niet al uw bestanden te uploaden via het netwerk. Voor grote naam ruimten kan dit hulp programma aanzienlijke netwerk bandbreedte en-tijd besparen.
- Wanneer u Azure File Sync gebruikt, ongeacht welk overdrachts programma u gebruikt (Data Box, Azure import/export-service, enzovoort), uploadt uw live-server alleen de bestanden die worden gewijzigd nadat u de gegevens naar Azure hebt verplaatst.
- Azure File Sync synchroniseert de Acl's voor bestanden en mappen, zelfs als het offline hulp programma voor bulk migratie geen Acl's transporteert.
- Data Box en Azure File Sync hebben geen downtime nodig. Wanneer u Data Box gebruikt om gegevens over te brengen naar Azure, kunt u de netwerk bandbreedte efficiënt gebruiken en de bestands kwaliteit behouden. U moet uw naam ruimte ook up-to-date houden door alleen de bestanden te uploaden die worden gewijzigd nadat u de gegevens naar Azure hebt verplaatst.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Vereisten voor de offline-gegevens overdracht
Schakel synchronisatie niet in op de server die u wilt migreren voordat u uw offline gegevens overdracht voltooit. Andere zaken die u moet overwegen voordat u begint, zijn als volgt:

- Als u van plan bent Data Box te gebruiken voor uw bulk migratie: Raadpleeg de [implementatie vereisten voor data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Uw uiteindelijke Azure File Sync topologie plannen: [plan voor een Azure file sync implementatie](storage-sync-files-planning.md)
- Selecteer Azure Storage-account (s) die de bestands shares bevatten die u wilt synchroniseren met. Zorg ervoor dat uw bulk migratie plaatsvindt naar tijdelijke staging-shares in dezelfde opslag account (s). Bulk migratie kan alleen worden ingeschakeld voor het gebruik van een definitief-en een staging-share die zich in hetzelfde opslag account bevindt.
- Een bulk migratie kan alleen worden gebruikt wanneer u een nieuwe synchronisatie relatie maakt met een server locatie. Het is niet mogelijk om een bulk migratie met een bestaande synchronisatie relatie in te scha kelen.


## <a name="process-for-offline-data-transfer"></a>Proces voor offline gegevens overdracht
Hier volgt een beschrijving van het instellen van Azure File Sync op een manier die compatibel is met hulpprogram ma's voor bulk migratie, zoals Azure Data Box:

![Diagram waarin wordt getoond hoe Azure File Sync worden ingesteld](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Stap | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Stap 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Bestel uw data Box](../../databox/data-box-deploy-ordered.md). De Data Box-serie biedt [verschillende producten](https://azure.microsoft.com/services/storage/databox/data) die aan uw behoeften voldoen. Wanneer u uw Data Box ontvangt, volgt u de bijbehorende [documentatie om uw gegevens te kopiëren](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) naar dit UNC-pad op de Data Box: *\\ \> \<StorageAccountName_AzFile\> \<ShareName\><DeviceIPAddres*. Hier is *sharename* de naam van de staging-share. Verzend de Data Box terug naar Azure. |
| ![Stap 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Wacht tot uw bestanden worden weer gegeven in de Azure-bestands shares die u hebt gekozen als tijdelijke share voor tijdelijke bestanden. *Schakel synchronisatie van deze shares niet in.* |
| ![Stap 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Maak een nieuwe lege share voor elke bestands share die Data Box voor u hebt gemaakt. Deze nieuwe share moet zich in hetzelfde opslag account bezien als de Data Box share. [Een nieuwe Azure-bestands share maken](storage-how-to-create-file-share.md).</li><li>[Maak een synchronisatie groep](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in een opslag synchronisatie service. Verwijs naar een lege share als een eind punt in de Cloud. Herhaal deze stap voor elke Data Box bestands share. [Azure file sync instellen](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Stap 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Voeg uw live server-map als een server eindpunt toe](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Geef in het proces op dat u de bestanden naar Azure hebt verplaatst en Raadpleeg de tijdelijke shares. U kunt Cloud lagen in-of uitschakelen als dat nodig is. Tijdens het maken van een server eindpunt op uw live server, verwijst u naar de faserings share. Selecteer op de Blade **Server eindpunt toevoegen** onder **offline gegevensoverdracht** de optie **ingeschakeld** en selecteer vervolgens de faserings share die zich in hetzelfde opslag account als het Cloud eindpunt moet beslaan. Hier wordt de lijst met beschik bare shares gefilterd op basis van het opslag account en de shares die nog niet worden gesynchroniseerd. In de scherm opname die volgt op deze tabel ziet u hoe u kunt verwijzen naar de DataBox-share tijdens het maken van de server-eind punten in de Azure Portal |
| ![Stap 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Zodra u het server-eind punt in de vorige stap hebt toegevoegd, worden de gegevens automatisch vanuit de juiste bron gestart. De sectie [share synchroniseren](#syncing-the-share) wordt uitgelegd wanneer gegevens stromen van de DataBox-share of van de Windows-Server |
| |

![Scherm afbeelding van de gebruikers interface van Azure Portal, waarin wordt weer gegeven hoe u offline gegevens overdracht inschakelt tijdens het maken van een nieuw server eindpunt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>De share synchroniseren
Nadat u het server eindpunt hebt gemaakt, wordt de synchronisatie gestart. Het synchronisatie proces bepaalt of elk bestand op de server ook bestaat in de staging-share waar Data Box de bestanden hebt gedeponeerd. Als het bestand daar bestaat, wordt het bestand door het synchronisatie proces gekopieerd van de staging-share in plaats van het te uploaden van de server. Als het bestand zich niet in de staging-share bevindt, of als er een nieuwere versie beschikbaar is op de lokale server, wordt het bestand door het synchronisatie proces geüpload vanaf de lokale server.

Bij het synchroniseren van de share worden de ontbrekende bestands kenmerken, machtigingen of tijds tempels van de bestands varianten op de lokale server samengevoegd. deze worden gecombineerd met de bijbehorende bestands equivalenten van de DataBox-share. Dit zorgt ervoor dat elk bestand en elke map arriveert met alle mogelijke bestands consistentie in de Azure-bestands share.

> [!IMPORTANT]
> U kunt de modus voor bulk migratie alleen inschakelen tijdens het maken van een server eindpunt. Nadat u een server eindpunt hebt ingesteld, kunt u bulksgewijs gemigreerde gegevens niet integreren van een server die al wordt gesynchroniseerd met de naam ruimte.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Acl's en tijds tempels voor bestanden en mappen
Azure File Sync zorgt ervoor dat de Acl's van bestanden en mappen worden gesynchroniseerd vanaf de live-server, zelfs als het hulp programma voor bulk migratie dat u hebt gebruikt, de Acl's niet oorspronkelijk heeft transporteren. Daarom hoeft de staging-share geen Acl's voor bestanden en mappen te bevatten. Wanneer u de functie voor het migreren van offline gegevens inschakelt tijdens het maken van een nieuw server eindpunt, worden alle Acl's van bestanden gesynchroniseerd op de server. Zojuist gemaakte en gewijzigde tijds tempels worden ook gesynchroniseerd.

## <a name="shape-of-the-namespace"></a>Vorm van de naam ruimte
Wanneer u de synchronisatie inschakelt, bepaalt de inhoud van de server de vorm van de naam ruimte. Als bestanden worden verwijderd van de lokale server na de Data Box moment opname en de migratie zijn voltooid, worden deze bestanden niet naar de Live-naam ruimte gesynchroniseerd. Ze blijven aanwezig in de staging-share, maar ze worden niet gekopieerd. Dit is nodig omdat de synchronisatie de naam ruimte behoudt volgens de live-server. De *moment opname* van de data box is slechts een fase ring voor een efficiënte bestands kopie. Het is niet de instantie voor de vorm van de Live naam ruimte.

## <a name="cleaning-up-after-bulk-migration"></a>Opschonen na bulk migratie 
Wanneer de server de initiële synchronisatie van de naam ruimte voltooit, gebruiken de Data Box bulksgewijs gemigreerde bestanden de staging-bestands share. Op de Blade **Eigenschappen voor Server eindpunt** in azure Portal in de sectie **offline gegevensoverdracht** de status wordt gewijzigd van **in voortgang** in **voltooid**. 

![Scherm opname van de Blade eigenschappen van het server eindpunt, waar de status en de besturings elementen voor offline gegevens overdracht zijn opgeslagen](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

U kunt de faserings share nu opschonen om kosten te besparen:

1. Selecteer op de Blade eigenschappen van het **Server eindpunt** wanneer de status is **voltooid** de optie **offline gegevens overdracht uitschakelen**.
2. U kunt de faserings share verwijderen om kosten te besparen. De staging-share bevat waarschijnlijk geen Acl's voor bestanden en mappen, dus het is waarschijnlijk niet nuttig. Voor back-uppunt-in-time-doel einden maakt u een echte [moment opname van de synchronisatie van de Azure-bestands share](storage-snapshots-files.md). U kunt [Azure backup instellen om moment opnamen te maken]( ../../backup/backup-afs.md) volgens een planning.

Schakel de offline modus voor gegevens overdracht alleen uit wanneer de status is **voltooid** of wanneer u wilt annuleren vanwege een onjuiste configuratie. Als u tijdens een implementatie de modus uitschakelt, wordt het uploaden van bestanden vanaf de server gestart, zelfs als uw staging-share nog steeds beschikbaar is.

> [!IMPORTANT]
> Nadat u de offline modus voor gegevens overdracht hebt uitgeschakeld, kunt u deze niet meer inschakelen, zelfs niet als de faserings share van de bulk migratie nog steeds beschikbaar is.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync en vooraf geseede bestanden in de Cloud

Als u geseede bestanden in een Azure-bestands share hebt op een andere manier dan DataBox, bijvoorbeeld via AzCopy, RoboCopy vanuit een Cloud back-up of een andere methode, moet u nog steeds het [Offline gegevensoverdracht proces](#process-for-offline-data-transfer) volgen dat in dit artikel wordt beschreven. U hoeft DataBox alleen te negeren als de methode die uw bestanden overzetten naar de Cloud. Het is echter van cruciaal belang om ervoor te zorgen dat u nog steeds het proces van het seeden van de bestanden naar een *staging-share* en niet de uiteindelijke, Azure file sync verbonden share.

> [!WARNING]
> **Volg het proces van het seeden van bestanden naar een staging-share en niet de uiteindelijke**, Azure file sync verbonden share. Als dat niet het geval is, kunnen er bestands conflicten optreden (beide bestands versies worden opgeslagen) en bestanden die op de live server worden verwijderd, kunnen terugkomen als ze nog steeds aanwezig zijn in uw oudere, seeded set bestanden. Daarnaast worden wijzigingen in mappen samen met elkaar samengevoegd, waardoor het zeer moeilijk is om de naam ruimte na een dergelijke fout te scheiden.

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
- [Azure File Sync implementeren](storage-sync-files-deployment-guide.md)
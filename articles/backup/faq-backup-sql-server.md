---
title: "Veelgestelde vragen: back-ups maken van SQL Server-data bases op Azure-Vm's"
description: Vind antwoorden op veelgestelde vragen over het maken van back-ups van SQL Server-data bases op virtuele machines van Azure met Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7518fc49f7d6d728bd8faa0de4cf0edc1c6d5831
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734110"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Veelgestelde vragen over SQL Server-data bases die worden uitgevoerd op een back-up van Azure VM

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van SQL Server-data bases die worden uitgevoerd op Azure virtual machines (Vm's) en de [Azure backup](backup-overview.md) -service.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Kan ik Azure Backup gebruiken voor IaaS VM en SQL Server op dezelfde computer?

Ja, u kunt zowel een VM-back-up als een SQL-back-up op dezelfde VM hebben. In dit geval wordt alleen de volledige back-up van kopiëren naar de virtuele machine intern geactiveerd, zodat de logboeken niet worden afgekapt.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Wordt de back-up opnieuw door de oplossing opnieuw of hersteld?

In sommige gevallen worden herstel back-ups door de Azure Backup-service geactiveerd. Automatisch herstellen kan plaatsvinden voor elk van de zes onderstaande voor waarden:

- Als het logboek of een differentiële back-up is mislukt vanwege een LSN-validatie fout, wordt het volgende logboek of een differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Als er geen volledige back-up is gemaakt vóór een logboek of differentiële back-up, wordt dat logboek of een differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Als het tijdstip van de laatste volledige back-up ouder is dan 15 dagen, wordt het volgende logboek of differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Alle back-uptaken die worden geannuleerd als gevolg van een upgrade van een extensie, worden opnieuw geactiveerd nadat de upgrade is voltooid en de uitbrei ding is gestart.
- Als u ervoor kiest om de data base te overschrijven tijdens het herstellen, mislukt het volgende logboek/differentiële back-up en wordt in plaats daarvan een volledige back-up geactiveerd.
- In gevallen waarin een volledige back-up is vereist voor het opnieuw instellen van de logboek ketens als gevolg van een wijziging in het database herstel model, wordt er automatisch een volledige activering uitgevoerd in de volgende planning.

Automatisch herstellen als een mogelijkheid is standaard ingeschakeld voor alle gebruikers. Als u er echter voor kiest om het uit te kiezen, voert u de volgende stappen uit:

- Op het SQL Server-exemplaar in de map *C:\Program Files\Azure workload Backup\bin* maakt of bewerkt u de **ExtensionSettingsOverrides.jsin** het bestand.
- Stel *{"EnableAutoHealer": False}* in het **ExtensionSettingsOverrides.js** in.
- Sla de wijzigingen op en sluit het bestand.
- Open op het SQL Server-exemplaar **taak beheer** en start de **AzureWLBackupCoordinatorSvc** -service vervolgens opnieuw.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Kan ik bepalen hoeveel gelijktijdige back-ups worden uitgevoerd op de SQL-server?

Ja. U kunt de snelheid waarmee het back-upbeleid wordt uitgevoerd, beperken om de impact op een SQL Server-exemplaar te minimaliseren. Ga als volgt te werk om de instelling te wijzigen:

1. Maak op het SQL Server-exemplaar in de map *C:\Program Files\Azure workload Backup\bin* de *ExtensionSettingsOverrides.jsin* het bestand.
2. Wijzig in het *ExtensionSettingsOverrides.js* bestand de instelling **DefaultBackupTasksThreshold** in een lagere waarde (bijvoorbeeld 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
De standaard waarde van DefaultBackupTasksThreshold is **20**.

3. Sla de wijzigingen op en sluit het bestand.
4. Open **Taakbeheer** op het SQL Server-exemplaar. Start de service **AzureWLBackupCoordinatorSvc** opnieuw.<br/> <br/>
 Hoewel deze methode ervoor zorgt dat de back-uptoepassing een groot aantal resources verbruikt, is SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) een meer algemene manier om limieten op te geven voor de hoeveelheid CPU, fysieke io en het geheugen die door binnenkomende toepassings aanvragen kunnen worden gebruikt.

> [!NOTE]
> In de UX kunt u nog steeds een aantal back-ups op een bepaald moment plannen. Ze worden echter verwerkt in een sliding window van de zeggen, 5, volgens het bovenstaande voor beeld.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan ik een volledige back-up van een secundaire replica uitvoeren?

Volgens de beperkingen van SQL kunt u alleen volledige back-up kopiëren op de secundaire replica. Volledige back-ups zijn echter niet toegestaan.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan ik beschikbaarheids groepen on-premises beveiligen?

Nee. Azure Backup beschermt SQL Server data bases die worden uitgevoerd in Azure. Als een beschikbaarheids groep (AG) wordt gespreid tussen Azure-en on-premises machines, kan de AG alleen worden beveiligd als de primaire replica wordt uitgevoerd in Azure. Azure Backup beschermt ook alleen de knoop punten die worden uitgevoerd in dezelfde Azure-regio als de Recovery Services kluis.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan ik beschikbaarheids groepen beveiligen in verschillende regio's?

De Azure Backup Recovery Services kluis kan alle knoop punten die zich in dezelfde regio bevinden als de kluis detecteren en beveiligen. Als uw SQL Server AlwaysOn-beschikbaarheids groep meerdere Azure-regio's omvat, stelt u de back-up in vanuit de regio die het primaire knoop punt heeft. Azure Backup kunnen alle data bases in de beschikbaarheids groep detecteren en beveiligen op basis van uw voor keuren voor back-ups. Wanneer er niet aan de voor keur voor de back-up wordt voldaan, mislukken de back-ups en wordt de fout melding weer geven.

## <a name="do-successful-backup-jobs-create-alerts"></a>Maken succesvolle back-uptaken waarschuwingen?

Nee. Succesvolle back-uptaken maken geen waarschuwingen. Er worden alleen waarschuwingen verzonden voor mislukte back-uptaken. Gedetailleerd gedrag voor portal waarschuwingen wordt [hier](backup-azure-monitoring-built-in-monitor.md)beschreven. Als u echter geïnteresseerd bent in waarschuwingen, zelfs voor geslaagde taken, kunt u de [bewaking gebruiken met behulp van Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan ik geplande back-uptaken weer geven in het menu back-uptaken?

In het menu **back-uptaak** worden alle geplande bewerkingen en acties op aanvraag weer gegeven, met uitzonde ring van de geplande back-ups van het logboek, omdat ze zeer veelvuldig kunnen zijn. Voor geplande logboek taken gebruikt u [bewaking met behulp van Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Worden toekomstige databases automatisch voor back-ups toegevoegd?

Ja, u kunt deze mogelijkheid met [automatische beveiliging](backup-sql-server-database-azure-vms.md#enable-auto-protection)krijgen.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Wat gebeurt er met de back-ups als ik een database uit een automatisch beveiligd exemplaar verwijder?

Als een Data Base uit een niet-beveiligd exemplaar wordt verwijderd, worden er nog steeds back-ups van de data base geprobeerd. Dit betekent dat de verwijderde data base wordt weer gegeven als beschadigd onder **Back-upitems** en nog steeds is beveiligd.

De juiste manier om de beveiliging van deze data base te stoppen, is het stoppen van de **back-up** met het **verwijderen van gegevens** in deze data base.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Als ik de back-upbewerking van een automatisch beveiligde data base stop, wat is dan het gedrag?

Als u **back-up met behoud van gegevens stopt**, zullen er geen back-ups meer worden gemaakt en worden de bestaande herstel punten intact gelaten. De data base wordt nog steeds beschouwd als beveiligd en wordt weer gegeven onder de **Back-upitems**.

Als u geen **back-up maakt met gegevens verwijderen**, zullen er geen toekomstige back-ups meer plaatsvinden en worden de bestaande herstel punten ook verwijderd. De data base wordt als niet-beveiligd beschouwd en wordt weer gegeven onder het exemplaar van de back-up configureren. Maar in tegens telling tot andere, beveiligde data bases die hand matig kunnen worden geselecteerd of die automatisch kunnen worden beveiligd, wordt deze data base grijs weer gegeven en kan deze niet worden geselecteerd. De enige manier om deze data base opnieuw te beveiligen, is door automatische beveiliging uit te scha kelen voor het exemplaar. U kunt deze data base nu selecteren en de beveiliging ervan configureren of de automatische beveiliging opnieuw inschakelen voor het exemplaar.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Als ik de naam van de data base Wijzig nadat deze is beveiligd, wat is dan het gedrag?

Een hernoemde data base wordt behandeld als een nieuwe data base. De service behandelt deze situatie dus als de data base niet is gevonden en de back-ups niet kunnen worden uitgevoerd.

U kunt de data base selecteren, waarvan de naam nu wordt gewijzigd en er beveiliging op configureren. Als de automatische beveiliging is ingeschakeld voor het exemplaar, wordt de naam van de data base automatisch gedetecteerd en beveiligd.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Waarom kan ik een toegevoegde data base niet zien voor een niet-beveiligde instantie?

Een Data Base die u [aan een niet-beveiligd exemplaar toevoegt](backup-sql-server-database-azure-vms.md#enable-auto-protection) , wordt mogelijk niet direct weer gegeven onder beveiligde items. Dat komt doordat de detectie doorgaans om de 8 uur wordt uitgevoerd. U kunt echter direct nieuwe data bases detecteren en beveiligen als u hand matig een detectie uitvoert door **Rediscover db's** te selecteren, zoals wordt weer gegeven in de volgende afbeelding:

  ![Een nieuw toegevoegde data base hand matig detecteren](./media/backup-azure-sql-database/view-newly-added-database.png)
  
## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Kan ik data bases beveiligen waarvoor TDE (Transparent Data Encryption) is ingeschakeld en de data base wordt versleuteld met het hele back-upproces?

Ja, Azure Backup ondersteunt back-ups van SQL Server-data bases of-server waarop TDE is ingeschakeld. Backup ondersteunt [TDe](/sql/relational-databases/security/encryption/transparent-data-encryption) met sleutels die worden beheerd door Azure, of met door de klant beheerde sleutels (BYOK).  Backup voert geen SQL-versleuteling uit als onderdeel van het back-upproces zodat de data base versleuteld blijft wanneer er een back-up wordt gemaakt.

## <a name="does-azure-backup-perform-a-checksum-operation-on-the-data-stream"></a>Voert Azure Backup een controlesom bewerking uit voor de gegevens stroom?

Er wordt een controlesom bewerking uitgevoerd voor de gegevens stroom. Dit is echter niet te verwarren met [SQL-controlesom](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
Back-up van Azure-workload berekent de controlesom voor de gegevens stroom en slaat deze expliciet op tijdens de back-upbewerking. Deze controlesom stroom wordt vervolgens als referentie gebruikt en wordt kruislings gecontroleerd met de controlesom van de gegevens stroom tijdens de herstel bewerking om ervoor te zorgen dat de gegevens consistent zijn.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een back-up van [een SQL Server-Data Base](backup-azure-sql-database.md) die wordt uitgevoerd op een virtuele machine van Azure.

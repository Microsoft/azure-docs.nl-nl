---
title: SAP HANA data bases herstellen op virtuele Azure-machines
description: In dit artikel vindt u informatie over het herstellen van SAP HANA-data bases die worden uitgevoerd op Azure Virtual Machines. U kunt ook meerdere regio's herstellen gebruiken om uw data bases terug te zetten naar een secundaire regio.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021670"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA data bases herstellen op virtuele Azure-machines

In dit artikel wordt beschreven hoe u SAP HANA-data bases kunt herstellen die worden uitgevoerd op een virtuele Azure-machine (VM), waarvan de Azure Backup-service een back-up heeft gemaakt op een Recovery Services kluis. Herstel bewerkingen kunnen worden gebruikt om kopieën te maken van de gegevens voor dev/test-scenario's of om terug te gaan naar een eerdere status.

Zie back-up maken van [SAP Hana data bases op Azure-vm's](./backup-azure-sap-hana-database.md)voor meer informatie over het maken van back-ups van SAP Hana-data bases.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Herstellen naar een bepaald tijdstip of naar een herstel punt

Azure Backup kan SAP HANA-databases die op virtuele Azure-machines worden uitgevoerd als volgt herstellen:

* Herstel naar een specifieke datum of tijd (op de seconde) met behulp van logboekback-ups. Azure Backup bepaalt automatisch de juiste keten van volledige back-ups, differentiële back-ups en logboekback-ups die nodig zijn voor het herstellen op basis van de geselecteerde tijd.

* Herstel naar een specifieke volledige of differentiële back-up om naar een specifiek herstelpunt te herstellen.

## <a name="prerequisites"></a>Vereisten

Let op het volgende voordat u een Data Base herstelt:

* U kunt de data base alleen herstellen naar een SAP HANA exemplaar dat zich in dezelfde regio bevindt.

* Het doel exemplaar moet zijn geregistreerd bij dezelfde kluis als de bron.

* Azure Backup kan niet twee verschillende SAP HANA-exemplaren op dezelfde VM herkennen. Het terugzetten van gegevens van het ene naar het andere exemplaar op dezelfde VM is niet mogelijk.

* Om ervoor te zorgen dat het doel SAP HANA exemplaar gereed is voor herstel, controleert u de status van de **back-up** :

  1. Open de kluis waarin het doel SAP HANA exemplaar is geregistreerd.

  1. Kies **back-up** in het kluis dashboard, onder **aan** de slag.

      ![Back-up in het kluis dashboard](media/sap-hana-db-restore/getting-started-backup.png)

  1. In **back-up**, onder **waarvan wilt u een back-up maken?** Kies **SAP Hana in azure VM**.

      ![SAP HANA kiezen in een Azure-VM](media/sap-hana-db-restore/sap-hana-backup.png)

  1. Selecteer **Details weer geven** onder **Db's detecteren in vm's**.

      ![Details weergeven](media/sap-hana-db-restore/view-details.png)

  1. Bekijk de **back-upgereedheids** van de doel-VM.

      ![Beveiligde servers](media/sap-hana-db-restore/protected-servers.png)

* Raadpleeg de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de typen herstel die SAP Hana ondersteunt.

## <a name="restore-a-database"></a>Een database herstellen

Als u wilt herstellen, hebt u de volgende machtigingen nodig:

* Machtigingen voor **Back-upoperators** in de kluis waar u de herstel bewerking uitvoert.
* **Inzender (write)** toegang tot de bron-VM waarvan een back-up is gemaakt.
* **Inzender (write**) toegang tot de doel-VM:
  * Als u naar dezelfde VM wilt herstellen, is dit de bron-VM.
  * Als u naar een alternatieve locatie wilt herstellen, is dit de nieuwe doel-VM.

1. Open de kluis waarin de SAP HANA data base moet worden hersteld

1. Kies op het kluis dashboard onder **beveiligde items** **Back-upitems**

    ![Back-upitems](media/sap-hana-db-restore/backup-items.png)

1. In **Back-upitems**, onder **type back-upbeheer** selecteert u **SAP Hana in azure VM**

    ![Type back-upbeheer](media/sap-hana-db-restore/backup-management-type.png)

1. Selecteer de data base die u wilt herstellen

    ![Te herstellen data base](media/sap-hana-db-restore/database-to-restore.png)

1. Bekijk het database menu. Het bevat informatie over back-up van de data base, waaronder:

    * De oudste en meest recente herstel punten

    * De back-upstatus van het logboek voor de afgelopen 24 en 72 uur voor de data base

    ![Menu Data Base](media/sap-hana-db-restore/database-menu.png)

1. **Data Base herstellen** selecteren

1. Onder **configuratie herstellen**, geeft u op waar (of hoe) u gegevens wilt herstellen:

    * **Alternatieve locatie**: herstel de Data Base op een andere locatie en behoud de oorspronkelijke bron database.

    * **Db overschrijven**: herstel de gegevens naar hetzelfde SAP Hana exemplaar als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschreven.

      ![Configuratie herstellen](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

1. Selecteer in het menu **herstellen configuratie** , onder **waar u wilt herstellen**, **alternatieve locatie**.

    ![Herstellen naar alternatieve locatie](media/sap-hana-db-restore/restore-alternate-location.png)

1. Selecteer de SAP HANA hostnaam en de naam van het exemplaar waarnaar u de Data Base wilt herstellen.
1. Controleer of het doel SAP HANA exemplaar gereed is voor herstel door de **back-up gereedheid** te garanderen. Raadpleeg de [sectie vereisten](#prerequisites) voor meer informatie.
1. Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.

    > [!NOTE]
    > Individuele database-container (dit SDC) moet deze [controles](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)volgen.

1. Indien van toepassing, selecteert u **overschrijven als de data base met dezelfde naam al bestaat in het geselecteerde Hana-exemplaar**.
1. Selecteer **OK**.

    ![Configuratie herstellen-laatste scherm](media/sap-hana-db-restore/restore-configuration-last.png)

1. Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

1. Selecteer in het menu **configuratie herstellen** onder **herstel van herstellen** de optie **Data Base overschrijven**  >  **OK**.

    ![DB overschrijven](media/sap-hana-db-restore/overwrite-db.png)

1. Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als bestanden in plaats van een Data Base, kiest u **herstellen als bestanden**. Zodra de bestanden zijn gedumpt naar een opgegeven pad, kunt u deze bestanden naar elke SAP HANA-machine overbrengen waarop u deze als een database wilt herstellen. Omdat u deze bestanden naar een willekeurige machine kunt verplaatsen, kunt u de gegevens nu in abonnementen en regio's herstellen.

1. Selecteer in het menu **configuratie herstellen** onder **waar en hoe u kunt herstellen** de optie **herstellen als bestanden**.
1. Selecteer de **host** /Hana-server naam waarnaar u de back-upbestanden wilt herstellen.
1. Voer in het doelpad **op de server** het mappad in op de server die u hebt geselecteerd in stap 2. Dit is de locatie waar de service alle benodigde back-upbestanden zal dumpen.

    De bestanden die worden gedumpt zijn:

    * Back-upbestanden van database
    * Catalogusbestanden
    * JSON-metagegevensbestanden (voor elk back-upbestand dat betrokken is)

    Normaal gesproken maakt een netwerksharepad of pad van een gekoppelde Azure-bestandsshare indien opgegeven als doelpad toegang tot deze bestanden door andere computers in hetzelfde netwerk of met dezelfde Azure-bestandsshare die eraan is gekoppeld eenvoudiger.

    >[!NOTE]
    >Als u de back-upbestanden van de database wilt herstellen op een Azure-bestandsshare die is gekoppeld aan de geregistreerde doel-VM, moet u ervoor zorgen dat het hoofdaccount lees- en schrijfmachtigingen heeft voor de Azure-bestandsshare.

    ![Doelpad kiezen](media/sap-hana-db-restore/restore-as-files.png)

1. Selecteer het **herstel punt** dat overeenkomt met de back-upbestanden en-mappen zullen worden hersteld.

    ![Herstel punt selecteren](media/sap-hana-db-restore/select-restore-point.png)

1. Alle back-upbestanden die zijn gekoppeld aan het geselecteerde herstel punt, worden in het doelpad gedumpt.
1. Op basis van het type herstelpunt dat is gekozen (**tijdstip** of **volledig en differentieel**), worden een of meer mappen weergegeven die in het doelpad zijn gemaakt. Een van de mappen met de naam `Data_<date and time of restore>` bevat de volledige en differentiële back-ups en de andere map met de naam `Log` bevat de logboekback-ups.
1. Verplaats deze teruggezette bestanden naar de SAP HANA-server waar u ze wilt herstellen als een database.
1. Volg nu deze stappen:
    1. Stel machtigingen in voor de map waarin de back-upbestanden zijn opgeslagen met behulp van de volgende opdracht:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Voer de volgende set opdrachten uit als `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Genereer het catalogusbestand voor herstel. Extraheer de **BackupId** uit het JSON-metagegevensbestand voor de volledige back-up, die later wordt gebruikt in de herstelbewerking. Zorg ervoor dat de volledige en logboekback-ups zich in verschillende mappen bevinden en verwijder de catalogusbestanden en JSON-bestanden met metagegevens in deze mappen.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        In de bovenstaande opdracht:

        * `<DataFileDir>`: de map die de volledige back-ups bevat
        * `<LogFilesDir>`: de map die de logboekback-ups bevat
        * `<PathToPlaceCatalogFile>`: de map waarin het gegenereerde catalogusbestand moet worden geplaatst

    1. Herstel met behulp van het zojuist gegenereerde catalogusbestand via HANA Studio of voer de HDBSQL-herstelquery uit met deze zojuist gegenereerde catalogus. HDBSQL-query's worden hieronder weergegeven:

    * Ga als volgt te werk om te herstellen naar een specifiek tijdstip:

        Als u een nieuwe herstelde database maakt, voert u de HDBSQL-opdracht uit om een nieuwe database te maken `<DatabaseName>` en vervolgens stopt u de database voor herstel. Als u echter alleen een bestaande database herstelt, voert u de HDBSQL-opdracht uit om de database te stoppen.

        Voer vervolgens de volgende opdracht uit om de database te herstellen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`: de naam van de nieuwe database of bestaande database die u wilt herstellen
        * `<Timestamp>`: exacte tijdstempel van het herstel naar een bepaald tijdstip
        * `<DatabaseName@HostName>`: de naam van de database waarvan de back-up wordt gebruikt voor herstel en de **host**/SAP HANA-servernaam waarop deze database zich bevindt. Met de optie `USING SOURCE <DatabaseName@HostName>` geeft u op dat de gegevensback-up (gebruikt voor herstel) van een database met een andere SID of een andere naam dan de SAP HANA-doelmachine is. Dit hoeft dus niet te worden opgegeven voor herstel bewerkingen die zijn uitgevoerd op dezelfde HANA-server vanaf waar de back-up wordt gemaakt.
        * `<PathToGeneratedCatalogInStep3>`-Pad naar het catalogus bestand dat in **stap C** is gegenereerd
        * `<DataFileDir>`: de map die de volledige back-ups bevat
        * `<LogFilesDir>`: de map die de logboekback-ups bevat
        * `<BackupIdFromJsonFile>` -de **BackupId** geëxtraheerd in **stap C**

    * Ga als volgt te werk om te herstellen naar een bepaalde volledige of differentiële back-up:

        Als u een nieuwe herstelde database maakt, voert u de HDBSQL-opdracht uit om een nieuwe database te maken `<DatabaseName>` en vervolgens stopt u de database voor herstel. Als u echter alleen een bestaande database herstelt, voert u de HDBSQL-opdracht uit om de database te stoppen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`: de naam van de nieuwe database of bestaande database die u wilt herstellen
        * `<Timestamp>`: het exacte tijdstempel van het herstel naar een bepaald tijdstip
        * `<DatabaseName@HostName>`: de naam van de database waarvan de back-up wordt gebruikt voor herstel en de **host** /SAP HANA-servernaam waarop deze database zich bevindt. Met de optie `USING SOURCE <DatabaseName@HostName>` geeft u op dat de gegevensback-up (gebruikt voor herstel) van een database met een andere SID of een andere naam dan de SAP HANA-doelmachine is. Dit hoeft dus niet te worden opgegeven voor herstelbewerkingen die worden uitgevoerd op dezelfde HANA-server als die waarop de back-up is gemaakt.
        * `<PathToGeneratedCatalogInStep3>`-het pad naar het catalogus bestand dat in **stap C** is gegenereerd
        * `<DataFileDir>`: de map die de volledige back-ups bevat
        * `<LogFilesDir>`: de map die de logboekback-ups bevat
        * `<BackupIdFromJsonFile>` -de **BackupId** geëxtraheerd in **stap C**

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een bepaald punt in de tijd

Ga als volgt te werk als u **Logboeken** hebt geselecteerd (tijdstip) als herstel type:

1. Selecteer een herstel punt in de logboek grafiek en selecteer **OK** om het herstel punt te kiezen.

    ![Herstelpunt](media/sap-hana-db-restore/restore-point.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

    ![Selecteer herstellen](media/sap-hana-db-restore/restore-restore.png)

1. Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

    ![De herstel bewerking is geactiveerd](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Herstellen naar een specifiek herstel punt

Ga als volgt te werk als u **volledige & differentieel** hebt geselecteerd als het herstel type:

1. Selecteer een herstel punt in de lijst en selecteer **OK** om het herstel punt te kiezen.

    ![Specifiek herstel punt herstellen](media/sap-hana-db-restore/specific-recovery-point.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

    ![Herstel taak starten](media/sap-hana-db-restore/restore-specific.png)

1. Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

    ![Voortgang van herstellen](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > In meerdere database container (MDC) herstel bewerkingen nadat de systeem database is hersteld naar een doel exemplaar, moet het script vooraf worden uitgevoerd. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen. Raadpleeg voor meer informatie over het [oplossen van problemen – MDC herstellen](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Meerdere regio's herstellen

Als een van de opties voor terugzetten met behulp van cross Region Restore (CRR) kunt u SAP HANA-data bases die worden gehost op virtuele machines van Azure in een secundaire regio herstellen. Dit is een Azure-gekoppelde regio.

Lees de [sectie voordat u begint](./backup-create-rs-vault.md#set-cross-region-restore)om het onderdeel tijdens de preview-periode onboarding uit te voeren.

Als u wilt zien of CRR is ingeschakeld, volgt u de instructies in [herstel van meerdere regio's configureren](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Back-upitems in secundaire regio weer geven

Als CRR is ingeschakeld, kunt u de back-upitems in de secundaire regio weer geven.

1. Ga vanuit de portal naar **Recovery Services kluis**  >  **Back-upitems**.
1. Selecteer **secundaire regio** om de items in de secundaire regio weer te geven.

>[!NOTE]
>Alleen back-upbeheer typen die ondersteuning bieden voor de functie CRR, worden weer gegeven in de lijst. Op dit moment is alleen ondersteuning voor het herstellen van secundaire regio gegevens naar een secundaire regio toegestaan.

![Back-upitems in secundaire regio](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Data bases in secundaire regio](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Herstellen in secundaire regio

De gebruikers ervaring voor het herstellen van de secundaire regio is vergelijkbaar met de gebruikers ervaring voor het herstellen van de primaire regio. Wanneer u details in het configuratie venster voor herstellen configureert om uw herstel te configureren, wordt u gevraagd om alleen para meters van secundaire regio's op te geven.

![Waar en hoe herstellen?](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>Het virtuele netwerk in de secundaire regio moet uniek worden toegewezen en kan niet worden gebruikt voor andere Vm's in die resource groep.

![Melding activeren wordt uitgevoerd](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* Nadat de herstel bewerking is geactiveerd en in de fase voor gegevens overdracht, kan de herstel taak niet worden geannuleerd.
>* De Azure-rollen die nodig zijn voor het herstellen van de secundaire regio, zijn hetzelfde als die in de primaire regio.

### <a name="monitoring-secondary-region-restore-jobs"></a>Taken voor het herstellen van secundaire regio's bewaken

1. Ga vanuit de portal naar **Recovery Services kluis**  >  **back-uptaken**
1. Selecteer **secundaire regio** om de items in de secundaire regio weer te geven.

    ![Gefilterde back-uptaken](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het](sap-hana-db-manage.md) beheren van SAP Hana databases waarvan een back-up is gemaakt met Azure backup

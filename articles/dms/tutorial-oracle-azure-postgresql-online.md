---
title: 'Zelfstudie: Oracle online migreren naar Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een online migratie uitvoert van Oracle on-premises of op virtuele machines naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: df789161bb9db8d49f069992600b5fcb4f78dd03
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006539"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Zelfstudie: Oracle online migreren naar Azure Database for PostgreSQL met DMS (preview)

U kunt Azure Database Migration Service gebruiken om Oracle-databases die on-premises worden gehost of op virtuele machines met minimale downtime te migreren van een on-premises PostgreSQL-exemplaar naar [Azure Database for PostgreSql](../postgresql/index.yml). Met andere woorden, u kunt de migratie voltooien met minimale downtime voor de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **HR** van een on-premises exemplaar of virtuele machine met Oracle 11g naar Azure Database for PostgreSQL met behulp van de online-migratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Evalueer de migratie met het hulpprogramma ora2pg.
> * Migreer het voorbeeldschema met het hulpprogramma ora2pg.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert van Oracle naar Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [Oracle 11g Release 2 (Standard Edition, Standard Edition One, or Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Download [hier](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002) de voorbeelddatabase **HR**.
* Download en [installeer ora2pg in Windows of Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Een exemplaar maken in Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Maak verbinding met het exemplaar maak een database aan de hand van de instructie in dit [document](../postgresql/tutorial-design-database-using-azure-portal.md).
* Maak een virtueel Microsoft Azure-netwerk voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Het netwerk biedt site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

  > [!NOTE]
  > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
  >
  > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
  > * Opslageindpunt
  > * Service Bus-eindpunt
  >
  > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de regels voor netwerkbeveiligingsgroep van uw virtueel netwerk niet de volgende poorten voor inkomende communicatie naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de bronserver van Oracle. Standaard verloopt dit via TCP-poort 1521.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor Azure Database for PostgreSQL om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* Maak toegang tot de Oracle-brondatabases mogelijk.

  > [!NOTE]
  > Een gebruiker heeft de rol van DBA nodig om verbinding te kunnen maken met de Oracle-bron.

  * Archive Redo Logs is vereist voor incrementele synchronisatie in Azure Database Migration Service om gegevenswijzigingen vast te leggen. Volg deze stappen om de Oracle-bron te configureren:
    * Meld u aan met de machtiging SYSDBA door de volgende opdracht uit te voeren:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Stop het database-exemplaar door de volgende opdracht uit te voeren:

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Wacht op de bevestiging `'ORACLE instance shut down'`.

    * Start het nieuwe exemplaar en koppel de database (maar open deze nog niet) om archivering in of uit te schakelen door de volgende opdracht uit te voeren:

      ```
      STARTUP MOUNT;
      ```

      De database moet zijn gekoppeld; wacht op de bevestiging 'Oracle instance started'.

    * Wijzig de archiveringsmodus voor de database door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Open de database voor normaal gebruik door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE OPEN;
      ```

      Het kan zijn dat u opnieuw moet opstarten om het ARC-bestand te zien.

    * Controleer dit met de volgende opdracht:

      ```
      SELECT log_mode FROM v$database;
      ```

      U moet de reactie `'ARCHIVELOG'` krijgen. Als u `'NOARCHIVELOG'` ziet, is er niet voldaan aan de vereiste.

  * Schakel aanvullende logboekregistratie in voor replicatie met behulp van een van de volgende opties.

    * **Optie 1**.
      Wijzig de aanvullende logboekregistratie op databaseniveau om alle tabellen met PK's en een unieke index te omvatten. De detectiequery retourneert `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Wijzig de aanvullende logboekregistratie op databaseniveau. Alleen uitvoeren voor tabellen die gegevens bewerken en geen PK' s of unieke indexen hebben.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Optie 2**.
      Wijzig de aanvullende logboekregistratie op databaseniveau om alle tabellen te omvatten, en de detectiequery retourneert `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Wijzig de aanvullende logboekregistratie op tabelniveau. Volg de onderstaande logica om slechts één instructie voor elke tabel uit te voeren.

      Als de tabel een primaire sleutel heeft:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Als de tabel een unieke index heeft:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Voer anders de volgende opdracht uit:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Controleer dit met de volgende opdracht:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    U moet de reactie `'YES'` krijgen.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>De inspanningen evalueren voor een migratie van Oracle naar Azure Database for PostgreSQL

We raden aan om ora2pg te gebruiken om de inspanningen te evalueren die nodig zijn om te migreren van Oracle naar Azure Database for PostgreSQL. Gebruik de instructie `ora2pg -t SHOW_REPORT` voor het maken van een rapportoverzicht met alle Oracle-objecten, de geschatte migratiekosten (in ontwikkeldagen) en bepaalde databaseobjecten die mogelijk speciale aandacht vereisen als onderdeel van de conversie.

De meeste klanten besteden aardig wat tijd aan het doornemen van het evaluatierapport en het maken van een keuze tussen automatische en handmatige conversie.

Als u ora2pg wilt configureren en uitvoeren om een evaluatierapport te maken, raadpleegt u de sectie **Premigration: Assessment** van [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). U vindt [hier](https://ora2pg.darold.net/report.html) een voorbeeld van een ora2pg-evaluatierapport ter referentie.

## <a name="export-the-oracle-schema"></a>Het Oracle-schema exporteren

We raden u aan om ora2pg te gebruiken om het Oracle-schema en andere Oracle-objecten (typen, procedures, functies, enzovoort) te converteren naar een schema dat compatibel is met Azure Database for PostgreSQL. ora2pg bevat veel instructies om u te helpen bij het vooraf definiëren van bepaalde gegevenstypen. U kunt bijvoorbeeld de instructie `DATA_TYPE` gebruiken om alle NUMBER(*,0) te vervangen door bigint in plaats van NUMERIC(38).

U kunt ora2pg uitvoeren om de databaseobjecten in .sql-bestanden te exporteren. Vervolgens kunt u de .sql-bestanden bekijken voordat u ze met psql importeert in Azure Database for PostgreSQL of u kunt het .SQL-script in PgAdmin uitvoeren.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Bijvoorbeeld:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Als u ora2pg wilt configureren en uitvoeren voor schemaconversie, raadpleegt u de sectie **Migration: Schema and data** van [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Het schema instellen in Azure Database for PostgreSQL

U kunt ervoor kiezen om tabelschema's, opgeslagen procedures, pakketten en andere databaseobjecten van Oracle te converteren om ze compatibel met Postgres te maken. Dit kan met behulp van ora2pg voordat u een migratiepijplijn start in Azure Database Migration Service. Volg de onderstaande links voor meer informatie over het werken met ora2pg:

* [Ora2pg installeren in Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle to Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kan ook het PostgreSQL-tabelschema maken. De service opent het tabelschema in de verbonden Oracle-bron en maakt een compatibel tabelschema in Azure Database for PostgreSQL. Zorg ervoor dat u de schema-indeling valideert en controleert in Azure Database for PostgreSQL als Azure Database Migration Service klaar is met het maken van het schema en het verplaatsen van de gegevens.

> [!IMPORTANT]
> Azure Database Migration Service maakt alleen het tabelschema. Andere databaseobjecten, zoals opgeslagen procedures, pakketten en indexen, worden niet gemaakt.

Vergeet ook niet om de refererende sleutel in de doeldatabase te verwijderen om deze full load uit te voeren. Raadpleeg de sectie **Het voorbeeldschema migreren** van [dit artikel](./tutorial-postgresql-azure-postgresql-online.md) voor een script dat u kunt gebruiken om de refererende sleutel te verwijderen. Gebruik Azure Database Migration Service voor uitvoeren met full load en synchronisatie.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Wanneer het PostgreSQL-tabelschema al bestaat

Als u een PostgreSQL-schema maakt met behulp van hulpprogramma's zoals ora2pg voordat u begint met het verplaatsen van gegevens met Azure Database Migration Service, kunt u de brontabellen toewijzen aan de doeltabellen in Azure Database Migration Service.

1. Wanneer u een nieuw project maakt voor een migratie van Oracle naar Azure Database for PostgreSQL, wordt u gevraagd om de doeldatabase en het doelschema te selecteren in de stap Schema's selecteren. Vul de doeldatabase en het doelschema in.

   ![Schermopname met Kaart om databases te selecteren.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. In het scherm **Migratie** ziet u een lijst met tabellen uit de Oracle-bron. Azure Database Migration Service probeert tabellen in de bron- en doeltabellen op basis van tabelnaam te koppelen. Als er meerdere doeltabellen bestaan met dezelfde naam maar met een ander hoofdlettergebruik, kunt u de doeltabel selecteren die u wilt gebruiken.

    ![Schermopname met instellingen voor Migratie.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Als u namen van brontabellen wilt koppelen aan tabellen met andere namen, stuurt u een e-mail naar [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) en krijgt u van ons een script om het proces te automatiseren.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Wanneer het PostgreSQL-tabelschema niet bestaat

Als de doel-PostgreSQL-database geen gegevens van een tabelschema bevat, converteert Azure Database Migration Service het bronschema en wordt het schema opnieuw gemaakt in de doeldatabase. Zoals gezegd, maakt Azure Database Migration Service maakt alleen het tabelschema. Er worden geen andere databaseobjecten gemaakt, zoals opgeslagen procedures, pakketten en indexen.
Als u Azure Database Migration Service het schema voor u wilt laten maken, moet u ervoor zorgen dat uw doelomgeving een schema bevat zonder bestaande tabellen. Als Azure Database Migration Service een tabel detecteert, gaat de service ervan uit dat het schema is gemaakt door een extern hulpprogramma zoals ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service vereist dat alle tabellen op dezelfde manier worden gemaakt, met behulp van Azure Database Migration Service of met een hulpprogramma zoals ora2pg, maar niet een mix.

Aan de slag:

1. Maak een schema in de doeldatabase op basis van uw toepassingsvereisten. Namen van tabelschema's en kolommen van PostgreSQL bestaan standaard uit kleine letters. In het geval van Oracle worden daarentegen standaard allemaal hoofdletters gebruikt.
2. Geef in de stap Schema's selecteren de doeldatabase en het doelschema op.
3. Op basis van het schema dat u in Azure Database for PostgreSQL maakt, gebruikt Azure Database Migration Service de volgende transformatieregels:

    Als de schemanaam in de Oracle-bron overeenkomt met de schemanaam in Azure Database for PostgreSQL, *maakt Azure Database Migration Service het tabelschema gemaakt met het hoofdlettergebruik van het doelschema*.

    Bijvoorbeeld:

    | Bronschema in Oracle | PostgreSQL Database.Schema | Door DMS gemaakt schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.HR | "HR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.Hr | *Kan geen namen met gemengd hoofdlettergebruik toewijzen |

    *Als u in PostgreSQL gebruik wilt maken van een mix van hoofdletters en kleine letters voor schema- en tabelnamen, neemt u contact op met [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com). U krijgt dan van ons een script voor het instellen van een schema met gemengd hoofdlettergebruik in de doel-PostgreSQL-database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de brondatabase van Oracle en het doelexemplaar van Azure Database for PostgreSQL.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

    ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **Oracle** en selecteer in het tekstvak **Doelservertype** de optie **Azure Database for PostgreSQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

   ![Azure Database Migration Service-project maken](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan**, lees de vereisten om met Azure Database Migration Service een onlinemigratie uit te voeren en selecteer vervolgens **Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van Oracle.

  ![Scherm Brondetails toevoegen](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI-stuurprogramma uploaden

1. Selecteer **Opslaan**, meld u op het scherm **OCI-stuurprogramma installeren** aan bij uw Oracle-account en download het stuurprogramma **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 Checksum: 865082268) vanaf [deze locatie](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Download het stuurprogramma naar een gedeelde map.

   Zorg ervoor dat de map wordt gedeeld met de gebruikersnaam die u hebt opgegeven met minimale alleen-lezen toegang. Azure Database Migration Service gaat naar de share en uploadt het OCI-stuurprogramma naar Azure door de gebruikersnaam te imiteren die u opgeeft.

   De gebruikersnaam die u opgeeft, moet de naam zijn van een Windows-gebruikersaccount.

   ![OCI-stuurprogramma installeren](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Doeldetails** de verbindingsgegevens op voor de doelserver van Azure Database for PostgreSQL. Dit is het vooraf ingerichte exemplaar van Azure Database for PostgreSQL waarnaar het schema **HR** is geïmplementeerd.

    ![Scherm Doeldetails](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

  Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

     ![Activiteitsstatus is Actief](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Selecteer onder **Databasenaam** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    ‘Alle gegevens worden geladen’ toont de migratiestatus van de eerste lading terwijl ‘Incrementele gegevenssynchronisatie’ de CDC-status (Change Data Capture) toont.

     ![Activiteitenstatus: Volledig geladen](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Activiteitenstatus: Incrementele gegevenssynchronisatie](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.

   ![Cutover starten](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de databasemigratie de status **Voltooid** heeft, verbindt u uw toepassingen met het nieuwe doelexemplaar van Azure Database for PostgreSQL.

 > [!NOTE]
 > Omdat in PostgreSQL schema.table.column standaard is ingesteld op kleine letters, kunt u hoofdletters omzetten in kleine letters met behulp van het script uit de sectie **Het schema instellen in Azure Database for PostgreSQL** eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Zie het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for PostgreSQL?](../postgresql/overview.md) voor informatie over Azure Database for PostgreSQL.
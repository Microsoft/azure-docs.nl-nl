---
title: 'Zelfstudie: MySQL online migreren naar Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een onlinemigratie uitvoert van MySQL on-premises naar Azure Database for MySQL met behulp van Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817952"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelfstudie: MySQL online migreren naar Azure Database for MySQL met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de databases met minimale downtime te migreren van een on-premises MySQL-instantie naar [Azure Database for MySQL](../mysql/index.yml). Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **Werknemers** van een on-premises exemplaar van MySQL 5.7 naar Azure Database for MySQL met behulp van een onlinemigratieactiviteit in Azure Database Migration Service.

> [!IMPORTANT]
> Het onlinemigratiescenario MySQL to Azure Database for MySQL wordt op 1 juni 2021 vervangen door een ge parallelliseerde, zeer goed presterende offlinemigratiescenario. Voor onlinemigraties kunt u deze nieuwe aanbieding gebruiken samen met [replicatie van binnenkomende gegevens.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) U kunt ook opensource-hulpprogramma's zoals [MyDumper/MyLoader](https://centminmod.com/mydumper.html) gebruiken met replicatie van binnenkomende gegevens voor onlinemigraties. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Het voorbeeldschema migreren met behulp van het hulpprogramma mysqldump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

> [!NOTE]
> Oordeelloze communicatie
>
> Microsoft biedt ondersteuning voor een gevarieerde en insluitende omgeving. Dit artikel bevat verwijzingen naar het woord _slaaf_. In de [stijlgids voor oordeelloze communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) wordt dit woord herkend als uitsluitend. Het woord wordt in dit artikel gebruikt voor consistentie, omdat het momenteel het woord is dat wordt weergegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel ook bijgewerkt zodat het is afgestemd.
>


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [MySQL-communityversie](https://dev.mysql.com/downloads/mysql/) 5.6 of 5.7. De on-premises MySQL-versie moet overeenkomen met de Azure Database for MySQL-versie. MySQL 5.6 kan bijvoorbeeld alleen migreren naar Azure Database for MySQL 5.6 en kan niet worden geüpgraded naar 5.7. Migraties naar of van MySQL 8.0 worden niet ondersteund.
* [Maak een exemplaar in Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Raadpleeg het artikel [MySQL Workbench](../mysql/connect-workbench.md) gebruiken om verbinding te maken en gegevens op te vragen voor meer informatie over het maken en verbinden van een database met behulp van de Workbench-toepassing.  
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit biedt site-naar-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de regels voor de netwerkbeveiligingsgroep van uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel de Windows-firewall open voor toegang van Azure Database Migration Service tot de brondatabase van MySQL. Standaard is dit TCP-poort 3306.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor Azure Database for MySQL om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* De brondatabase van MySQL moet op een ondersteunde MySQL-communityversie zijn. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.

* Schakel binaire logboekregistratie in het bestand my.ini (Windows) of my.cnf (Unix) in de brondatabase in met behulp van de volgende configuratie:

  * **server_id** = 1 of hoger (alleen relevant voor MySQL 5.6)
  * **log-bin** =\<path> (alleen relevant voor MySQL 5.6)    Bijvoorbeeld: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (het wordt aanbevolen om niet nul te gebruiken; alleen relevant voor MySQL 5.6)
  * **Binlog_row_image** = full (alleen relevant voor MySQL 5.6)
  * **log_slave_updates** = 1

* De gebruiker moet beschikken over de rol ReplicationAdmin met de volgende bevoegdheden:

  * **REPLICATIECLIENT**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **REPLICATIEREPLICA**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **SUPER**: alleen vereist in eerdere versies dan MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen. Om het schema te extraheren, kunt u mysqldump gebruiken met de parameter `--no-data`.

Ervan uitgaande dat de MySQL-voorbeelddatabase **Werknemers** in het on-premises systeem staat, is de opdracht voor de schemamigratie met behulp van mysqldump als volgt:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Bijvoorbeeld:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Voer de volgende opdracht uit om het schema te importeren in de Azure Database for MySQL-doeldatabase:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Bijvoorbeeld:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie.  Voer het volgende script uit in MySQL Workbench om het script voor verwijdering van refererende sleutels en het script voor toevoeging van refererende sleutels te extraheren.

```sql
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Voer het script voor verwijdering van refererende sleutels (de tweede kolom) in het queryresultaat uit om de refererende sleutel te verwijderen.

> [!NOTE]
> Azure DMS biedt geen ondersteuning voor de referentiële actie CASCADE, waarmee u een overeenkomende rij in de onderliggende tabel automatisch kunt verwijderen of bijwerken wanneer een rij wordt verwijderd of bijgewerkt in de bovenliggende tabel. Zie de sectie Referentiële acties in het artikel [Beperkingen voor REFERERENDE SLEUTELS](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html) voor meer informatie.
> Voor Azure DMS moet u beperkingen voor refererende sleutels in de doeldatabaseserver weghalen tijdens het initieel laden van gegevens, en kunt u geen referentiële acties gebruiken. Als uw workload afhankelijk is van het bijwerken van een gerelateerde onderliggende tabel via deze referentiële actie, raden we u aan om in plaats hiervan een [dump en herstel](../mysql/concepts-migrate-dump-restore.md)-bewerking uit te voeren. 


> [!IMPORTANT]
> Als u gegevens importeert met behulp van een back-up, verwijdert u de opdrachten CREATE DEFINER handmatig of met de opdracht --skip-definer bij het uitvoeren van een mysqldump. DEFINER heeft superbevoegdheden nodig voor maken en is beperkt in Azure Database for MySQL.

Als de database triggers bevat, wordt de gegevensintegriteit in het doel afgedwongen, voorafgaand aan de volledige gegevensmigratie van de bron. Het wordt aanbevolen triggers uit te schakelen voor alle tabellen in het doel tijdens de migratie en de triggers in te schakelen nadat de migratie is uitgevoerd.

Voer het volgende script uit in MySQL Workbench op de doeldatabase om het triggerscript voor neerzetten te extraheren en een triggerscript toe te voegen.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Voer de gegenereerde drop trigger-query (kolom DropQuery) uit in het resultaat om triggers in de doeldatabase te verwijderen. De triggerquery toevoegen kan worden opgeslagen om te worden gebruikt na voltooiing van de gegevensmigratie.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Een Database Migration Service maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een prijscategorie en ga naar het netwerkscherm. De mogelijkheid voor offlinemigratie is beschikbaar in de prijscategorie Standard en Premium.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Basisinstellingen Azure Database Migration Service configureren](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Selecteer een bestaand virtueel netwerk in de lijst of geef de naam op van het nieuwe virtuele netwerk dat moet worden gemaakt. Ga naar het scherm Beoordelen en maken. U kunt eventueel tags toevoegen aan de service met behulp van het tagsscherm.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel-Azure SQL Database-exemplaar.

    ![Netwerkinstellingen Azure Database Migration Service configureren](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Controleer de configuraties en selecteer **Maken om** de service te maken.
    
    ![Azure Database Migration Service maken](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.  

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Selecteer uw migration service-exemplaar in de zoekresultaten en selecteer + **Nieuw migratieproject.**
    
    ![Een nieuw migratieproject maken](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Geef in het scherm Nieuw **migratieproject** een naam  op voor het project, selecteer in  het selectievak Bronservertype de optie **MySQL,** selecteer in het selectievak Doelservertype de optie **Azure Database For MySQL** en selecteer in het selectievak Migratieactiviteittype de optie **Onlinegegevensmigratie.**  Selecteer **Activiteit maken en uitvoeren**.

    ![Azure Database Migration Service-project maken](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

## <a name="configure-migration-project"></a>Migratieproject configureren

1. Geef in **het scherm Bron** selecteren de verbindingsgegevens op voor het bron-MySQL-exemplaar en selecteer **Volgende: Doel selecteren>>**

    ![Scherm Brondetails toevoegen](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. Geef in **het scherm** Doel selecteren de verbindingsgegevens op voor het doel-Azure Database for MySQL en selecteer **Volgende: Databases selecteren>>**

    ![Scherm Doeldetails toevoegen](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Wijs in **het scherm Databases** selecteren de bron- en doeldatabase toe voor migratie en selecteer **Volgende: Migratie-instellingen configureren>>**. U kunt de optie **Bronserver alleen-lezen** maken selecteren om de bron als alleen-lezen te maken, maar wees voorzichtig dat dit een instelling op serverniveau is. Als deze optie is geselecteerd, wordt de hele server ingesteld op alleen-lezen, niet alleen op de geselecteerde databases.
    
    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.
    ![Scherm Databasedetails selecteren](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Hoewel u meerdere databases kunt selecteren in deze stap, biedt elke instantie van Azure Database Migration Service ondersteuning voor maximaal vier databases voor gelijktijdige migratie. Ook is er een limiet van tien Azure Database Migration Service-instanties per abonnement per regio. Als u bijvoorbeeld 80 databases hebt om te migreren, kunt u 40 hiervan gelijktijdig migreren naar dezelfde regio, maar alleen als u tien Azure Database Migration Service-instanties hebt gemaakt.

4. Selecteer in **het scherm Migratie-instellingen** configureren de tabellen die deel moeten uitmaken van de migratie en selecteer **Volgende: Samenvatting>>.** Als de doeltabellen gegevens bevatten, worden ze niet standaard geselecteerd, maar u kunt ze expliciet selecteren en worden ze afgekapt voordat de migratie wordt begonnen.

    ![Scherm Tabellen selecteren](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Geef op **het** scherm  Samenvatting in het tekstvak Activiteitsnaam een naam op voor de migratieactiviteit en controleer de samenvatting om ervoor te zorgen dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Overzicht van migratieproject](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Selecteer **Migratie starten**. Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**. De **Status** wordt gewijzigd **in Wordt uitgevoerd** wanneer de tabelmigraties starten.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Activiteitenstatus: Voltooid](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Selecteer onder **Databasenaam** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    ‘Alle gegevens worden geladen’ toont de migratiestatus van de eerste lading terwijl ‘Incrementele gegevenssynchronisatie’ de CDC-status (Change Data Capture) toont.

     ![Activiteitenstatus: Volledig geladen](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Activiteitenstatus: Incrementele gegevenssynchronisatie](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Cutover starten](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de databasemigratiestatus **Voltooid** toont, verbindt u uw toepassingen met de nieuwe doel-Azure SQL Database.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for MySQL](known-issues-azure-mysql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for MySQL.
* Zie het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for MySQL?](../mysql/overview.md) voor informatie over Azure Database for MySQL.

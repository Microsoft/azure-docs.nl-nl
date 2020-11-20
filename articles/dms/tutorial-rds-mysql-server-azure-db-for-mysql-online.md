---
title: 'Zelfstudie: RDS MySQL online migreren naar Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een online migratie uitvoert van RDS MySQL naar Azure Database for MySQL met behulp van Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: b8d5c763b68a9f69add14ab8430c117e5705a515
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955086"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelfstudie: RDS MySQL online migreren naar Azure Database for MySQL met behulp van DMS

U kunt Azure Database Migration Service gebruiken om databases met minimale downtime te migreren van een RDS MySQL-exemplaar naar [Azure Database for MySQL](../mysql/index.yml) terwijl de brondatabase online blijft tijdens de migratie. Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **Werknemers** van een on-premises exemplaar van RDS MySQL naar Azure Database for MySQL met behulp van de online migratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met de hulpprogramma's mysqldump en mysql.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert van een exemplaar van RDS MySQL naar Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Zorg ervoor dat op de MySQL-bronserver een ondersteunde MySQL-community-editie wordt uitgevoerd. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench deze opdracht uit:

    ```
    SELECT @@version;
    ```

    Zie het artikel [Ondersteunde versies van Azure Database for MySQL](../mysql/concepts-supported-versions.md) voor meer informatie.

* Download en installeer de [MySQL **-** voorbeelddatabase](https://dev.mysql.com/doc/employee/en/employees-installation.html) Werknemers.
* Maak een exemplaar van [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.
* Zorg ervoor dat de regels voor netwerkbeveiligingsgroep van uw virtueel netwerk niet de volgende poorten voor inkomende communicatie naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445 en 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (of uw Linux-firewall) voor toegang tot de database-engine. Sta poort 3306 toe voor connectiviteit voor de MySQL-server.

> [!NOTE]
> Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.

### <a name="set-up-aws-rds-mysql-for-replication"></a>AWS RDS MySQL instellen voor replicatie

1. Als u een nieuwe parametergroep wilt maken, volgt u de instructies van AWS in het artikel [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) (MySQL-databaselogboekbestanden) in de sectie **Binary Logging Format** (Indeling voor binaire logboekregistratie).
2. Maak een nieuwe parametergroep met de volgende configuratie:
    * log_bin = ON
    * binlog_format = row
    * binlog_checksum = NONE
3. Sla de nieuwe parametergroep op.
4. Koppel de nieuwe parametergroep aan het RDS MySQL-exemplaar. Opnieuw opstarten kan nodig zijn.
5. Nadat de parametergroep is ingesteld, maakt u verbinding met het MySQL-exemplaar en [stelt u de retentieperiode van het binaire logbestand (binlog)](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) op ten minste vijf dagen in.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Het schema migreren

1. Extraheer het schema uit de brondatabase en pas het toe op de doeldatabase om de migratie van alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien.

    De eenvoudigste manier om alleen het schema te migreren, is mysqldump te gebruiken met de parameter --no-data. De opdracht voor het migreren van het schema is:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Gebruik bijvoorbeeld de volgende opdracht om een schemabestand te dumpen voor de database **Werknemers**:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importeer het schema in de doelservice, Azure Database for MySQL. Voer de volgende opdracht uit om het schemadumpbestand te herstellen:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Als u bijvoorbeeld het schema voor de database **Werknemers** wilt importeren, gebruikt u:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Voer het volgende script uit in MySQL Workbench om het 'drop foreign key'-script uit te pakken en voeg het 'foreign key'-script toe op de doeldatabase (Azure Database for MySQL):

    ```
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

4. Voer het script voor verwijdering van refererende sleutels (de tweede kolom) in het queryresultaat uit om de refererende sleutel te verwijderen.

> [!NOTE]
> Azure DMS biedt geen ondersteuning voor de referentiële actie CASCADE, waarmee u een overeenkomende rij in de onderliggende tabel automatisch kunt verwijderen of bijwerken wanneer een rij wordt verwijderd of bijgewerkt in de bovenliggende tabel. Zie de sectie Referential Actions (Referentiële acties) in het artikel [FOREIGN KEY Constraints](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html) (beperkingen voor refererende sleutels) voor meer informatie.
> Voor Azure DMS moet u beperkingen voor refererende sleutels in de doeldatabaseserver weghalen tijdens het initieel laden van gegevens en kunt u geen referentiële acties gebruiken. Als uw werkbelasting afhankelijk is van het bijwerken van een gerelateerde onderliggende tabel via deze referentiële actie, raden we u aan om in plaats daarvan een [dump en herstel](../mysql/concepts-migrate-dump-restore.md)-bewerking uit te voeren. 

5. Als u een triggers (invoegen of bijwerken) in de gegevens hebt, dwingt deze gegevensintegriteit in de doeldatabase af vóór de gerepliceerde gegevens uit de brondatabase. Het is raadzaam triggers in alle tabellen *in de doeldatabase* uit te schakelen tijdens de migratie, en de triggers dan weer in te schakelen zodra de migratie is voltooid.

    Triggers in de doeldatabase uitschakelen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Als er instanties van tabellen zijn die ENUM-gegevenstypen bevatten, wordt u geadviseerd om deze in de doeltabel tijdelijk bij te werken naar een 'character varying'-gegevenstype (teken variërend op doel). Wanneer de gegevensreplicatie is voltooid, wijzigt u het gegevenstype weer in ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot het bronexemplaar van MySQL en het doelexemplaar van Azure Database for MySQL.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijscategorie. Zorg dat u Premium selecteert voor deze onlinemigratie: Prijscategorie 4vCores.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

     ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **bronservertype** de optie **MySQL** en selecteer vervolgens in het tekstvak **doelservertype** de optie **AzureDbForMySQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    > [!IMPORTANT]
    > Zorg ervoor dat u **Onlinegegevensmigratie** selecteert. Offlinemigraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan**.

7. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Noteer de vereisten voor het instellen van de onlinemigratie in de blade voor het maken van het project.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **Gegevens migratiebron** de verbindingsgegevens op voor het bronexemplaar van MySQL.

   ![Brondetails](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Doeldetails** de verbindingsgegevens op voor de Azure Database for MySQL-doelserver. Dit is het vooraf ingerichte exemplaar waarin het schema **Werknemers** is geïmplementeerd met behulp van mysqldump.

    ![Doel selecteren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus is Actief](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Selecteer onder **DATABASENAAM** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    **Alle gegevens worden geladen** toont de migratiestatus van de eerste lading terwijl **Incrementele gegevenssynchronisatie** de CDC-status (Change Data Capture) toont.

    ![Inventarisatiescherm - alle gegevens laden](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Inventarisatiescherm - incrementele gegevenssynchronisatie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige laadbewerking is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Cutover starten](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de databasemigratie de status **Voltooid** heeft, verbindt u uw toepassingen met het nieuwe doelexemplaar van Azure Database for MySQL.

Uw onlinemigratie van een on-premises exemplaar van MySQL naar Azure Database for MySQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for MySQL?](../mysql/overview.md) voor informatie over Azure Database for MySQL.
* Voor andere vragen stuurt u een e-mail naar de alias voor [vragen over Azure-databasemigraties](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
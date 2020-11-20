---
title: 'Zelfstudie: RDS PostgreSQL online migreren naar Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een online migratie uitvoert van RDS PostgreSQL naar Azure Database for PostgreSQL met behulp van de Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 3bd79dc4935f5dfeb65d80ada544139dc88e129c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954950"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Zelfstudie: RDS PostgreSQL online migreren naar Azure Database for PostgreSQL met behulp van DMS

U kunt Azure Database Migration Service gebruiken om databases met minimale downtime te migreren van een RDS PostgreSQL-exemplaar naar [Azure Database for PostgreSQL](../postgresql/index.yml) terwijl de brondatabase online blijft tijdens de migratie. Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **DVD Rental** van een exemplaar van RDS PostgreSQL 9.6 naar Azure Database for PostgreSQL met behulp van een onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met behulp van het hulpprogramma pg_dump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.
> * Voer de migratie-cutover uit.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie. De schijf wordt versleuteld om diefstal van gegevens tijdens het migratieproces te voorkomen.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert vanaf een on-premises exemplaar van PostgreSQL naar Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.5.11, 9.6.7, 10 of hoger zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).

   Houd er ook rekening mee dat de doelversie van Azure Database for PostgreSQL gelijk moet zijn aan of hoger moet zijn dan de RDS PostgreSQL-versie. RDS PostgreSQL 9.6 kan bijvoorbeeld alleen worden gemigreerd naar Azure Database for PostgreSQL 9.6, 10 of 11 maar niet naar Azure Database for PostgreSQL 9.5.

* Maak een exemplaar van [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) of [Azure Database for PostgreSQL - Hyperscale (Citus)](../postgresql/quickstart-create-hyperscale-portal.md). Raadpleeg deze [sectie](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) van het document voor meer informatie over het verbinding maken met de PostgreSQL-server met behulp van pgAdmin.
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.
* Zorg ervoor dat de regels voor netwerkbeveiligingsgroep van uw virtueel netwerk niet de volgende poorten voor inkomende communicatie naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445 en 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de oorspronkelijke PostgreSQL Server. Standaard verloopt dit via TCP-poort 5432.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor de Azure Database for PostgreSQL-server om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>AWS RDS PostgreSQL instellen voor replicatie

1. Als u een nieuwe parametergroep wilt maken, volgt u de instructies van AWS in het artikel [Working with DB Parameter Groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html) (Met DB-parametergroepen werken).
2. Gebruik de naam van de hoofdgebruiker om vanuit Azure Database Migration Service verbinding te maken met de bron. Als u een ander account dan het hoofdgebruikersaccount gebruikt, moet het account beschikken over de rollen rds_superuser en rds_replication. De rol rds_replication verleent machtigingen voor het beheren van logische sleuven en het streamen van gegevens met behulp van logische sleuven.
3. Maak een nieuwe parametergroep met de volgende configuratie:

    a. Stel de parameter rds.logical_replication in uw DB-parametergroep in op 1.

    b. max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. 10 taken wordt aanbevolen.

    c. max_replication_slots – = [aantal sleuven]. Aanbevolen instelling is vijf sleuven.

4. Koppel de parametergroep die u hebt gemaakt aan het RDS PostgreSQL-exemplaar.

## <a name="migrate-the-schema"></a>Het schema migreren

1. Extraheer het schema uit de brondatabase en pas het toe op de doeldatabase om de migratie van alle databaseobjecten zoals tabelschema's, indexen en opgeslagen procedures te voltooien.

    De eenvoudigste manier om alleen het schema te migreren, is pg_dump te gebruiken met de optie -s. Zie de [voorbeelden](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) in de zelfstudie voor Postgres pg_dump voor meer informatie.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Gebruik bijvoorbeeld de volgende opdracht om een schemabestand te dumpen voor de database **dvdrental**:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Maak een lege database in de doelservice (Azure Database for PostgreSQL). Raadpleeg een van de volgende artikelen als u een database wilt maken en koppelen:

    * [Een Azure Database for PostgreSQL-server maken met behulp van de Azure-portal](../postgresql/quickstart-create-server-database-portal.md)
    * [Een Azure Database for PostgreSQL - Hyperscale (Citus)-server maken met behulp van de Azure-portal](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importeer het schema naar de doelservice (Azure Database for PostgreSQL). Voer de volgende opdracht uit om het schemadumpbestand te herstellen:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Voer het volgende script uit in PgAdmin of psql om het 'drop foreign key'-script uit te pakken en het 'foreign key'-script toe te voegen op de bestemming (Azure Database for PostgreSQL):
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Voer het script voor verwijdering van refererende sleutels (de tweede kolom) in het queryresultaat uit om de refererende sleutel te verwijderen.

6. Als de gegevens triggers (invoegen of bijwerken) bevatten, dwingen deze gegevensintegriteit in de doeldatabase af voordat er gegevens worden gerepliceerd vanuit de brondatabase. Het is raadzaam triggers in alle tabellen *in de doeldatabase* uit te schakelen tijdens de migratie, en de triggers dan weer in te schakelen zodra de migratie is voltooid.

    Triggers in de doeldatabase uitschakelen:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot het bronexemplaar van PostgreSQL en het doelexemplaar van Azure Database for PostgreSQL.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijscategorie. Zorg dat u Premium selecteert voor deze onlinemigratie: Prijscategorie 4vCores.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Services** naar de naam van het Azure Database Migration Service-exemplaar dat u hebt gemaakt, selecteer deze en selecteer vervolgens + **Nieuw migratieproject**.
3. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **AWS RDS voor PostgreSQL** en selecteer in het tekstvak **Doelservertype** de optie **Azure Database for PostgreSQL**.
4. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    > [!IMPORTANT]
    > Zorg ervoor dat u **Onlinegegevensmigratie** selecteert. Offlinemigraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

5. Selecteer **Opslaan**.

6. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Noteer de vereisten voor het instellen van de onlinemigratie in de blade voor het maken van het project.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van PostgreSQL.

   ![Brondetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Doeldetails** de verbindingsgegevens op voor de Azure Database for PostgreSQL-doelserver. Dit is het vooraf ingerichte exemplaar waarin het schema **DVD Rentals** is geïmplementeerd met behulp van pg_dump.

    ![Doeldetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus is Actief](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Selecteer onder **DATABASENAAM** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    **Alle gegevens worden geladen** toont de migratiestatus van de eerste lading terwijl **Incrementele gegevenssynchronisatie** de CDC-status (Change Data Capture) toont.

    ![Inventarisatiescherm - alle gegevens laden](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Inventarisatiescherm - incrementele gegevenssynchronisatie](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige laadbewerking is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht tot de teller **Wijzigingen in behandeling** op **0** staat om er zeker van te zijn dat alle inkomende transacties naar de brondatabase zijn gestopt. Schakel dan het selectievakje **Bevestigen** in en selecteer **Toepassen**.

    ![Scherm Cutover voltooien](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Wanneer de databasemigratie de status **Voltooid** heeft, verbindt u uw toepassingen met de nieuwe doeldatabase voor Azure Database for PostgreSQL.

Uw onlinemigratie van een on-premises exemplaar van RDS PostgreSQL naar Azure Database for PostgreSQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for PostgreSQL?](../postgresql/overview.md) voor informatie over Azure Database for PostgreSQL.
* Voor andere vragen stuurt u een e-mail naar de alias voor [vragen over Azure-databasemigraties](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
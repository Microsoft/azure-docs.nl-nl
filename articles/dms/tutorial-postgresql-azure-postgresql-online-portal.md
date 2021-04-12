---
title: 'Zelfstudie: PostgreSQL online migreren naar Azure DB for PostgreSQL via de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een onlinemigratie uitvoert van PostgreSQL on-premises naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service en de Azure-portal.
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
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709917"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Zelfstudie: PostgreSQL online migreren naar Azure DB for PostgreSQL met behulp van DMS en de Azure-portal

U kunt Azure Database Migration Service gebruiken om de databases met minimale downtime voor de toepassing te migreren van een on-premises PostgreSQL-exemplaar naar [Azure Database for MySQL](../postgresql/index.yml). In deze zelfstudie migreert u de voorbeelddatabase **DVD Rental** van een on-premises exemplaar van PostgreSQL 9.6 naar Azure Database for PostgreSQL met behulp van een onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met behulp van het hulpprogramma pg_dump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject in Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.
> * Voer de migratie-cutover uit.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. De schijf wordt versleuteld om diefstal van gegevens tijdens het migratieproces te voorkomen.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL community edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.4, 9.5, 9.6, 10 of 11 zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).

    Houd er ook rekening mee dat de doelversie van Azure Database for PostgreSQL gelijk moet zijn aan of hoger moet zijn dan de on-premises PostgreSQL-versie. PostgreSQL 9.6 kan bijvoorbeeld alleen worden gemigreerd naar Azure Database for PostgreSQL 9.6, 10 of 11 maar niet naar Azure Database for PostgreSQL 9.5.

* [Maak een Azure Database for PostgreSQL-server](../postgresql/quickstart-create-server-database-portal.md) of [maak een Azure Database for PostgreSQL - Hyperscale (Citus)-server](../postgresql/quickstart-create-hyperscale-portal.md).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de NSG-regels (netwerk beveiligings groep) voor uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blok keren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de oorspronkelijke PostgreSQL Server. Standaard verloopt dit via TCP-poort 5432.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewallregel](../postgresql/concepts-firewall-rules.md) op serverniveau voor Azure Database for PostgreSQL om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* Schakel logische replicatie in het bestand postgresql.config in en stel de volgende parameters in:

  * wal_level = **logical**
  * max_replication_slots = [aantal sleuven], aanbevolen instelling is **vijf sleuven**
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

> [!IMPORTANT]
> Alle tabellen in uw bestaande database hebben een primaire sleutel nodig om ervoor te zorgen dat de wijzigingen kunnen worden gesynchroniseerd met de doeldatabase.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen.

1. Gebruik de opdracht pg_dump -s om een dumpbestand van het schema te maken voor een database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Als u bijvoorbeeld een schema-dumpbestand wilt maken voor de database **dvdrental**:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Zie voor meer informatie over het gebruik van het hulpprogramma pg_dump de voorbeelden in de zelfstudie [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Maak een lege database maken in uw doelomgeving, dit is Azure Database for PostgreSQL.

    Raadpleeg het artikel [Create an Azure Database for PostgreSQL server in the Azure portal](../postgresql/quickstart-create-server-database-portal.md) (Een Azure Database for PostgreSQL-server maken in Azure Portal) of [Create an Azure Database for PostgreSQL - Hyperscale (Citus) server in the Azure portal](../postgresql/quickstart-create-hyperscale-portal.md) (Een Azure Database for PostgreSQL - Hyperscale (Citus)-server maken in de Azure-portal) voor meer informatie over hoe u een database maakt en verbindt.

    > [!NOTE]
    > Een exemplaar van Azure Database for PostgreSQL - Hyperscale (Citus) heeft slechts één database: **citus**.

3. Importeer het schema in de doeldatabase die u hebt gemaakt, door het dumpbestand van het schema te herstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Voer het volgende script uit om het 'drop foreign key'-script uit te pakken en toe te voegen op de bestemming (Azure Database for PostgreSQL).

   > [!IMPORTANT]
   > Refererende sleutels in het schema hebben tot gevolg dat de eerste lading en doorlopende synchronisatie van de migratie mislukken.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

6. Voer het onderstaande script uit om triggers in de doeldatabase uit te schakelen.

   > [!IMPORTANT]
   > Triggers in de gegevens (insert of update) dwingen gegevensintegriteit in de doeldatabase af vóór de gegevens die uit de brondatabase worden gerepliceerd. Het wordt daarom aanbevolen om triggers in alle tabellen **in de doeldatabase** uit te schakelen tijdens de migratie, en de triggers weer in te schakelen zodra de migratie is voltooid.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement, een nieuwe of bestaande resourcegroep en de locatie.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bronserver van PostgreSQL en het doelexemplaar van Azure Database for PostgreSQL.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecteer **Beoordelen en maken** om de service te maken.

   Het maken van een service duurt ongeveer 10 tot 15 minuten.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt, selecteer de instantie en selecteer vervolgens **Nieuw migratieproject**.

3. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **PostgreSQL** en selecteer in het tekstvak **Doelservertype** de optie **Azure Database for PostgreSQL**.

4. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

5. Selecteer **Opslaan**, lees de vereisten om met Azure Database Migration Service gegevens te migreren en selecteer vervolgens **Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van PostgreSQL.

    ![Scherm Brondetails toevoegen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Doeldetails** de verbindingsgegevens op voor de Hyperscale (Citus)-doelserver. Dit is het vooraf ingerichte exemplaar van Hyperscale (Citus) waarnaar het schema **DVD Rentals** is geïmplementeerd met behulp van pg_dump.

    ![Scherm Doeldetails](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Scherm Toewijzen aan doeldatabases](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecteer **Opslaan** en accepteer vervolgens de standaardwaarden in het scherm **Migratie-instellingen**.

    ![Scherm Migratie-instellingen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Scherm Migratieoverzicht](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **status** van de activiteit moet nu **Back-up wordt uitgevoerd** zijn.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Migratievoortgang controleren](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Als de migratie is voltooid, selecteert u onder **Databasenaam** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

   > [!NOTE]
   > **Alle gegevens worden geladen** toont de migratiestatus van de eerste lading terwijl **Incrementele gegevenssynchronisatie** de CDC-status (Change Data Capture) toont.

     ![Details van Alle gegevens worden geladen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Details van Incrementele gegevenssynchronisatie](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht tot de teller **Wijzigingen in behandeling** op **0** staat om er zeker van te zijn dat alle inkomende transacties naar de brondatabase zijn gestopt. Schakel dan het selectievakje **Bevestigen** in en selecteer **Toepassen**.

    ![Scherm Cutover voltooien](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Wanneer de databasemigratie de status **Voltooid** heeft, [maakt u de reeksen opnieuw](https://wiki.postgresql.org/wiki/Fixing_Sequences) (indien van toepassing) en verbindt u uw toepassingen met de nieuwe doelinstantie van Azure Database for PostgreSQL.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for PostgreSQL?](../postgresql/overview.md) voor informatie over Azure Database for PostgreSQL.
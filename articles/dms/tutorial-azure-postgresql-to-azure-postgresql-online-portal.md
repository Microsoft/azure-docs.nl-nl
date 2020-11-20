---
title: 'Zelfstudie: Azure DB for PostgreSQL online migreren naar Azure DB for PostgreSQL via de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een onlinemigratie uitvoert van de ene Azure DB voor PostgreSQL naar een andere Azure Database for PostgreSQL met behulp van Azure Database Migration Service en de Azure-portal.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 05e523c368cfa8407d66ff57fc481a756664b1ea
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961665"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Zelfstudie: Azure DB for PostgreSQL - Single Server online migreren/upgraden naar Azure DB for PostgreSQL - Single Server online met behulp van DMS via de Azure-portal

U kunt Azure Database Migration Service gebruiken om de databases met minimale downtime te migreren van een exemplaar van [Azure Database for PostgreSQL - Single Server](../postgresql/overview.md#azure-database-for-postgresql---single-server) naar dezelfde of een andere versie van Azure Database for PostgreSQL - Single Server of Azure Database for PostgresSQL - Flexible Server. In deze zelfstudie migreert u de voorbeelddatabase **DVD Rental** van Azure Database for PostgreSQL v10 naar Azure Database for PostgresSQL - Single Server met behulp van de onlinemigratieactiviteit in Azure Database Migration Service.

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

> [!IMPORTANT]
> Migratie van Azure Database for PostgreSQL wordt ondersteund voor PostgreSQL versie 10 en hoger. U kunt deze zelfstudie ook gebruiken om te migreren van een exemplaar van Azure Database for PostgreSQL naar een ander exemplaar van Azure Database for PostgreSQL of naar een exemplaar van Hyperscale (Citus).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Controleer de [status van migratiescenario's die worden ondersteund door Azure Database Migration Service](./resource-scenario-status.md) voor ondersteunde combinaties van migratie en versie. 
* Een bestaand exemplaar van [Azure Database for PostgreSQL](../postgresql/index.yml) versie 10 en hoger met de database **DVD Rental**. 

    Houd er ook rekening mee dat de doelversie van Azure Database for PostgreSQL gelijk moet zijn aan of hoger moet zijn dan de on-premises PostgreSQL-versie. PostgreSQL 10 kan bijvoorbeeld alleen worden gemigreerd naar Azure Database for PostgreSQL 10 of 11 maar niet naar Azure Database for PostgreSQL 9.6.

* [Maak een Azure Database for PostgreSQL-server](../postgresql/quickstart-create-server-database-portal.md) of [maak een Azure Database for PostgreSQL - Hyperscale (Citus)-server](../postgresql/quickstart-create-hyperscale-portal.md) als de doeldatabaseserver om gegevens naar te migreren.
* Maak een virtueel Microsoft Azure-netwerk voor Azure Database Migration Service met behulp van het implementatiemodel Azure Resource Manager. Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

* Zorg ervoor dat de regels voor netwerkbeveiligingsgroepen voor uw virtuele netwerk niet de volgende poorten voor inkomende communicatie naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor de bron van Azure Database for PostgreSQL om Azure Database Migration Service toegang te bieden tot de brondatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor het doel van Azure Database for PostgreSQL om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* [Logische replicatie inschakelen](../postgresql/concepts-logical.md) in de Azure Database for PostgresSQL-bron. 
* Stel in het exemplaar van Azure Database for PostgreSQL dat als bron wordt gebruikt de volgende serverparameters in:

  * max_replication_slots = [aantal sleuven], aanbevolen instelling is **tien sleuven**
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

> [!NOTE]
> De bovenstaande serverparameters zijn statisch en het exemplaar van Azure Database for PostgreSQL moet opnieuw worden opgestart om ze van kracht te laten worden. Zie [Parameters van Azure Database for PostgreSQL Server configureren](../postgresql/howto-configure-server-parameters-using-portal.md) voor meer informatie over het in- en uitschakelen van serverparameters.

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
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
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
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
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

   ![Portal-abonnementen weergeven](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement, een nieuwe of bestaande resourcegroep en de locatie.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bronserver van PostgreSQL en het doelexemplaar van Azure Database for PostgreSQL.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecteer **Beoordelen en maken** om de service te maken.

   Het maken van een service duurt ongeveer 10 tot 15 minuten.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt, selecteer de instantie en selecteer vervolgens **Nieuw migratieproject**.

3. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **PostgreSQL** en selecteer in het tekstvak **Doelservertype** de optie **Azure Database for PostgreSQL**.
    > [!NOTE]
    > Kies **PostgreSQL** bij **Bronservertype**, ook al is de bronserver een exemplaar van **Azure Database for PostgreSQL**.  

4. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

5. Selecteer **Opslaan**, lees de vereisten om met Azure Database Migration Service gegevens te migreren en selecteer vervolgens **Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van PostgreSQL.

    ![Scherm Brondetails toevoegen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Details zoals Servernaam, Serverpoort en Databasenaam vindt u in de portal van **Azure Database for PostgreSQL**.

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Doeldetails** de verbindingsgegevens op voor de Hyperscale (Citus)-doelserver. Dit is het vooraf ingerichte exemplaar van Hyperscale (Citus) waarnaar het schema **DVD Rentals** is geïmplementeerd met behulp van pg_dump.

    ![Scherm Doeldetails](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > U kunt migreren van een exemplaar van Azure Database for PostgreSQL naar een ander exemplaar van Azure Database for PostgreSQL met één server of naar een Hyperscale (Citus)-server.

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Scherm Toewijzen aan doeldatabases](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecteer **Opslaan** en accepteer vervolgens de standaardwaarden in het scherm **Migratie-instellingen**.

    ![Scherm Migratie-instellingen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Scherm Migratieoverzicht](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

Het venster van de migratieactiviteit wordt weergegeven en de **status** van de activiteit moet nu **Back-up wordt uitgevoerd** zijn. U kunt de volgende fout krijgen bij het uitvoeren van een upgrade van Azure DB for PostgreSQL 9.5 of 9.6:

**Een scenario heeft een onbekende fout gemeld. 28000: no pg_hba.conf entry for replication connection from host "40.121.141.121", user "sr"**

Dit komt doordat PostgreSQL niet over de juiste bevoegdheden beschikt om vereiste logische replicatieartefacten te maken. Als u de vereiste bevoegdheden wilt inschakelen, kunt u het volgende doen:

1. Open de instellingen voor Verbindingsbeveiliging voor de Azure DB for PostgreSQL-server van waaruit u wilt migreren of waarvoor u een upgrade wilt uitvoeren.
2. Voeg een nieuwe firewallregel toe met een naam die eindigt op _replrule en voeg het IP-adres uit het foutbericht toe aan de velden Eerste IP-adres en Eind-IP-adres. Voor het bovenstaande foutvoorbeeld:
> Naam firewallregel = sr_replrule; eerste IP-adres = 40.121.141.121; eind-IP-adres = 40.121.141.121

3. Klik op Opslaan en wacht tot de wijziging is doorgevoerd. 
4. Probeer de DMS-activiteit opnieuw. 

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Migratievoortgang controleren](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Als de migratie is voltooid, selecteert u onder **Databasenaam** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

   > [!NOTE]
   > **Alle gegevens worden geladen** toont de migratiestatus van de eerste lading terwijl **Incrementele gegevenssynchronisatie** de CDC-status (Change Data Capture) toont.

     ![Details van Alle gegevens worden geladen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Details van Incrementele gegevenssynchronisatie](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht tot de teller **Wijzigingen in behandeling** op **0** staat om er zeker van te zijn dat alle inkomende transacties naar de brondatabase zijn gestopt. Schakel dan het selectievakje **Bevestigen** in en selecteer **Toepassen**.

    ![Scherm Cutover voltooien](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Wanneer de databasemigratie de status **Voltooid** heeft, [maakt u de reeksen opnieuw](https://wiki.postgresql.org/wiki/Fixing_Sequences) (indien van toepassing) en verbindt u uw toepassingen met de nieuwe doelinstantie van Azure Database for PostgreSQL.
 
> [!NOTE]
> Azure Database Migration Service kan worden gebruikt voor het uitvoeren van upgrades van belangrijke versies met een lagere downtime in Azure Database for PostgreSQL - Single Server. U configureert eerst een doeldatabase met de gewenste hogere versie, netwerkinstellingen en parameters van PostgresSQL. Daarna kunt u de migratie naar de doeldatabases initiëren met behulp van de hierboven beschreven procedure. Na de cutover naar de doeldatabaseserver kunt u de verbindingsreeks voor uw toepassing bijwerken zodat deze naar de doeldatabaseserver verwijst. 

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for PostgreSQL?](../postgresql/overview.md) voor informatie over Azure Database for PostgreSQL.
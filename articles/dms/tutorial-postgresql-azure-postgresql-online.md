---
title: 'Zelfstudie: PostgreSQL online migreren naar Azure Database for PostgreSQL via Azure CLI'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een onlinemigratie uitvoert van PostgreSQL on-premises naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service en CLI.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: bb4dd08b4f30982ec4572fd4e130a89112578175
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203552"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Zelfstudie: PostgreSQL online migreren naar Azure DB for PostgreSQL met behulp van DMS en Azure CLI

U kunt de Azure Database Migration Service gebruiken om de databases met minimale downtime te migreren van een on-premises PostgreSQL-exemplaar naar [Azure Database for PostgreSQL](../postgresql/index.yml). Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **DVD Rental** van een on-premises exemplaar van PostgreSQL 9.6 naar Azure Database for PostgreSQL met behulp van een onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Het voorbeeldschema migreren met behulp van het hulpprogramma pg_dump.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Een migratieproject maken met behulp van de Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. De schijf wordt versleuteld om diefstal van gegevens tijdens het migratieproces te voorkomen.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.5.11, 9.6.7, 10 of hoger zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).

    Houd er ook rekening mee dat de doelversie van Azure Database for PostgreSQL gelijk moet zijn aan of hoger moet zijn dan de on-premises PostgreSQL-versie. PostgreSQL 9.6 kan bijvoorbeeld alleen worden gemigreerd naar Azure Database for PostgreSQL 9.6, 10 of 11 maar niet naar Azure Database for PostgreSQL 9.5.

* [Maak een exemplaar in Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) of [maak een Azure Database for PostgreSQL - Hyperscale (Citus)-server](../postgresql/quickstart-create-hyperscale-portal.md).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de regels voor de uitgaande poort 443 van ServiceTag voor ServiceBus, opslag en AzureMonitor niet worden geblokkeerd door de NSG Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de oorspronkelijke PostgreSQL Server. Standaard verloopt dit via TCP-poort 5432.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewallregel](../postgresql/concepts-firewall-rules.md) op serverniveau voor Azure Database for PostgreSQL om Azure Database Migration Service toegang te bieden tot de doeldatabases. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* Er zijn twee methoden voor het aanroepen van de CLI:

  * Klik in de rechterbovenhoek van de Azure-portal en selecteer de knop Cloud Shell:

       ![Knop Cloud Shell in de Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Installeer de CLI lokaal en voer deze uit. CLI 2.0 is het opdrachtregelprogramma voor het beheer van Azure-resources.

       Volg de instructies in het artikel [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) als u de CLI wilt downloaden. In het artikel worden ook de platforms genoemd die ondersteuning bieden voor CLI 2.0.

       Volg als u Windows-subsysteem voor Linux (WSL) wilt instellen de instructies in de [installatiehandleiding voor Windows 10](/windows/wsl/install-win10)

* Schakel logische replicatie in het bestand postgresql.config in en stel de volgende parameters in:

  * wal_level = **logical**
  * max_replication_slots = [aantal sleuven], aanbevolen instelling is **vijf sleuven**
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen.

1. Gebruik de opdracht pg_dump -s om een dumpbestand van het schema te maken voor een database. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ga bijvoorbeeld als volgt te werk als u een dump wilt maken van het schemabestand van de dvdrental-database:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Zie voor meer informatie over het gebruik van het hulpprogramma pg_dump de voorbeelden in de zelfstudie [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Maak een lege database maken in uw doelomgeving, dit is Azure Database for PostgreSQL.

    Raadpleeg het artikel [Create an Azure Database for PostgreSQL server in the Azure portal](../postgresql/quickstart-create-server-database-portal.md) (Een Azure Database for PostgreSQL-server maken in Azure Portal) of [Create an Azure Database for PostgreSQL - Hyperscale (Citus) server in the Azure portal](../postgresql/quickstart-create-hyperscale-portal.md) (Een Azure Database for PostgreSQL - Hyperscale (Citus)-server maken in de Azure-portal) voor meer informatie over hoe u een database maakt en verbindt.

3. Importeer het schema in de doeldatabase die u hebt gemaakt, door het dumpbestand van het schema te herstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Voer het volgende script uit in PgAdmin of in psql om het 'drop foreign key'-script uit te pakken en voeg het 'foreign key'-script toe op de doeldatabase (Azure Database for PostgreSQL).
  
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

    Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

5. Triggers in de gegevens (invoeg- of bijwerktriggers) dwingen de gegevensintegriteit in de doeldatabase af vóór de gerepliceerde gegevens uit de brondatabase. Het wordt aanbevolen om triggers in alle tabellen **in de doeldatabase** uit te schakelen tijdens de migratie, en de triggers weer in te schakelen zodra de migratie is voltooid.

    Gebruik de volgende opdracht om triggers in de doeldatabase uit te schakelen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Als er tabellen zijn die ENUM-gegevenstypen bevatten, wordt u geadviseerd om deze in de doeltabel tijdelijk bij te werken naar een 'character varying'-gegevenstype. Wanneer de gegevensreplicatie is voltooid, wijzigt u het gegevenstype weer in ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Een exemplaar van DMS inrichten met behulp van de CLI

1. Installeer als volgt de DMS-synchronisatie-extensie:
   * Meld u aan bij Azure door de volgende opdracht uit te voeren:
       ```azurecli
       az login
       ```

   * Wanneer dit wordt gevraagd, opent u een webbrowser en voert u een code in om uw apparaat te verifiëren. Volg de aanwijzingen.
   * Voeg de DMS-extensie toe:
       * Voer de volgende opdracht uit om de beschikbare extensies weer te geven:

           ```azurecli
           az extension list-available –otable
           ```

       * Voer vervolgens de volgende opdracht uit om de extensie te installeren:

           ```azurecli
           az extension add –n dms-preview
           ```

   * Voer de volgende opdracht uit om te controleren of de DMS-extensie correct is geïnstalleerd:

       ```azurecli
       az extension list -otable
       ```
       U moet de volgende uitvoer zien:

       ```output
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

      > [!IMPORTANT]
      > Zorg ervoor dat uw extensie een hogere versie dan 0.11.0 heeft.

   * U kunt op elk gewenst moment alle opdrachten bekijken die in DMS worden ondersteund door de volgende opdracht uit te voeren:

       ```azurecli
       az dms -h
       ```

   * Als u meerdere Azure-abonnementen hebt, voert u de volgende opdracht uit om het abonnement in te stellen waarmee u een exemplaar van de DMS-service wilt inrichten.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Voer de volgende opdracht uit om een exemplaar van DMS in te richten:

   ```azurecli
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Met de volgende opdracht bijvoorbeeld maakt u een service in:

   * Locatie: VS - oost 2
   * Abonnement: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Naam resourcegroep: PostgresDemo
   * Naam DMS-service: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   Het duurt ongeveer 10-12 minuten om het exemplaar van de DMS-service te maken.

3. Voer de volgende opdracht uit om het IP-adres van de DMS-agent te identificeren, zodat u het kunt toevoegen aan het Postgres-bestand pg_hba.conf:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Bijvoorbeeld:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Als het goed is, lijkt het resultaat op het volgende adres: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Voeg het IP-adres van de DMS-agent toe aan het Postgres-bestand pg_hba.conf.

    * Noteer het IP-adres in DMS wanneer u klaar bent met het inrichten in DMS.
    * Voeg het IP-adres toe aan het bestand pg_hba.conf op de bron, vergelijkbaar met de volgende vermelding:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. Voer vervolgens de volgende opdracht uit om een PostgreSQL-migratieproject te maken:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Zo maakt u met de volgende opdracht een project met deze parameters:

   * Locatie: VS - west-centraal
   * Naam resourcegroep: PostgresDemo
   * Servicenaam: PostgresCLI
   * Projectnaam: PGMigration
   * Bronplatform: PostgreSQL
   * Doelplatform: AzureDbForPostgreSql

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Voer de volgende stappen uit om een PostgreSQL-migratietaak te maken.

    Deze stap omvat het gebruik van de bron-IP, gebruikers-id en -wachtwoord, doel-IP, gebruikers-id en -wachtwoord en taaktype om verbinding te maken.

   * Voer de volgende opdracht uit als u een volledige lijst van opties wilt bekijken:

       ```azurecli
       az dms project task create -h
       ```

       Voor zowel de bron- als de doelverbinding verwijst de invoerparameter naar een json-bestand dat de lijst met objecten bevat.

       De indeling van het JSON-object van de verbinding voor PostgreSQL-verbindingen.
        
       ```json
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Er is ook een database-variant van het JSON-bestand dat een lijst met JSON-objecten bevat. Hieronder ziet u de indeling van het JSON-object van de database-optie voor PostgreSQL:

       ```json
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Maak een json-bestand in Kladblok, kopieer de volgende opdrachten en plak ze in het bestand. Sla het bestand op in C:\DMS\source.json.

        ```json
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Maak een ander bestand met de naam target.json en sla het op als C:\DMS\target.json. Neem daarin de volgende opdrachten op:

       ```json
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Maak een json-bestand voor de database-optie dat de inventaris weergeeft als de te migreren database:

       ```json
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Voer de volgende opdracht, waarbij het bronbestand, het doelbestand en het json-bestand voor de database-optie worden gebruikt.

       ```azurecli
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     U hebt nu een migratietaak ingediend.

7. Voer de volgende opdracht uit om de voortgang van de taak weer te geven:

   ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   OF

    ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. U kunt ook de migrationState opvragen met de expand-parameter:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

Het uitvoerbestand bevat diverse parameters die de voortgang van de migratie aangeven. Zie bijvoorbeeld onderstaande uitvoerbestand:

  ```output
    "output": [                                 // Database Level
          {
            "appliedChanges": 0,         // Total incremental sync applied after full load
            "cdcDeleteCounter": 0        // Total delete operation  applied after full load
            "cdcInsertCounter": 0,       // Total insert operation applied after full load
            "cdcUpdateCounter": 0,       // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0,     //Number of tables that contain migration error
            "fullLoadLoadingTables": 0,     //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,      //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,           //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",    //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                        // Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",    //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                     //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,            //Volume in Bytes in full load
            "id": "or|inventory|public|actor",
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                       //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                    //Total sync changes that applied after full load
          },
          {                                            //Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                                      // DMS migration task state
        "state": "Running",    //Migration task state – Running means it is still listening to any changes that might come in
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
  ```

## <a name="cutover-migration-task"></a>Cutover-migratietaak

De database is gereed voor cutover wanneer de database volledig is geladen. Afhankelijk van hoe druk de bronserver is met de nieuwe transacties die binnenkomen, is het mogelijk dat de DMS-taak nog steeds wijzigingen toepast nadat het volledig laden is voltooid.

Valideer het aantal rijen in de bron- en doeldatabases om te controleren of alle gegevens zijn bijgewerkt. U kunt hiervoor bijvoorbeeld de volgende opdracht gebruiken:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0, //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0, //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112, //full load for table 2
```

1. Voer de cutover-databasemigratietaak uit met behulp van de volgende opdracht:

    ```azurecli
    az dms project task cutover -h
    ```

    Bijvoorbeeld:

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. Voer de volgende opdracht uit als u de voortgang van de cutover wilt bijhouden:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```
3. Wanneer de databasemigratie de status **Voltooid** heeft, [maakt u de reeksen opnieuw](https://wiki.postgresql.org/wiki/Fixing_Sequences) (indien van toepassing) en verbindt u uw toepassingen met het nieuwe doelexemplaar van Azure Database for PostgreSQL.

## <a name="service-project-task-cleanup"></a>Service, project, taak opschonen

Als u een DMS-taak, -project of -service wilt annuleren of verwijderen, voert u de annulering uit in de volgende volgorde:

* Een actieve taak annuleren
* De taak verwijderen
* Het project verwijderen
* De DMS-service verwijderen

1. Gebruik de volgende opdracht als u een actieve taak wilt annuleren:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Gebruik de volgende opdracht als u een actieve taak wilt verwijderen:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Gebruik de volgende opdracht als u een actief project wilt annuleren:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Gebruik de volgende opdracht als u een actief project wilt verwijderen:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Gebruik de volgende opdracht als u een DMS-service wilt verwijderen:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for PostgreSQL?](../postgresql/overview.md) voor informatie over Azure Database for PostgreSQL.
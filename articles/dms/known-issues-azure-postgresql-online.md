---
title: 'Bekende problemen: online migraties van PostgreSQL naar Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratie beperkingen met online migraties van PostgreSQL naar Azure Database for PostgreSQL met behulp van de Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258826"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Bekende problemen/migratie beperkingen met online migraties van PostgreSQL naar Azure DB voor PostgreSQL

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van PostgreSQL naar Azure Database for PostgreSQL worden beschreven in de volgende secties.

## <a name="online-migration-configuration"></a>Configuratie van online migratie

- Op de bron PostgreSQL-server moet versie 9,4, 9,5, 9,6, 10 of 11 worden uitgevoerd. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).
- Alleen migraties naar dezelfde of een hogere versie worden ondersteund. U kunt bijvoorbeeld PostgreSQL 9,5 migreren naar Azure Database for PostgreSQL 9,6 of 10 wordt ondersteund, maar de migratie van PostgreSQL 11 naar PostgreSQL 9,6 wordt niet ondersteund.
- Stel de volgende para meters in om logische replicatie in te scha kelen in het bestand **postgresql. conf van de bron postgresql** :
  - **wal_level** = logische
  - **max_replication_slots** = [Maxi maal maximum aantal data bases voor migratie]; Als u vier data bases wilt migreren, stelt u de waarde in op ten minste 4.
  - **max_wal_senders** = [aantal data bases dat gelijktijdig wordt uitgevoerd]; de aanbevolen waarde is 10
- Het IP-adres van de DMS-agent toevoegen aan de bron PostgreSQL pg_hba. conf
  1. Noteer het DMS IP-adres nadat u klaar bent met het inrichten van een exemplaar van Azure Database Migration Service.
  2. Voeg het IP-adres toe aan het bestand pg_hba. conf zoals wordt weer gegeven:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- De gebruiker moet de replicatie functie hebben op de server die als host fungeert voor de bron database.
- De bron-en doel database schema's moeten overeenkomen.
- Het schema in de doel-Azure-Data Base voor PostgreSQL-Single server mag geen refererende sleutels hebben. Gebruik de volgende query om refererende sleutels te verwijderen:

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

- Het schema in de doel-Azure-Data Base voor PostgreSQL-Single server mag geen triggers hebben. Gebruik het volgende om triggers in doel database uit te scha kelen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Grootte beperkingen
- U kunt Maxi maal 2 TB aan gegevens migreren van PostgreSQL naar Azure DB voor PostgreSQL met behulp van één DMS-service.
## <a name="datatype-limitations"></a>Beperkingen van gegevens typen

  **Beperking**: als er geen primaire sleutel is voor tabellen, worden wijzigingen mogelijk niet gesynchroniseerd met de doel database.

  **Tijdelijke oplossing**: Stel tijdelijk een primaire sleutel in voor de tabel voor migratie om door te gaan. U kunt de primaire sleutel verwijderen nadat de gegevens migratie is voltooid.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Beperkingen bij het online migreren van AWS RDS PostgreSQL

Wanneer u probeert een online migratie uit te voeren vanaf AWS RDS PostgreSQL naar Azure Database for PostgreSQL, kunnen de volgende fouten optreden.

- **Fout**: De standaardwaarde van de kolom {kolom} in tabel {tabel} in database {database} is verschillend op bron- en doelservers. De waarde {waarde op bron} in de bron en {waarde op doel} op het doel.

  **Beperking**: deze fout treedt op wanneer de standaard waarde op een kolom schema afwijkt van de bron-en doel database.
  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

- **Fout**: Doeldatabase {database} heeft {aantal tabellen} tabellen terwijl brondatabase {database} {aantal tabellen} tabellen heeft. Het aantal tabellen op de bron- en doeldatabase moet overeenkomen.

  **Beperking**: deze fout treedt op wanneer het aantal tabellen verschilt van de bron-en doel database.

  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

- **Fout:** De bron database {Data Base} is leeg.

  **Beperking**: deze fout treedt op wanneer de bron database leeg is. Waarschijnlijk omdat u de verkeerde data base hebt geselecteerd als bron.

  **Tijdelijke oplossing**: dubbel Controleer de bron database die u hebt geselecteerd voor migratie en probeer het opnieuw.

- **Fout:** De doel database {Data Base} is leeg. Migreer het schema.

  **Beperking**: deze fout treedt op wanneer er geen schema is in de doel database. Zorg ervoor dat schema op het doel overeenkomt met het schema op de bron.
  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

## <a name="other-limitations"></a>Andere beperkingen

- De database naam mag geen punt komma (;)) bevatten.
- Een vastgelegde tabel moet een primaire sleutel hebben. Als een tabel geen primaire sleutel heeft, is het resultaat van de bewerkingen DELETE en UPDATE record onvoorspelbaar.
- Het bijwerken van een primaire-sleutel segment wordt genegeerd. In dergelijke gevallen wordt het Toep assen van een dergelijke update geïdentificeerd door het doel als een update waarbij geen rijen zijn bijgewerkt en resulteert dit in een record die naar de uitzonderingen tabel wordt geschreven.
- Migratie van meerdere tabellen met dezelfde naam, maar een andere case (bijvoorbeeld Tabel1, TABEL1 en Tabel1) kan onvoorspelbaar gedrag veroorzaken en wordt daarom niet ondersteund.
- De verwerking van [maken | ALTER | NEERZETTEN | TRUNCATe] tabel DDLs wordt niet ondersteund.
- In Azure Database Migration Service kan één migratie activiteit Maxi maal vier data bases bevatten.
- De migratie van de pg_largeobject tabel wordt niet ondersteund. 

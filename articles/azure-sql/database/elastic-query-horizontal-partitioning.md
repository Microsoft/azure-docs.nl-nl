---
title: Rapportage over uitgeschaalde Cloud databases
description: elastische query's instellen via horizontale partities
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792103"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportage over uitgeschaalde Cloud databases (preview-versie)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Query's uitvoeren op Shards][1]

Shard-data bases verdelen rijen over een uitgeschaalde gegevenslaag. Het schema is identiek voor alle deelnemende data bases, ook wel horizon taal partitioneren genoemd. Met behulp van een elastische query kunt u rapporten maken die alle data bases in een Shard-data base omvatten.

Zie [rapportage over uitgeschaalde Cloud databases](elastic-query-getting-started.md)voor een Snelstartgids.

Zie [Query's uitvoeren in Cloud databases met verschillende schema's](elastic-query-vertical-partitioning.md)voor niet-Shard-data bases.

## <a name="prerequisites"></a>Vereisten

* Maak een Shard-kaart met behulp van de client bibliotheek voor Elastic data base. Zie [Shard-toewijzings beheer](elastic-scale-shard-map-management.md). U kunt ook de voor beeld-app gebruiken in aan de [slag met Elastic data base-hulpprogram ma's](elastic-scale-get-started.md).
* U kunt ook de [bestaande data bases migreren naar uitgeschaalde data bases](elastic-convert-to-use-elastic-tools.md).
* De gebruiker moet beschikken over de machtiging voor het wijzigen van externe gegevens bronnen. Deze machtiging is opgenomen in de machtiging ALTER DATABASE.
* Machtigingen voor ALTER ANY EXTERNAL DATA SOURCE zijn nodig om te verwijzen naar de onderliggende gegevensbron.

## <a name="overview"></a>Overzicht

Deze instructies maken de meta gegevens weergave van uw Shard-gegevenslaag in de Elastic query-data base.

1. [HOOFD SLEUTEL MAKEN](/sql/t-sql/statements/create-master-key-transact-sql)
2. [DATA BASE-SCOPED REFERENTIE MAKEN](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 Create Data Base scoped Master Key and referenties

De referentie wordt gebruikt door de elastische query om verbinding te maken met uw externe data bases.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Zorg ervoor dat het achtervoegsel ' *\<username\> "* geen *\@ Server naam"* bevat.

## <a name="12-create-external-data-sources"></a>1,2 externe gegevens bronnen maken

Syntaxis:

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>Voorbeeld

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

De lijst met huidige externe gegevens bronnen ophalen:

```sql
select * from sys.external_data_sources;
```

De externe gegevens bron verwijst naar de Shard-kaart. Een elastische query gebruikt vervolgens de externe gegevens bron en de onderliggende Shard-toewijzing om de data bases op te sommen die deel uitmaken van de gegevenslaag.
Dezelfde referenties worden gebruikt voor het lezen van de Shard-kaart en om toegang te krijgen tot de gegevens op de Shards tijdens de verwerking van een elastische query.

## <a name="13-create-external-tables"></a>1,3 externe tabellen maken

Syntaxis:  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**Voorbeeld**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

De lijst met externe tabellen ophalen uit de huidige Data Base:

```sql
SELECT * from sys.external_tables;
```

Externe tabellen verwijderen:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>Opmerkingen

De \_ component gegevens bron definieert de externe gegevens bron (een Shard-toewijzing) die wordt gebruikt voor de externe tabel.  

De \_ component schema naam en object \_ naam wijzen de definitie van de externe tabel toe aan een tabel in een ander schema. Als u dit weglaat, wordt ervan uitgegaan dat het schema van het externe object "dbo" is en de naam ervan wordt aangenomen dat deze identiek is aan de naam van de externe tabel die wordt gedefinieerd. Dit is handig als de naam van uw externe tabel al is opgenomen in de Data Base waarin u de externe tabel wilt maken. Stel dat u een externe tabel wilt definiëren voor een geaggregeerde weer gave van catalogus weergaven of Dmv's in de geschaalde gegevenslaag. Omdat catalogus weergaven en Dmv's al lokaal bestaan, kunt u de namen niet gebruiken voor de definitie van de externe tabel. Gebruik in plaats daarvan een andere naam en gebruik de naam van de catalogus weergave of de DMV in de \_ component schema naam en/of object \_ naam. (Zie het onderstaande voor beeld.)

De distributie component geeft de gegevens distributie op die voor deze tabel wordt gebruikt. De query processor maakt gebruik van de informatie in de distributie component om de meest efficiënte query plannen te maken.

1. **Shard** betekent dat gegevens Horizon taal zijn gepartitioneerd over de data bases. De partitie sleutel voor de gegevens distributie is de **<sharding_column_name>** para meter.
2. **Gerepliceerd** betekent dat identieke kopieën van de tabel aanwezig zijn op elke Data Base. Het is uw verantwoordelijkheid om ervoor te zorgen dat de replica's identiek zijn in de data bases.
3. **Ronde \_ ROBIN** betekent dat de tabel horizon taal is gepartitioneerd met behulp van een toepassings afhankelijke distributie methode.

**Referentie gegevenslaag**: de externe tabel verwijst DDL naar een externe gegevens bron. De externe gegevens bron geeft een Shard-toewijzing op waarmee de externe tabel wordt voorzien van de informatie die nodig is om alle data bases in uw gegevenslaag te vinden.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Gebruikers met toegang tot de externe tabel krijgen automatisch toegang tot de onderliggende externe tabellen onder de referentie die is opgegeven in de definitie van de externe gegevens bron. Voorkom ongewenste uitbrei ding van bevoegdheden via de referentie van de externe gegevens bron. Gebruik GRANT of REVOKE voor een externe tabel, alsof het een normale tabel is.  

Wanneer u uw externe gegevens bron en uw externe tabellen hebt gedefinieerd, kunt u nu volledige T-SQL gebruiken voor uw externe tabellen.

## <a name="example-querying-horizontal-partitioned-databases"></a>Voor beeld: een query uitvoeren op horizontale gepartitioneerde data bases

Met de volgende query wordt een drieweg-koppeling tussen magazijnen, orders en order regels uitgevoerd en worden verschillende aggregaties en selectief filter gebruikt. Hierbij wordt ervan uitgegaan (1) horizontale partitionering (sharding) en (2) dat magazijnen, orders en order regels worden Shard op basis van de kolom Warehouse ID en dat de elastische query de samen voegingen op het Shards kan vinden en het dure deel van de query op de Shards parallel kan verwerken.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Opgeslagen procedure voor het uitvoeren van externe T-SQL-uitvoering: SP \_ execute_remote

Met elastische query's wordt ook een opgeslagen procedure geïntroduceerd waarmee direct toegang tot de Shards wordt geboden. De opgeslagen procedure heet [ \_ \_ extern uitvoeren](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) en kan worden gebruikt om externe opgeslagen procedures of T-SQL-code uit te voeren op de externe data bases. Hierbij worden de volgende para meters gebruikt:

* Naam van de gegevens bron (nvarchar): de naam van de externe gegevens bron van het type RDBMS.
* Query (nvarchar): de T-SQL-query die moet worden uitgevoerd op elke Shard.
* Parameter declaratie (nvarchar)-optioneel: string met definities van gegevens typen voor de para meters die worden gebruikt in de query parameter (zoals sp_executesql).
* Lijst met parameter waarden-optioneel: door Komma's gescheiden lijst met parameter waarden (zoals sp_executesql).

De extern \_ uitvoeren op \_ afstand maakt gebruik van de externe gegevens bron die in de aanroep parameters is opgegeven om de opgegeven T-SQL-instructie uit te voeren op de externe data bases. De referentie van de externe gegevens bron wordt gebruikt om verbinding te maken met de shardmap manager-data base en de externe data bases.  

Voorbeeld:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Connectiviteit voor hulpprogramma's

Gebruik reguliere SQL Server verbindings reeksen om uw toepassing, uw BI-en gegevens integratie Programma's te koppelen aan de data base met uw externe tabel definities. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Ga vervolgens naar de Elastic query-data base, zoals alle andere SQL Server data bases die zijn verbonden met het hulp programma, en gebruik externe tabellen van uw hulp programma of toepassing alsof ze lokale tabellen zijn.

## <a name="best-practices"></a>Aanbevolen procedures

* Zorg ervoor dat de data base van het Elastic query-eind punt toegang heeft gekregen tot de shardmap-data base en alle Shards via de firewalls van SQL Database.  
* Valideer of afdwingen van de gegevens distributie die is gedefinieerd door de externe tabel. Als uw werkelijke gegevens distributie afwijkt van de distributie die in de tabel definitie is opgegeven, kunnen de query's onverwachte resultaten opleveren.
* Met elastische query's wordt op dit moment geen Shard-eliminatie uitgevoerd wanneer de predikaten over de sharding-sleutel beschikken, waardoor het mogelijk is om op veilige wijze bepaalde Shards uit te sluiten van verwerking.
* Elastische query's werken het beste voor query's waarbij het meren deel van de berekening op de Shards kan worden uitgevoerd. Normaal gesp roken krijgt u de beste query prestaties met selectieve filter predikaten die kunnen worden geëvalueerd op de Shards of worden toegevoegd aan de gepartitioneerde sleutels die kunnen worden uitgevoerd in een gepartitioneerde manier op alle Shards. Andere query patronen moeten mogelijk grote hoeveel heden gegevens van de Shards naar het hoofd knooppunt laden en kunnen goed worden uitgevoerd

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van elastische query's](elastic-query-overview.md)voor een overzicht van elastische query's.
* Zie [Aan de slag met query's op meerdere databases (verticale partitionering)](elastic-query-getting-started-vertical.md) voor een zelfstudie over verticale partitionering.
* Zie [Query's uitvoeren op verticaal gepartitioneerde gegevens](elastic-query-vertical-partitioning.md) voor de syntaxis van en voorbeeldquery's voor verticaal gepartitioneerde gegevens
* Zie [Aan de slag met elastische query's voor horizontale partitionering (sharding)](elastic-query-getting-started.md) voor een zelfstudie over horizontale partitionering (sharding).
* Zie [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL-database of een aantal databases die als shards fungeren in een schema voor horizontale partitionering.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
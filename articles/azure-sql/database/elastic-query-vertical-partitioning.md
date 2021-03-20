---
title: Query's uitvoeren in Cloud databases met een ander schema
description: query's voor meerdere data bases instellen voor verticale partities
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792086"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Query's uitvoeren in Cloud databases met verschillende schema's (preview-versie)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Query's uitvoeren voor meerdere tabellen in verschillende data bases][1]

Verticaal gepartitioneerde data bases gebruiken verschillende sets tabellen in verschillende data bases. Dit betekent dat het schema afwijkt in verschillende databases. Zo bevinden alle tabellen voor de inventarisatie zich in één database, terwijl alle aan de administratie gerelateerde tabellen zich in een tweede database bevinden.

## <a name="prerequisites"></a>Vereisten

* De gebruiker moet beschikken over de machtiging voor het wijzigen van externe gegevens bronnen. Deze machtiging is opgenomen in de machtiging ALTER DATABASE.
* Machtigingen voor ALTER ANY EXTERNAL DATA SOURCE zijn nodig om te verwijzen naar de onderliggende gegevensbron.

## <a name="overview"></a>Overzicht

> [!NOTE]
> In tegens telling tot horizontale partitionering zijn deze DDL-instructies niet afhankelijk van het definiëren van een gegevenslaag met een Shard-toewijzing via de client bibliotheek voor Elastic data base.
>

1. [HOOFD SLEUTEL MAKEN](/sql/t-sql/statements/create-master-key-transact-sql)
2. [DATA BASE-SCOPED REFERENTIE MAKEN](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Data base-scoped Master sleutel en referenties maken

De referentie wordt gebruikt door de elastische query om verbinding te maken met uw externe data bases.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Zorg ervoor dat het `<username>` achtervoegsel **' \@ Server naam '** niet wordt vermeld.

## <a name="create-external-data-sources"></a>Externe gegevens bronnen maken

Syntaxis:

<External_Data_Source>:: = externe gegevens bron maken <data_source_name> met (TYPE = RDBMS, LOCATION = ' <fully_qualified_server_name> ', DATABASE_NAME = ' <remote_database_name> ',  
    CREDENTIAL = <credential_name>) [;]

> [!IMPORTANT]
> De TYPE parameter moet worden ingesteld op **RDBMS**.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u het gebruik van de instructie CREATE voor externe gegevens bronnen.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

De lijst met huidige externe gegevens bronnen ophalen:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Externe tabellen

Syntaxis:

Maak een externe tabel [database_name. [schema_name]. | schema_name. ] table_name  
    ({<column_definition>} [,... n]) {met (<rdbms_external_table_options>)}) [;]

<rdbms_external_table_options>:: = DATA_SOURCE = <External_Data_Source>, [SCHEMA_NAME = N ' nonescaped_schema_name ',] [OBJECT_NAME = N ' nonescaped_object_name ',]

### <a name="example"></a>Voorbeeld

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

In het volgende voor beeld ziet u hoe u de lijst met externe tabellen kunt ophalen uit de huidige Data Base:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Opmerkingen

Met elastische query's wordt de bestaande syntaxis van de externe tabel uitgebreid met het definiëren van externe tabellen die gebruikmaken van externe gegevens bronnen van het type RDBMS. Een externe tabel definitie voor verticale partitionering heeft betrekking op de volgende aspecten:

* **Schema**: de externe tabel DDL definieert een schema dat door uw query's kan worden gebruikt. Het schema dat is opgegeven in de definitie van de externe tabel moet overeenkomen met het schema van de tabellen in de externe data base waarin de daad werkelijke gegevens zijn opgeslagen.
* **Externe database referentie**: de externe tabel DDL verwijst naar een externe gegevens bron. De externe gegevens bron geeft de naam van de server en de data base van de externe data base waar de gegevens van de actuele tabel worden opgeslagen.

Als u een externe gegevens bron gebruikt, zoals beschreven in de vorige sectie, is de syntaxis voor het maken van externe tabellen als volgt:

De DATA_SOURCE-component definieert de externe gegevens bron (dat wil zeggen de externe data base in het geval van verticale partitionering) die wordt gebruikt voor de externe tabel.  

De componenten SCHEMA_NAME en OBJECT_NAME bieden de mogelijkheid om de definitie van de externe tabel toe te wijzen aan een tabel in een ander schema op de externe data base of aan een tabel met een andere naam. Dit is handig als u een externe tabel wilt definiëren in een catalogus weergave of DMV op uw externe data base, of een andere situatie waarin de naam van de externe tabel al lokaal wordt overgenomen.  

De volgende DDL-instructie verwijdert een bestaande definitie van een externe tabel uit de lokale catalogus. Dit heeft geen invloed op de externe data base.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Machtigingen voor maken/verwijderen van externe tabel**: machtigingen voor externe gegevens bronnen wijzigen is vereist voor de DDL van externe tabellen die ook nodig is om te verwijzen naar de onderliggende gegevens bron.  

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Gebruikers met toegang tot de externe tabel krijgen automatisch toegang tot de onderliggende externe tabellen onder de referentie die is opgegeven in de definitie van de externe gegevens bron. U moet de toegang tot de externe tabel zorgvuldig beheren om te voor komen dat er ongewenste bevoegdheden worden uitgebreid via de referentie van de externe gegevens bron. Reguliere SQL-machtigingen kunnen worden gebruikt om toegang tot een externe tabel toe te kennen of in te trekken, net alsof het een normale tabel is.  

## <a name="example-querying-vertically-partitioned-databases"></a>Voor beeld: een query uitvoeren op verticaal gepartitioneerde data bases

Met de volgende query wordt een drie richtings relatie uitgevoerd tussen de twee lokale tabellen voor orders en order regels en de externe tabel voor klanten. Dit is een voor beeld van het gebruik van referentie gegevens voor elastische query's:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Opgeslagen procedure voor het uitvoeren van externe T-SQL-uitvoering: SP \_ execute_remote

Met elastische query's wordt ook een opgeslagen procedure geïntroduceerd die directe toegang biedt tot de externe data base. De opgeslagen procedure heet [ \_ \_ extern uitvoeren](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) en kan worden gebruikt om externe opgeslagen procedures of T-SQL-code uit te voeren op de externe data base. Hierbij worden de volgende para meters gebruikt:

* Naam van de gegevens bron (nvarchar): de naam van de externe gegevens bron van het type RDBMS.
* Query (nvarchar): de T-SQL-query die moet worden uitgevoerd op de externe data base.
* Parameter declaratie (nvarchar)-optioneel: string met definities van gegevens typen voor de para meters die worden gebruikt in de query parameter (zoals sp_executesql).
* Lijst met parameter waarden-optioneel: door Komma's gescheiden lijst met parameter waarden (zoals sp_executesql).

De extern \_ uitvoeren op \_ afstand maakt gebruik van de externe gegevens bron die is opgegeven in de aanroep parameters om de opgegeven T-SQL-instructie uit te voeren op de externe data base. De referentie van de externe gegevens bron wordt gebruikt om verbinding te maken met de externe data base.  

Voorbeeld:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Connectiviteit voor hulpprogramma's

U kunt gewone SQL Server verbindings reeksen gebruiken om uw BI-en gegevens integratie hulpprogramma's te koppelen aan data bases op de server waarop elastische query's zijn ingeschakeld en externe tabellen zijn gedefinieerd. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Raadpleeg vervolgens de elastische query database en de externe tabellen, net als andere SQL Server Data Base waarmee u verbinding wilt maken met uw hulp programma.

## <a name="best-practices"></a>Aanbevolen procedures

* Zorg ervoor dat de data base van het Elastic query-eind punt toegang heeft gekregen tot de externe data base door toegang in te scha kelen voor Azure-Services in de Azure SQL Database firewall configuratie. Zorg er ook voor dat de referenties die in de definitie van de externe gegevens bron zijn opgenomen, kunnen worden aangemeld bij de externe data base en de machtigingen hebben voor toegang tot de externe tabel.  
* Elastische query's werken het beste voor query's waarbij de meeste berekeningen kunnen worden uitgevoerd voor de externe data bases. Normaal gesp roken krijgt u de beste query prestaties met selectieve filter predikaten die kunnen worden geëvalueerd op de externe data bases of samen voegingen die volledig kunnen worden uitgevoerd op de externe data base. Andere query patronen moeten mogelijk grote hoeveel heden gegevens van de externe data base laden en kunnen goed worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van elastische query's](elastic-query-overview.md)voor een overzicht van elastische query's.
* Zie [Aan de slag met query's op meerdere databases (verticale partitionering)](elastic-query-getting-started-vertical.md) voor een zelfstudie over verticale partitionering.
* Zie [Aan de slag met elastische query's voor horizontale partitionering (sharding)](elastic-query-getting-started.md) voor een zelfstudie over horizontale partitionering (sharding).
* Zie [Query's uitvoeren op horizontaal gepartitioneerde gegevens](elastic-query-horizontal-partitioning.md) voor de syntaxis van en voorbeeldquery's voor horizontaal gepartitioneerde gegevens
* Zie [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL-database of een aantal databases die als shards fungeren in een schema voor horizontale partitionering.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->
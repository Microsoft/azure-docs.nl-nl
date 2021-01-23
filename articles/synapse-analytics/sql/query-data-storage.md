---
title: Query's op gegevensopslag uitvoeren met een serverloze SQL-pool
description: In dit artikel wordt beschreven hoe u een query uitvoert voor Azure-opslag, met behulp van een serverloze SQL-pool in Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: b5025aa322ae26f9dd7c683d0e54762fd33eb355
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735378"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Query's op opslagbestanden uitvoeren met een serverloze SQL-pool in Azure Synapse Analytics

Met een serverloze SQL-pool kunt u query's uitvoeren op uw gegevens in uw data lake. Het biedt een T-SQL query-surface area dat geschikt is voor semi-gestructureerde en ongestructureerde gegevensquery's. Voor het uitvoeren van query's worden de volgende T-SQL-aspecten ondersteund:

- Volledige [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-surface area, met inbegrip van de meeste [SQL-functies en -operatoren](overview-features.md).
- Met CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) wordt een [externe tabel](develop-tables-external-tables.md) gemaakt en parallel de resultaten van een Transact-SQL SELECT-instructie geëxporteerd naar Azure Storage.

Lees het artikel [Overzicht van serverloze SQL-pools](on-demand-workspace-overview.md) voor meer informatie over wat momenteel niet wordt ondersteund, of lees de volgende artikelen:
- [Ontwikkel opslagtoegang](develop-storage-files-overview.md) waar u kunt leren hoe u [Externe tabellen](develop-tables-external-tables.md) en de functie [OPENROWSET](develop-openrowset.md) kunt gebruiken om gegevens uit de opslag te lezen.
- [Toegang tot opslag beheren](develop-storage-files-storage-access-control.md) waar u kunt leren hoe u Synapse SQL in staat stelt om toegang te krijgen tot opslag met SAS-verificatie of beheerde identiteit van de werkruimte.

## <a name="overview"></a>Overzicht

Om een soepele ervaring te kunnen bieden voor het lokaal zoeken naar gegevens die zich bevinden in Azure Storage-bestanden, gebruikt een serverloze SQL-pool de functie [OPENROWSET](develop-openrowset.md) met aanvullende mogelijkheden:

- [Meerdere bestanden of mappen doorzoeken](#query-multiple-files-or-folders)
- [Bestandsindeling PARQUET](#query-parquet-files)
- [Query-CSV en gescheiden tekst (veldeindteken, rij-eindteken, escape-teken)](#query-csv-files)
- [Een gekozen subset van kolommen lezen](#read-a-chosen-subset-of-columns)
- [Schema-deductie](#schema-inference)
- [Bestandsnaamfunctie](#filename-function)
- [Bestandspadfunctie](#filepath-function)
- [Werken met complexe typen en geneste of herhaalde gegevensstructuren](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Query's uitvoeren op PARQUET-bestanden

FORMAT = 'PARQUET' gebruiken om een query uit te voeren op Parquet-gegevens

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Lees het artikel [Query uitvoeren op Parquet-bestanden](query-parquet-files.md) voor gebruiksvoorbeelden.

## <a name="query-csv-files"></a>Query uitvoeren op CSV-bestanden

Gebruik FORMAT = 'CSV' om een query uit te voeren op CSV-brongegevens. U kunt het schema van het CSV-bestand opgeven als onderdeel van de `OPENROWSET`-functie bij het opvragen van CSV-bestanden:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Er zijn een aantal extra opties die kunnen worden gebruikt om het parseren van af te stemmen op een aangepaste CSV-indeling:
- ESCAPE_CHAR = 'char' Het teken in het bestand dat wordt gebruikt om zichzelf te escapen evenals alle scheidingstekens in het bestand. Als het escape-teken wordt gevolgd door een andere waarde dan het teken zelf, of een van de scheidingstekens, wordt het escape-teken genegeerd bij het lezen van de waarde.
De parameter ESCAPE_CHAR wordt toegepast ongeacht of FIELDQUOTE al dan niet is ingeschakeld. De parameter wordt niet gebruikt om het aanhalingsteken te escapen. Het aanhalingsteken moet worden a”gesloten door een ander aanhalingsteken. Een aanhalingsteken kan alleen in een kolomwaarde worden weer gegeven als de waarde tussen aanhalingstekens staat.
- FIELDTERMINATOR ='field_terminator' Geeft aan welk teken moet worden gebruikt om het einde van een veld aan te geven. Het standaardeindteken voor velden is een komma (' **,** ')
- ROWTERMINATOR ='row_terminator' Geeft aan welk teken moet worden gebruikt om het einde van een regel aan te geven. Het standaardeindteken voor riijen is een teken voor nieuwe regel: **\r\n**.

## <a name="file-schema"></a>Bestandsschema

Met SQL-taal in Synapse SQL kunt u het schema van het bestand definiëren als onderdeel van de functie `OPENROWSET` en alle kolommen of een subset daarvan lezen, of er wordt geprobeerd om de kolomtypen van het bestand automatisch te bepalen met behulp van schema-afleiding.

### <a name="read-a-chosen-subset-of-columns"></a>Een gekozen subset van kolommen lezen

Om aan te geven welke kolommen u wilt lezen, kunt u een optionele WITH-clausule opgeven binnen uw `OPENROWSET`-opdracht.

- Als u wilt dat alle kolommen worden gelezen uit CSV-gegevensbestanden, geeft u de kolomnamen en de bijbehorende gegevenstypen op. Als u een subset van deze kolommen wilt opvragen, gebruikt u rangtelwoorden om de kolommen uit de oorspronkelijke gegevensbestanden te kiezen op rangtelwoord. Kolommen worden gebonden op aanduiding via rangnummer.
- Als het Parquet-gegevensbestanden betreft, geeft u kolomnamen op die overeenkomen met de kolomnamen in de oorspronkelijke gegevensbestanden. Kolommen worden op naam gebonden.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

Voor elke kolom moet u de naam van de kolom opgeven en de `WITH`-component typen.
Raadpleeg [CSV-bestanden lezen zonder alle kolommen op te geven](query-single-csv-file.md#return-a-subset-of-columns) voor voorbeelden.

## <a name="schema-inference"></a>Schema-deductie

Door de WITH-clausule weg te laten in de `OPENROWSET`-opdracht, kunt u de service de opdracht geven automatisch het schema te detecteren (afleiden) van de onderliggende bestanden.

> [!NOTE]
> Dit werkt momenteel alleen voor de PARQUET-bestandsindeling.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Zorg ervoor dat [de juiste gededuceerde datatypes](best-practices-sql-on-demand.md#check-inferred-data-types) worden gebruikt voor de beste prestatie. 

## <a name="query-multiple-files-or-folders"></a>Meerdere bestanden of mappen doorzoeken

Geef een pad of een map of een patroon (met jokertekens) over een verzameling bestanden of mappen op om een T-SQL-query uit te voeren op een verzameling bestanden in een map of een verzameling mappen en ze te behandelen als een enkele entiteit of rijenset.

De volgende regels zijn van toepassing:

- Patronen kunnen voorkomen in een deel van een mappad of in een bestandsnaam.
- Verschillende patronen kunnen in dezelfde mapstap of bestandsnaam verschijnen.
- Als er meerdere jokertekens zijn gebruikt, worden alle bestanden in overeenkomende paden inbegrepen in de resulterende bestandsverzameling.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Raadpleeg [Query uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md) voor gebruiksvoorbeelden.

## <a name="file-metadata-functions"></a>Bestandsmetagegevensfuncties

### <a name="filename-function"></a>Bestandsnaamfunctie

Deze functie retourneert de bestandsnaam waaruit de rij afkomstig is. 

Lees de sectie Bestandsnaam in het artikel [Query's uitvoeren op specifieke bestanden](query-specific-files.md#filename)om een query uit te voeren op specifieke bestanden.

Het retourgegevenstype is nvarchar(1024). Cast het resultaat van de functie bestandsnaam altijd naar het juiste gegevenstype voor de beste prestatie. Als u een tekengegevenstype gebruikt, zorg er dan voor dat de juiste lengte wordt gebruikt.

### <a name="filepath-function"></a>Bestandspadfunctie

Deze functie retourneert een volledig pad of een deel van een pad:

- Wanneer de functie wordt aangeroepen zonder parameter, wordt het volledige bestandspad geretourneerd waaruit een rij afkomstig is.
- Wanneer de functie wordt aangeroepen met parameter, wordt het deel van het pad geretourneerd dat overeenkomt met het jokerteken op de positie die is gespecificeerd in de parameter. Parameterwaarde 1 zou bijvoorbeeld het deel van het pad retourneren dat overeenkomt met het eerste jokerteken.

Lees de sectie Bestandspad van het artikel [Query uitvoeren op specifieke bestanden](query-specific-files.md#filepath) voor meer informatie.

Het retourgegevenstype is nvarchar(1024). Cast het resultaat van de functie bestandspad altijd naar het juiste gegevenstype voor de beste prestatie. Als u een tekengegevenstype gebruikt, zorg er dan voor dat de juiste lengte wordt gebruikt.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Werken met complexe types en geneste of herhaalde gegevensstructuren

Aan de serverloze SQL-pool zijn onderstaande extensies toegevoegd om een soepele ervaring te kunnen bieden bij gegevens die zijn opgeslagen in geneste of herhaalde gegevenstypes, zoals in [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types)-bestanden.

#### <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Voer een SELECT-opdracht uit op het Parquet-bestand dat kolommen of geneste gegevenstypes bevat om gegevens te projecteren. Geneste waarden worden met serienummer in JSON geplaatst en geretourneerd als een SQL-gegevenstype varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Raadpleeg de sectie Geneste of herhaalde gegevens projecteren van het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#project-nested-or-repeated-data) voor meer informatie.

#### <a name="access-elements-from-nested-columns"></a>Elementen benaderen vanuit geneste kolommen

Gebruik 'puntnotatie' om veldnamen samen te voegen in het pad om geneste elementen te benaderen van een geneste kolom, zoals Struct. Geef het pad op als kolomnaam in de WITH-clausule van de functie `OPENROWSET`.

Het voorbeeld van het syntaxisfragment ziet er als volgt uit:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

De functie `OPENROWSET` zoekt standaard naar overeenkomsten van de bronveldnaam en het bronveldpad met de kolomnamen die zijn opgegeven in de WITH-clausule. Elementen binnen verschillende nestniveaus binnen hetzelfde Parquet-bronbestand kunnen worden benaderd via de WITH-clausule.

**Retourwaarden**

- Voor alle Parquet-types die zich niet in de groep Genest type bevinden, retourneert deze functie een scalaire waarde, zoals integer, decimaal en varchar van het opgegeven element en op het opgegeven pad.
- Als het pad wijst naar een element van het Geneste type, retourneert de functie een JSON-fragment dat begint met het hoogste element in het opgegeven pad. Het JSON-fragment is van het type varchar(8000).
- Als de eigenschap niet kan worden gevonden in de opgegeven kolomnaam, retourneert de functie een fout.
- Als de eigenschap niet kan worden gevonden in de opgegeven kolomnaam, retourneert de functie, afhankelijk van [Padmodus](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#PATHMODE), een fout in de strict-modus of null in de lax-modus.

Raadpleeg de sectie Elementen benaderen vanuit geneste kolommen in het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#read-properties-from-nested-object-columns) voor voorbeelden van query's.

#### <a name="access-elements-from-repeated-columns"></a>Elementen benaderen vanuit herhaalde kolommen

Gebruik de functie [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor elk scalair element dat u wilt projecteren om elementen van een herhaalde kolom te benaderen, zoals een element van een Matrix of Map en geef het volgende op:

- als eerste parameter Geneste of herhaalde kolom
- als tweede parameter een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dat de elementen of de eigenschappen opgeeft die moeten worden benaderd

Gebruik de functie [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor elk niet-scalair element dat u wilt projecteren om niet-scalaire elementen van een herhaalde kolom te benaderen en geef het volgende op:

- als eerste parameter Geneste of herhaalde kolom
- als tweede parameter een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dat de elementen of de eigenschappen opgeeft die moeten worden benaderd

Zie hieronder een syntaxis-fragment:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

U kunt voorbeelden van query's voor het benaderen van elementen van herhaalde kolommen vinden in het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="query-samples"></a>Voorbeelden van query's

Met behulp van de voorbeeldquery's kunt u meer leren over het uitvoeren van query's op verschillende gegevenstypen.

### <a name="tools"></a>Hulpprogramma's

De hulpprogramma's die u nodig hebt om query's uit te voeren:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Demo-instellingen

De eerste stap bestaat uit het **maken van een database** waarin u de query's gaat uitvoeren. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. 

Met dit installatiescript worden de gegevensbronnen, referenties voor het databasebereik en externe bestandsindelingen gemaakt die worden gebruikt om gegevens in deze voorbeelden te lezen.

> [!NOTE]
> Databases worden alleen gebruikt voor het weergeven van metagegevens, niet voor de werkelijke gegevens.  Noteer de naam van de database die u gebruikt. U hebt deze later nodig.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Beschikbare demogegevens

Demogegevens bevatten de volgende gegevenssets:

- NYC Taxi - Records van Yellow Taxi-ritten - onderdeel van openbare NYC-gegevensset in CSV- en Parquet-indeling
- Populatiegegevensset in CSV-indeling
- Voorbeeld van Parquet-bestanden met geneste kolommen
- Boeken in JSON-indeling

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Bovenliggende map voor gegevens in CSV-indeling                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappen met gegevensbestanden met een populatie in verschillende CSV-indelingen. |
| /csv/taxi/                                                   | Map met openbare NYC-gegevensbestanden in CSV-indeling              |
| /parquet/                                                    | Bovenliggende map voor gegevens in Parquet-indeling                     |
| /parquet/taxi                                                | Openbare gegevensbestanden van NYC in Parquet-indeling, gepartitioneerd per jaar en maand met Hive/Hadoop-partitieschema. |
| /parquet/nested/                                             | Voorbeeld van Parquet-bestanden met geneste kolommen                     |
| /json/                                                       | Bovenliggende map voor gegevens in JSON-indeling                        |
| /json/books/                                                 | JSON-bestanden met boekengegevens                                   |


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het uitvoeren van query's op verschillende bestandstypes en het maken en gebruiken van weergaven:

- [Query's uitvoeren op CSV-bestanden](query-single-csv-file.md)
- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Query's uitvoeren op JSON-bestanden](query-json-files.md)
- [Geneste waarden opvragen](query-parquet-nested-types.md)
- [Query's uitvoeren op mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Metagegevens van bestand gebruiken in query's](query-specific-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)

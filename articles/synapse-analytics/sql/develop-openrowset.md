---
title: OPENROWSET gebruiken in een serverloze SQL-pool (preview)
description: In dit artikel wordt de syntaxis beschreven van OPENROWSET in serverloze SQL-pool en wordt uitgelegd hoe u argumenten gebruikt.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2458b5f3f0c0091bb6ec24e62a1d5614e4e1ecd8
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888586"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>OPENROWSET gebruiken met behulp van serverloze SQL-pool (preview) in Azure Synapse Analytics

Met de functie `OPENROWSET(BULK...)` kunt u toegang krijgen tot bestanden in Azure Storage. `OPENROWSET` leest de inhoud van een externe gegevensbron (bijvoorbeeld een bestand) en retourneert de inhoud als een set rijen. Binnen de resource van serverloze SQL-pool (preview) wordt de bulksgewijze rijensetprovider van OPENROWSET benaderd door het aanroepen van de functie OPENROWSET en het opgeven van de optie BULK.  

Naar de functie `OPENROWSET` kan worden verwezen in de `FROM`-component van een query alsof het een tabelnaam `OPENROWSET` is. De functie ondersteunt bulkbewerkingen via een ingebouwde BULK-provider waarmee gegevens uit een bestand kunnen worden gelezen om deze vervolgens te retourneren als een rijenset.

## <a name="data-source"></a>Gegevensbron

De functie OPENROWSET in Synapse SQL leest de inhoud van een of meer bestanden uit een gegevensbron. De gegevensbron is een Azure-opslagaccount waarnaar expliciet kan worden verwezen in de functie `OPENROWSET` of die kan dynamisch worden afgeleid van de URL van de bestanden die u wilt lezen.
De functie `OPENROWSET` kan optioneel een parameter `DATA_SOURCE` bevatten om de gegevensbron op te geven die bestanden bevat.
- `OPENROWSET` zonder `DATA_SOURCE` kan worden gebruikt om de inhoud van de bestanden rechtstreeks te lezen vanaf de URL-locatie die is opgegeven via de optie `BULK`:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Dit is een snelle en eenvoudige manier om de inhoud van de bestanden te lezen zonder voorafgaande configuratie. Met deze optie kunt u de optie voor basisverificatie gebruiken om toegang te krijgen tot de opslag (Azure AD Pass-Through voor Azure AD-aanmeldingen en SAS-token voor SQL-aanmeldingen). 

- `OPENROWSET` met `DATA_SOURCE` kan worden gebruikt om toegang te krijgen tot bestanden in het opgegeven opslagaccount:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Met deze optie kunt u de locatie van het opslagaccount in de gegevensbron configureren en de verificatiemethode opgeven die moet worden gebruikt voor toegang tot de opslag. 
    
    > [!IMPORTANT]
    > `OPENROWSET` zonder `DATA_SOURCE` biedt een snelle en eenvoudige manier om toegang te krijgen tot de opslagbestanden, maar met beperkte verificatieopties. Zo kunnen Microsoft Azure AD-principals alleen toegang krijgen tot bestanden met behulp van hun [Azure AD-identiteit](develop-storage-files-storage-access-control.md?tabs=user-identity) of tot openbaar beschikbare bestanden. Als u krachtigere verificatieopties nodig hebt, gebruikt u `DATA_SOURCE` optie en definieert u de referenties die u wilt gebruiken voor toegang tot de opslag.


## <a name="security"></a>Beveiliging

Een databasegebruiker moet beschikken over de machtiging `ADMINISTER BULK OPERATIONS` om de functie `OPENROWSET` te kunnen gebruiken.

De opslagbeheerder moet een gebruiker ook in staat stellen om toegang te krijgen tot de bestanden door een geldig SAS-token te verstrekken of door de Azure AD-principal toegang te geven tot opslagbestanden. Meer informatie over toegangsbeheer voor opslag vindt u in [dit artikel](develop-storage-files-storage-access-control.md).

`OPENROWSET` gebruikt de volgende regels om te bepalen hoe verificatie voor toegang tot opslag moet worden uitgevoerd:
- Het verificatiemechanisme `OPENROWSET` zonder `DATA_SOURCE` is afhankelijk van het type aanroeper.
  - Elke gebruiker kan `OPENROWSET` gebruiken zonder `DATA_SOURCE` om openbaar beschikbare bestanden in Azure Storage te lezen.
  - Microsoft Azure AD-aanmeldingen hebben toegang tot beveiligde bestanden met hun eigen [Azure AD-identiteit](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) als Azure Storage de Azure AD-gebruiker toegang geeft tot onderliggende bestanden (bijvoorbeeld als de oproepende functie `Storage Reader` toestemming heeft voor Azure-opslag).
  - SQL-aanmeldingen kunnen ook `OPENROWSET` gebruiken zonder `DATA_SOURCE` om toegang te krijgen tot openbaar beschikbare bestanden, bestanden die zijn beveiligd met een SAS-token of beheerde identiteit van een Synapse-werkruimte. U moet [referenties binnen serverbereik maken](develop-storage-files-storage-access-control.md#examples) om toegang tot opslagbestanden toe te staan. 
- In `OPENROWSET` met `DATA_SOURCE` worden verificatiemechanismen gedefinieerd in de referentie binnen databasebereik die is toegewezen aan de gegevensbron waarnaar wordt verwezen. Met deze methode kunt u toegang krijgen tot openbaar beschikbare opslag, of toegang krijgen tot opslag met behulp van een SAS-token, een beheerde identiteit van de werkruimte of [Azure AD-identiteit van de aanroeper](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (als dat een Azure AD-principal is). Als `DATA_SOURCE` verwijst naar Azure-opslag die niet openbaar is, moet u [referenties binnen het databasebereik maken](develop-storage-files-storage-access-control.md#examples) en hiernaar verwijzen in `DATA SOURCE` om toegang tot opslagbestanden te geven.

De aanroeper moet beschikken over de machtiging `REFERENCES` voor de referenties om deze te kunnen gebruiken voor verificatie bij de opslag.

## <a name="syntax"></a>Syntaxis

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
```

## <a name="arguments"></a>Argumenten

U hebt twee keuzen voor invoerbestanden die de doelgegevens bevatten waarop query's worden uitgevoerd. Geldige waarden zijn:

- 'CSV': een tekstbestand met scheidingstekens voor rijen en kolommen. Elk teken kan worden gebruikt als veldscheidingsteken, zoals TSV: FIELDTERMINATOR = tab.

- 'PARQUET': binair bestand in Parquet-indeling 

**'unstructured_data_path'**

Het argument unstructured_data_path is een pad naar de gegevens in de vorm van een absolute of relatieve verwijzing:
- Absoluut pad in de indeling \<prefix>://\<storage_account_path>/\<storage_path> stelt een gebruiker in staat de bestanden rechtstreeks te lezen.
- Een relatief pad in de indeling <pad_naar_opslag> dat moet worden gebruikt met de parameter `DATA_SOURCE` en dat het bestandspatroon beschrijft op de locatie <pad_naar_opslagaccount> locatie die is gedefinieerd in `EXTERNAL DATA SOURCE`. 

Hieronder vindt u de relevante <storage account path> waarden voor koppeling aan uw specifieke externe gegevensbron. 

| Externe gegevensbron       | Voorvoegsel | Pad van opslagaccount                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/pad/bestand   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/pad/bestand |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net/pad/bestand   |
| Azure Data Lake Store Gen2 | aufs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/pad/bestand](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Een pad binnen de opslag dat verwijst naar de map of het bestand dat u wilt lezen. Als het pad naar een container of map verwijst, worden alle bestanden in die specifieke container of map gelezen. Bestanden in submappen worden uitgesloten. 

U kunt jokertekens gebruiken om meerdere bestanden of mappen op te geven. Het gebruik van meerdere, niet-opeenvolgende jokertekens is toegestaan.
Hieronder ziet u een voorbeeld waarmee alle *CSV-* bestanden worden gelezen die beginnen met *population* uit alle mappen die beginnen met */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Als u opgeeft dat unstructured_data_path een map is, haalt een serverloze SQL-pool-query bestanden op uit die map. 

U kunt een serverloze SQL-pool instrueren om mappen te doorlopen door /* toe te voegen aan het einde van het pad, zoals in het voorbeeld: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> In tegenstelling tot Hadoop en PolyBase, retourneert een serverloze SQL-pool geen submappen tenzij u /* * aan het einde van het pad toevoegt. Eveneens in tegenstelling tot Hadoop en PolyBase, retourneert een serverloze SQL-pool wel bestanden waarvan de bestandsnaam begint met een onderstrepingsteken (_) of een punt (.).

Als in het onderstaande voorbeeld unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, retourneert een serverloze SQL-pool-query rijen uit mydata.txt en _hidden.txt. Het retourneert niet mydata2.txt en mydata3.txt omdat deze bestanden zich in een submap bevinden.

![Recursieve gegevens voor externe tabellen](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Met de WITH-component kunt u opgeven welke kolommen u uit bestanden wilt lezen.

- Als u wilt dat alle kolommen worden gelezen uit CSV-gegevensbestanden, geeft u de kolomnamen en de bijbehorende gegevenstypen op. Als u een subset van deze kolommen wilt opvragen, gebruikt u rangtelwoorden om de kolommen uit de oorspronkelijke gegevensbestanden te kiezen op rangtelwoord. Kolommen worden gebonden op aanduiding via rangnummer. 
    > [!TIP]
    > U kunt ook de WITH-component voor CSV-bestanden weglaten. Gegevenstypen worden automatisch afgeleid van bestandsinhoud. U kunt het argument HEADER_ROW gebruiken om het bestaan van headerrij op te geven. In dat geval worden de kolomnamen afgelezen uit de headerrij. Bekijk [automatische schemadetectie](#automatic-schema-discovery) voor meer informatie.
    
- Voor Parquet-gegevensbestanden geeft u kolomnamen op die overeenkomen met de kolomnamen in de oorspronkelijke gegevensbestanden. Kolommen worden gebonden op naam en zijn hoofdlettergevoelig. Als de WITH-component wordt weggelaten, worden alle kolommen uit Parquet-bestanden geretourneerd.
    > [!IMPORTANT]
    > Kolomnamen in Parquet-bestanden zijn hoofdlettergevoelig. Als u de kolomnaam met een ander hoofdlettergebruik opgeeft dan de kolomnaam in het Parquet-bestand, worden er NULL-waarden geretourneerd voor die kolom.


column_name = De naam voor de uitvoerkolom. Als u deze naam opgeeft wordt de kolomnaam in het bronbestand vervangen. Als daarnaast een kolomnaam in het JSON-pad wordt doorgegeven, wordt ook deze kolomnaam vervangen. Als json_path niet is ingesteld, wordt deze automatisch toegevoegd als '$.column_name'. Controleer het gedrag van het argument json_path.

column_type = Het gegevenstype voor de uitvoerkolom. De impliciete conversie van het gegevenstype wordt hier uitgevoerd.

column_ordinal = Het rangnummer van de kolom in het bronbestand of de bronbestanden. Dit argument wordt genegeerd voor Parquet-bestanden omdat binding op naam wordt uitgevoerd. In het volgende voorbeeld wordt alleen een tweede kolom geretourneerd uit een CSV-bestand:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [expressie van het JSON-pad](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15) naar kolom of geneste eigenschap. De standaard voor [padenmodus](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15#PATHMODE) is lax.

> [!NOTE]
> In de strikte modus zal de query mislukken en een fout weergeven als het gegeven pad niet bestaat. In de lax-modus zal de query slagen en zal de expressie van het JSON-pad worden geëvalueerd als NULL.

**\<bulk_options>**

FIELDTERMINATOR ='eindteken_veld'

Geeft aan welk teken moet worden gebruikt om het einde van een veld aan te geven. Het standaardeindteken voor velden is een komma (' **,** ').

ROWTERMINATOR ='eindteken_rij'

Geeft aan welk teken moet worden gebruikt om het einde van een rij aan te geven. Als er geen rij-eindteken wordt opgegeven, wordt een van de standaardeindtekens gebruikt. De standaardeindtekens voor PARSER_VERSION = '1.0' zijn \r\n \n en \r. De standaardeindtekens voor PARSER_VERSION = '2.0' zijn \r\n en \n.

ESCAPE_CHAR = 'teken'

Het teken in het bestand dat wordt gebruikt om zichzelf te escapen evenals alle scheidingstekens in het bestand. Als het escape-teken wordt gevolgd door een andere waarde dan het teken zelf, of een van de scheidingstekens, wordt het escape-teken genegeerd bij het lezen van de waarde. 

De parameter ESCAPE_CHAR wordt toegepast ongeacht of FIELDQUOTE al dan niet is ingeschakeld. De parameter wordt niet gebruikt om het aanhalingsteken te escapen. Het aanhalingsteken moet worden a”gesloten door een ander aanhalingsteken. Een aanhalingsteken kan alleen in een kolomwaarde worden weer gegeven als de waarde tussen aanhalingstekens staat.

FIRSTROW = 'eerste_rij' 

Het nummer van de eerste rij die moet worden geladen. De standaard is 1 en geeft de eerste rij in het opgegeven gegevensbestand aan. De rijnummers worden bepaald door het tellen van het aantal eindtekens voor rijen. FIRSTROW begint bij 1.

FIELDQUOTE = 'aanhalingsteken_veld' 

Het teken op dat wordt gebruikt als het aanhalingsteken in het CSV-bestand. Als u niets opgeeft, wordt het aanhalingsteken " gebruikt. 

DATA_COMPRESSION = 'methode_voor_gegevenscompressie'

De compressiemethode. Wordt alleen ondersteund in PARSER_VERSION='1.0'. De volgende compressiemethode wordt ondersteund:

- GZIP

PARSER_VERSION = 'versie_van_parser'

De parser-versie die moet worden gebruikt bij het lezen van bestanden. De momenteel ondersteunde parser-versies voor CSV zijn 1.0 en 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

De CSV-parser versie 1.0 is standaard en zit boordevol functies. Versie 2.0 is gebouwd voor prestaties en biedt geen ondersteuning voor alle opties en coderingen. 

Kenmerken van parser-versie 1.0 voor CSV:

- De volgende opties worden niet ondersteund: HEADER_ROW.

Kenmerken van parser-versie 2.0 voor CSV:

- Niet alle gegevenstypen worden ondersteund.
- De maximale limiet voor de rijgrootte is 8 MB.
- De volgende opties worden niet ondersteund: DATA_COMPRESSION.
- Een lege tekenreeks tussen aanhalingstekens ("") wordt geïnterpreteerd als een lege tekenreeks.
- Ondersteunde indeling voor DATE-gegevenstype: JJJJ-MM-DD
- Ondersteunde indeling voor TIME-gegevenstype: UU:MM:SS [. fractieseconden]
- Ondersteunde indeling voor DATETIME2-gegevenstype: JJJJ-MM-DD UU:MM:SS[.fractieseconden]

HEADER_ROW = { TRUE | FALSE }

Hiermee wordt opgegeven of het CSV-bestand een headerrij bevat. Standaard is FALSE. Wordt ondersteund in PARSER_VERSION='2.0'. Indien RUE, worden kolomnamen uit de eerste rij gelezen aan de hand van het argument FIRSTROW.

DATAFILETYPE = { 'char' | 'widechar' }

Hiermee geeft u de codering op: char wordt gebruikt voor UTF8, widechar wordt gebruikt voor UTF16-bestanden.

## <a name="fast-delimited-text-parsing"></a>Snel parseren van tekstbestand met scheidingstekens

Er zijn twee parserversies voor tekstbestanden met scheidingstekens die u kunt gebruiken. De CSV-parser versie 1.0 is standaard en bevat een groot aantal functies, terwijl versie 2.0 is gebouwd voor prestaties. Prestatieverbeteringen in parser 2.0 zijn afkomstig van geavanceerde technieken voor parseren en van multithreading. Het verschil in snelheid wordt groter naarmate de bestandsgrootte groeit.

## <a name="automatic-schema-discovery"></a>Automatische schemadetectie

U kunt eenvoudig query’s uitvoeren op zowel CSV- als Parquet-bestanden zonder dat u een schema hoeft te kennen of specificeren door de WITH-component weg te laten. Kolomnamen en gegevenstypen worden afgeleid uit bestanden.

Parquet-bestanden bevatten kolom-metagegevens die worden gelezen; typetoewijzingen kunnen worden gevonden in [typetoewijzingen voor Parquet](#type-mapping-for-parquet). Bekijk [het lezen van Parquet-bestanden zonder schema op te geven](#read-parquet-files-without-specifying-schema) voor voorbeelden.

Voor CSV-bestanden kunnen kolomnamen worden afgelezen uit een headerrij. U kunt opgeven of de headerrij bestaat met behulp van het argument HEADER_ROW. Als HEADER_ROW = FALSE, worden algemene kolomnamen gebruikt: C1, C2, ... Cn waarbij n het aantal kolommen is in het bestand. Gegevenstypen worden afgeleid uit de eerste 100 gegevensrijen. Bekijk [het lezen van CSV-bestanden zonder schema op te geven](#read-csv-files-without-specifying-schema) voor voorbeelden.

> [!IMPORTANT]
> Er zijn gevallen waarin het juiste gegevenstype niet kan worden afgeleid omdat er te weinig gegevens zijn en er wordt in plaats daarvan een groter gegevenstype gebruikt. Dit neemt prestatieoverhead met zich mee en is met name belangrijk voor tekenkolommen die worden afgeleid als varchar (8000). Voor optimale prestaties [controleert u afgeleide gegevenstypen](best-practices-sql-on-demand.md#check-inferred-data-types) en [gebruikt u de juiste gegevenstypen](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Typetoewijzing voor Parquet

Parquet-bestanden bevatten typebeschrijvingen voor elke kolom. In de volgende tabel wordt beschreven hoe Parquet-types worden toegewezen aan systeemeigen SQL-typen.

| Parquet-type | Logisch type van Parquet (annotatie) | SQL-gegevenstype |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | werkelijk |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binair |
| BINARY |UTF8 |varchar \*(UTF8-sortering) |
| BINARY |STRING |varchar \*(UTF8-sortering) |
| BINARY |ENUM|varchar \*(UTF8-sortering) |
| FIXED_LEN_BYTE_ARRAY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(8000) \*(UTF8-sortering) |
| BINARY |BSON | Niet ondersteund |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL | Niet ondersteund |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|tijd |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS) |time-TIME (NANOS) wordt niet ondersteund |
|INT64 |TIMESTAMP (MILLIS / MICROS) |datetime2 - TIMESTAMP(NANOS) wordt niet ondersteund |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LIST |varchar(8000), geserialiseerd naar JSON |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(8000), geserialiseerd naar JSON |

## <a name="examples"></a>Voorbeelden

### <a name="read-csv-files-without-specifying-schema"></a>CSV-bestanden lezen zonder schema op te geven

In het volgende voorbeeld wordt het CSV-bestand dat de headerrij bevat, gelezen zonder dat kolomnamen en gegevenstypen worden opgegeven: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

In het volgende voorbeeld wordt het CSV-bestand dat geen headerrij bevat, gelezen zonder dat kolomnamen en gegevenstypen worden opgegeven: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Parquet-bestanden lezen zonder schema op te geven

In het volgende voorbeeld worden voor de gegevensset Census alle kolommen uit de eerste rij als resultaat gegeven in de Parquet-indeling zonder kolomnamen en gegevenstypen op te geven: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Specifieke kolommen lezen uit CSV-bestand

In het volgende voorbeeld worden slechts twee kolommen met de rangnummers 1 en 4 geretourneerd uit de bestanden population*.csv. Omdat de bestanden geen veldnamenrij bevatten, begint het lezen bij de eerste regel:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Specifieke kolommen lezen uit Parquet-bestand

In het volgende voorbeeld worden voor de gegevensset Census slechts twee kolommen uit de eerste rij als resultaat gegeven in de Parquet-indeling: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Kolommen opgeven met behulp van JSON-paden

In het volgende voorbeeld ziet u hoe u [Expressies van JSON-paden](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15) kunt gebruiken in de component WITH. U ziet ook het verschil tussen de strikte en de lax modi: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer voorbeelden naar de [quickstart voor querygegevensopslag ](query-data-storage.md) om te leren hoe u `OPENROWSET` gebruikt en hoe u [CSV-](query-single-csv-file.md), [PARQUET-](query-parquet-files.md) en [JSON](query-json-files.md)-bestandsindelingen leest. Bekijk [best practices](best-practices-sql-on-demand.md) om optimale prestaties te behalen. U kunt ook lezen hoe u de resultaten van een query kunt opslaan in Azure Storage met behulp van [CETAS](develop-tables-cetas.md).

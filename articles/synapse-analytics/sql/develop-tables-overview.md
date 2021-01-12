---
title: Tabellen ontwerpen met behulp van Synapse SQL
description: Inleiding tot het ontwerpen van tabellen in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3778b6046c750bb131be1e51bf1afdc7b0df7184
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116786"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Tabellen ontwerpen met behulp van Synapse SQL in azure Synapse Analytics

Dit document bevat belang rijke concepten voor het ontwerpen van tabellen met een exclusieve SQL-groep en serverloze SQL-groep.  

Een [serverloze SQL-groep](on-demand-workspace-overview.md) is een query service voor de gegevens in uw data Lake. Het bevat geen lokale opslag voor gegevens opname. Een [toegewezen SQL-groep](best-practices-sql-pool.md) vertegenwoordigt een verzameling analytische resources die worden ingericht wanneer Synapse SQL wordt gebruikt. De grootte van een toegewezen SQL-pool wordt bepaald door DWU’s (Data Warehousing Unit).

De volgende tabel bevat de onderwerpen die relevant zijn voor een exclusieve SQL-groep versus serverloze SQL-groep:

| Onderwerp                                                        | toegewezen SQL-groep | serverloze SQL-pool |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Tabel categorie bepalen](#determine-table-category)        | Ja                | Nee                      |
| [Schema namen](#schema-names)                                | Ja                | Ja                     |
| [Tabel namen](#table-names)                                  | Ja                | Nee                      |
| [Tabel persistentie](#table-persistence)                      | Ja                | Nee                      |
| [Reguliere tabel](#regular-table)                              | Ja                | Nee                      |
| [Tijdelijke tabel](#temporary-table)                          | Ja                | Ja                     |
| [Externe tabel](#external-table)                            | Ja                | Ja                     |
| [Gegevenstypen](#data-types)                                    | Ja                | Ja                     |
| [Gedistribueerde tabellen](#distributed-tables)                    | Ja                | Nee                      |
| [Met hash gedistribueerde tabellen](#hash-distributed-tables)          | Ja                | Nee                      |
| [Gerepliceerde tabellen](#replicated-tables)                      | Ja                | Nee                      |
| [Round-Robin tabellen](#round-robin-tables)                    | Ja                | Nee                      |
| [Algemene distributie methoden voor tabellen](#common-distribution-methods-for-tables) | Ja                | Nee                      |
| [Partities](#partitions)                                    | Ja                | Ja                     |
| [Columnstore-indexen](#columnstore-indexes)                  | Ja                | Nee                      |
| [Statistieken](#statistics)                                    | Ja                | Ja                     |
| [Primaire sleutel en unieke sleutel](#primary-key-and-unique-key)    | Ja                | Nee                      |
| [Opdrachten voor het maken van tabellen](#commands-for-creating-tables) | Ja                | Nee                      |
| [Bron gegevens uitlijnen met het Data Warehouse](#align-source-data-with-the-data-warehouse) | Ja                | Nee                      |
| [Niet-ondersteunde tabel functies](#unsupported-table-features)    | Ja                | Nee                      |
| [Tabel grootte query's](#table-size-queries)                    | Ja                | Nee                      |

## <a name="determine-table-category"></a>Tabel categorie bepalen

Met een [ster schema](https://en.wikipedia.org/wiki/Star_schema) worden gegevens ingedeeld in feiten-en dimensie tabellen. Sommige tabellen worden gebruikt voor integratie-of faserings gegevens voordat ze naar een feiten-of dimensie tabel worden verplaatst. Bepaal tijdens het ontwerpen van een tabel of de tabel gegevens in een feiten-, dimensie-of integratie tabel horen. Met deze beslissing wordt de juiste tabel structuur en distributie informeerd.

- **Feiten tabellen** bevatten kwantitatieve gegevens die vaak worden gegenereerd in een transactioneel systeem en vervolgens in het Data Warehouse worden geladen. Een retail bedrijf genereert bijvoorbeeld elke dag verkoop transacties en laadt vervolgens de gegevens in een feiten tabel van een Data Warehouse voor analyse.

- **Dimensie tabellen** bevatten kenmerk gegevens die mogelijk worden gewijzigd, maar die meestal ongewijzigd zijn. Bijvoorbeeld, de naam en het adres van een klant worden opgeslagen in een dimensie tabel en alleen bijgewerkt wanneer het profiel van de klant wordt gewijzigd. Als u de grootte van een grote feiten tabel wilt minimaliseren, hoeven de naam en het adres van de klant niet in elke rij van een feiten tabel te staan. In plaats daarvan kan de feiten tabel en de dimensie tabel een klant-ID delen. Een query kan worden toegevoegd aan de twee tabellen om het profiel en de trans acties van een klant te koppelen.

- **Integratie tabellen** bieden een plaats voor het integreren of klaarzetten van gegevens. U kunt een integratie tabel maken als een normale tabel, een externe tabel of een tijdelijke tabel. U kunt bijvoorbeeld gegevens laden naar een faserings tabel, trans formaties uitvoeren op de gegevens in staging en vervolgens de gegevens in een productie tabel invoegen.

## <a name="schema-names"></a>Schema namen

Schema's zijn een goede manier om objecten die op een vergelijk bare wijze worden gebruikt, te groeperen. Met de volgende code wordt een door de [gebruiker gedefinieerd schema gemaakt met](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) de naam WWI.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Tabel namen

Als u meerdere data bases migreert van een on-premises oplossing naar een toegewezen SQL-groep, is het best practice alle feiten, dimensies en integratie tabellen naar één SQL-groeps schema te migreren. U kunt bijvoorbeeld alle tabellen in het [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -voorbeeld data warehouse opslaan binnen één schema met de naam WWI.

Als u de organisatie van de tabellen in de toegewezen SQL-groep wilt weer geven, kunt u fact, Dim en int gebruiken als voor voegsels voor de tabel namen. In de volgende tabel ziet u enkele schema-en tabel namen voor WideWorldImportersDW.  

| WideWorldImportersDW-tabel  | Tabel type | toegewezen SQL-groep |
|:-----|:-----|:------|:-----|
| Plaats | Dimensie | WWI. DimCity |
| Volgorde | Fact | WWI. FactOrder |

## <a name="table-persistence"></a>Tabel persistentie

Tabellen slaan gegevens permanent op in Azure Storage, tijdelijk in Azure Storage, of in een gegevens archief buiten het Data Warehouse.

### <a name="regular-table"></a>Reguliere tabel

In een normale tabel worden gegevens in Azure Storage opgeslagen als onderdeel van het Data Warehouse. De tabel en de gegevens blijven bestaan, ongeacht of er al dan niet een sessie is geopend.  In het onderstaande voor beeld maakt u een reguliere tabel met twee kolommen.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tijdelijke tabel

Er bestaat alleen een tijdelijke tabel voor de duur van de sessie. U kunt een tijdelijke tabel gebruiken om te voor komen dat andere gebruikers tijdelijke resultaten bekijken. Het gebruik van tijdelijke tabellen vermindert ook de nood zaak van opschonen.  Tijdelijke tabellen maken gebruik van lokale opslag en, in toegewezen SQL-groepen, kunnen betere prestaties bieden.  

Een serverloze SQL-groep ondersteunt tijdelijke tabellen. Maar het gebruik ervan is beperkt omdat u kunt kiezen uit een tijdelijke tabel, maar niet kan worden gekoppeld aan bestanden in de opslag.

Zie voor meer informatie  [tijdelijke tabellen](develop-tables-temporary.md).

### <a name="external-table"></a>Externe tabel

[Externe tabellen](develop-tables-external-tables.md) verwijzen naar gegevens die zich bevinden in azure Storage blob of Azure data Lake Storage.

Importeer gegevens uit externe tabellen in toegewezen SQL-groepen met behulp van de [Create Table als Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) -instructie. Zie [poly Base gebruiken voor het laden van gegevens uit Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)voor het laden van een zelf studie.

Voor serverloze SQL-groepen kunt u [CETAS](develop-tables-cetas.md) gebruiken om het query resultaat op te slaan in een externe tabel in azure Storage.

## <a name="data-types"></a>Gegevenstypen

De toegewezen SQL-groep ondersteunt het meest gebruikte gegevens type. Zie [gegevens typen in Create Table verwijzing](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes&preserve-view=true) in de CREATE TABLE-instructie voor een lijst met ondersteunde gegevens typen. Zie [gegevens typen](../sql/develop-tables-data-types.md)voor meer informatie over het gebruik van gegevens typen.

## <a name="distributed-tables"></a>Gedistribueerde tabellen

Een fundamenteel onderdeel van de exclusieve SQL-groep is de manier waarop het kan worden opgeslagen en toegepast op tabellen in [distributies](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions).  De toegewezen SQL-pool ondersteunt drie methoden voor het distribueren van gegevens:

- Round robin (standaard)
- Hash
- Gerepliceerd

### <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen

Een gedistribueerde hash-tabel distribueert rijen op basis van de waarde in de kolom distributie. Een gedistribueerde hash-tabel is ontworpen om hoge prestaties te leveren voor query's op grote tabellen. Er zijn verschillende factoren waarmee u rekening moet houden bij het kiezen van een distributie kolom.

Zie [ontwerp richtlijnen voor gedistribueerde tabellen](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

### <a name="replicated-tables"></a>Gerepliceerde tabellen

Een gerepliceerde tabel bevat een volledige kopie van de tabel die beschikbaar is op elk reken knooppunt. Query's worden snel uitgevoerd op gerepliceerde tabellen, omdat voor deelname aan gerepliceerde tabellen geen gegevens verplaatsing nodig is. Replicatie vereist extra opslag, maar is niet praktisch voor grote tabellen.

Zie [ontwerp richtlijnen voor gerepliceerde tabellen](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

### <a name="round-robin-tables"></a>Round-Robin tabellen

Een Round Robin-tabel verdeelt tabel rijen gelijkmatig over alle distributies. De rijen worden wille keurig gedistribueerd. Het laden van gegevens in een Round-Robin tabel is snel.  Query's kunnen echter meer gegevens verplaatsing vereisen dan de andere distributie methoden.

Zie [ontwerp richtlijnen voor gedistribueerde tabellen](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

### <a name="common-distribution-methods-for-tables"></a>Algemene distributie methoden voor tabellen

De tabel categorie bepaalt vaak de optimale optie voor tabel distributie.

| Tabel categorie | Aanbevolen distributie optie |
|:---------------|:--------------------|
| Fact           | Hash-distributie met geclusterde column store-index gebruiken. De prestaties zijn verbeterd wanneer twee hash-tabellen worden gekoppeld aan dezelfde distributie kolom. |
| Dimensie      | Gebruik gerepliceerde voor kleinere tabellen. Als tabellen te groot zijn om op elk reken knooppunt te worden opgeslagen, gebruikt u hash-gedistribueerd. |
| Faseren        | Gebruik Round-Robin voor de faserings tabel. De belasting met CTAS is snel. Wanneer de gegevens zich in de faserings tabel bevindt, gebruikt u invoegen... Selecteer deze optie om de gegevens naar productie tabellen te verplaatsen. |

## <a name="partitions"></a>Partities

In toegewezen SQL-groepen, worden in een gepartitioneerde tabel de bewerkingen op de tabel rijen op basis van gegevensbereiken opgeslagen en uitgevoerd. Een tabel kan bijvoorbeeld worden gepartitioneerd op dag, maand of jaar. U kunt de query prestaties verbeteren via partitie-eliminatie, waardoor een query scan wordt beperkt tot gegevens in een partitie.

U kunt de gegevens ook onderhouden via partitie wisseling. Omdat de gegevens in een toegewezen SQL-groep al zijn gedistribueerd, kunnen er te veel partities de query prestaties vertragen. Zie [richt lijnen voor partitioneren](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.  

> [!TIP]
> Als de partitie wordt overgeschakeld naar tabel partities die niet leeg zijn, kunt u de optie TRUNCATE_TARGET gebruiken in de instructie [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) als de bestaande gegevens moeten worden afgekapt.

Met de volgende code worden de getransformeerde dagelijkse gegevens omgezet in een SalesFact-partitie en worden eventuele bestaande gegevens overschreven.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

In een serverloze SQL-groep kunt u de bestanden/mappen (partities) beperken die door uw query worden gelezen. Partitioneren op pad wordt ondersteund met behulp van de functies filepath en file info die worden beschreven in het [uitvoeren van query's op opslag bestanden](develop-storage-files-overview.md). In het volgende voor beeld wordt een map met gegevens voor jaar 2017 gelezen:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Columnstore-indexen

Standaard slaat een toegewezen SQL-groep een tabel op als een geclusterde column store-index. Deze vorm van gegevens opslag behaalt hoge gegevens compressie en query prestaties op grote tabellen.  De geclusterde column store-index is doorgaans de beste keuze, maar in sommige gevallen is een geclusterde index of een heap de juiste opslag structuur.  

> [!TIP]
> Een heap-tabel kan vooral handig zijn voor het laden van tijdelijke gegevens, zoals een faserings tabel, die wordt omgezet in een eind tabel.

Zie [Wat is er nieuw voor column Store-indexen](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor een overzicht van Column Store-functies. Zie [maximale Rijg roep-kwaliteit voor column Store-indexen](../sql/data-load-columnstore-compression.md)voor het verbeteren van Column store-index prestaties.

## <a name="statistics"></a>statistieken

De query optimalisatie maakt gebruik van statistieken op kolom niveau wanneer het plan voor het uitvoeren van een query wordt gemaakt. Om de query prestaties te verbeteren, is het belang rijk om statistieken te hebben over afzonderlijke kolommen, met name kolommen die worden gebruikt in query-samen voegingen. Synapse SQL ondersteunt het automatisch maken van statistieken. 

Statistische updates worden niet automatisch uitgevoerd. Statistieken bijwerken nadat een groot aantal rijen zijn toegevoegd of gewijzigd. U kunt bijvoorbeeld statistieken bijwerken na een belasting. Meer informatie vindt u in het artikel [Statistieken richtlijnen](develop-tables-statistics.md) .

## <a name="primary-key-and-unique-key"></a>Primaire sleutel en unieke sleutel

Voor een toegewezen SQL-groep wordt de primaire sleutel alleen ondersteund wanneer deze niet is geclusterd en niet wordt afgedwongen.  UNIQUE-beperking wordt alleen ondersteund wanneer niet afgedwongen wordt gebruikt.  Zie het artikel [beperkingen van SQL-groeps tabellen](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

## <a name="commands-for-creating-tables"></a>Opdrachten voor het maken van tabellen

Voor een toegewezen SQL-groep kunt u een tabel als een nieuwe lege tabel maken. U kunt ook een tabel maken en vullen met de resultaten van een SELECT-instructie. Hieronder vindt u de T-SQL-opdrachten voor het maken van een tabel.

| T-SQL-instructie | Beschrijving |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Hiermee maakt u een lege tabel door alle tabel kolommen en opties te definiëren. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Hiermee maakt u een externe tabel. De definitie van de tabel wordt opgeslagen in de toegewezen SQL-groep. De tabel gegevens worden opgeslagen in Azure Blob Storage of Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Vult een nieuwe tabel met de resultaten van een SELECT-instructie. De tabel kolommen en gegevens typen zijn gebaseerd op de resultaten van de SELECT-instructie. Voor het importeren van gegevens kan deze instructie een selectie uit een externe tabel selecteren. |
| [EXTERNE TABEL MAKEN ALS SELECTEREN](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Hiermee maakt u een nieuwe externe tabel door de resultaten van een SELECT-instructie te exporteren naar een externe locatie.  De locatie is Azure Blob-opslag of Azure Data Lake Storage. |

## <a name="align-source-data-with-the-data-warehouse"></a>Bron gegevens uitlijnen met het Data Warehouse

Toegewezen SQL-groeps tabellen worden gevuld door gegevens uit een andere gegevens bron te laden. Om een succes volle belasting te krijgen, moeten het aantal en de gegevens typen van de kolommen in de bron gegevens worden uitgelijnd met de tabel definitie in het Data Warehouse.

> [!NOTE]
> Het ophalen van de gegevens die moeten worden uitgelijnd, is mogelijk het lastigste deel van het ontwerpen van uw tabellen.

Als er gegevens uit meerdere gegevens archieven afkomstig zijn, kunt u de gegevens in het Data Warehouse overbrengen en in een integratie tabel opslaan. Zodra de gegevens in de integratie tabel staan, kunt u de kracht van een toegewezen SQL-groep gebruiken om transformatie bewerkingen te implementeren. Zodra de gegevens zijn voor bereid, kunt u deze invoegen in productie tabellen.

## <a name="unsupported-table-features"></a>Niet-ondersteunde tabel functies

De toegewezen SQL-groep ondersteunt veel, maar niet alle, van de tabel functies die worden aangeboden door andere data bases.  De volgende lijst bevat enkele van de tabel functies die niet worden ondersteund in een toegewezen SQL-groep.

- Refererende sleutel, [tabel beperkingen](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) controleren
- [Berekende kolommen](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Geïndexeerde weer gaven](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Reeks](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Sparse kolommen](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- Surrogaat sleutels, implementeren met [identiteit](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Synoniemen](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Triggers](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Unieke indexen](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Door de gebruiker gedefinieerde typen](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Tabel grootte query's

In toegewezen SQL-groep is een eenvoudige manier om ruimte en rijen te identificeren die worden gebruikt door een tabel in elk van de 60-distributies. Gebruik [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Houd er rekening mee dat het gebruik van DBCC-opdrachten erg kan worden beperkt.  Dynamische beheer weergaven (Dmv's) tonen meer details dan DBCC-opdrachten. Begin met het maken van de weer gave hieronder.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Overzicht van tabel ruimte

Deze query retourneert de rijen en de ruimte op tabel.  In de tabel ruimte-samen vatting kunt u zien welke tabellen uw grootste tabellen zijn. U ziet ook of ze Round-Robin, gerepliceerd of hash-Distributed zijn.  Voor door hash-gedistribueerde tabellen wordt in de query de kolom distributie weer gegeven.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabel ruimte op distributie type

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabel ruimte op index type

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Samen vatting van distributie ruimte

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de tabellen voor uw data warehouse hebt gemaakt, is de volgende stap het laden van gegevens in de tabel.  Zie [gegevens laden in een toegewezen SQL-groep](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)voor een zelf studie voor het laden van een hand leiding.
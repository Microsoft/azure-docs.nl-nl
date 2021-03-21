---
title: Tabellen indexeren
description: Aanbevelingen en voor beelden voor het indexeren van tabellen in een toegewezen SQL-groep.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: fabbdf330d43737ffa85379f9cc4d5ac59c4a734
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673515"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Unieke SQL-pool tabellen indexeren in azure Synapse Analytics

Aanbevelingen en voor beelden voor het indexeren van tabellen in een toegewezen SQL-groep.

## <a name="index-types"></a>Indextypen

Een toegewezen SQL-groep biedt verschillende indexerings opties, waaronder [geclusterde column Store-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [geclusterde indexen en niet-geclusterde indexen](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), en een niet-index optie die ook wel een [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)is genoemd.  

Als u een tabel met een index wilt maken, raadpleegt u de documentatie van [Create Table (exclusieve SQL-groep)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="clustered-columnstore-indexes"></a>Geclusterde column Store-indexen

Standaard maakt exclusieve SQL-groep een geclusterde column store-index wanneer er geen index opties zijn opgegeven in een tabel. Geclusterde column Store-tabellen bieden zowel het hoogste niveau van gegevens compressie als de beste algemene query prestaties.  Geclusterde column Store-tabellen leverde in het algemeen geclusterde index-of heap-tabellen en zijn doorgaans de beste keuze voor grote tabellen.  Om die reden is geclusterde column Store de beste plaats om te starten wanneer u niet zeker weet hoe u de tabel wilt indexeren.  

Als u een geclusterde column Store-tabel wilt maken, geeft u een geclusterde column Store-INDEX op in de WITH-component of verlaat u de WITH-component uit:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Er zijn enkele scenario's waarin geclusterde column Store mogelijk geen goede optie is:

- Column Store-tabellen bieden geen ondersteuning voor varchar (max), nvarchar (max) en varbinary (max). Overweeg in plaats daarvan heap of geclusterde index.
- Column Store-tabellen zijn mogelijk minder efficiënt voor tijdelijke gegevens. Overweeg heap en mogelijk zelfs tijdelijke tabellen.
- Kleine tabellen met minder dan 60.000.000 rijen. Overweeg heap-tabellen.

## <a name="heap-tables"></a>Heap-tabellen

Wanneer u tijdelijk gegevens in een toegewezen SQL-groep onderbrengt, is het mogelijk dat het hele proces wordt versneld met behulp van een heap-tabel. Dit komt doordat het laden van heaps sneller is dan het indexeren van tabellen, en in sommige gevallen kan de volgende Lees bewerking vanuit de cache worden uitgevoerd.  Als u alleen gegevens laadt om deze te stageen voordat u meer trans formaties uitvoert, is het laden van de tabel naar de heap-tabel veel sneller dan het laden van de gegevens naar een geclusterde column Store-tabel. Daarnaast laadt het laden van gegevens naar een [tijdelijke tabel](sql-data-warehouse-tables-temporary.md) sneller dan het laden van een tabel naar permanente opslag.  Nadat het laden van gegevens is uitgevoerd, kunt u in de tabel indexen maken voor snellere query prestaties.  

Cluster column Store-tabellen beginnen met het uitvoeren van optimale compressie wanneer er meer dan 60.000.000 rijen zijn.  Voor kleine opzoek tabellen, kleiner dan 60.000.000 rijen, kunt u overwegen HEAP of geclusterde index te gebruiken voor snellere query prestaties. 

Als u een heap-tabel wilt maken, geeft u een HEAP op in de WITH-component:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Geclusterde en niet-geclusterde indexen

Geclusterde indexen kunnen geclusterde column Store-tabellen leverde wanneer één rij snel moet worden opgehaald. Voor query's waarbij een enkele of enkele rij lookup vereist is om met extreme snelheid uit te voeren, moet u rekening houden met een cluster index of een niet-geclusterde secundaire index. Het nadeel van het gebruik van een geclusterde index is dat alleen query's die gebruikmaken van een zeer selectief filter in de kolom geclusterde index. Als u het filter op andere kolommen wilt verbeteren, kunt u een niet-geclusterde index aan andere kolommen toevoegen. Elke index die wordt toegevoegd aan een tabel voegt echter zowel de ruimte als de verwerkings tijd toe om te laden.

Als u een geclusterde index tabel wilt maken, geeft u een geclusterde INDEX op in de WITH-component:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Als u een niet-geclusterde index voor een tabel wilt toevoegen, gebruikt u de volgende syntaxis:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Geclusterde column Store-indexen optimaliseren

Geclusterde column Store-tabellen zijn ingedeeld in gegevens in segmenten.  Een hoge segment kwaliteit is van cruciaal belang voor optimale query prestaties op een column Store-tabel.  De segment kwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde Rijg groep.  De segment kwaliteit is het meest optimaal wanneer er ten minste 100.000 rijen per groep bevatten en de prestaties van het aantal rijen per Rijg-groep worden 1.048.576 bereikt. Dit zijn de meeste rijen die een Rijg werk groep kan hebben.

De onderstaande weer gave kan worden gemaakt en gebruikt op uw systeem om het gemiddelde aantal rijen per Rijg werk groep te berekenen en eventuele suboptimale cluster-column Store-indexen te identificeren.  De laatste kolom in deze weer gave genereert een SQL-instructie die kan worden gebruikt om uw indexen opnieuw samen te stellen.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Nu u de weer gave hebt gemaakt, voert u deze query uit om tabellen te identificeren met Rijg roepen die minder dan 100.000 rijen bevatten. Natuurlijk kunt u de drempel waarde van 100.000 verhogen als u op zoek bent naar een optimale segment kwaliteit.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Zodra u de query hebt uitgevoerd, kunt u beginnen met het bekijken van de gegevens en de resultaten analyseren. In deze tabel wordt uitgelegd wat u moet opzoeken in de analyse van uw Rijg-groep.

| Kolom | Deze gegevens gebruiken |
| --- | --- |
| [table_partition_count] |Als de tabel is gepartitioneerd, ziet u mogelijk een groter aantal open Row-groepen. Voor elke partitie in de distributie is theorie een geopende Rijg groep gekoppeld. U hebt deze factor in uw analyse. Een gepartitioneerde kleine tabel kan worden geoptimaliseerd door de partitie verdeling te verwijderen, omdat hierdoor de compressie wordt verbeterd. |
| [row_count_total] |Totaal aantal rijen voor de tabel. U kunt deze waarde bijvoorbeeld gebruiken om het percentage rijen in de gecomprimeerde staat te berekenen. |
| [row_count_per_distribution_MAX] |Als alle rijen gelijkmatig zijn verdeeld, zou deze waarde het doel aantal rijen per distributie zijn. Vergelijk deze waarde met de compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totaal aantal rijen in de column Store-indeling voor de tabel. |
| [COMPRESSED_rowgroup_rows_AVG] |Als het gemiddelde aantal rijen aanzienlijk kleiner is dan het maximum van de rijen voor een Rijg groep, overweeg dan om CTAS of ALTER INDEX Rebuild te gebruiken om de gegevens opnieuw te comprimeren |
| [COMPRESSED_rowgroup_count] |Aantal Rijg roepen in de column Store-indeling. Als dit getal zeer hoog is ten opzichte van de tabel, is het een indicator dat de column Store-dichtheid laag is. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rijen worden logisch verwijderd in de column Store-indeling. Als het getal hoog relatief is ten opzichte van tabel grootte, kunt u overwegen de partitie opnieuw te maken of de index opnieuw op te bouwen, omdat deze fysiek worden verwijderd. |
| [COMPRESSED_rowgroup_rows_MIN] |Gebruik dit in combi natie met de kolommen Gem en MAX om het bereik van waarden voor de Rijg roepen in uw column Store te begrijpen. Bij een lage waarde boven de drempel waarde voor laden (102.400 per partitie, wordt de verdeling van de gegevens beschikbaar in de belasting van de belasting). |
| [COMPRESSED_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_count] |Open Rijg roepen zijn normaal. De ene geopende Rijg-groep verwacht per tabel distributie (60). Door buitensporige getallen wordt het laden van gegevens voor alle partities Voorst Ellen. Controleer de strategie voor partitioneren om er zeker van te zijn dat deze klinkt |
| [OPEN_rowgroup_rows] |Elke Rijg groep kan Maxi maal 1.048.576 rijen bevatten. Gebruik deze waarde om te zien hoe vol de geopende Rijg roepen momenteel worden |
| [OPEN_rowgroup_rows_MIN] |Open groepen geven aan dat de gegevens in de tabel worden trickle geladen of dat de vorige belasting over de resterende rijen in deze Rijg groep is overgelopen. Gebruik de kolommen MIN, MAX en AVG om te zien hoeveel gegevens er in OPEN Rijg roepen staan. Voor kleine tabellen kan het 100% van alle gegevens zijn. In dat geval wijzigt ALTER INDEX Rebuild om de gegevens af te dwingen voor column Store. |
| [OPEN_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_rows_AVG] |Zoals hierboven |
| [CLOSED_rowgroup_rows] |Bekijk de rij met gesloten rijen als een Sanity-controle. |
| [CLOSED_rowgroup_count] |Het aantal gesloten Rijg roepen moet laag zijn als alle groepen worden weer gegeven. Gesloten Rijg roepen kunnen worden geconverteerd naar gecomprimeerde Rijg roepen met behulp van de ALTER INDEX... Opdracht opnieuw indelen. Dit is echter normaal gesp roken niet vereist. Gesloten groepen worden automatisch geconverteerd naar column Store-Rijg roepen op het proces van de achtergrond ' tuple-overdrijfing '. |
| [CLOSED_rowgroup_rows_MIN] |Gesloten Rijg roepen moeten een zeer hoog opvul aantal hebben. Als het doorvoer tempo voor een gesloten Rijg tekstrij laag is, is verdere analyse van de column Store vereist. |
| [CLOSED_rowgroup_rows_MAX] |Zoals hierboven |
| [CLOSED_rowgroup_rows_AVG] |Zoals hierboven |
| [Rebuild_Index_SQL] |SQL om de column store-index voor een tabel opnieuw op te bouwen |

## <a name="causes-of-poor-columnstore-index-quality"></a>Oorzaken van slechte kwaliteit voor columnstore-indexen

Als u tabellen met slechte segment kwaliteit hebt geïdentificeerd, wilt u de hoofd oorzaak identificeren.  Hieronder vindt u enkele andere veelvoorkomende oorzaken van slechte segment kwaliteit:

1. Geheugen druk wanneer de index is gemaakt
2. Hoog volume aan DML-bewerkingen
3. Kleine of trickle laad bewerkingen
4. Te veel partities

Deze factoren kunnen ertoe leiden dat een column store-index aanzienlijk kleiner is dan de optimale 1.000.000 rijen per Rijg tekstrij. Ze kunnen er ook voor zorgen dat rijen naar de Delta-Rijg groep gaan in plaats van een gecomprimeerde Rijg groep.

### <a name="memory-pressure-when-index-was-built"></a>Geheugen druk wanneer de index is gemaakt

Het aantal rijen per gecomprimeerde Rijg werk groep is direct gerelateerd aan de breedte van de rij en de hoeveelheid geheugen die beschikbaar is voor het verwerken van de Rijg groep.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Daarom is het best practice de sessie te geven die naar uw column store-index tabellen wordt geschreven en zo veel mogelijk geheugen toegang tot.  Omdat er sprake is van een afweging tussen geheugen en gelijktijdigheid, is de richt lijn voor de juiste geheugen toewijzing afhankelijk van de gegevens in elke rij van de tabel, de Data Warehouse-eenheden die aan uw systeem zijn toegewezen en het aantal gelijktijdigheids sleuven dat u kunt geven aan de sessie die gegevens naar uw tabel schrijft.

### <a name="high-volume-of-dml-operations"></a>Hoog volume aan DML-bewerkingen

Een groot aantal DML-bewerkingen dat rijen bijwerkt en verwijdert kan inefficiëntie in de column Store veroorzaken. Dit geldt met name wanneer het meren deel van de rijen in een Rijg groep wordt gewijzigd.

- Als u een rij uit een gecomprimeerde Rijg groep verwijdert, wordt de rij alleen als verwijderd logisch gemarkeerd. De rij blijft in de gecomprimeerde Rijg groep totdat de partitie of tabel opnieuw is opgebouwd.
- Als u een rij invoegt, wordt de rij toegevoegd aan een interne rowstore-tabel met de naam Delta Rijg Row. De ingevoegde rij wordt niet geconverteerd naar de column Store tot de Delta-Rijg groep vol is en is gemarkeerd als gesloten. Rijg roepen worden gesloten zodra de maximum capaciteit van 1.048.576 rijen is bereikt.
- Het bijwerken van een rij in de column Store-indeling wordt verwerkt als logische verwijdering en vervolgens een invoeg actie. De ingevoegde rij kan worden opgeslagen in het Delta-archief.

Batch-update-en Insert-bewerkingen die de bulk drempel van 102.400 rijen per partitie-afgevulde verdeling overschrijden, gaan rechtstreeks naar de column Store-indeling. Uitgaande van een even distributie, moet u echter meer dan 6.144.000 rijen wijzigen in één bewerking voor dit probleem. Als het aantal rijen voor een bepaalde partitie met uitlijning kleiner is dan 102.400, gaan de rijen naar het Delta-archief en blijven deze totdat er voldoende rijen zijn ingevoegd of gewijzigd om de Rijg groep te sluiten of de index is opnieuw opgebouwd.

### <a name="small-or-trickle-load-operations"></a>Kleine of trickle laad bewerkingen

Kleine belasting die in een toegewezen SQL-pool stroomt, ook wel bekend als trickle-belasting. Ze vertegenwoordigen meestal een nabije constante stroom van gegevens die door het systeem worden opgenomen. Omdat deze stroom bijna continu is, is de hoeveelheid rijen echter niet bijzonder groot. Vaker dan niet de gegevens zijn aanzienlijk onder de drempel waarde die is vereist voor het direct laden naar de column Store-indeling.

In deze situaties is het vaak beter om de gegevens eerst in Azure Blob-opslag in te voeren en deze te laten oplopen voordat ze worden geladen. Deze techniek wordt vaak wel *micro-batching* genoemd.

### <a name="too-many-partitions"></a>Te veel partities

Een andere manier om rekening mee te houden is de impact van het partitioneren van de geclusterde column Store-tabellen.  Voordat partitioneren wordt toegewezen, worden uw gegevens al verdeeld in 60-data bases.  Door partities te partitioneren, worden uw gegevens verder verdeeld.  Als u uw gegevens partitioneert, moet u er rekening mee houden dat **elke** partitie ten minste 1.000.000 rijen nodig heeft om te profiteren van een geclusterde column store-index.  Als u een tabel in 100 partities partitioneert, moet uw tabel ten minste 6.000.000.000 rijen hebben om te profiteren van een geclusterde column store-index (60 distributies *100 partities* 1.000.000 rijen). Als uw 100-partitie tabel geen 6.000.000.000-rijen bevat, verlaagt u het aantal partities of kunt u in plaats daarvan een heap-tabel gebruiken.

Als uw tabellen zijn geladen met enkele gegevens, volgt u de onderstaande stappen om tabellen te identificeren en opnieuw te maken met behulp van suboptimale geclusterde column Store-indexen.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexen opnieuw samen stellen om de segment kwaliteit te verbeteren

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Stap 1: een gebruiker identificeren of maken die gebruikmaakt van de juiste resource klasse

Een snelle manier om de segment kwaliteit direct te verbeteren is het opnieuw samen stellen van de index.  De SQL geretourneerd door de bovenstaande weer gave retourneert een instructie ALTER INDEX Rebuild die kan worden gebruikt om uw indexen opnieuw samen te stellen. Wanneer u de indexen opnieuw bouwt, moet u ervoor zorgen dat u voldoende geheugen toewijst aan de sessie die uw index opnieuw bouwt.  Als u dit wilt doen, verhoogt u de resource klasse van een gebruiker die machtigingen heeft om de index op deze tabel opnieuw op te bouwen naar het aanbevolen minimum.

Hieronder ziet u een voor beeld van hoe u meer geheugen aan een gebruiker toewijst door de resource klasse te verg Roten. Zie [resource klassen voor werkbelasting beheer](resource-classes-for-workload-management.md)als u wilt werken met resource klassen.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Stap 2: geclusterde column Store-indexen opnieuw samen stellen met een hogere resource class-gebruiker

Meld u aan als de gebruiker uit stap 1 (bijvoorbeeld LoadUser), dat nu gebruikmaakt van een hogere resource klasse en voer de instructies ALTER INDEX uit. Zorg ervoor dat deze gebruiker gewijzigde machtigingen heeft voor de tabellen waarin de index opnieuw wordt opgebouwd. In deze voor beelden ziet u hoe u de volledige column store-index opnieuw bouwt of hoe u een enkele partitie opnieuw bouwt. In grote tabellen is het praktisch om indexen op één keer tegelijk opnieuw op te bouwen.

U kunt de tabel ook kopiëren naar een nieuwe tabel [met behulp van CTAS](sql-data-warehouse-develop-ctas.md)in plaats van de index opnieuw samen te stellen. Wat is de beste manier? Voor grote hoeveel heden gegevens is CTAS doorgaans sneller dan [ALTER index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Voor kleinere gegevens volumes is ALTER INDEX eenvoudiger te gebruiken en hoeft u de tabel niet uit te wisselen.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Het opnieuw opbouwen van een index in de toegewezen SQL-groep is een offline bewerking.  Voor meer informatie over het opnieuw opbouwen van indexen raadpleegt u de sectie ALTER INDEX Rebuild in [Column Store-indexen defragmenteren](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en [ALTER index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Stap 3: controleren of de kwaliteit van geclusterde column Store-segmenten is verbeterd

Voer de query opnieuw uit met een slechte segment kwaliteit en controleer of de segment kwaliteit is verbeterd.  Als de segment kwaliteit niet is verbeterd, kan het zijn dat de rijen in de tabel extra breed zijn.  Overweeg het gebruik van een hogere resource klasse of DWU bij het opnieuw opbouwen van uw indexen.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexen opnieuw samen stellen met CTAS en partitie wisseling

In dit voor beeld wordt de instructie [create table as select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) en Partition switch gebruikt voor het opnieuw samen stellen van een tabel partitie.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Zie [using partitions in DEDICATED SQL pool](sql-data-warehouse-tables-partition.md)voor meer informatie over het opnieuw maken van partities met CTAS.

## <a name="next-steps"></a>Volgende stappen

Zie [tabellen ontwikkelen](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.

---
title: Tabellen indexeren
description: Aanbevelingen en voorbeelden voor het indexeren van tabellen in toegewezen SQL-pool.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/16/2021
author: XiaoyuMSFT
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 58f3eed8b16ff3ed02c6dfac6dc7d72ebb4ca374
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599975"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indexeren van toegewezen SQL-pooltabellen in Azure Synapse Analytics

Aanbevelingen en voorbeelden voor het indexeren van tabellen in toegewezen SQL-pool.

## <a name="index-types"></a>Indextypen

Toegewezen SQL-pool biedt verschillende indexeringsopties, waaronder geclusterde [columnstore-indexen,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [geclusterde indexen en niet-geclusterde indexen](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en een niet-indexoptie, ook wel [heap genoemd.](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

Zie de documentatie CREATE TABLE [(toegewezen SQL-pool)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) als u een tabel met een index wilt maken.

## <a name="clustered-columnstore-indexes"></a>Geclusterde columnstore-indexen

Toegewezen SQL-pool maakt standaard een geclusterde columnstore-index wanneer er geen indexopties zijn opgegeven voor een tabel. Geclusterde columnstore-tabellen bieden zowel het hoogste niveau van gegevenscompressie als de beste algemene queryprestaties.  Geclusterde columnstore-tabellen presteren over het algemeen beter dan geclusterde index- of heap-tabellen en zijn meestal de beste keuze voor grote tabellen.  Daarom is geclusterde columnstore de beste plek om te beginnen wanneer u niet zeker weet hoe u uw tabel indexeert.  

Als u een geclusterde columnstore-tabel wilt maken, geeft u CLUSTERED COLUMNSTORE INDEX op in de WITH-component of laat u de WITH-component uitgeschakeld:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Er zijn enkele scenario's waarbij geclusterde columnstore mogelijk geen goede optie is:

- Columnstore-tabellen bieden geen ondersteuning voor varchar(max), nvarchar(max) en varbinary(max). Overweeg in plaats daarvan heap- of geclusterde index.
- Columnstore-tabellen zijn mogelijk minder efficiënt voor tijdelijke gegevens. Overweeg heap en misschien zelfs tijdelijke tabellen.
- Kleine tabellen met minder dan 60 miljoen rijen. Overweeg heap-tabellen.

## <a name="heap-tables"></a>Heap-tabellen

Wanneer u tijdelijk gegevens in een toegewezen SQL-pool landing, kan het zijn dat het gebruik van een heap-tabel het algehele proces sneller maakt. Dit komt doordat de belasting van heaps sneller is dan voor indextabellen en in sommige gevallen de volgende lees-uit-de-cache kan worden uitgevoerd.  Als u alleen gegevens laadt om ze te faseeren voordat u meer transformaties gaat uitvoeren, is het laden van de tabel naar heap-tabel veel sneller dan het laden van de gegevens naar een geclusterde columnstore-tabel. Daarnaast wordt het laden van gegevens naar een [tijdelijke tabel](sql-data-warehouse-tables-temporary.md) sneller geladen dan het laden van een tabel naar permanente opslag.  Nadat de gegevens zijn geladen, kunt u indexen in de tabel maken voor snellere queryprestaties.  

Cluster columnstore-tabellen krijgen optimale compressie zodra er meer dan 60 miljoen rijen zijn.  Voor kleine opzoektabellen, minder dan 60 miljoen rijen, kunt u heap of een geclusterde index gebruiken voor snellere queryprestaties. 

Als u een heap-tabel wilt maken, geeft u heap op in de WITH-component:

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

Geclusterde indexen presteren mogelijk beter dan geclusterde columnstore-tabellen wanneer één rij snel moet worden opgehaald. Voor query's waarbij een enkele of zeer weinig rijzoekactie met extreme snelheid is vereist, kunt u een geclusterde of niet-geclusterde secundaire index overwegen. Het nadeel van het gebruik van een geclusterde index is dat alleen query's profiteren van query's die gebruikmaken van een zeer selectief filter op de geclusterde indexkolom. Als u het filter op andere kolommen wilt verbeteren, kan een niet-geclusterde index worden toegevoegd aan andere kolommen. Elke index die aan een tabel wordt toegevoegd, voegt echter zowel ruimte als verwerkingstijd toe aan de belasting.

Als u een geclusterde indextabel wilt maken, geeft u clustered INDEX op in de WITH-component:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Als u een niet-geclusterde index wilt toevoegen aan een tabel, gebruikt u de volgende syntaxis:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Geclusterde columnstore-indexen optimaliseren

Geclusterde columnstore-tabellen zijn ingedeeld in gegevens in segmenten.  Een hoge segmentkwaliteit is essentieel voor het bereiken van optimale queryprestaties in een columnstore-tabel.  Segmentkwaliteit kan worden gemeten met het aantal rijen in een gecomprimeerde rijgroep.  Segmentkwaliteit is het meest optimaal wanneer er ten minste 100.000 rijen per gecomprimeerde rijgroep zijn en de prestaties verbeteren, omdat het aantal rijen per rijgroep 1.048.576 rijen benadert, wat de meeste rijen zijn die een rijgroep kan bevatten.

De onderstaande weergave kan op uw systeem worden gemaakt en gebruikt om de gemiddelde rijen per rijgroep te berekenen en suboptimate columnstore-indexen voor clusters te identificeren.  De laatste kolom in deze weergave genereert een SQL-instructie die kan worden gebruikt om uw indexen opnieuw te bouwen.

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

Nu u de weergave hebt gemaakt, kunt u deze query uitvoeren om tabellen met rijgroepen met minder dan 100.000 rijen te identificeren. Het is natuurlijk mogelijk dat u de drempelwaarde van 100.000 wilt verhogen als u op zoek bent naar een optimale segmentkwaliteit.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Zodra u de query hebt uitgevoerd, kunt u de gegevens bekijken en de resultaten analyseren. In deze tabel wordt uitgelegd waar u naar moet zoeken in de analyse van uw rijgroep.

| Kolom | Deze gegevens gebruiken |
| --- | --- |
| [table_partition_count] |Als de tabel is gepartitief, verwacht u mogelijk hogere groepstellingen Voor open rijen. Aan elke partitie in de distributie kan in theorie een open rijgroep zijn gekoppeld. Factor this in your analysis. Een kleine tabel die is gepartitiefd, kan worden geoptimaliseerd door de partitionering helemaal te verwijderen, omdat dit de compressie zou verbeteren. |
| [row_count_total] |Totaal aantal rijen voor de tabel. U kunt deze waarde bijvoorbeeld gebruiken om het percentage rijen met de gecomprimeerde status te berekenen. |
| [row_count_per_distribution_MAX] |Als alle rijen gelijkmatig zijn verdeeld, is deze waarde het doelaantal rijen per distributie. Vergelijk deze waarde met de compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totaal aantal rijen in columnstore-indeling voor de tabel. |
| [COMPRESSED_rowgroup_rows_AVG] |Als het gemiddelde aantal rijen aanzienlijk lager is dan het maximumaantal rijen voor een rijgroep, kunt u CTAS of ALTER INDEX REBUILD gebruiken om de gegevens opnieuw tecomprimeren |
| [COMPRESSED_rowgroup_count] |Aantal rijgroepen in columnstore-indeling. Als dit aantal zeer hoog is ten opzichte van de tabel, is dit een indicator dat de columnstore-dichtheid laag is. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rijen worden logisch verwijderd in columnstore-indeling. Als het getal hoog is ten opzichte van de tabelgrootte, kunt u overwegen de partitie opnieuw te maken of de index opnieuw te bouwen, omdat deze fysiek wordt verwijderd. |
| [COMPRESSED_rowgroup_rows_MIN] |Gebruik dit in combinatie met de kolommen AVG en MAX om inzicht te krijgen in het bereik van waarden voor de rijgroepen in uw columnstore. Een laag getal boven de drempelwaarde voor belasting (102.400 per distributie op partitie uitgelijnd) geeft aan dat er optimalisaties beschikbaar zijn in de gegevensbelasting |
| [COMPRESSED_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_count] |Open rijgroepen zijn normaal. U kunt redelijk één OPEN-rijgroep per tabeldistributie verwachten (60). Overmatige getallen stellen voor dat gegevens over meerdere partities worden geladen. Controleer de partitioneringsstrategie om er zeker van te zijn dat deze goed werkt |
| [OPEN_rowgroup_rows] |Elke rijgroep kan maximaal 1.048.576 rijen hebben. Gebruik deze waarde om te zien hoe vol de open rijgroepen momenteel zijn |
| [OPEN_rowgroup_rows_MIN] |Open groepen geven aan dat gegevens lastig in de tabel worden geladen of dat de vorige belasting over de resterende rijen in deze rijgroep is gesleed. Gebruik de kolommen MIN, MAX en AVG om te zien hoeveel gegevens er in rijgroepen OPENEN staan. Voor kleine tabellen kan dit 100% van alle gegevens zijn. In dat geval wijzigt u INDEX REBUILD om de gegevens te forceeren voor columnstore. |
| [OPEN_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_rows_AVG] |Zoals hierboven |
| [CLOSED_rowgroup_rows] |Bekijk de rijen van de gesloten rijgroep als een controle op de gezondheid. |
| [CLOSED_rowgroup_count] |Het aantal gesloten rijgroepen moet laag zijn als deze al worden gezien. Gesloten rijgroepen kunnen worden geconverteerd naar gecomprimeerde rijgroepen met behulp van ALTER INDEX ... Opdracht REORGANIZE. Dit is echter normaal gesproken niet vereist. Gesloten groepen worden automatisch geconverteerd naar columnstore-rijgroepen door het achtergrondproces 'tuple mover'. |
| [CLOSED_rowgroup_rows_MIN] |Gesloten rijgroepen moeten een zeer hoge opvulsnelheid hebben. Als de opvulsnelheid voor een gesloten rijgroep laag is, is verdere analyse van de columnstore vereist. |
| [CLOSED_rowgroup_rows_MAX] |Zoals hierboven |
| [CLOSED_rowgroup_rows_AVG] |Zoals hierboven |
| [Rebuild_Index_SQL] |SQL voor het herbouwen van de columnstore-index voor een tabel |

## <a name="impact-of-index-maintenance"></a>Gevolgen van indexonderhoud

De kolom `Rebuild_Index_SQL` in de weergave bevat een instructie die kan worden gebruikt om uw `vColumnstoreDensity` `ALTER INDEX REBUILD` indexen opnieuw te bouwen. Zorg er bij het herbouwen van uw indexen voor dat u voldoende geheugen toekent aan de sessie die uw index herbouwt. Om dit te doen, verhoogt u [de resourceklasse](resource-classes-for-workload-management.md) van een gebruiker die machtigingen heeft om de index voor deze tabel opnieuw te bouwen tot het aanbevolen minimum. Zie Indexen herbouwen om [de segmentkwaliteit](#rebuilding-indexes-to-improve-segment-quality) te verbeteren verder in dit artikel voor een voorbeeld.

Voor een tabel met een geordende geclusterde columnstore-index worden de gegevens opnieuw gesorteerd `ALTER INDEX REBUILD` met tempdb. Tempdb bewaken tijdens herbouwbewerkingen. Als u meer tempdb-ruimte nodig hebt, schaalt u de databasegroep omhoog. Schaal weer omlaag zodra de index opnieuw is opgebouwd.

Voor een tabel met een geordende geclusterde columnstore-index worden `ALTER INDEX REORGANIZE` de gegevens niet opnieuw gesorteerd. Als u gegevens opnieuw wilt sorteren, gebruikt u `ALTER INDEX REBUILD` .

Zie Prestaties afstemmen met geordende geclusterde columnstore-index voor meer informatie over geordende [geclusterde columnstore-indexen.](performance-tuning-ordered-cci.md)

## <a name="causes-of-poor-columnstore-index-quality"></a>Oorzaken van slechte kwaliteit voor columnstore-indexen

Als u tabellen met slechte segmentkwaliteit hebt geïdentificeerd, wilt u de hoofdoorzaak identificeren.  Hieronder vindt u enkele andere veelvoorkomende oorzaken van slechte segmentkwaliteit:

1. Geheugendruk bij het bouwen van de index
2. Groot aantal DML-bewerkingen
3. Kleine of lastige belastingsbewerkingen
4. Te veel partities

Deze factoren kunnen ertoe leiden dat een columnstore-index aanzienlijk minder heeft dan de optimale 1 miljoen rijen per rijgroep. Ze kunnen er ook voor zorgen dat rijen naar de deltarijgroep gaan in plaats van naar een gecomprimeerde rijgroep.

### <a name="memory-pressure-when-index-was-built"></a>Geheugendruk bij het bouwen van de index

Het aantal rijen per gecomprimeerde rijgroep is rechtstreeks gerelateerd aan de breedte van de rij en de hoeveelheid geheugen die beschikbaar is voor het verwerken van de rijgroep.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Daarom is het best practice de sessie die naar uw columnstore-indextabellen schrijft toegang te geven tot zoveel mogelijk geheugen.  Omdat er sprake is van een balans tussen geheugen en gelijktijdigheid, zijn de richtlijnen voor de juiste geheugentoewijzing afhankelijk van de gegevens in elke rij van uw tabel, de datawarehouse-eenheden die zijn toegewezen aan uw systeem en het aantal gelijktijdigheidssleuven dat u aan de sessie kunt geven die gegevens naar uw tabel schrijft.

### <a name="high-volume-of-dml-operations"></a>Groot aantal DML-bewerkingen

Een groot aantal DML-bewerkingen die rijen bijwerken en verwijderen, kan inefficiëntie in de columnstore introduceren. Dit geldt met name wanneer de meeste rijen in een rijgroep worden gewijzigd.

- Als u een rij uit een gecomprimeerde rijgroep verwijderd, wordt de rij alleen logisch als verwijderd markeert. De rij blijft in de gecomprimeerde rijgroep totdat de partitie of tabel opnieuw is opgebouwd.
- Als u een rij invoegt, wordt de rij toegevoegd aan een interne rowstore-tabel die een deltarijgroep wordt genoemd. De ingevoegde rij wordt pas geconverteerd naar columnstore als de deltarijgroep vol is en is gemarkeerd als gesloten. Rijgroepen worden gesloten zodra ze de maximale capaciteit van 1.048.576 rijen hebben bereikt.
- Het bijwerken van een rij in columnstore-indeling wordt verwerkt als een logische delete en vervolgens een invoeging. De ingevoegde rij kan worden opgeslagen in de Delta Store.

Batchbewerkingen voor bijwerken en invoegen die de bulkdrempel van 102.400 rijen per distributie met partitie uitgelijnd overschrijden, gaan rechtstreeks naar de columnstore-indeling. Als we echter uitgaan van een gelijkmatige verdeling, moet u in één bewerking meer dan 6,144 miljoen rijen wijzigen om dit te kunnen uitvoeren. Als het aantal rijen voor een bepaalde partitieverdeling kleiner is dan 102.400, gaan de rijen naar de Delta-opslag en blijven ze totdat er voldoende rijen zijn ingevoegd of gewijzigd om de rijgroep te sluiten of de index opnieuw is opgebouwd.

### <a name="small-or-trickle-load-operations"></a>Kleine of lastige belastingsbewerkingen

Kleine belastingen die naar toegewezen SQL-pool stromen, worden ook wel lastige belastingen genoemd. Ze vertegenwoordigen doorgaans een bijna constante stroom van gegevens die door het systeem worden opgenomen. Omdat deze stroom echter bijna doorlopend is, is het volume van rijen niet erg groot. Vaak vallen de gegevens aanzienlijk onder de drempelwaarde die is vereist voor een directe belasting naar columnstore-indeling.

In dergelijke situaties is het vaak beter om de gegevens eerst in Azure Blob Storage te laden en ze te laten verzamelen voordat ze worden geladen. Deze techniek wordt vaak *microbatching genoemd.*

### <a name="too-many-partitions"></a>Te veel partities

Een ander ding om rekening mee te houden, is de impact van partitionering op uw geclusterde columnstore-tabellen.  Voordat u partitioneert, verdeelt een toegewezen SQL-pool uw gegevens al in 60 databases.  Partitioneren verdeelt uw gegevens verder.  Als u uw gegevens partitioneert, moet u er rekening mee houden dat elke partitie ten minste 1 miljoen rijen nodig heeft om te kunnen profiteren van een geclusterde columnstore-index.   Als u uw tabel partitioneert in 100 partities, heeft uw tabel ten minste 6 miljard rijen nodig om te profiteren van een geclusterde columnstore-index (60 distributies *100* partities 1 miljoen rijen). Als uw tabel met 100 partities geen 6 miljard rijen heeft, vermindert u het aantal partities of overweegt u in plaats daarvan een heap-tabel te gebruiken.

Nadat uw tabellen met enkele gegevens zijn geladen, volgt u de onderstaande stappen om tabellen te identificeren en opnieuw te bouwen met suboptimalen van geclusterde columnstore-indexen.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexen herbouwen om segmentkwaliteit te verbeteren

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Stap 1: identificeer of maak een gebruiker die de juiste resourceklasse gebruikt

Een snelle manier om de kwaliteit van segmenten onmiddellijk te verbeteren, is door de index opnieuw te bouwen.  De SQL die door de bovenstaande weergave wordt geretourneerd, retourneert een ALTER INDEX REBUILD-instructie die kan worden gebruikt om uw indexen opnieuw te bouwen. Zorg er bij het herbouwen van uw indexen voor dat u voldoende geheugen toekent aan de sessie die uw index herbouwt. Als u dit wilt doen, verhoogt u de resourceklasse van een gebruiker die machtigingen heeft om de index voor deze tabel opnieuw te bouwen tot het aanbevolen minimum.

Hieronder vindt u een voorbeeld van hoe u meer geheugen kunt toewijzen aan een gebruiker door de resourceklasse te vergroten. Zie Resourceklassen voor workloadbeheer voor informatie over het werken met [resourceklassen.](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser';
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Stap 2: geclusterde columnstore-indexen herbouwen met een hogere resourceklassegebruiker

Meld u aan als de gebruiker uit stap 1 (LoadUser), die nu een hogere resourceklasse gebruikt, en voer de ALTER INDEX-instructies uit. Zorg ervoor dat deze gebruiker de machtiging ALTER heeft voor de tabellen waarin de index opnieuw wordt opgebouwd. Deze voorbeelden laten zien hoe u de hele columnstore-index opnieuw bouwt of hoe u één partitie opnieuw bouwt. Bij grote tabellen is het praktischer om indexen één partitie tegelijk opnieuw te bouwen.

U kunt de tabel ook kopiëren naar een nieuwe tabel met CTAS in plaats van de index opnieuw [te bouwen.](sql-data-warehouse-develop-ctas.md) Welke manier is het beste? Voor grote hoeveelheden gegevens is CTAS meestal sneller dan [ALTER INDEX.](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Voor kleinere hoeveelheden gegevens is ALTER INDEX eenvoudiger te gebruiken en hoeft u de tabel niet om te wisselen.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5;
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE);
```

Het herbouwen van een index in een toegewezen SQL-pool is een offlinebewerking.  Zie de sectie ALTER INDEX REBUILD in [Columnstore Indexes Defragmentation](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en ALTER INDEX voor meer informatie over het herbouwen [van indexen.](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Stap 3: Controleren of de kwaliteit van geclusterde columnstore-segmenten is verbeterd

Opnieuw uitvoeren van de query die tabel met slechte segmentkwaliteit geïdentificeerd en controleren of segmentkwaliteit is verbeterd.  Als de segmentkwaliteit niet is verbeterd, kan het zijn dat de rijen in uw tabel extra breed zijn.  Overweeg het gebruik van een hogere resourceklasse of DWU bij het herbouwen van uw indexen.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexen herbouwen met CTAS en partition switching

In dit voorbeeld worden de [CREATE TABLE CTAS-instructie (AS SELECT)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) en partities overschakeling gebruikt om een tabelpartitie opnieuw te bouwen.

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

Zie Using partitions in dedicated SQL pool (Partities gebruiken in toegewezen [SQL-pool)](sql-data-warehouse-tables-partition.md)voor meer informatie over het opnieuw maken van partities met behulp van CTAS.

## <a name="next-steps"></a>Volgende stappen

Zie Tabellen ontwikkelen voor meer informatie over het ontwikkelen [van tabellen.](sql-data-warehouse-tables-overview.md)

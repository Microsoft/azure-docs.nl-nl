---
title: Tabellen partitioneren
description: Aanbevelingen en voor beelden voor het gebruik van tabel partities in een toegewezen SQL-groep
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
ms.openlocfilehash: f65c1d6fda09d7762a59fb5a932a72ad706a767a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448019"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>Partitioneren van tabellen in toegewezen SQL-groep

Aanbevelingen en voor beelden voor het gebruik van tabel partities in een toegewezen SQL-groep.

## <a name="what-are-table-partitions"></a>Wat zijn tabel partities?

Met tabel partities kunt u uw gegevens onderverdelen in kleinere gegevens groepen. In de meeste gevallen worden tabel partities gemaakt in een datum kolom. Partitioneren wordt ondersteund op alle toegewezen SQL-groeps tabel typen. inclusief geclusterde column Store, geclusterde index en heap. Partitioneren wordt ook ondersteund voor alle distributie typen, inclusief hash-of round robin gedistribueerd.  

Partitioneren kan gebruikmaken van gegevens onderhoud en query prestaties. Of de IT-mede werkers al dan niet van elkaar afhankelijk zijn van de manier waarop gegevens worden geladen en of voor beide doel einden dezelfde kolom kan worden gebruikt, omdat partitioneren slechts op één kolom kan worden uitgevoerd.

### <a name="benefits-to-loads"></a>Te laden voor delen

Het belangrijkste voor deel van partitionering in een toegewezen SQL-groep is het verbeteren van de efficiëntie en prestaties van het laden van gegevens door het gebruik van het verwijderen van partities, scha kelen en samen voegen. In de meeste gevallen worden gegevens gepartitioneerd in een datum kolom die nauw is verbonden met de volg orde waarin de gegevens in de SQL-groep worden geladen. Een van de grootste voor delen van het gebruik van partities voor het onderhouden van gegevens is het vermijden van transactie logboek registratie. Hoewel het eenvoudig is om gegevens in te voegen, bij te werken of te verwijderen, kan dit de eenvoudigste benadering zijn, met een beetje goed en moeite, met partitioneren tijdens het laad proces en de prestaties aanzienlijk kunnen verbeteren.

Het wisselen van partities kan worden gebruikt om een sectie van een tabel snel te verwijderen of te vervangen.  Een tabel met verkoop feiten kan bijvoorbeeld alleen gegevens bevatten over de afgelopen 36 maanden. Aan het einde van elke maand worden de oudste verkoop gegevens uit de tabel verwijderd.  Deze gegevens kunnen worden verwijderd met behulp van een instructie DELETE om de gegevens voor de oudste maand te verwijderen. 

Het verwijderen van een grote hoeveelheid gegevens rijen per rij met een DELETE-instructie kan echter te veel tijd in beslag nemen en het risico van grote trans acties te maken die lang duren als er iets fout gaat. Een optimale benadering is het verwijderen van de oudste gegevens partitie. Als het verwijderen van de afzonderlijke rijen uren kan duren, kan het verwijderen van een volledige partitie enkele seconden duren.

### <a name="benefits-to-queries"></a>Voor delen van query's

Partitioneren kan ook worden gebruikt om de query prestaties te verbeteren. Een query waarmee een filter op gepartitioneerde gegevens wordt toegepast, kan de scan beperken tot alleen de in aanmerking komende partities. Met deze filter methode kunt u een volledige tabel scan voor komen en alleen een kleinere subset van gegevens scannen. Met de introductie van geclusterde column Store-indexen zijn de voor delen van eliminatie prestaties minder gunstig, maar in sommige gevallen kan er sprake zijn van een voor deel van query's. 

Als bijvoorbeeld de tabel sales feiten is gepartitioneerd in 36 maanden met behulp van het veld verkoop datum, kunnen query's die filteren op de verkoop datum, de zoek actie overs laan in partities die niet overeenkomen met het filter.

## <a name="sizing-partitions"></a>Grootte van partities aanpassen

Hoewel partitioneren kan worden gebruikt om de prestaties van bepaalde scenario's te verbeteren, kan het maken van een tabel met **te veel** partities in bepaalde omstandigheden de prestaties nadelig beïnvloeden.  Deze problemen zijn vooral van toepassing op geclusterde column Store-tabellen. 

Voor partitionering is het belang rijk te weten wanneer u partitioneren gebruikt en het aantal partities dat moet worden gemaakt. Er is geen harde regel om te bepalen hoeveel partities te vaak zijn. Dit is afhankelijk van uw gegevens en het aantal partities dat u tegelijkertijd laadt. Een geslaagd partitie schema is doorgaans tien tot honderden partities, geen duizenden.

Wanneer u partities maakt voor **geclusterde column Store** -tabellen, is het belang rijk om te bepalen hoeveel rijen bij elke partitie horen. Voor optimale compressie en prestaties van geclusterde column Store-tabellen, is mini maal 1.000.000 rijen per distributie en partitie nodig. Voordat partities worden gemaakt, splitst de toegewezen SQL-pool al elke tabel in 60 gedistribueerde data bases. 

Elke partitie die is toegevoegd aan een tabel, is een aanvulling op de distributies die zijn gemaakt achter de schermen. Als in dit voor beeld de tabel sales feiten 36 maandelijkse partities bevat, en u hebt gezien dat een toegewezen SQL-groep 60 distributies heeft, moet de tabel verkoop feiten 60.000.000 rijen per maand of 2.100.000.000 rijen bevatten wanneer alle maanden worden ingevuld. Als een tabel minder dan het aanbevolen minimum aantal rijen per partitie bevat, kunt u overwegen minder partities te gebruiken om het aantal rijen per partitie te verg Roten. 

Zie het artikel [indexeren](sql-data-warehouse-tables-index.md) , dat query's bevat waarmee de kwaliteit van de cluster-column Store-indexen kan worden beoordeeld voor meer informatie.

## <a name="syntax-differences-from-sql-server"></a>Syntaxis verschillen ten opzichte van SQL Server

Een toegewezen SQL-pool introduceert een manier om partities te definiëren die eenvoudiger zijn dan SQL Server. Het partitioneren van functies en schema's wordt niet gebruikt in een toegewezen SQL-groep, omdat deze zich in SQL Server bevinden. In plaats daarvan hoeft u alleen de gepartitioneerde kolom en de grens punten aan te duiden. 

De syntaxis van partitionering kan enigszins afwijken van SQL Server, de basis concepten zijn hetzelfde. SQL Server en toegewezen SQL-pool ondersteunen één partitie kolom per tabel, die partities kan bevatten. Zie [gepartitioneerde tabellen en indexen](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over partitioneren.

In het volgende voor beeld wordt de instructie [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruikt voor het partitioneren van de tabel FactInternetSales in de kolom OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Partitioneren migreren van SQL Server

Als u SQL Server partitie definities wilt migreren naar een exclusieve SQL-groep:

- Elimineer het SQL Server [partitie schema](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Voeg de [partitie functie](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) definitie toe aan uw Create Table.

Als u een gepartitioneerde tabel van een SQL Server-exemplaar migreert, kan de volgende SQL u helpen bij het berekenen van het aantal rijen in elke partitie. Houd er rekening mee dat als dezelfde partitie granulatie wordt gebruikt in een toegewezen SQL-groep, het aantal rijen per partitie afneemt met een factor van 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partitie overschakelen

De toegewezen SQL-groep ondersteunt het splitsen van partities, samen voegen en scha kelen. Elk van deze functies wordt uitgevoerd met behulp van de instructie [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Als u wilt scha kelen tussen partities tussen twee tabellen, moet u ervoor zorgen dat de partities worden uitgelijnd op de respectievelijke grenzen en dat de tabel definities overeenkomen. Als er geen controle beperkingen beschikbaar zijn voor het afdwingen van het bereik van waarden in een tabel, moet de bron tabel dezelfde partitie grenzen bevatten als de doel tabel. Als de grenzen van de partitie niet hetzelfde zijn, mislukt de partitie-switch omdat de meta gegevens van de partitie niet worden gesynchroniseerd.

### <a name="how-to-split-a-partition-that-contains-data"></a>Een partitie splitsen die gegevens bevat

De meest efficiënte methode voor het splitsen van een partitie die al gegevens bevat, is het gebruik van een- `CTAS` instructie. Als de gepartitioneerde tabel een geclusterde column Store is, moet de tabel partitie leeg zijn voordat deze kan worden gesplitst.

In het volgende voor beeld wordt een gepartitioneerde column Store-tabel gemaakt. Er wordt één rij in elke partitie ingevoegd:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Met de volgende query wordt het aantal rijen gezocht met behulp van de `sys.partitions` Catalogus weergave:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

De volgende opdracht splitsen ontvangt een fout bericht:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, niveau 15, status 1, regel 44 SPLIT van de instructie ALTER PARTITION is mislukt omdat de partitie niet leeg is. Alleen lege partities kunnen worden gesplitst wanneer een column store-index in de tabel voor komt. Overweeg om de column store-index uit te scha kelen voordat u de instructie ALTER PARTITION geeft en bouw vervolgens de column store-index opnieuw op nadat ALTER PARTITION is voltooid.

U kunt echter gebruiken `CTAS` om een nieuwe tabel te maken om de gegevens op te slaan.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Wanneer de grenzen van de partitie zijn uitgelijnd, is een switch toegestaan. Hiermee verlaat u de bron tabel met een lege partitie die u vervolgens kunt splitsen.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

U kunt de gegevens het beste uitlijnen met de nieuwe partitie grenzen met `CTAS` en de gegevens vervolgens weer omzetten in de hoofd tabel.

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Wanneer u de gegevens verplaatsing hebt voltooid, is het een goed idee om de statistieken op de doel tabel te vernieuwen. Het bijwerken van statistieken zorgt ervoor dat de statistieken de nieuwe distributie van de gegevens in hun respectieve partities nauw keurig weer spie gelen.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Nieuwe gegevens laden in partities die gegevens in één stap bevatten

Het laden van gegevens in partities met partitie wisseling is een handige manier om nieuwe gegevens in een tabel te plaatsen die niet zichtbaar zijn voor gebruikers.  Het kan lastig zijn om te omgaan met de vergren deling van de vergrendelings conflicten die zijn gekoppeld aan het overschakelen van de partitie.  

Als u de bestaande gegevens in een partitie wilt verwijderen, moet u hiervoor een `ALTER TABLE` vereiste gebruiken om de gegevens te deactiveren.  Daarna was een andere `ALTER TABLE` nood zakelijk om te scha kelen in de nieuwe gegevens.  

In de toegewezen SQL-groep `TRUNCATE_TARGET` wordt de optie ondersteund in de `ALTER TABLE` opdracht.  Met `TRUNCATE_TARGET` de `ALTER TABLE` opdracht worden bestaande gegevens in de partitie overschreven met nieuwe gegevens.  Hieronder ziet u een voor beeld waarin wordt gebruikt `CTAS` om een nieuwe tabel te maken met de bestaande gegevens, nieuwe gegevens in te voegen en vervolgens alle gegevens weer te scha kelen in de doel tabel, waarbij de bestaande gegevens worden overschreven.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
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

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Broncode beheer voor tabel partities

Als u wilt voor komen dat uw tabel wordt **geroestd** in uw broncode beheer systeem, kunt u de volgende aanpak overwegen:

1. De tabel maken als een gepartitioneerde tabel, maar zonder partitie waarden

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` de tabel als onderdeel van het implementatie proces:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Met deze methode blijft de code in broncode beheer statisch en kunnen de grens waarden voor partitioneren dynamisch zijn. in de loop van de tijd met het ontwikkelen van de SQL-groep.

## <a name="next-steps"></a>Volgende stappen

Zie de artikelen in [tabel Overview](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.

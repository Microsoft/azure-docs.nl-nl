---
title: Transacties optimaliseren
description: Meer informatie over het optimaliseren van de prestaties van uw transactionele code in de toegewezen SQL-pool en het minimaliseren van het risico op lange terugdraai acties.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f7d6f8ca285fdc024db9ba952af9f7d169e7188
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678471"
---
# <a name="optimizing-transactions-in-dedicated-sql-pool-in-azure-synapse-analytics"></a>Trans acties in de toegewezen SQL-groep in azure Synapse Analytics optimaliseren

Meer informatie over het optimaliseren van de prestaties van uw transactionele code in de toegewezen SQL-pool en het minimaliseren van het risico op lange terugdraai acties.

## <a name="transactions-and-logging"></a>Trans acties en logboek registratie

Trans acties vormen een belang rijk onderdeel van een relationele SQL-groeps engine. Trans acties worden gebruikt tijdens het wijzigen van de gegevens. Deze trans acties kunnen expliciet of impliciet zijn. Enkele INSERT-, UPDATE-en DELETE-instructies zijn alle voor beelden van impliciete trans acties. Expliciete trans acties gebruiken BEGIN TRAN, COMMIT TRAN of ROLLBACK TRAN. Expliciete trans acties worden meestal gebruikt wanneer meerdere wijzigings instructies samen in één atomische eenheid moeten worden gebonden.

Wijzigingen in de SQL-groep worden bijgehouden met transactie Logboeken. Elke distributie heeft een eigen transactie logboek. Schrijf bewerkingen in transactie logboeken worden automatisch uitgevoerd. Er is geen configuratie vereist. Hoewel dit proces de schrijf bewerking echter waarborgt, wordt er een overhead in het systeem geïntroduceerd. U kunt dit effect minimaliseren door transactionele code te schrijven. Transactionele code is breed onderverdeeld in twee categorieën.

* Gebruik minimale logboek registratie constructies, indien mogelijk
* Gegevens verwerken met behulp van het bereik van batches om enkelvoudige langlopende trans acties te voor komen
* Een patroon voor het wisselen van partities voor grote wijzigingen in een bepaalde partitie aannemen

## <a name="minimal-vs-full-logging"></a>Minimale versus volledige logboek registratie

In tegens telling tot volledig geregistreerde bewerkingen, waarbij het transactie logboek wordt gebruikt voor het bijhouden van elke wijziging in rijen, worden minimale geregistreerde bewerkingen bijgehouden voor de mate van toewijzings-en meta gegevens wijzigingen. Daarom vergt minimale logboek registratie alleen de gegevens die nodig zijn voor het terugdraaien van de trans actie na een fout of voor een expliciete aanvraag (terugdraaiende TRAN). Als er veel minder informatie wordt bijgehouden in het transactie logboek, voert een mini maal geregistreerde bewerking beter uit dan een volledig geregistreerde volledige logboek bewerking. Omdat minder schrijf bewerkingen het transactie logboek gaan volgen, wordt er een veel kleinere hoeveelheid logboek gegevens gegenereerd en dus meer I/O-efficiënt.

De limieten voor de transactie beveiliging zijn alleen van toepassing op volledig geregistreerde bewerkingen.

> [!NOTE]
> Mini maal geregistreerde bewerkingen kunnen deel nemen aan expliciete trans acties. Als alle wijzigingen in de toewijzings structuren worden bijgehouden, is het mogelijk om mini maal geregistreerde bewerkingen terug te draaien.

## <a name="minimally-logged-operations"></a>Mini maal geregistreerde bewerkingen

De volgende bewerkingen kunnen mini maal worden geregistreerd:

* CREATE TABLE als selecteren ([CTAS](sql-data-warehouse-develop-ctas.md))
* INVOEGEN.. UITGESCHAKELD
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH-PARTITIE

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Bewerkingen voor het verplaatsen van interne gegevens (zoals BROADCASTen en wille keurige volg orde) worden niet beïnvloed door de limiet voor trans actie-beveiliging.

## <a name="minimal-logging-with-bulk-load"></a>Minimale logboek registratie met bulksgewijs laden

CTAS en invoegen... Selecteer beide bewerkingen voor bulksgewijs laden. Beide worden echter beïnvloed door de definitie van de doel tabel en zijn afhankelijk van het laad scenario. In de volgende tabel wordt uitgelegd wanneer bulk bewerkingen volledig of mini maal zijn geregistreerd:  

| Primaire index | Scenario voor laden | Modus logboek registratie |
| --- | --- | --- |
| Heap |Alle |**Minimaal** |
| Geclusterde index |Lege doel tabel |**Minimaal** |
| Geclusterde index |Geladen rijen overlappen niet met bestaande pagina's in het doel |**Minimaal** |
| Geclusterde index |Geladen rijen overlappen met bestaande pagina's in het doel |Volledig |
| Geclusterde column store-index |Batch grootte >= 102.400 per partitie, uitgelijnde distributie |**Minimaal** |
| Geclusterde column store-index |Batch grootte < 102.400 per partitie, uitgelijnde distributie |Volledig |

Het is een goed idee dat elke schrijf bewerking voor het bijwerken van secundaire of niet-geclusterde indexen altijd volledige geregistreerde bewerkingen is.

> [!IMPORTANT]
> Een toegewezen SQL-groep heeft 60 distributies. Als alle rijen gelijkmatig worden gedistribueerd en gespreid over één partitie, moet uw batch dus 6.144.000 rijen bevatten of groter zijn om mini maal te worden aangemeld bij het schrijven naar een geclusterde column store-index. Als de tabel is gepartitioneerd en de rijen worden ingevoegd, worden de limieten voor 6.144.000 rijen per partitie in rekening gebracht, uitgaande van de gegevens distributie. Elke partitie in elke distributie moet onafhankelijk de drempel waarde voor de 102.400-rij overschrijden voor het invoegen om mini maal te worden aangemeld bij de distributie.

Het laden van gegevens in een niet-lege tabel met een geclusterde index kan vaak een combi natie van volledig vastgelegde en mini maal geregistreerde rijen bevatten. Een geclusterde index is een evenwichtige boom structuur (b-structuur) van pagina's. Als de pagina die wordt geschreven, al rijen van een andere trans actie bevat, worden deze schrijf bewerkingen volledig vastgelegd. Als de pagina echter leeg is, wordt de schrijf bewerking naar die pagina mini maal vastgelegd.

## <a name="optimizing-deletes"></a>Verwijderingen optimaliseren

VERWIJDEREN is een volledig geregistreerde bewerking.  Als u een grote hoeveelheid gegevens in een tabel of partitie moet verwijderen, is het vaak beter voor `SELECT` de gegevens die u wilt blijven gebruiken, die kunnen worden uitgevoerd als een mini maal vastgelegde bewerking.  Als u de gegevens wilt selecteren, maakt u een nieuwe tabel met [CTAS](sql-data-warehouse-develop-ctas.md).  Nadat deze is gemaakt, gebruikt u de [naam wijzigen](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om de oude tabel om te wisselen met de zojuist gemaakte tabel.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Updates optimaliseren

UPDATE is een volledig geregistreerde bewerking.  Als u een groot aantal rijen in een tabel of partitie moet bijwerken, kan het veel efficiënter zijn om een mini maal geregistreerde bewerking zoals [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) te gebruiken.

In het voor beeld onder een volledige tabel update is geconverteerd naar een CTAS, zodat de logboek registratie mini maal mogelijk is.

In dit geval voegen we retro actief een kortings bedrag toe aan de verkoop in de tabel:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Het opnieuw maken van grote tabellen kan profiteren van het gebruik van speciale werk belasting beheer functies voor een SQL-groep. Zie [resource klassen voor workload Management](resource-classes-for-workload-management.md)voor meer informatie.

## <a name="optimizing-with-partition-switching"></a>Optimaliseren met partitie wisseling

Als u in een [tabel partitie](sql-data-warehouse-tables-partition.md)op grote schaal wijzigingen ondervindt, is een patroon voor het wisselen van partities zinvol. Als het wijzigen van de gegevens significant is en meerdere partities omvat, wordt door het herhalen van de partities hetzelfde resultaat gerealiseerd.

De stappen voor het uitvoeren van een partitie-switch zijn als volgt:

1. Een lege out-partitie maken
2. De update uitvoeren als een CTAS
3. De bestaande gegevens in de tabel uitzetten
4. Scha kelen tussen de nieuwe gegevens
5. De gegevens opschonen

Maak echter de volgende Help-procedure om te helpen bij het identificeren van de te scha kelen partities.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Met deze procedure wordt het hergebruik van code gemaximaliseerd en blijft het voor beeld van de partitie-switch compacter.

De volgende code toont de eerder genoemde stappen voor het behalen van een volledige partitie switch-routine.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Logboek registratie met kleine batches minimaliseren

Voor bewerkingen voor grote gegevens wijziging kan het zinvol zijn om de bewerking te verdelen in segmenten of batches om de werk eenheid te bereiken.

Een van de volgende code is een werkend voor beeld. De Batch grootte is ingesteld op een trivial getal om de techniek te markeren. In werkelijkheid zou de Batch grootte aanzienlijk groter zijn.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Richt lijnen voor onderbreken en schalen

Met exclusieve SQL-pool kunt u uw toegewezen SQL-groep op aanvraag [onderbreken, hervatten en schalen](sql-data-warehouse-manage-compute-overview.md) . Wanneer u uw toegewezen SQL-groep pauzeert of schaalt, is het belang rijk om te begrijpen dat alle trans acties in de vlucht onmiddellijk worden beëindigd. zorgt ervoor dat alle openstaande trans acties worden teruggedraaid. Als uw werk belasting een langlopende en onvolledige gegevens wijziging heeft ondergaan voordat de onderbreking of schaal bewerking wordt uitgevoerd, moet dit werk ongedaan worden gemaakt. Dit kan invloed hebben op de tijd die nodig is om uw toegewezen SQL-groep te onderbreken of te schalen.

> [!IMPORTANT]
> Beide `UPDATE` en `DELETE` zijn volledig geregistreerde bewerkingen, waardoor deze bewerkingen voor ongedaan maken/opnieuw uitvoeren aanzienlijk langer duren dan de gelijkwaardige minimale geregistreerde bewerkingen.

Het beste scenario is om trans acties voor het wijzigen van de vlucht gegevens te laten volt ooien voordat een toegewezen SQL-groep wordt onderbroken of geschaald. Dit scenario is echter mogelijk niet altijd praktisch. Als u het risico van een lange terugdraai actie wilt beperken, kunt u een van de volgende opties overwegen:

* Langlopende bewerkingen opnieuw uitvoeren met [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* De bewerking in segmenten opsplitsen. werken op een subset van de rijen

## <a name="next-steps"></a>Volgende stappen

Bekijk [trans acties in de toegewezen SQL-groep](sql-data-warehouse-develop-transactions.md) voor meer informatie over isolatie niveaus en transactionele limieten.  Zie voor een overzicht van andere aanbevolen procedures [speciale aanbevolen procedures voor SQL-Pools](sql-data-warehouse-best-practices.md).

---
title: Statistieken voor tabellen maken en bijwerken
description: Aanbevelingen en voor beelden voor het maken en bijwerken van statistieken voor query optimalisatie voor tabellen in een toegewezen SQL-groep.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673363"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tabel statistieken voor exclusieve SQL-groep in azure Synapse Analytics

In dit artikel vindt u aanbevelingen en voor beelden voor het maken en bijwerken van statistieken voor query optimalisatie voor tabellen in een toegewezen SQL-groep.

## <a name="why-use-statistics"></a>Waarom statistieken gebruiken?

Hoe meer exclusieve SQL-groepen weet wat uw gegevens zijn, des te sneller query's kunnen worden uitgevoerd. Na het laden van gegevens in een toegewezen SQL-groep is het verzamelen van statistieken voor uw gegevens een van de belangrijkste dingen die u kunt doen om uw query's te optimaliseren.

De exclusieve SQL pool-query optimalisatie is een op kosten gebaseerd Optimizer. Hiermee worden de kosten van verschillende query plannen vergeleken en wordt vervolgens het abonnement met de laagste kosten gekozen. In de meeste gevallen kiest u het plan dat het snelst wordt uitgevoerd.

Als de Optimizer bijvoorbeeld een schatting maakt van de datum waarop uw query wordt gefilterd, wordt er één regel geretourneerd. Als er wordt geschat dat de geselecteerde datum 1.000.000 rijen retourneert, wordt een ander plan geretourneerd.

## <a name="automatic-creation-of-statistic"></a>Automatisch statistieken maken

Wanneer de optie Data Base AUTO_CREATE_STATISTICS is ingeschakeld, analyseert de toegewezen SQL-groep binnenkomende gebruikers query's voor ontbrekende statistieken.

Als er statistieken ontbreken, worden in de query Optimizer statistieken gemaakt voor afzonderlijke kolommen in het query-predikaat of de samenvoegings voorwaarde om de kardinaliteit voor het query plan te verbeteren.

> [!NOTE]
> Automatisch maken van statistieken is momenteel standaard ingeschakeld.

U kunt controleren of uw toegewezen SQL-groep AUTO_CREATE_STATISTICS geconfigureerd door de volgende opdracht uit te voeren:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Als uw toegewezen SQL-groep niet AUTO_CREATE_STATISTICS is geconfigureerd, raden we u aan deze eigenschap in te scha kelen door de volgende opdracht uit te voeren:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Met deze instructies wordt het automatisch maken van statistieken geactiveerd:

- SELECT
- INVOEGEN-SELECTEREN
- CTAS
- UPDATE
- DELETE
- UITLEGGEN wanneer een samen voeging of aanwezigheid van een predikaat wordt gedetecteerd

> [!NOTE]
> Het automatisch maken van statistieken wordt niet gemaakt voor tijdelijke of externe tabellen.

Het automatisch maken van statistieken wordt synchroon uitgevoerd, zodat u een slechtere query prestaties kunt opdoen als in uw kolommen statistieken ontbreken. De tijd om statistieken voor één kolom te maken, is afhankelijk van de grootte van de tabel.

Om te voor komen dat de prestaties meetbaar zijn, moet u ervoor zorgen dat de statistieken zijn gemaakt door de benchmark workload uit te voeren voordat u het systeem profileert.

> [!NOTE]
> Het maken van statistieken wordt geregistreerd in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) onder een andere gebruikers context.

Wanneer er automatische statistieken worden gemaakt, worden de volgende notatie toegepast: _WA_Sys_<kolom-id van 8 cijfers in hex>_<tabel-ID van 8 cijfers in hexadecimale>. U kunt de statistieken weer geven die al zijn gemaakt door de [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -opdracht uit te voeren:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

De table_name is de naam van de tabel die de statistieken bevat die moeten worden weer gegeven. Deze tabel kan geen externe tabel zijn. Het doel is de naam van de doel index, statistieken of kolom waarvoor statistische gegevens moeten worden weer gegeven.

## <a name="update-statistics"></a>Statistieken bijwerken

Een best practice is het bijwerken van statistieken voor datum kolommen elke dag wanneer er nieuwe datums worden toegevoegd. Elke keer dat er nieuwe rijen worden geladen in de toegewezen SQL-groep, worden nieuwe laad datums of transactie datums toegevoegd. Deze toevoegingen wijzigen de gegevens distributie en maken de statistieken verouderd.

Statistieken voor een kolom land/regio in een tabel Klant hoeven nooit te worden bijgewerkt omdat de verdeling van waarden doorgaans niet wordt gewijzigd. Ervan uitgaande dat de distributie een constante is tussen klanten, het toevoegen van nieuwe rijen aan de tabel variatie is geen wijziging van de gegevens distributie.

Als uw toegewezen SQL-groep echter slechts één land/regio bevat en u gegevens uit een nieuw land/nieuwe regio hebt, worden er gegevens uit meerdere landen/regio's opgeslagen. vervolgens moet u de statistieken bijwerken in de kolom land/regio.

Hier volgen de aanbevelingen voor het bijwerken van statistieken:

|||
|-|-|
| **Frequentie van updates voor statistieken**  | Conservatief: dagelijks </br> Nadat u uw gegevens hebt geladen of getransformeerd |
| **Steekproeven** |  Minder dan 1.000.000.000 rijen, gebruik standaard steekproef (20 procent). </br> Gebruik meer dan 1.000.000.000 rijen om de steek proef van twee procent te gebruiken. |

Een van de eerste vragen die u kunt stellen wanneer u problemen met een query wilt oplossen, **zijn "zijn de statistieken bijgewerkt?"**

Deze vraag is niet een die kan worden beantwoord door de leeftijd van de gegevens. Een up-to-date statistieken object kan oud zijn als er geen wezenlijke wijzigingen zijn aangebracht in de onderliggende gegevens. Wanneer het aantal rijen ingrijpend is gewijzigd of als er sprake is van een aanmerkelijke wijziging in de verdeling van waarden voor een kolom, is het tijd om statistieken *bij te werken* . 

Er is geen dynamische beheer weergave om te bepalen of de gegevens in de tabel zijn gewijzigd sinds de laatste keer dat de statistieken zijn bijgewerkt.  U kunt met behulp van de volgende twee query's bepalen of uw statistieken verouderd zijn.

**Query 1:**  Bepaal het verschil tussen het aantal rijen uit de statistieken (**stats_row_count**) en het werkelijke aantal rijen (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Query 2:** Bepaal de leeftijd van uw statistieken door de laatste keer te controleren of uw statistieken zijn bijgewerkt op elke tabel. 

> [!NOTE]
> Als er sprake is van een aanmerkelijke wijziging in de verdeling van waarden voor een kolom, moet u de statistieken bijwerken, onafhankelijk van de laatste keer dat ze zijn bijgewerkt.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Voor **datum kolommen** in een toegewezen SQL-groep zijn bijvoorbeeld vaak updates met statistieken nodig. Elke keer dat er nieuwe rijen worden geladen in de toegewezen SQL-groep, worden nieuwe laad datums of transactie datums toegevoegd. Deze toevoegingen wijzigen de gegevens distributie en maken de statistieken verouderd.

De statistieken voor de kolom gender in een tabel Klant kunnen echter nooit worden bijgewerkt. Ervan uitgaande dat de distributie een constante is tussen klanten, het toevoegen van nieuwe rijen aan de tabel variatie is geen wijziging van de gegevens distributie.

Als uw toegewezen SQL-groep slechts één gender bevat en een nieuwe vereiste resulteert in meerdere geslachten, moet u de statistieken bijwerken in de kolom gender.

Zie algemene richt lijnen voor [Statistieken](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor meer informatie.

## <a name="implementing-statistics-management"></a>Statistieken beheer implementeren

Het is vaak een goed idee om het proces voor het laden van gegevens uit te breiden om ervoor te zorgen dat de statistieken aan het einde van de belasting worden bijgewerkt om het blok keren of de conflicten tussen gelijktijdige query's te voor komen.  

Het laden van gegevens is het vaak wijzigen van de grootte van de tabellen en/of de verdeling van waarden. Het laden van gegevens is een logische plaats voor het implementeren van een aantal beheer processen.

De volgende richt lijnen zijn voor het bijwerken van uw statistieken:

- Zorg ervoor dat voor elke geladen tabel ten minste één statistiek object is bijgewerkt. Hiermee wordt de informatie over de tabel grootte (aantal rijen en aantal pagina's) bijgewerkt als onderdeel van de statistieken-update.
- Focus op kolommen die deel uitmaken van de componenten samen voegen, groeperen op, ORDER BY en DISTINCT.
- Overweeg om kolommen met een oplopende sleutel, zoals transactie datums, vaker bij te werken, omdat deze waarden niet in het statistieken histogram worden opgenomen.
- Overweeg om kolommen met statische distributie minder vaak bij te werken.
- Houd er rekening mee dat elk statistiek object in volg orde wordt bijgewerkt. Eenvoudig implementeren `UPDATE STATISTICS <TABLE_NAME>` is niet altijd ideaal, met name voor Wide tables met veel statistieken objecten.

Zie voor meer informatie de [schatting van kardinaliteit](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Voor beelden: Statistieken maken

Deze voor beelden laten zien hoe u verschillende opties kunt gebruiken om statistieken te maken. De opties die u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en de manier waarop de kolom wordt gebruikt in query's.

### <a name="create-single-column-statistics-with-default-options"></a>Statistieken met één kolom maken met standaard opties

Als u statistieken wilt maken voor een kolom, geeft u een naam op voor het statistiek object en de naam van de kolom.

Deze syntaxis maakt gebruik van alle standaard opties. Standaard wordt **20 procent** van de tabel gesampled bij het maken van statistieken.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken voor één kolom maken door elke rij te controleren

Het standaard sampling-percentage van 20 procent is voldoende voor de meeste situaties. U kunt de sampling frequentie echter aanpassen.

Gebruik de volgende syntaxis om een voor beeld te hebben van de volledige tabel:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproef grootte op te geven

U kunt de steekproef grootte ook opgeven als een percentage:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Statistieken voor één kolom maken op enkele rijen

U kunt ook statistieken maken voor een deel van de rijen in uw tabel. Dit wordt een gefilterde statistiek genoemd.

U kunt bijvoorbeeld gefilterde statistieken gebruiken wanneer u van plan bent om een specifieke partitie van een grote gepartitioneerde tabel op te vragen. Door alleen statistieken te maken op basis van de partitie waarden, wordt de nauw keurigheid van de statistieken verbeterd, waardoor de query prestaties worden verbeterd.

In dit voor beeld worden statistieken gemaakt voor een reeks waarden. De waarden kunnen eenvoudig worden gedefinieerd, zodat ze overeenkomen met het bereik van de waarden in een partitie.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Voor het query optimalisatie programma om te overwegen gefilterde statistieken te gebruiken bij het kiezen van het gedistribueerde query plan, moet de query binnen de definitie van het statistiek object passen. In het vorige voor beeld moet de WHERE-component van de query Kol1 waarden opgeven tussen 2000101 en 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Statistieken met één kolom maken met alle opties

U kunt de opties ook samen combi neren. In het volgende voor beeld wordt een gefilterd statistiek object gemaakt met een aangepaste steekproef grootte:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Zie [Statistieken maken](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor de volledige verwijzing.

### <a name="create-multi-column-statistics"></a>Statistieken over meerdere kolommen maken

Als u een statistieken object met meerdere kolommen wilt maken, gebruikt u de voor gaande voor beelden, maar geeft u meer kolommen op.

> [!NOTE]
> Het histogram dat wordt gebruikt om het aantal rijen in het query resultaat te schatten, is alleen beschikbaar voor de eerste kolom die wordt vermeld in de definitie van het statistieken-object.

In dit voor beeld is het histogram voor de *product \_ categorie*. Statistieken voor meerdere kolommen worden berekend voor *product \_ categorie* -en *product \_ sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Omdat er een correlatie bestaat tussen *product \_ categorie* en *product \_ \_ subcategorie*, kan een statistieken object met meerdere kolommen nuttig zijn als deze kolommen tegelijkertijd worden gebruikt.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Statistieken maken voor alle kolommen in een tabel

Een manier om statistieken te maken, is door opdrachten voor het maken van statistieken op te geven nadat u de tabel hebt gemaakt:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Een opgeslagen procedure gebruiken om statistieken te maken voor alle kolommen in een SQL-groep

De toegewezen SQL-groep heeft geen door het systeem opgeslagen procedure die gelijk is aan sp_create_stats in SQL Server. Met deze opgeslagen procedure maakt u één kolom statistieken-object op elke kolom in een SQL-groep die nog geen statistieken heeft.

Het volgende voor beeld helpt u aan de slag te gaan met het ontwerp van uw SQL-groep. U kunt deze aanpassen aan uw behoeften.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Voer de opgeslagen procedure uit om statistieken te maken voor alle kolommen in de tabel met behulp van de standaard waarden.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Aanroepen van deze procedure om statistieken te maken voor alle kolommen in de tabel met behulp van een fullscan.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Als u voorbeeld statistieken wilt maken voor alle kolommen in de tabel, typt u 3 en het steek proef percentage. Deze procedure maakt gebruik van een sample frequentie van 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Voor beelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, kunt u het volgende doen:

- Eén statistiek object bijwerken. Geef de naam op van het statistiek object dat u wilt bijwerken.
- Alle statistieken-objecten in een tabel bijwerken. Geef de naam van de tabel op in plaats van één specifiek statistiek object.

### <a name="update-one-specific-statistics-object"></a>Een specifiek statistieken object bijwerken

Gebruik de volgende syntaxis om een specifiek statistiek object bij te werken:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Door specifieke statistieken objecten bij te werken, kunt u de tijd en resources die nodig zijn om statistieken te beheren, minimaliseren. Hiervoor moet u de beste statistieken objecten kiezen om bij te werken.

### <a name="update-all-statistics-on-a-table"></a>Alle statistieken voor een tabel bijwerken

Een eenvoudige methode voor het bijwerken van alle statistieken objecten in een tabel is:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS dbo.table1;
```

De instructie UPDATE STATISTICs is eenvoudig te gebruiken. Houd er rekening mee dat *alle* statistieken in de tabel worden bijgewerkt en dat daarom meer werk kan worden uitgevoerd dan nodig is. Als de prestaties geen probleem zijn, is dit de eenvoudigste en meest volledige manier om te garanderen dat statistieken up-to-date zijn.

> [!NOTE]
> Wanneer alle statistieken voor een tabel worden bijgewerkt, voert de toegewezen SQL-pool een scan uit om een voor beeld van de tabel voor elk statistiek object te bekijken. Als de tabel groot is en veel kolommen en veel statistieken heeft, kan het efficiënter zijn om afzonderlijke statistieken bij te werken op basis van de behoefte.

`UPDATE STATISTICS`Zie [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md)voor een implementatie van een procedure. De implementatie methode wijkt enigszins af van de voor gaande `CREATE STATISTICS` procedure, maar het resultaat is hetzelfde.

Zie [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor de volledige syntaxis.

## <a name="statistics-metadata"></a>Statistieken meta gegevens

Er zijn verschillende systeem weergaven en-functies die u kunt gebruiken om informatie te vinden over statistieken. U kunt bijvoorbeeld zien of een statistiek object verouderd is door gebruik te maken van de functie voor statistieken-datum om te zien wanneer de statistieken voor het laatst zijn gemaakt of bijgewerkt.

### <a name="catalog-views-for-statistics"></a>Catalogus weergaven voor statistieken

Deze systeem weergaven bieden informatie over statistieken:

| Catalogus weergave | Beschrijving |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke kolom. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk object in de data base. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk schema in de data base. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk statistiek object. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke kolom in het statistiek object. Koppelingen terug naar sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke tabel (inclusief externe tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk gegevens type. |

### <a name="system-functions-for-statistics"></a>Systeem functies voor statistieken

Deze systeem functies zijn handig voor het werken met statistieken:

| Systeem functie | Beschrijving |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Datum waarop het statistieken object voor het laatst is bijgewerkt. |
| [DBCC-SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Overzichts niveau en gedetailleerde informatie over de distributie van waarden, zoals begrepen door het statistiek object. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statistische kolommen en-functies combi neren in één weer gave

In deze weer gave worden kolommen met betrekking tot statistieken en resultaten van de functie STATS_DATE () gecombineerd.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS ()-voor beelden

DBCC SHOW_STATISTICS () toont de gegevens binnen een statistiek object. Deze gegevens zijn afkomstig uit drie delen:

- Header
- Dichtheids vector
- Histogram

De meta gegevens van de koptekst over de statistieken. In het histogram wordt de verdeling weer gegeven van de waarden in de eerste sleutel kolom van het statistiek object. De dichtheids vector meet de correlatie tussen kolommen.

> [!NOTE]
> De toegewezen SQL-groep berekent de kardinaliteit met een van de gegevens in het statistiek object.

### <a name="show-header-density-and-histogram"></a>Koptekst, densiteit en histogram weer geven

In dit eenvoudige voor beeld worden alle drie delen van een statistiek object weer gegeven:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Een of meer onderdelen van DBCC SHOW_STATISTICS () weer geven

Als u alleen specifieke onderdelen wilt weer geven, gebruikt u de- `WITH` component en geeft u op welke onderdelen u wilt zien:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Verschillen DBCC SHOW_STATISTICS ()

DBCC SHOW_STATISTICS () wordt in een specifieke SQL-groep uitgebreid vergeleken met SQL Server:

- Niet-gedocumenteerde functies worden niet ondersteund.
- Kan Stats_stream niet gebruiken.
- Kan geen resultaten voor specifieke subsets met statistische gegevens samen voegen. Bijvoorbeeld STAT_HEADER toevoegen DENSITY_VECTOR.
- NO_INFOMSGS kan niet worden ingesteld voor het onderdrukken van berichten.
- De namen van de vier Kante haken rondom statistieken kunnen niet worden gebruikt.
- Kan geen kolom namen gebruiken om statistieken objecten te identificeren.
- Aangepaste fout 2767 wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie [uw werk belasting controleren](sql-data-warehouse-manage-monitor.md) voor meer informatie over het verbeteren van de prestaties van query's

---
title: Statistieken maken en bijwerken met behulp van Azure Synapse SQL-resources
description: Aanbevelingen en voor beelden voor het maken en bijwerken van statistieken voor het optimaliseren van query's in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 450a089c6cc1c77ac26cb0aa339277d5c49b41c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594781"
---
# <a name="statistics-in-synapse-sql"></a>Statistieken in Synapse SQL

In dit artikel vindt u aanbevelingen en voor beelden voor het maken en bijwerken van statistieken voor het optimaliseren van query's met behulp van de Synapse SQL-resources: exclusieve SQL-groep en serverloze SQL-groep.

## <a name="statistics-in-dedicated-sql-pool"></a>Statistieken in de toegewezen SQL-groep

### <a name="why-use-statistics"></a>Waarom statistieken gebruiken?

Hoe meer exclusieve SQL-groepen weet over uw gegevens, hoe sneller query's kunnen worden uitgevoerd. Wanneer u gegevens in een toegewezen SQL-groep hebt geladen, is het verzamelen van statistieken voor uw gegevens een van de belangrijkste dingen die u kunt doen voor het optimaliseren van query's.  

De exclusieve SQL pool-query optimalisatie is een op kosten gebaseerd Optimizer. Hiermee worden de kosten van verschillende query plannen vergeleken en wordt vervolgens het abonnement met de laagste kosten gekozen. In de meeste gevallen kiest u het plan dat het snelst wordt uitgevoerd.

Als de Optimizer bijvoorbeeld een schatting maakt van de datum waarop uw query wordt gefilterd, wordt er één regel geretourneerd. Als er wordt geschat dat de geselecteerde datum 1.000.000 rijen retourneert, wordt een ander plan geretourneerd.

### <a name="automatic-creation-of-statistics"></a>Automatisch statistieken maken

De toegewezen SQL-groeps engine analyseert binnenkomende gebruikers query's voor ontbrekende statistieken wanneer de data base-AUTO_CREATE_STATISTICS optie is ingesteld op `ON` .  Als er statistieken ontbreken, worden in de query Optimizer statistieken gemaakt voor afzonderlijke kolommen in het predikaat query of de voor waarde voor samen voegen. 

Deze functie wordt gebruikt om de schattingen voor de kardinaliteit van het query plan te verbeteren.

> [!IMPORTANT]
> Automatisch maken van statistieken is momenteel standaard ingeschakeld.

U kunt controleren of uw data warehouse AUTO_CREATE_STATISTICS geconfigureerd door de volgende opdracht uit te voeren:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Als uw data warehouse geen AUTO_CREATE_STATISTICS ingeschakeld, raden we u aan deze eigenschap in te scha kelen door de volgende opdracht uit te voeren:

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
> Het automatisch maken van statistieken wordt niet gegenereerd voor tijdelijke of externe tabellen.

Het automatisch maken van statistieken wordt synchroon uitgevoerd. U kunt de query prestaties dus enigszins afnemen als in uw kolommen statistieken ontbreken. De tijd om statistieken voor één kolom te maken, is afhankelijk van de grootte van de tabel.

Om te voor komen dat de prestaties meetbaar zijn, moet u ervoor zorgen dat de statistieken zijn gemaakt door de benchmark workload uit te voeren voordat u het systeem profileert.

> [!NOTE]
> Het maken van statistieken wordt geregistreerd in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) onder een andere gebruikers context.

Wanneer er automatische statistieken worden gemaakt, worden de volgende notatie toegepast: _WA_Sys_<kolom-id van 8 cijfers in hex>_<tabel-ID van 8 cijfers in hexadecimale>. U kunt al gemaakte statistieken weer geven door de [DBCC-SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) opdracht uit te voeren:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

De table_name is de naam van de tabel die de statistieken bevat die moeten worden weer gegeven. Dit kan geen externe tabel zijn. Het doel is de naam van de doel index, statistieken of kolom waarvoor statistische gegevens moeten worden weer gegeven.

### <a name="update-statistics"></a>Statistieken bijwerken

Een best practice is het bijwerken van statistieken voor datum kolommen elke dag wanneer er nieuwe datums worden toegevoegd. Telkens wanneer er nieuwe rijen worden geladen in het Data Warehouse, worden er nieuwe belasting-of transactie datums toegevoegd. Deze toevoegingen wijzigen de gegevens distributie en maken de statistieken verouderd.

Statistieken voor een kolom land of regio in een tabel Klant hoeven nooit te worden bijgewerkt omdat de verdeling van waarden doorgaans niet wordt gewijzigd. Ervan uitgaande dat de distributie een constante is tussen klanten, het toevoegen van nieuwe rijen aan de tabel variatie is geen wijziging van de gegevens distributie.

Als uw data warehouse echter slechts één land of regio bevat en u gegevens uit een nieuw land of nieuwe regio haalt, moet u de statistieken bijwerken in de kolom land of regio.

Hier volgen enkele aanbevelingen voor het bijwerken van statistieken:

|Type|Aanbeveling|
|-|-|
| **Frequentie van updates voor statistieken**  | Conservatief: dagelijks </br> Nadat u uw gegevens hebt geladen of getransformeerd |
| **Steekproeven** |  Minder dan 1.000.000.000 rijen, gebruik standaard steekproef (20 procent). </br> Gebruik meer dan 1.000.000.000 rijen om de steek proef van twee procent te gebruiken. |

### <a name="determine-last-statistics-update"></a>De laatste statistieken-update bepalen

Een van de eerste vragen die u kunt stellen wanneer u problemen met een query wilt oplossen, **zijn "zijn de statistieken bijgewerkt?"**

Deze vraag is niet een die kan worden beantwoord door de leeftijd van de gegevens. Een up-to-date statistieken object kan oud zijn als er geen wezenlijke wijzigingen zijn aangebracht in de onderliggende gegevens. Wanneer het aantal rijen ingrijpend is gewijzigd of als er een wijziging in de verdeling van waarden voor een kolom *optreedt, is* het tijd om de statistieken bij te werken.

Er is geen dynamische beheer weergave beschikbaar om te bepalen of gegevens in de tabel zijn gewijzigd sinds de laatste keer dat de statistieken zijn bijgewerkt. Als u de leeftijd van uw statistieken kent, kunt u deel uitmaken van de afbeelding. 

U kunt de volgende query gebruiken om de laatste keer te bepalen dat uw statistieken op elke tabel zijn bijgewerkt.

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

Voor **datum kolommen** in een Data Warehouse, bijvoorbeeld, moeten updates voor regel matige statistieken doorgaans worden bijgewerkt. Telkens wanneer er nieuwe rijen worden geladen in het Data Warehouse, worden er nieuwe belasting-of transactie datums toegevoegd. Deze toevoegingen wijzigen de gegevens distributie en maken de statistieken verouderd.

Statistieken voor een gender kolom in een tabel van klanten hoeven nooit te worden bijgewerkt. Ervan uitgaande dat de distributie een constante is tussen klanten, het toevoegen van nieuwe rijen aan de tabel variatie is geen wijziging van de gegevens distributie.

Maar als uw data warehouse slechts één gender bevat en een nieuwe vereiste resulteert in meerdere geslachten, moet u de statistieken bijwerken in de kolom gender. 

Raadpleeg het artikel [over statistieken](/sql/relational-databases/statistics/statistics) voor meer informatie.

### <a name="implement-statistics-management"></a>Statistieken beheer implementeren

Het is vaak een goed idee om uw proces voor het laden van gegevens uit te breiden om ervoor te zorgen dat de statistieken aan het einde van de belasting worden bijgewerkt. Het laden van gegevens is het vaak wijzigen van de grootte van de tabellen, het distribueren van waarden of beide. Als zodanig is het laad proces een logische plaats voor het implementeren van een aantal beheer processen.

De volgende richt lijnen zijn van toepassing op het bijwerken van uw statistieken tijdens het laad proces:

- Zorg ervoor dat voor elke geladen tabel ten minste één statistiek object is bijgewerkt. Met dit proces wordt de tabel grootte (aantal rijen en pagina-items) bijgewerkt als onderdeel van de statistieken-update.
- Focus op kolommen die deel uitmaken van de componenten samen voegen, groeperen op, ORDER BY en DISTINCT.
- Overweeg om kolommen met een oplopende sleutel, zoals transactie datums, vaker bij te werken omdat deze waarden niet worden opgenomen in het statistieken histogram.
- Overweeg om kolommen met statische distributie minder vaak bij te werken.
- Houd er rekening mee dat elk statistiek object in volg orde wordt bijgewerkt. Eenvoudig implementeren `UPDATE STATISTICS <TABLE_NAME>` is niet altijd ideaal, met name voor Wide tables met veel statistieken objecten.

Zie voor meer informatie de [schatting van kardinaliteit](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Voor beelden: Statistieken maken

Deze voor beelden laten zien hoe u verschillende opties kunt gebruiken om statistieken te maken. De opties die u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en de manier waarop de kolom wordt gebruikt in query's.

#### <a name="create-single-column-statistics-with-default-options"></a>Statistieken met één kolom maken met standaard opties

Als u statistieken wilt maken voor een kolom, geeft u een naam op voor het statistiek object en de naam van de kolom.
Deze syntaxis maakt gebruik van alle standaard opties. Standaard worden met exclusieve SQL-pool voor **20 procent** van de tabel monsters gemaakt bij het maken van statistieken.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken voor één kolom maken door elke rij te controleren

Het standaard sampling-percentage van 20 procent is voldoende voor de meeste situaties. U kunt de sampling frequentie echter aanpassen. Gebruik de volgende syntaxis om een voor beeld te hebben van de volledige tabel:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproef grootte op te geven

U kunt ook de steekproef grootte opgeven als een percentage:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Statistieken voor één kolom maken op enkele rijen

U kunt ook statistieken maken voor een deel van de rijen in uw tabel. dit wordt een gefilterde statistiek genoemd.

U kunt bijvoorbeeld gefilterde statistieken gebruiken wanneer u van plan bent om een specifieke partitie van een grote gepartitioneerde tabel op te vragen. Door alleen statistieken te maken op basis van de partitie waarden, wordt de nauw keurigheid van de statistieken verbeterd. U ervaart ook de prestaties van query's.

In dit voor beeld worden statistieken gemaakt voor een reeks waarden. De waarden kunnen eenvoudig worden gedefinieerd, zodat ze overeenkomen met het bereik van de waarden in een partitie.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Voor het query optimalisatie programma om te overwegen gefilterde statistieken te gebruiken bij het kiezen van het gedistribueerde query plan, moet de query binnen de definitie van het statistiek object passen. In het vorige voor beeld moet de WHERE-component van de query Kol1 waarden opgeven tussen 2000101 en 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Statistieken met één kolom maken met alle opties

U kunt de opties ook samen combi neren. In het volgende voor beeld wordt een gefilterd statistiek object gemaakt met een aangepaste steekproef grootte:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Zie [Statistieken maken](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)voor de volledige verwijzing.

#### <a name="create-multi-column-statistics"></a>Statistieken over meerdere kolommen maken

Als u een statistieken object met meerdere kolommen wilt maken, gebruikt u de voor gaande voor beelden, maar geeft u meer kolommen op.

> [!NOTE]
> Het histogram dat wordt gebruikt om het aantal rijen in het query resultaat te schatten, is alleen beschikbaar voor de eerste kolom die wordt vermeld in de definitie van het statistieken-object.

In dit voor beeld is het histogram voor de *product \_ categorie*. Statistieken voor meerdere kolommen worden berekend voor *product \_ categorie* -en *product \_ sub_category*:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Omdat er een correlatie bestaat tussen *product \_ categorie* en *product \_ \_ subcategorie*, kan een statistieken object met meerdere kolommen nuttig zijn als deze kolommen tegelijkertijd worden gebruikt.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Statistieken maken voor alle kolommen in een tabel

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Een opgeslagen procedure gebruiken om statistieken te maken voor alle kolommen in een Data Base

De SQL-pool heeft geen opgeslagen systeem procedure die gelijk is aan sp_create_stats in SQL Server. Met deze opgeslagen procedure maakt u één kolom statistieken-object op elke kolom van de data base waarvoor nog geen statistieken zijn.

Het volgende voor beeld helpt u aan de slag te gaan met uw database ontwerp. U kunt deze aanpassen aan uw behoeften:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Als u statistieken wilt maken voor alle kolommen in de tabel met behulp van een fullscan, roept u deze procedure aan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Als u voorbeeld statistieken wilt maken voor alle kolommen in de tabel, typt u 3 en het steek proef percentage. In de onderstaande procedure wordt een sample frequentie van 20 procent gebruikt.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Voor beelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, kunt u het volgende doen:

- Eén statistiek object bijwerken. Geef de naam op van het statistiek object dat u wilt bijwerken.
- Alle statistieken-objecten in een tabel bijwerken. Geef de naam van de tabel op in plaats van één specifiek statistiek object.

#### <a name="update-one-specific-statistics-object"></a>Een specifiek statistieken object bijwerken

Gebruik de volgende syntaxis om een specifiek statistiek object bij te werken:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Door specifieke statistieken objecten bij te werken, kunt u de tijd en resources die nodig zijn om statistieken te beheren, minimaliseren. Deze actie vereist enkele voor waarden om de beste statistieken objecten te selecteren die moeten worden bijgewerkt.

#### <a name="update-all-statistics-on-a-table"></a>Alle statistieken voor een tabel bijwerken

Een eenvoudige methode voor het bijwerken van alle statistieken objecten in een tabel is:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS dbo.table1;
```

De instructie UPDATE STATISTICs is eenvoudig te gebruiken. Houd er rekening mee dat *alle* statistieken in de tabel worden bijgewerkt en dat er meer werk wordt gevraagd dan u nodig hebt. 

Als de prestaties niet van belang zijn, is deze methode de eenvoudigste en meest volledige manier om te garanderen dat statistieken up-to-date zijn.

> [!NOTE]
> Wanneer alle statistieken voor een tabel worden bijgewerkt, voert de toegewezen SQL-pool een scan uit om een voor beeld van de tabel voor elk statistiek object te bekijken. Als de tabel groot is en veel kolommen en veel statistieken heeft, kan het efficiënter zijn om afzonderlijke statistieken bij te werken op basis van de behoefte.

`UPDATE STATISTICS`Zie [tijdelijke tabellen](develop-tables-temporary.md)voor een implementatie van een procedure. De implementatie methode wijkt enigszins af van de voor gaande `CREATE STATISTICS` procedure, maar het resultaat is hetzelfde.
Zie [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)voor de volledige syntaxis.

### <a name="statistics-metadata"></a>Statistieken meta gegevens

Er zijn verschillende systeem weergaven en-functies die u kunt gebruiken om informatie te vinden over statistieken. U kunt bijvoorbeeld zien of een statistiek object mogelijk verouderd is door de functie STATS_DATE () te gebruiken. Met STATS_DATE () kunt u zien wanneer de statistieken voor het laatst zijn gemaakt of bijgewerkt.

#### <a name="catalog-views-for-statistics"></a>Catalogus weergaven voor statistieken

Deze systeem weergaven bieden informatie over statistieken:

| Catalogus weergave | Description |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke kolom. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk object in de data base. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk schema in de data base. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk statistiek object. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke kolom in het statistiek object. Koppelingen terug naar sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elke tabel (inclusief externe tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Eén rij voor elk gegevens type. |

#### <a name="system-functions-for-statistics"></a>Systeem functies voor statistieken

Deze systeem functies zijn handig voor het werken met statistieken:

| Systeem functie | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Datum waarop het statistieken object voor het laatst is bijgewerkt. |
| [DBCC-SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Overzichts niveau en gedetailleerde informatie over de distributie van waarden, zoals begrepen door het statistiek object. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statistische kolommen en-functies combi neren in één weer gave

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS ()-voor beelden

DBCC SHOW_STATISTICS () toont de gegevens binnen een statistiek object. Deze gegevens zijn afkomstig uit drie delen:

- Header
- Dichtheids vector
- Histogram

De kop bevat de meta gegevens over de statistieken. In het histogram wordt de verdeling weer gegeven van de waarden in de eerste sleutel kolom van het statistiek object. 

De dichtheids vector meet de correlatie tussen kolommen. De toegewezen SQL-groep berekent de kardinaliteit met een van de gegevens in het statistiek object.

#### <a name="show-header-density-and-histogram"></a>Koptekst, densiteit en histogram weer geven

In dit eenvoudige voor beeld worden alle drie delen van een statistiek object weer gegeven:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Een of meer onderdelen van DBCC SHOW_STATISTICS () weer geven

Als u alleen specifieke onderdelen wilt weer geven, gebruikt u de- `WITH` component en geeft u op welke onderdelen u wilt zien:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Verschillen DBCC SHOW_STATISTICS ()

`DBCC SHOW_STATISTICS()` is meer strikt geïmplementeerd in de toegewezen SQL-groep vergeleken met SQL Server:

- Niet-gedocumenteerde functies worden niet ondersteund.
- Kan Stats_stream niet gebruiken.
- Kan geen resultaten voor specifieke subsets met statistische gegevens samen voegen. Bijvoorbeeld STAT_HEADER toevoegen DENSITY_VECTOR.
- NO_INFOMSGS kan niet worden ingesteld voor het onderdrukken van berichten.
- De namen van de vier Kante haken rondom statistieken kunnen niet worden gebruikt.
- Kan geen kolom namen gebruiken om statistieken objecten te identificeren.
- Aangepaste fout 2767 wordt niet ondersteund.


## <a name="statistics-in-serverless-sql-pool"></a>Statistieken in de serverloze SQL-groep

Er worden statistieken gemaakt per bepaalde kolom voor een bepaalde gegevensset (opslagpad).

> [!NOTE]
> Er kunnen geen statistieken worden gemaakt voor LOB-kolommen.

### <a name="why-use-statistics"></a>Waarom statistieken gebruiken?

Hoe meer serverloze SQL-groepen weet wat uw gegevens zijn, des te sneller query's kunnen worden uitgevoerd. Het verzamelen van statistieken voor uw gegevens is een van de belangrijkste dingen die u kunt doen om uw query's te optimaliseren. 

De query optimalisatie van de serverloze SQL-groep is een op kosten gebaseerd Optimizer. Hiermee worden de kosten van verschillende query plannen vergeleken en wordt vervolgens het abonnement met de laagste kosten gekozen. In de meeste gevallen kiest u het plan dat het snelst wordt uitgevoerd. 

Als de Optimizer bijvoorbeeld een schatting maakt van de datum waarop uw query wordt gefilterd, wordt er één regel geretourneerd. Als er wordt geschat dat de geselecteerde datum 1.000.000 rijen retourneert, wordt een ander plan geretourneerd.

### <a name="automatic-creation-of-statistics"></a>Automatisch statistieken maken

Serverloze SQL-pool analyseert binnenkomende gebruikers query's voor ontbrekende statistieken. Als er statistieken ontbreken, worden in de query Optimizer statistieken gemaakt voor afzonderlijke kolommen in het query-predikaat of de samenvoegings voorwaarde om de kardinaliteit voor het query plan te verbeteren.

Met de instructie SELECT wordt het automatisch maken van statistieken geactiveerd.

> [!NOTE]
> Automatisch maken van statistieken is ingeschakeld voor Parquet-bestanden. Voor CSV-bestanden moet u hand matig statistieken maken totdat het automatisch maken van statistieken voor CSV-bestanden wordt ondersteund.

Het automatisch maken van statistieken wordt synchroon uitgevoerd, zodat u een slechtere query prestaties kunt opdoen als in uw kolommen statistieken ontbreken. De tijd voor het maken van statistieken voor één kolom is afhankelijk van de grootte van de beoogde bestanden.

### <a name="manual-creation-of-statistics"></a>Hand matig statistieken maken

Met serverloze SQL-pool kunt u statistieken hand matig maken. Voor CSV-bestanden moet u hand matig statistieken maken omdat het automatisch maken van statistieken niet is ingeschakeld voor CSV-bestanden. 

Raadpleeg de volgende voor beelden voor instructies over het hand matig maken van statistieken.

### <a name="update-statistics"></a>Statistieken bijwerken

Wijzigingen in gegevens in bestanden, verwijderen en toevoegen van bestanden resulteren in wijzigingen in de gegevens distributie en maakt statistieken verouderd. In dat geval moeten statistieken worden bijgewerkt.

Serverloze SQL-groep maakt statistieken automatisch opnieuw wanneer gegevens ingrijpend worden gewijzigd. Telkens wanneer statistieken automatisch worden gemaakt, wordt de huidige status van de gegevensset ook opgeslagen: bestands paden, grootten, datum van laatste wijziging.

Wanneer statistieken verouderd zijn, worden nieuwe gemaakt. Het algoritme doorloopt de gegevens en vergelijkt deze met de huidige status van de gegevensset. Als de grootte van de wijzigingen groter is dan de specifieke drempel waarde, worden de oude statistieken verwijderd en wordt de nieuwe gegevensset opnieuw gemaakt.

Hand matige statistieken worden nooit als verouderd gedeclareerd.

> [!NOTE]
> Automatische recreatie van statistieken is ingeschakeld voor Parquet-bestanden. Voor CSV-bestanden moet u hand matig statistieken verwijderen en maken totdat het automatisch maken van statistieken voor CSV-bestanden wordt ondersteund. Raadpleeg de voor beelden hieronder voor informatie over het verwijderen en maken van statistieken.

Een van de eerste vragen die u kunt stellen wanneer u problemen met een query wilt oplossen, **zijn "zijn de statistieken bijgewerkt?"**

Wanneer het aantal rijen ingrijpend is gewijzigd of als er sprake is van een aanmerkelijke wijziging in de verdeling van waarden voor een kolom, is het tijd om statistieken *bij te werken* .

> [!NOTE]
> Als er sprake is van een aanmerkelijke wijziging in de verdeling van waarden voor een kolom, moet u de statistieken bijwerken, onafhankelijk van de laatste keer dat ze zijn bijgewerkt.

### <a name="implement-statistics-management"></a>Statistieken beheer implementeren

U kunt uw gegevens pijplijn uitbreiden om ervoor te zorgen dat de statistieken worden bijgewerkt wanneer de gegevens ingrijpend worden gewijzigd door het toevoegen, verwijderen of wijzigen van bestanden.

De volgende richt lijnen zijn voor het bijwerken van uw statistieken:

- Zorg ervoor dat er ten minste één statistieken object is bijgewerkt voor de gegevensset. Dit is de informatie over de grootte (aantal rijen en pagina-items) als onderdeel van de statistieken-update.
- Focus op kolommen die deel uitmaken van de componenten WHERE, samen voegen, groeperen op, ORDER BY en DISTINCT.
- De kolommen met een oplopende sleutel, zoals transactie datums, vaker bijwerken omdat deze waarden niet worden opgenomen in het statistieken histogram.
- Werk kolommen met statische distributie minder vaak bij.

Zie voor meer informatie de [schatting van kardinaliteit](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Voor beelden: Statistieken maken voor een kolom in OpenRowset-pad

In de volgende voor beelden ziet u hoe u verschillende opties kunt gebruiken om statistieken te maken. De opties die u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en de manier waarop de kolom wordt gebruikt in query's.

> [!NOTE]
> U kunt op dit moment alleen statistieken voor één kolom maken.
>
> De volgende machtigingen zijn vereist om sp_create_openrowset_statistics en sp_drop_openrowset_statistics uit te voeren: BULK bewerkingen beheren of data base-BULK bewerkingen beheren.

De volgende opgeslagen procedure wordt gebruikt om statistieken te maken:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumenten: [ @stmt =] N ' statement_text '-Hiermee geeft u een Transact-SQL-instructie op waarmee kolom waarden worden geretourneerd die moeten worden gebruikt voor statistieken. U kunt TABLESAMPLE gebruiken om voor beelden van gegevens op te geven die moeten worden gebruikt. Als TABLESAMPLE niet is opgegeven, wordt FULLSCAN gebruikt.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV-steek proeven werken op dit moment niet, alleen FULLSCAN wordt ondersteund voor CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken voor één kolom maken door elke rij te controleren

Als u statistieken wilt maken voor een kolom, geeft u een query op die de kolom retourneert waarvoor u statistieken nodig hebt.

Standaard, als u niet anders opgeeft, gebruikt de serverloze SQL-pool 100% van de gegevens die in de gegevensset zijn opgegeven bij het maken van statistieken.

Als u bijvoorbeeld statistieken met standaard opties (FULLSCAN) wilt maken voor een kolom Year van de gegevensset op basis van het population.csv bestand:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproef grootte op te geven

U kunt de steekproef grootte opgeven als een percentage:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Voor beelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, moet u statistieken verwijderen en maken. De volgende opgeslagen procedure wordt gebruikt om statistieken neer te zetten:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> De volgende machtigingen zijn vereist om sp_create_openrowset_statistics en sp_drop_openrowset_statistics uit te voeren: BULK bewerkingen beheren of data base-BULK bewerkingen beheren.

Argumenten: [ @stmt =] N ' statement_text '-Hiermee geeft u de Transact-SQL-instructie op die wordt gebruikt wanneer de statistieken zijn gemaakt.

Als u de statistieken voor de kolom Year in de gegevensset, die is gebaseerd op het population.csv bestand, wilt bijwerken, moet u statistieken verwijderen en maken:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Voor beelden: Statistieken maken voor externe tabel kolom

In de volgende voor beelden ziet u hoe u verschillende opties kunt gebruiken om statistieken te maken. De opties die u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en de manier waarop de kolom wordt gebruikt in query's.

> [!NOTE]
> U kunt op dit moment alleen statistieken voor één kolom maken.

Als u statistieken wilt maken voor een kolom, geeft u een naam op voor het statistiek object en de naam van de kolom.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Arguments: external_table geeft een externe tabel op waarin statistieken moeten worden gemaakt.

FULLSCAN Compute-statistieken door alle rijen te scannen. FULLSCAN en voor beeld 100 PROCENT hebben dezelfde resultaten. FULLSCAN kan niet worden gebruikt met de voorbeeld optie.

Als voor beeld van een percentage wordt het percentage of het aantal rijen in de tabel of de geïndexeerde weer gave voor het query optimalisatie programma opgegeven dat moet worden gebruikt bij het maken van statistieken. Aantal kan tussen 0 en 100.

Voor beeld kan niet worden gebruikt met de optie FULLSCAN.

> [!NOTE]
> CSV-steek proeven werken op dit moment niet, alleen FULLSCAN wordt ondersteund voor CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken voor één kolom maken door elke rij te controleren

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproef grootte op te geven

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Voor beelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, moet u statistieken verwijderen en maken. Statistieken eerst neerzetten:

```sql
DROP STATISTICS census_external_table.sState
```

En maken statistieken:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Statistieken meta gegevens

Er zijn verschillende systeem weergaven en-functies die u kunt gebruiken om informatie te vinden over statistieken. U kunt bijvoorbeeld zien of een statistiek object mogelijk verouderd is door de functie STATS_DATE () te gebruiken. Met STATS_DATE () kunt u zien wanneer de statistieken voor het laatst zijn gemaakt of bijgewerkt.

> [!NOTE]
> Statistische meta gegevens zijn alleen beschikbaar voor externe tabel kolommen. Statistische meta gegevens zijn niet beschikbaar voor OPENROWSET-kolommen.

#### <a name="catalog-views-for-statistics"></a>Catalogus weergaven voor statistieken

Deze systeem weergaven bieden informatie over statistieken:

| Catalogus weergave                                                 | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elke kolom.                                     |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elk object in de data base.                     |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elk schema in de data base.                     |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elk statistiek object.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elke kolom in het statistiek object. Koppelingen terug naar sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elke tabel (inclusief externe tabellen).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Eén rij voor elk gegevens type.                                  |

#### <a name="system-functions-for-statistics"></a>Systeem functies voor statistieken

Deze systeem functies zijn handig voor het werken met statistieken:

| Systeem functie                                              | Description                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Datum waarop het statistieken object voor het laatst is bijgewerkt. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statistische kolommen en-functies combi neren in één weer gave

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Volgende stappen

Zie [uw werk belasting](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en [Aanbevolen procedures voor een toegewezen SQL-groep](best-practices-dedicated-sql-pool.md#maintain-statistics)controleren voor meer informatie over het verbeteren van query prestaties voor een toegewezen SQL-groep.

Zie [Aanbevolen procedures voor serverloze SQL-groepen](best-practices-serverless-sql-pool.md) voor meer informatie over het verbeteren van de query prestaties voor een SERVERloze SQL-groep

---
title: Tijdelijke tabellen in Synapse SQL
description: Essentiële richtlijnen voor het gebruik van tijdelijke tabellen in Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8513df83196b3521a749515c6bb22caad7d255b7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816030"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tijdelijke tabellen in Synapse SQL

Dit artikel bevat essentiële richtlijnen voor het gebruik van tijdelijke tabellen en bevat de principes van tijdelijke tabellen op sessieniveau binnen Synapse SQL. 

Zowel de toegewezen SQL-pool als serverloze SQL-poolbronnen kunnen gebruikmaken van tijdelijke tabellen. Serverloze SQL-pool heeft beperkingen die aan het einde van dit artikel worden besproken. 

## <a name="temporary-tables"></a>Tijdelijke tabellen

Tijdelijke tabellen zijn handig bij het verwerken van gegevens, met name tijdens transformatie waarbij de tussenliggende resultaten tijdelijk zijn. Met Synapse SQL bestaan tijdelijke tabellen op sessieniveau.  Ze zijn alleen zichtbaar voor de sessie waarin ze zijn gemaakt. Als zodanig worden ze automatisch uitgevallen wanneer de sessie wordt afgekapt. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tijdelijke tabellen in toegewezen SQL-pool

In de toegewezen SQL-poolresource bieden tijdelijke tabellen prestatievoordeel omdat de resultaten naar lokale in plaats van naar externe opslag worden geschreven.

### <a name="create-a-temporary-table"></a>Een tijdelijke tabel maken

Tijdelijke tabellen worden gemaakt door de tabelnaam vooraf te laten gaan door een `#` .  Bijvoorbeeld:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Tijdelijke tabellen kunnen ook worden gemaakt met exact `CTAS` dezelfde benadering:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` is een krachtige opdracht en heeft het extra voordeel efficiënt te zijn in het gebruik van transactielogboekruimte. 
> 
> 

### <a name="drop-temporary-tables"></a>Tijdelijke tabellen neerzetten

Wanneer er een nieuwe sessie wordt gemaakt, mogen er geen tijdelijke tabellen bestaan.  Als u echter dezelfde opgeslagen procedure aanroept die een tijdelijke met dezelfde naam maakt, gebruikt u een eenvoudige controle vooraf met om ervoor te zorgen dat uw instructies `CREATE TABLE` zijn  `DROP` geslaagd: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Voor het coderen van consistentie is het een goed idee om dit patroon te gebruiken voor zowel tabellen als tijdelijke tabellen.  Het is ook een goed idee om te gebruiken om `DROP TABLE` tijdelijke tabellen te verwijderen wanneer u hiermee klaar bent.  

Bij de ontwikkeling van opgeslagen procedures is het gebruikelijk dat de drop-opdrachten aan het einde van een procedure worden gebundeld om ervoor te zorgen dat deze objecten worden opgeschoond.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Code modulariseren

Tijdelijke tabellen kunnen overal in een gebruikerssessie worden gebruikt. Deze mogelijkheid kan vervolgens worden gebruikt om u te helpen uw toepassingscode te modulariseren.  Ter demonstratie genereert de volgende opgeslagen procedure DDL om alle statistieken in de database bij te werken op basis van de statistieknaam:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    #stats_ddl
;
GO
```

In deze fase is de enige actie die heeft plaatsgevonden het maken van een opgeslagen procedure die de tijdelijke #stats_ddl genereert.  De opgeslagen procedure wordt #stats_ddl als deze al bestaat. Deze daling zorgt ervoor dat deze niet mislukt als deze meer dan één keer binnen een sessie wordt uitgevoerd.  

Omdat er geen aan het einde van de opgeslagen procedure is, blijft de gemaakte tabel achter en kan deze buiten de opgeslagen procedure worden gelezen wanneer de opgeslagen `DROP TABLE` procedure is voltooid.  

In tegenstelling tot andere SQL Server databases kunt Synapse SQL tijdelijke tabel gebruiken buiten de procedure waarmee deze is gemaakt.  De tijdelijke tabellen die zijn gemaakt via een toegewezen SQL-pool, kunnen overal **in** de sessie worden gebruikt. Als gevolg hiervan hebt u meer modulaire en beheerbare code, zoals wordt gedemonstreerd in het onderstaande voorbeeld:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Beperkingen van tijdelijke tabel

Toegewezen SQL-pool heeft een aantal implementatiebeperkingen voor tijdelijke tabellen:

- Alleen tijdelijke tabellen met sessiebereik worden ondersteund.  Globale tijdelijke tabellen worden niet ondersteund.
- Weergaven kunnen niet worden gemaakt in tijdelijke tabellen.
- Tijdelijke tabellen kunnen alleen worden gemaakt met hash- of round robin distributie.  Gerepliceerde tijdelijke tabeldistributie wordt niet ondersteund. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Tijdelijke tabellen in serverloze SQL-pool

Tijdelijke tabellen in een serverloze SQL-pool worden ondersteund, maar het gebruik ervan is beperkt. Ze kunnen niet worden gebruikt in query's die gericht zijn op bestanden. 

U kunt bijvoorbeeld geen tijdelijke tabel met gegevens uit bestanden in de opslag toevoegen. Het aantal tijdelijke tabellen is beperkt tot 100 en de totale grootte is beperkt tot 100 MB.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Tabellen ontwerpen met behulp van de Synapse SQL voor meer informatie [over het ontwikkelen van tabellen.](develop-tables-overview.md)


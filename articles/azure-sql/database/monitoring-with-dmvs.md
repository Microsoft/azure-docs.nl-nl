---
title: Prestaties bewaken met Dmv's
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over het detecteren en diagnosticeren van veelvoorkomende prestatie problemen door dynamische beheer weergaven te gebruiken om Microsoft Azure SQL Database en Azure SQL Managed instance te bewaken.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592146"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Prestaties van Microsoft Azure SQL Database en Azure SQL Managed Instance bewaken met dynamische beheerweergaven
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL Database en Azure SQL Managed instance bieden een subset van dynamische beheer weergaven om prestatie problemen op te sporen. Dit kan worden veroorzaakt door geblokkeerde of langlopende query's, knel punten in resources, slechte query plannen, enzovoort. Dit artikel bevat informatie over het detecteren van veelvoorkomende prestatie problemen met dynamische beheer weergaven.

Microsoft Azure SQL Database en Azure SQL Managed instance ondersteunen gedeeltelijk drie categorieën dynamische beheer weergaven:

- Dynamische beheer weergaven die betrekking hebben op de data base.
- Aan uitvoering gerelateerde dynamische beheer weergaven.
- Dynamische beheer weergaven die betrekking hebben op trans acties.

Zie [dynamische beheer weergaven en-functies (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)voor gedetailleerde informatie over dynamische beheer weergaven.

## <a name="monitor-with-sql-insights"></a>Bewaken met SQL Insights

[Azure monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) is een hulp programma voor het bewaken van Azure SQL Managed instances, Azure SQL data bases en SQL Server instances in Azure SQL vm's. Deze service maakt gebruik van een externe agent voor het vastleggen van gegevens uit dynamische beheer weergaven (Dmv's) en stuurt de gegevens naar Azure Log Analytics, waar deze kunnen worden bewaakt en geanalyseerd. U kunt deze gegevens weer geven van [Azure monitor](../../azure-monitor/overview.md) in de beschik bare weer gaven of de logboek gegevens rechtstreeks openen om query's uit te voeren en trends te analyseren. Zie [SQL Insights inschakelen](../../azure-monitor/insights/sql-insights-enable.md)om te beginnen met het gebruik van Azure monitor SQL Insights.

## <a name="permissions"></a>Machtigingen

In Azure SQL Database moet u voor het uitvoeren van een query op een dynamische beheer weergave machtigingen **voor de database status weer geven** hebben. De machtiging **database status weer geven** retourneert informatie over alle objecten in de huidige data base.
Voer de volgende query uit om de machtiging status van de **weergave database** te verlenen aan een specifieke database gebruiker:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

In Azure SQL Managed instance zijn voor het uitvoeren van een query op een dynamische beheer weergave de **status machtigingen voor weergave servers** vereist. Zie [dynamische systeem beheer weergaven](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions)voor meer informatie.

In een exemplaar van SQL Server en in Azure SQL Managed instance retour neren dynamische beheer weergaven de status informatie van de server. In Azure SQL Database retour neren ze alleen informatie over uw huidige logische data base.

Dit artikel bevat een verzameling DMV-query's die u kunt uitvoeren met behulp van SQL Server Management Studio of Azure Data Studio om de volgende typen query prestatie problemen op te sporen:

- [Query's identificeren die betrekking hebben op buitensporig CPU-verbruik](#identify-cpu-performance-issues)
- [PAGELATCH_ * en WRITE_LOG wachten op IO-knel punten](#identify-io-performance-issues)
- [PAGELATCH_ * wacht op bytTempDB-conflicten](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE wachten door geheugen toekenning wacht op problemen](#identify-memory-grant-wait-performance-issues)
- [Data Base-en object grootte identificeren](#calculating-database-and-objects-sizes)
- [Informatie over actieve sessies ophalen](#monitoring-connections)
- [Informatie over het hele systeem en het gebruik van database bronnen ophalen](#monitor-resource-use)
- [Gegevens over query prestaties ophalen](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>CPU-prestatieproblemen identificeren

Als het CPU-verbruik langer is dan 80% gedurende langere Peri Oden, kunt u de volgende stappen voor probleem oplossing overwegen:

### <a name="the-cpu-issue-is-occurring-now"></a>Het CPU-probleem nu optreedt

Als er nu een probleem optreedt, zijn er twee mogelijke scenario's:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Veel afzonderlijke query's die cumulatief hoge CPU verbruiken

Gebruik de volgende query om de belangrijkste hashes van query's te identificeren:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Langlopende query's die gebruikmaken van CPU worden nog steeds uitgevoerd

Gebruik de volgende query om deze query's te identificeren:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Het CPU-probleem is opgetreden in het verleden

Als het probleem zich in het verleden heeft voorgedaan en u de analyse van de hoofd oorzaak wilt uitvoeren, gebruikt u [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Gebruikers met database toegang kunnen T-SQL gebruiken om query's in query Store-gegevens op te vragen. De standaard configuraties van de query-Store gebruiken een granulatie van 1 uur. Gebruik de volgende query om activiteiten te bekijken voor hoge CPU-verbruiks query's. Met deze query worden de top 15 van de CPU-verbruikte query's geretourneerd. Vergeet niet om te wijzigen `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` :

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Zodra u de problematische query's hebt geïdentificeerd, is het tijd om deze query's af te stemmen om het CPU-gebruik te verminderen.  Als u geen tijd hebt om de query's af te stemmen, kunt u er ook voor kiezen om de SLO van de data base bij te werken om het probleem te omzeilen.

## <a name="identify-io-performance-issues"></a>Problemen met IO-prestaties identificeren

Bij het identificeren van IO-prestatieproblemen zijn de belangrijkste wachttypen die verband houden met IO-problemen:

- `PAGEIOLATCH_*`

  Voor IO-problemen met gegevens bestanden (inclusief `PAGEIOLATCH_SH` , `PAGEIOLATCH_EX` , `PAGEIOLATCH_UP` ).  Als de wait-type naam **io** bevat, verwijst deze naar een io-probleem. Als er geen **io** is in de wacht naam voor de vergren deling van de pagina, wijst deze naar een ander type probleem (bijvoorbeeld TempDB-conflicten).

- `WRITE_LOG`

  Voor IO-problemen met transactie Logboeken.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Als het IO-probleem nu optreedt

Gebruik de [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) of [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) om de en weer te geven `wait_type` `wait_time` .

#### <a name="identify-data-and-log-io-usage"></a>Gegevens identificeren en i/o-logboek gebruiken

Gebruik de volgende query om gegevens en logboek-IO-gebruik te identificeren. Als de gegevens-of logboek-IO meer dan 80% is, betekent dit dat gebruikers de beschik bare IO voor de SQL Database servicelaag hebben gebruikt.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Als de i/o-limiet is bereikt, hebt u twee opties:

- Optie 1: De rekengrootte of servicelaag upgraden
- Optie 2: de query's identificeren en afstemmen die de meeste IO gebruiken.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Aan de buffer gerelateerde IO weer geven met behulp van de query Store

Voor optie 2 kunt u de volgende query gebruiken voor query Store voor de buffer-gerelateerde IO om de laatste twee uur van bijgehouden activiteiten weer te geven:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Totale logboek-IO voor WRITELOG-wacht tijden weer geven

Als het wacht type is `WRITELOG` , gebruikt u de volgende query om het totale aantal logboek-io per-instructie weer te geven:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>`tempdb`Prestatie problemen identificeren

Bij het identificeren van i/o-prestatie problemen, zijn de meest voorkomende wacht typen die zijn gekoppeld aan `tempdb` problemen `PAGELATCH_*` (niet `PAGEIOLATCH_*` ). `PAGELATCH_*`Wacht tijden zijn echter niet altijd de bedoeling dat u `tempdb` conflicten hebt.  Dit wachten kan ook betekenen dat er een conflict voor een gegevenspagina voor gebruikersobjecten is opgetreden vanwege gelijktijdige aanvragen voor dezelfde gegevenspagina. `tempdb`Gebruik [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) om te bevestigen dat de wait_resource-waarde begint met 2 als de `2:x:y` `tempdb` Data Base-ID, `x` de bestands-id en de `y` pagina-id is om de conflicten verder te bevestigen.  

Voor TempDB-conflicten is een gemeen schappelijke methode het verminderen of herschrijven van toepassings code die afhankelijk is van `tempdb` .  Algemene `tempdb` gebruiks gebieden zijn:

- Tijdelijke tabellen
- Tabelvariabelen
- Tabelwaardeparameters
- Gebruik van versie opslag (gekoppeld aan langlopende trans acties)
- Query's met queryplannen die gebruikmaken van sorteringen, hash joins en spools

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Meest voorkomende query's die tabel variabelen en tijdelijke tabellen gebruiken

Gebruik de volgende query om de belangrijkste query's te identificeren die gebruikmaken van tabel variabelen en tijdelijke tabellen:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Langlopende trans acties identificeren

Gebruik de volgende query om langlopende trans acties te identificeren. Langlopende trans acties verhinderen het opruimen van versie opslag.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Prestatie problemen met geheugen toekenning identificeren

Als uw hoogste wacht type is `RESOURCE_SEMAHPORE` en u geen hoog CPU-gebruik hebt, is het mogelijk dat er een wacht probleem is met geheugen toekenning.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Bepalen of een `RESOURCE_SEMAHPORE` wachten een ogen blik geduld

Gebruik de volgende query om te bepalen of een `RESOURCE_SEMAHPORE` wachten een ogen blik geduld

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Hoge geheugen-verbruiks instructies identificeren

Gebruik de volgende query om hoge geheugen-verbruiks instructies te identificeren:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>De belangrijkste 10 actieve geheugen subsidies identificeren

Gebruik de volgende query om de belangrijkste 10 actieve geheugen toekenningen te identificeren:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>De grootte van de database en objecten berekenen

Met de volgende query wordt de grootte van uw database geretourneerd (in megabytes):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Met de volgende query wordt de grootte van afzonderlijke objecten geretourneerd (in megabytes):

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Bewakings verbindingen

U kunt de weer gave [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) gebruiken om informatie op te halen over de verbindingen die tot stand zijn gebracht met een specifieke server en een beheerd exemplaar en de details van elke verbinding. Daarnaast is de weer gave [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) nuttig wanneer u gegevens ophaalt over alle actieve gebruikers verbindingen en interne taken.

Met de volgende query wordt informatie opgehaald over de huidige verbinding:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Als u de **sys.dm_exec_requests** en **sys.dm_exec_sessions weer gaven** uitvoert en u de machtiging **database status weer geven** voor de Data Base hebt, ziet u alle sessies die worden uitgevoerd op de data base. anders ziet u alleen de huidige sessie.

## <a name="monitor-resource-use"></a>Resource gebruik bewaken

U kunt Azure SQL Database Resource gebruik bewaken met behulp van [SQL Database query Performance Insight](query-performance-insight-use.md). Voor Azure SQL Database en Azure SQL Managed Instance kunt u bewaken met behulp van [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

U kunt het gebruik ook controleren met behulp van de volgende weer gaven:

- Azure SQL Database: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL Managed instance: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Zowel Azure SQL Database als Azure SQL Managed instance: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

U kunt de weer gave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in elke Data Base gebruiken. In de weer gave **sys.dm_db_resource_stats** worden recent gebruikte resource gegevens weer gegeven ten opzichte van de servicelaag. Gemiddeld percentages voor CPU, gegevens-IO, logboek schrijf bewerkingen en geheugen worden elke 15 seconden geregistreerd en worden gedurende één uur bewaard.

Omdat deze weer gave een gedetailleerdere vormgeving biedt in het resource gebruik, gebruikt u **sys.dm_db_resource_stats** eerst voor een analyse van de huidige status of het oplossen van problemen. Met deze query wordt bijvoorbeeld het gemiddelde en maximum resource gebruik voor de huidige data base in het afgelopen uur weer gegeven:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Zie de voor beelden in [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)voor andere query's.

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

U kunt [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) gebruiken om CPU-verbruik, io en opslag gegevens te retour neren voor een Azure SQL Managed instance. De gegevens worden binnen een interval van vijf minuten verzameld en geaggregeerd. Er is één rij voor elke 15 seconden rapportage. De geretourneerde gegevens zijn onder andere CPU-gebruik, opslag grootte, IO-gebruik en Managed instance SKU. Historische gegevens worden ongeveer 14 dagen bewaard.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

De [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) weer gave in de **hoofd** database bevat aanvullende informatie die u kan helpen bij het bewaken van de prestaties van uw data base in de specifieke servicelaag en de berekenings grootte. De gegevens worden elke vijf minuten verzameld en worden ongeveer 14 dagen bewaard. Deze weer gave is handig voor een historisch historische analyse van hoe uw data base resources gebruikt.

In de volgende grafiek ziet u het gebruik van de CPU-resource voor een Premium-data base met de P2-reken grootte voor elk uur in een week. Deze grafiek begint op een maandag, toont vijf werk dagen en toont vervolgens een weekend, wanneer er veel minder gebeurt met de toepassing.

![Gebruik van database bronnen](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Van de gegevens heeft deze data base momenteel een piek CPU-belasting van meer dan 50 procent van het CPU-gebruik ten opzichte van de P2-reken grootte (rond middaguur op dinsdag). Als CPU de dominante factor in het bron Profiel van de toepassing is, kunt u besluiten dat P2 de juiste reken grootte is om te garanderen dat de werk belasting altijd past. Als u verwacht dat een toepassing na verloop van tijd toeneemt, is het een goed idee om een extra bron buffer te hebben, zodat de toepassing niet ooit de limiet van het prestatie niveau bereikt. Als u de berekenings grootte verg root, kunt u voor komen dat klant-zicht bare fouten die zich kunnen voordoen wanneer een Data Base onvoldoende stroom heeft om aanvragen effectief te verwerken, met name in latentie gevoelige omgevingen. Een voor beeld is een Data Base die ondersteuning biedt voor een toepassing die webpagina's tekent op basis van de resultaten van database aanroepen.

Andere toepassings typen kunnen dezelfde grafiek anders interpreteren. Als een toepassing bijvoorbeeld elke dag salaris gegevens wil verwerken en dezelfde grafiek heeft, kan dit soort ' batch-taak model ' nauw keurig zijn bij een berekenings grootte van P1. De berekenings grootte van P1 heeft 100 Dtu's vergeleken met 200 Dtu's bij de grootte van P2-berekeningen. De berekenings grootte van P1 biedt de helft van de prestaties van de P2-reken grootte. 50 procent van het CPU-gebruik in P2 is dus gelijk aan 100 procent van het CPU-gebruik in P1. Als de toepassing geen time-outs heeft, is het mogelijk dat het niet van belang is dat een taak twee uur of 2,5 uur duurt, als deze vandaag wordt uitgevoerd. Een toepassing in deze categorie kan waarschijnlijk een P1-reken grootte gebruiken. U kunt profiteren van het feit dat er Peri Oden zijn gedurende de dag dat het gebruik van resources lager is, zodat elke ' grote piek ' in een van de troughs later op de dag kan overlopen. De berekenings grootte van P1 kan geschikt zijn voor dat soort toepassing (en geld besparen), zolang de taken op tijd elke dag kunnen worden voltooid.

De data base-engine biedt geconsumeerde resource gegevens voor elke actieve data base in de weer gave **sys.resource_stats** van de **hoofd** database op elke server. De gegevens in de tabel worden samengevoegd gedurende een interval van vijf minuten. Met de service lagen Basic, Standard en Premium kan het langer dan vijf minuten duren voordat de gegevens in de tabel worden weer gegeven. deze gegevens zijn dus handiger voor historische analyses in plaats van bijna realtime analyses. Zoek in de weer gave **sys.resource_stats** om de recente geschiedenis van een Data Base te zien en te controleren of de door u gekozen reserve ring de gewenste prestaties heeft geleverd wanneer dat nodig is.

> [!NOTE]
> Op Azure SQL Database moet u verbinding hebben met de **hoofd** database om in de volgende voor beelden te kunnen zoeken naar **sys.resource_stats** .

In dit voor beeld ziet u hoe de gegevens in deze weer gave worden blootgesteld:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![De catalogus weergave sys.resource_stats](./media/monitoring-with-dmvs/sys_resource_stats.png)

In het volgende voor beeld ziet u verschillende manieren waarop u de **sys.resource_stats** catalogus weergave kunt gebruiken om informatie op te halen over hoe uw data base resources gebruikt:

1. Als u het resource gebruik van de afgelopen week wilt bekijken voor de data base userdb1, kunt u deze query uitvoeren:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Als u wilt evalueren hoe goed uw werk belasting past bij de berekenings grootte, moet u inzoomen op elk aspect van de metrische gegevens van de resource: CPU, lees bewerkingen, schrijf bewerkingen, aantal werk rollen en aantal sessies. Hier volgt een aangepaste query met **sys.resource_stats** voor het rapporteren van de gemiddelde en maximum waarden van deze resource gegevens:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Met deze informatie over de gemiddelde en de maximum waarde van elke resource metriek kunt u bepalen hoe goed de workload past in de reken grootte die u hebt gekozen. Normaal gesp roken geven gemiddelde waarden van **sys.resource_stats** u een goede basis lijn om te gebruiken voor de doel grootte. Dit moet uw primaire meet stick zijn. Voor een voor beeld gebruikt u mogelijk de standaard service-laag met de compute-grootte S2. De gemiddelde percentages voor CPU-en IO-Lees bewerkingen en-schrijf bewerkingen zijn lager dan 40 procent, het gemiddelde aantal werkers ligt onder 50 en het gemiddelde aantal sessies is lager dan 200. De werk belasting kan in de reken grootte van S1 passen. Het is eenvoudig om te zien of uw data base past bij de limieten van de werk nemer en de sessie. Als u wilt weten of een Data Base past in een lagere reken grootte met betrekking tot CPU, lees-en schrijf bewerkingen, deelt u het DTU-nummer van de lagere reken grootte door het DTU-nummer van de huidige reken grootte en vermenigvuldigt u het resultaat met 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Het resultaat is het relatieve prestatie verschil tussen de twee reken grootten in procenten. Als uw resource gebruik deze hoeveelheid niet overschrijdt, kan uw werk belasting in de lagere reken grootte passen. U moet echter alle bereiken van resource waarden bekijken en bepalen hoe vaak de werk belasting van de data base in de lagere reken grootte past. Met de volgende query wordt het aanpassings percentage per resource dimensie uitgevoerd op basis van de drempel van 40 procent die we in dit voor beeld hebben berekend:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Op basis van de servicelaag van de Data Base kunt u bepalen of uw workload in de lagere reken grootte past. Als het doel van de data base-workload 99,9 procent is en de voor gaande query waarden retourneert die groter zijn dan 99,9 procent voor alle drie de resource dimensies, past uw werk belasting waarschijnlijk toe op de lagere reken grootte.

    Als u het aanpassings percentage bekijkt, kunt u ook zien of u moet overstappen op de volgende hogere reken grootte om te voldoen aan uw doel. Userdb1 toont bijvoorbeeld het volgende CPU-gebruik voor de afgelopen week:

   | Gemiddeld CPU-percentage | Maximum CPU-percentage |
   | --- | --- |
   | 24,5 |100,00 |

    De gemiddelde CPU is ongeveer een kwar taal van de limiet van de berekenings grootte, die goed overeenkomt met de reken grootte van de data base. Maar de maximum waarde laat zien dat de data base de limiet van de reken grootte bereikt. Moet u overstappen naar de volgende hogere reken grootte? Bekijk hoe vaak uw werk belasting 100 procent bereikt en vergelijkt deze met het doel van uw data base-workload.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Als met deze query een waarde van minder dan 99,9 procent wordt geretourneerd voor een van de drie resource dimensies, kunt u overwegen om over te stappen op de volgende hogere reken grootte of door technieken voor het afstemmen van toepassingen te gebruiken om de belasting van de data base te reduceren.

4. Deze oefening houdt ook rekening met de toename van de geschatte werk belasting in de toekomst.

Voor elastische pools kunt u afzonderlijke databases in de pool bewaken met de technieken die in deze sectie zijn beschreven. U kunt echter ook de groep als geheel bewaken. Zie [Een elastische pool bewaken en beheren](elastic-pool-overview.md) voor meer informatie.

### <a name="maximum-concurrent-requests"></a>Maximum aantal gelijktijdige aanvragen

Als u het aantal gelijktijdige aanvragen wilt zien, voert u deze Transact-SQL-query uit voor uw Data Base:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Als u de werk belasting van een SQL Server-Data Base wilt analyseren, wijzigt u deze query om te filteren op de specifieke data base die u wilt analyseren. Als u bijvoorbeeld een on-premises Data Base hebt met de naam MyDatabase, retourneert deze Transact-SQL-query het aantal gelijktijdige aanvragen in die Data Base:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Dit is slechts een moment opname op één punt in de tijd. Als u een beter inzicht wilt krijgen in uw werk belasting en gelijktijdige aanvraag vereisten, moet u een groot aantal voor beelden in de loop van de tijd verzamelen.

### <a name="maximum-concurrent-logins"></a>Maximum aantal gelijktijdige aanmeldingen

U kunt uw gebruikers-en toepassings patronen analyseren om een idee te krijgen van de frequentie van aanmeldingen. U kunt ook praktijk belasting in een test omgeving uitvoeren om ervoor te zorgen dat u niet aan deze of andere limieten komt die in dit artikel worden besproken. Er is geen enkele query of dynamische beheer weergave (DMV) waarmee u gelijktijdige aanmeldings aantallen of-geschiedenis kunt weer geven.

Als meerdere clients hetzelfde connection string gebruiken, verifieert de service elke aanmelding. Als 10 gebruikers gelijktijdig verbinding maken met een Data Base met behulp van dezelfde gebruikers naam en hetzelfde wacht woord, zijn er 10 gelijktijdige aanmeldingen. Deze limiet geldt alleen voor de duur van de aanmelding en verificatie. Als dezelfde 10 gebruikers opeenvolgend verbinding maken met de data base, is het aantal gelijktijdige aanmeldingen nooit groter dan 1.

> [!NOTE]
> Deze limiet geldt momenteel niet voor data bases in elastische Pools.

### <a name="maximum-sessions"></a>Maximum aantal sessies

Als u het aantal actieve sessies wilt zien, voert u deze Transact-SQL-query uit op uw Data Base:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Als u een SQL Server workload analyseert, wijzigt u de query zodanig dat deze zich op een specifieke Data Base bevindt. Deze query helpt u bij het bepalen van de mogelijke sessie vereisten voor de Data Base als u overweegt om deze naar Azure te verplaatsen.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

Deze query's retour neren een bepaald aantal tijdstippen. Als u meerdere voor beelden in de loop van de tijd verzamelt, hebt u een goed beeld van het gebruik van uw sessie.

U kunt historische statistieken voor sessies ontvangen door de [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) weer te geven en de **active_session_count** kolom te controleren.

## <a name="monitoring-query-performance"></a>Query prestaties bewaken

Trage of langlopende query's kunnen aanzienlijke systeem bronnen in beslag nemen. In deze sectie ziet u hoe u dynamische beheer weergaven gebruikt voor het detecteren van enkele veelvoorkomende prestatie problemen met query's.

### <a name="finding-top-n-queries"></a>Eerste N query's zoeken

In het volgende voor beeld wordt informatie gegeven over de top vijf query's gerangschikt op gemiddelde CPU-tijd. In dit voor beeld worden de query's geaggregeerd volgens hun query-hash, zodat logische equivalente query's worden gegroepeerd op basis van het cumulatieve Resource verbruik.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Geblokkeerde query's bewaken

Trage of langlopende query's kunnen bijdragen aan overmatig bronnen gebruik en zijn het gevolg van geblokkeerde query's. De oorzaak van de blok kering kan zijn dat het toepassings ontwerp, ongeldige query plannen, het gebrek aan handige indexen, enzovoort. U kunt de weer gave sys.dm_tran_locks gebruiken om informatie over de huidige vergrendelings activiteit in de Data Base op te halen. Zie [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)voor voorbeeld code. Zie voor meer informatie over het oplossen van problemen met het blok keren van problemen met [Azure SQL blocking en oplossen](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Query plannen bewaken

Een inefficiënt query plan kan ook het CPU-verbruik verhogen. In het volgende voor beeld wordt de weer gave [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) gebruikt om te bepalen welke query de meest cumulatieve CPU gebruikt.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Zie ook

[Inleiding tot Azure SQL Database en Azure SQL Managed instance](sql-database-paas-overview.md)
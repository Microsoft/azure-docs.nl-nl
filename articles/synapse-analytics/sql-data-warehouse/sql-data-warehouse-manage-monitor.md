---
title: De toegewezen werk belasting van een SQL-groep bewaken met Dmv's
description: Meer informatie over het bewaken van de werk belasting van uw specifieke SQL-groep voor Azure Synapse Analytics en het uitvoeren van query's met Dmv's.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 1992c3d525fc1f5a098e1969887a752233d47990
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453795"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>De toegewezen werk belasting voor Azure Synapse Analytics bewaken met behulp van Dmv's

In dit artikel wordt beschreven hoe u dynamische beheer weergaven (Dmv's) gebruikt voor het bewaken van uw werk belasting, zoals het onderzoeken van query's die in de SQL-groep worden uitgevoerd.

## <a name="permissions"></a>Machtigingen

Als u een query wilt uitvoeren voor de Dmv's in dit artikel, moet u de status van de data base of **het besturings element** **weer geven** . Normaal gesp roken is de status van de **weer gave-data base** de voorkeurs machtiging omdat deze veel meer beperkend is.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Verbindingen controleren

Alle aanmeldingen bij uw data warehouse worden vastgelegd in [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Deze DMV bevat de laatste 10.000 aanmeldingen.  De session_id is de primaire sleutel en wordt opeenvolgend toegewezen voor elke nieuwe aanmelding.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Query uitvoering bewaken

Alle query's die worden uitgevoerd op de SQL-groep worden vastgelegd in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Deze DMV bevat de laatste 10.000 query's die zijn uitgevoerd.  De request_id unieke identificatie van elke query en is de primaire sleutel voor deze DMV.  De request_id wordt opeenvolgend toegewezen voor elke nieuwe query en wordt voorafgegaan door QID, wat staat voor query-ID.  Bij het uitvoeren van een query op deze DMV voor een gegeven session_id worden alle query's voor een bepaalde aanmelding weer gegeven.

> [!NOTE]
> Opgeslagen procedures gebruiken meerdere aanvraag-Id's.  Aanvraag-Id's worden in sequentiële volg orde toegewezen.

Dit zijn de stappen die u moet volgen om de uitvoering van query's en tijden voor een bepaalde query te onderzoeken.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STAP 1: de query die u wilt onderzoeken identificeren

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

In de voor gaande query resultaten **noteert u de aanvraag-id** van de query die u wilt onderzoeken.

Query's in de **onderbroken** status kunnen worden geplaatst als gevolg van een groot aantal actieve query's. Deze query's worden ook weer gegeven in de [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wacht op query met een type UserConcurrencyResourceType. Zie [geheugen-en gelijktijdigheids limieten](memory-concurrency-limits.md) of [resource klassen voor werkbelasting beheer](resource-classes-for-workload-management.md)voor meer informatie over gelijktijdigheids limieten. Query's kunnen ook worden gewacht op andere redenen, zoals voor object vergrendelingen.  Als uw query wacht op een resource, raadpleegt u [Query's onderzoeken](#monitor-waiting-queries) die in dit artikel wachten op resources.

Als u de zoek opdracht van een query in de tabel [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wilt vereenvoudigen, gebruikt u [Label](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om een opmerking toe te wijzen aan uw query, die kan worden opgezocht in de weer gave sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>STAP 2: het query plan onderzoeken

De aanvraag-ID gebruiken om het gedistribueerde SQL-plan (DSQL) van de query op te halen uit [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Wanneer een DSQL-plan langer duurt dan verwacht, kan de oorzaak een complex plan zijn met veel DSQL-stappen of kan slechts één stap lang duren.  Als het plan veel stappen bevat met verschillende verplaatsings bewerkingen, kunt u de tabel distributies optimaliseren om de gegevens verplaatsing te verminderen. In het [tabel distributie](sql-data-warehouse-tables-distribute.md) artikel wordt uitgelegd waarom gegevens moeten worden verplaatst om een query op te lossen. In dit artikel wordt ook een aantal distributie strategieën uitgelegd om de verplaatsing van gegevens te minimaliseren.

Als u meer informatie wilt over één stap, de *operation_type* kolom van de langlopende query stap en noteer de **stap index**:

* Ga verder met stap 3 voor **SQL-bewerkingen**: OnOperation, RemoteOperation, ReturnOperation.
* Ga door met stap 4 voor **gegevens verplaatsings bewerkingen**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>STAP 3: SQL onderzoeken op gedistribueerde data bases

Gebruik de aanvraag-ID en de stap index om gegevens op te halen uit [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), die informatie over de uitvoering van de query stap op alle gedistribueerde data bases bevat.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Wanneer de query stap wordt uitgevoerd, kan [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) worden gebruikt om het SQL Server geschatte plan op te halen uit de SQL Server-schema cache voor de stap die wordt uitgevoerd op een bepaalde distributie.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>STAP 4: de verplaatsing van gegevens op de gedistribueerde data bases onderzoeken

Gebruik de aanvraag-ID en de stap index om informatie op te halen over een stap voor het verplaatsen van gegevens die wordt uitgevoerd op elke distributie uit [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Controleer de *total_elapsed_time* kolom om te zien of een bepaalde distributie aanzienlijk langer duurt dan andere.
* Controleer voor de langlopende distributie de *rows_processed* kolom om te zien of het aantal rijen dat wordt verplaatst van die distributie aanzienlijk groter is dan de andere. Als dit het geval is, kan dit wijzen op het hellen van de onderliggende gegevens. Een van de oorzaken voor het scheef trekken van gegevens wordt gedistribueerd op een kolom met veel NULL-waarden (waarvan alle rijen alle grond in dezelfde distributie hebben). Voorkom langzame query's door distributie te voor komen op deze typen kolommen of door de query te filteren om zo mogelijk NULLen te elimineren. 

Als de query wordt uitgevoerd, kunt u [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om het SQL Server geschatte plan op te halen uit de SQL Server plan cache voor de huidige actieve SQL-stap binnen een bepaalde distributie.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Wacht query's bewaken

Als u ontdekt dat uw query geen voortgang heeft omdat deze wacht op een resource, is dit een query waarin alle resources worden weer gegeven waarvoor een query wordt ingewisseld.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Als de query actief wacht op resources van een andere query, wordt de status **AcquireResources**.  Als de query alle vereiste resources heeft, wordt de status **verleend**.

## <a name="monitor-tempdb"></a>TempDB bewaken

TempDB wordt gebruikt voor het opslaan van tussenliggende resultaten tijdens het uitvoeren van query's. Hoog gebruik van de tempdb-data base kan leiden tot trage query prestaties. Voor elke DW100c die is geconfigureerd, wordt 399 GB aan TempDB-ruimte toegewezen (DW1000c heeft 3,99 TB aan totale TempDB-ruimte).  Hieronder vindt u tips voor het bewaken van het gebruik van tempdb en voor het verminderen van het gebruik van tempdb in uw query's.

### <a name="monitoring-tempdb-with-views"></a>TempDB bewaken met weer gaven

Als u het gebruik van tempdb wilt bewaken, moet u eerst de [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) -weer gave installeren vanuit de [micro soft Toolkit voor SQL-groep](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Vervolgens kunt u de volgende query uitvoeren om het TempDB-gebruik per knoop punt voor alle uitgevoerde query's weer te geven:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Als u een query hebt die gebruikmaakt van een grote hoeveelheid geheugen of een fout bericht hebt ontvangen dat is gerelateerd aan de toewijzing van tempdb, kan dit worden veroorzaakt door een zeer grote [Create Table als Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) of [INSERT SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) -instructie die wordt uitgevoerd bij het uitvoeren van de laatste bewerking voor gegevens verplaatsing. Dit kan meestal worden geïdentificeerd als een ShuffleMove-bewerking in het gedistribueerde query plan voordat de laatste invoegen wordt geselecteerd.  Gebruik [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om ShuffleMove-bewerkingen te bewaken.

De meest voorkomende oplossing is om uw CTAS te verstoren of een SELECT-instructie in meerdere laad instructies te plaatsen, zodat het gegevens volume niet de limiet van 1 TB per Node TempDB overschrijdt. U kunt uw cluster ook schalen naar een grotere grootte, waardoor de tempdb-grootte wordt verdeeld over meerdere knoop punten die de tempdb op elk afzonderlijke knoop punt verminderen.

Naast CTAS en INSERT SELECT-instructies kunnen grote, complexe query's die met onvoldoende geheugen worden uitgevoerd, overlopen in TempDB, waardoor query's mislukken.  U kunt overwegen met een grotere [resource klasse](resource-classes-for-workload-management.md) om te voor komen dat er wordt overgelopen naar Tempdb.

## <a name="monitor-memory"></a>Geheugen bewaken

Het geheugen kan de hoofd oorzaak zijn voor trage prestaties en geheugen problemen. U kunt uw data warehouse schalen als u vindt dat het geheugen gebruik van SQL Server de limieten bereikt tijdens de uitvoering van de query.

De volgende query retourneert SQL Server geheugen gebruik en geheugen druk per knoop punt:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Transactie logboek grootte bewaken

Met de volgende query wordt de grootte van het transactie logboek op elke distributie geretourneerd. Als een van de logboek bestanden 160 GB bereikt, kunt u overwegen om uw exemplaar te schalen of de grootte van uw trans actie te beperken.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Het terugdraaien van transactie logboeken bewaken

Als uw query's mislukken of als u een lange tijd hebt om door te gaan, kunt u controleren en controleren of er trans acties worden teruggedraaid.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Poly base-belasting controleren

De volgende query biedt een geschatte schatting van de voortgang van de belasting. In de query worden alleen de bestanden weer gegeven die momenteel worden verwerkt.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Volgende stappen

Zie [systeem weergaven](../sql/reference-tsql-system-views.md)voor meer informatie over dmv's.

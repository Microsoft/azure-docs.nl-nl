---
title: Analyseer uw werk belasting voor de toegewezen SQL-groep
description: Technieken voor het analyseren van de query prioriteit voor een toegewezen SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14c3ad30bac7cec4c11822d825323bb9db2ba440
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454530"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Analyseer uw werk belasting voor de exclusieve SQL-groep in azure Synapse Analytics

Technieken voor het analyseren van uw toegewezen werk belasting van een SQL-groep in azure Synapse Analytics. 

## <a name="resource-classes"></a>Resourceklassen

De toegewezen SQL-groep biedt resource klassen voor het toewijzen van systeem bronnen aan query's.  Zie [resource klassen & workload Management](resource-classes-for-workload-management.md)voor meer informatie over resource klassen.  Query's wachten of de resource klasse die aan een query is toegewezen, meer resources nodig heeft dan momenteel beschikbaar zijn.

## <a name="queued-query-detection-and-other-dmvs"></a>Detectie van query's in de wachtrij en andere Dmv's

U kunt de `sys.dm_pdw_exec_requests` dmv gebruiken om query's te identificeren die in een gelijktijdigheids wachtrij wachten. Query's die wachten op een gelijktijdigheids sleuf hebben de status **opgeschort**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Werkbelasting beheer rollen kunnen worden weer gegeven met `sys.database_principals` .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

De volgende query geeft aan aan welke rol elke gebruiker is toegewezen.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

De toegewezen SQL-groep heeft de volgende wacht typen:

* **LocalQueriesConcurrencyResourceType**: query's die buiten het Framework voor gelijktijdigheids sleuven vallen. DMV query's en systeem functies zoals `SELECT @@VERSION` voor beelden van lokale query's.
* **UserConcurrencyResourceType**: query's die binnen het Framework voor gelijktijdigheids sleuven zitten. Query's voor de tabellen van de eind gebruiker zijn voor beelden die gebruikmaken van dit resource type.
* **DmsConcurrencyResourceType**: er wordt gewacht op het verplaatsen van gegevens.
* **BackupConcurrencyResourceType**: deze wacht tijd geeft aan dat er een back-up van een Data Base wordt gemaakt. De maximum waarde voor dit resource type is 1. Als er tegelijkertijd meerdere back-ups zijn aangevraagd, de andere wachtrij. Over het algemeen raden we een minimum tijd aan tussen opeenvolgende moment opnamen van 10 minuten.

De `sys.dm_pdw_waits` dmv kan worden gebruikt om te zien op welke bronnen een aanvraag wordt gewacht.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

De `sys.dm_pdw_resource_waits` dmv toont de wacht gegevens voor een bepaalde query. De tijds duur van de resource meet de tijd die wacht totdat de resources zijn opgenomen. De wacht tijd voor het signaal is de tijd die nodig is voor de onderliggende SQL-servers voor het plannen van de query op de CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

U kunt ook het dmv gebruiken om te `sys.dm_pdw_resource_waits` berekenen hoeveel gelijktijdigheids sleuven zijn toegekend.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

De `sys.dm_pdw_wait_stats` dmv kan worden gebruikt voor historische trend analyse van wacht tijden.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beheren van database gebruikers en-beveiliging, [een speciale SQL-groep (voorheen SQL DW) beveiligen](sql-data-warehouse-overview-manage-security.md). Zie [indexen opnieuw samen stellen om de segment kwaliteit te verbeteren](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)voor meer informatie over hoe grotere resource klassen de geclusterde column store-index kwaliteit kunnen verbeteren.

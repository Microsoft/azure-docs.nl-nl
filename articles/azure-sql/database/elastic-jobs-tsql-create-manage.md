---
title: Elastic Database-taken (preview) maken en beheren met Transact-SQL (T-SQL)
description: Voer scripts uit in veel data bases met Elastic Database-taak agent met behulp van Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/01/2021
ms.openlocfilehash: 11b94ba5bcedf56f0115b8730dc58f808aff5c58
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371597"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>Transact-SQL (T-SQL) gebruiken om Elastic Database taken te maken en te beheren (preview-versie)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit artikel bevat een groot aantal voorbeeld scenario's om aan de slag te gaan met elastische taken met behulp van T-SQL.

De voor beelden gebruiken de [opgeslagen procedures](#job-stored-procedures) en [weer gaven](#job-views) die beschikbaar zijn in de [*taak database*](job-automation-overview.md#elastic-job-database).

Transact-SQL (T-SQL) wordt gebruikt om taken te maken, te configureren, uit te voeren en te beheren. Het maken van de elastische taak agent wordt niet ondersteund in T-SQL, dus u moet eerst een *elastische taak agent* maken met behulp van de portal of [Power shell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

## <a name="create-a-credential-for-job-execution"></a>Een referentie maken voor het uitvoeren van taken

De referentie wordt gebruikt om verbinding te maken met uw doel databases voor het uitvoeren van scripts. De referentie moet de juiste machtigingen hebben op de data bases die zijn opgegeven door de doel groep om het script uit te voeren. Wanneer u een [logisch SQL Server](logical-servers.md) -en/of groeps doel groepslid gebruikt, wordt het nadrukkelijk aanbevolen een referentie te maken voor gebruik om de referentie te vernieuwen vóór de uitbrei ding van de server en/of pool op het moment van de uitvoering van de taak. De referentie data base-scope wordt gemaakt op de data base van de taak agent. Dezelfde referentie moet worden gebruikt om *een aanmelding te maken* en *een gebruiker te maken op basis van de aanmelding om de aanmeldings database machtigingen te verlenen* voor de doel databases.

```sql
--Connect to the new job database specified when creating the Elastic Job agent

-- Create a database master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create two database scoped credentials.  
-- The credential to connect to the Azure SQL logical server, to execute jobs
CREATE DATABASE SCOPED CREDENTIAL job_credential WITH IDENTITY = 'job_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
-- The credential to connect to the Azure SQL logical server, to refresh the database metadata in server
CREATE DATABASE SCOPED CREDENTIAL refresh_credential WITH IDENTITY = 'refresh_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Een doel groep maken (servers)

In het volgende voor beeld ziet u hoe u een taak uitvoert voor alle data bases op een server.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net';

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Een afzonderlijke data base uitsluiten

In het volgende voor beeld ziet u hoe u een taak uitvoert voor alle data bases op een server, met uitzonde ring van de data base met de naam *MappingDB*.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = N'London.database.windows.net';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server2.database.windows.net';
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name = N'MappingDB';
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Een doel groep maken (groepen)

In het volgende voor beeld ziet u hoe u alle data bases in een of meer elastische Pools kunt richten.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup';

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net',
@elastic_pool_name = 'ElasticPool-1';

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Nieuw schema implementeren in veel data bases

In het volgende voor beeld ziet u hoe u een nieuw schema voor alle data bases implementeert.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name = 'CreateTableTest', @description = 'Create Table Test';

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name = 'CreateTableTest',
@command = N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup';
```

## <a name="data-collection-using-built-in-parameters"></a>Gegevens verzameling met ingebouwde para meters

In veel scenario's voor het verzamelen van gegevens kan het nuttig zijn om een aantal van deze script variabelen op te geven om de resultaten van de taak te kunnen volgen.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Als u bijvoorbeeld alle resultaten van dezelfde taak uitvoering samen wilt groeperen, gebruikt u *$ (job_execution_id)* , zoals wordt weer gegeven in de volgende opdracht:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Databaseprestaties bewaken

In het volgende voor beeld wordt een nieuwe taak gemaakt voor het verzamelen van prestatie gegevens uit meerdere data bases.

Standaard maakt de taak agent de uitvoer tabel om geretourneerde resultaten op te slaan. Daarom moet de databaseprincipal die is gekoppeld aan de uitvoer referentie mini maal de volgende machtigingen hebben: `CREATE TABLE` op de data base, `ALTER` , `SELECT` , `INSERT` , `DELETE` in de uitvoer tabel of het bijbehorende schema, en `SELECT` in de catalogus weergave [sys. Indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) .

Als u de tabel vooraf hand matig wilt maken, moet deze de volgende eigenschappen hebben:

1. Kolommen met de juiste naam en gegevens typen voor de resultatenset.
2. Aanvullende kolom voor internal_execution_id met het gegevens type UniqueIdentifier.
3. Een niet-geclusterde index `IX_<TableName>_Internal_Execution_ID` met de naam in de internal_execution_id kolom.
4. Alle machtigingen die hierboven worden vermeld, met uitzonde ring van machtigingen voor `CREATE TABLE` de data base.

Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdrachten uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name = 'ResultsJob',
@command = N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = '<resultsdb>',
@output_table_name = '<resutlstable>';

--Create a job to monitor pool performance

--Connect to the job database specified when creating the job agent

-- Add a target group containing Elastic Job database
EXEC jobs.sp_add_target_group 'ElasticJobGroup';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ElasticJobGroup',
@target_type = 'SqlDatabase',
@server_name = 'server1.database.windows.net',
@database_name = 'master';

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name = 'ResultsPoolsJob',
@description = 'Demo: Collection Performance data from all pools',
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name = 'job_credential',
@target_group_name = 'ElasticJobGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = 'resultsdb',
@output_table_name = 'resutlstable';
```

## <a name="view-job-definitions"></a>Taak definities weer geven

In het volgende voor beeld ziet u hoe huidige taak definities worden weer gegeven.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs;

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version;

-- View the steps of all versions of all jobs
SELECT * FROM jobs.jobsteps;
```

## <a name="begin-unplanned-execution-of-a-job"></a>Ongeplande uitvoering van een taak starten

In het volgende voor beeld ziet u hoe u een taak onmiddellijk kunt starten.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest';

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier;
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output;
select @je;

select * from jobs.job_executions where job_execution_id = @je;

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1;
```

## <a name="schedule-execution-of-a-job"></a>De uitvoering van een taak plannen

In het volgende voor beeld ziet u hoe u een taak plant voor toekomstige uitvoering.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name = 'ResultsJob',
@enabled=1,
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;
```

## <a name="monitor-job-execution-status"></a>Uitvoerings status van taak bewaken

In het volgende voor beeld ziet u hoe Details van de uitvoerings status voor alle taken worden weer gegeven.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC;

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC;

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC;

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC;
```

## <a name="cancel-a-job"></a>Een taak annuleren

In het volgende voor beeld ziet u hoe u een taak annuleert.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC;
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef';
```

## <a name="delete-old-job-history"></a>Oude taak geschiedenis verwijderen

In het volgende voor beeld ziet u hoe u de taak geschiedenis verwijdert vóór een specifieke datum.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job's executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Een taak en alle bijbehorende taak geschiedenis verwijderen

In het volgende voor beeld ziet u hoe u een taak en alle gerelateerde taak geschiedenis verwijdert.  
Maak verbinding met de [*taak database*](job-automation-overview.md#elastic-job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>Opgeslagen procedures voor taken

De volgende opgeslagen procedures bevinden zich in de [Jobs-data base](job-automation-overview.md#elastic-job-database).

|Opgeslagen procedure  |Description  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Hiermee wordt een nieuwe taak toegevoegd.    |
|[sp_update_job](#sp_update_job)    |      Hiermee wordt een bestaande taak bijgewerkt.   |
|[sp_delete_job](#sp_delete_job)     |      Hiermee verwijdert u een bestaande taak.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Voegt een stap toe aan een taak.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Hiermee wordt een taak stap bijgewerkt.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Hiermee verwijdert u een taak stap.    |
|[sp_start_job](#sp_start_job)    |  De uitvoering van een taak wordt gestart.       |
|[sp_stop_job](#sp_stop_job)     |     Hiermee stopt u de uitvoering van een taak.   |
|[sp_add_target_group](#sp_add_target_group)    |     Hiermee voegt u een doel groep toe.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Hiermee verwijdert u een doel groep.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Hiermee voegt u een Data Base of een groep data bases toe aan een doel groep.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Hiermee verwijdert u een lid van een doel groep uit een doel groep.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Hiermee verwijdert u de geschiedenis records voor een taak.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Hiermee wordt een nieuwe taak toegevoegd.
  
#### <a name="syntax"></a>Syntaxis  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenten  

[ **\@ job_name =** ] job_name  
De naam van de taak. De naam moet uniek zijn en mag niet het percentage (%) bevatten ring. job_name is nvarchar (128), zonder standaard waarde.

[ **\@ Description =** ] ' Beschrijving '  
De beschrijving van de taak. de beschrijving is nvarchar (512), met een standaard waarde van NULL. Als de beschrijving wordt wegge laten, wordt een lege teken reeks gebruikt.

[ **\@ enabled =** ] ingeschakeld  
Hiermee wordt aangegeven of de planning van de taak is ingeschakeld. Ingeschakeld is bit, met een standaard waarde van 0 (uitgeschakeld). Als de waarde 0 is, is de taak niet ingeschakeld en wordt deze niet volgens het schema uitgevoerd. het kan echter ook hand matig worden uitgevoerd. Als dit het geval is, wordt de taak uitgevoerd volgens de planning en kan deze ook hand matig worden uitgevoerd.

[ **\@ schedule_interval_type =**] schedule_interval_type  
De waarde geeft aan wanneer de taak moet worden uitgevoerd. schedule_interval_type is nvarchar (50), met een standaard waarde van één, en kan een van de volgende waarden hebben:

- Eenmaal,
- ' Minuten ',
- ' Hours ',
- Dagen,
- Weken,
- Vastgesteld

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Aantal schedule_interval_count Peri Oden dat moet worden uitgevoerd tussen elke uitvoering van de taak. schedule_interval_count is int, met een standaard waarde van 1. De waarde moet groter zijn dan of gelijk zijn aan 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
De datum waarop de uitvoering van de taak kan beginnen. schedule_start_time is DATETIME2, met de standaard waarde van 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
De datum waarop de uitvoering van de taak kan worden gestopt. schedule_end_time is DATETIME2, met de standaard waarde van 9999-12-31 11:59:59.0000000.

[ **\@ job_id =** ] job_id uitvoer  
Het taak-id-nummer dat is toegewezen aan de taak als deze is gemaakt. job_id is een uitvoer variabele van het type UniqueIdentifier.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

sp_add_job moet worden uitgevoerd vanuit de data base van de taak agent die is opgegeven bij het maken van de taak agent.
Nadat sp_add_job is uitgevoerd om een taak toe te voegen, kunt u sp_add_jobstep gebruiken om stappen toe te voegen waarmee de activiteiten voor de taak worden uitgevoerd. Het eerste versie nummer van de taak is 0, wat wordt verhoogd naar 1 wanneer de eerste stap wordt toegevoegd.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Hiermee wordt een bestaande taak bijgewerkt.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak die moet worden bijgewerkt. job_name is nvarchar (128).

[ **\@ new_name =** ] new_name  
De nieuwe naam van de taak. new_name is nvarchar (128).

[ **\@ Description =** ] ' Beschrijving '  
De beschrijving van de taak. de beschrijving is nvarchar (512).

[ **\@ enabled =** ] ingeschakeld  
Hiermee geeft u op of de planning van de taak is ingeschakeld (1) of niet is ingeschakeld (0). Ingeschakeld is bit.

[ **\@ schedule_interval_type =** ] schedule_interval_type  
De waarde geeft aan wanneer de taak moet worden uitgevoerd. schedule_interval_type is nvarchar (50) en kan een van de volgende waarden hebben:

- Eenmaal,
- ' Minuten ',
- ' Hours ',
- Dagen,
- Weken,
- Vastgesteld

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Aantal schedule_interval_count Peri Oden dat moet worden uitgevoerd tussen elke uitvoering van de taak. schedule_interval_count is int, met een standaard waarde van 1. De waarde moet groter zijn dan of gelijk zijn aan 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
De datum waarop de uitvoering van de taak kan beginnen. schedule_start_time is DATETIME2, met de standaard waarde van 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
De datum waarop de uitvoering van de taak kan worden gestopt. schedule_end_time is DATETIME2, met de standaard waarde van 9999-12-31 11:59:59.0000000.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Nadat sp_add_job is uitgevoerd om een taak toe te voegen, kunt u sp_add_jobstep gebruiken om stappen toe te voegen waarmee de activiteiten voor de taak worden uitgevoerd. Het eerste versie nummer van de taak is 0, wat wordt verhoogd naar 1 wanneer de eerste stap wordt toegevoegd.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Hiermee verwijdert u een bestaande taak.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak die moet worden verwijderd. job_name is nvarchar (128).

[ **\@ Force =** ] forceren  
Hiermee wordt aangegeven of wordt verwijderd als de uitvoering van de taak wordt uitgevoerd en alle uitgevoerde uitvoeringen worden geannuleerd (1) of als er een fout optreedt als er taak uitvoeringen worden uitgevoerd (0). Force is bit.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

De taak geschiedenis wordt automatisch verwijderd wanneer een taak wordt verwijderd.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Voegt een stap toe aan een taak.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak waaraan de stap moet worden toegevoegd. job_name is nvarchar (128).

[ **\@ step_id =** ] step_id  
Het id-nummer van de reeks voor de taak stap. De identificatie nummers van de stappen beginnen bij 1 en worden verhoogd zonder onderbrekingen. Als een bestaande stap al deze ID heeft, wordt voor die stap en alle volgende stappen de ID verhoogd, zodat deze nieuwe stap in de reeks kan worden ingevoegd. Als u niets opgeeft, wordt de step_id automatisch toegewezen aan de laatste in de volg orde van de stappen. step_id is een int.

[ **\@ step_name =** ] step_name  
De naam van de stap. Moet worden opgegeven, met uitzonde ring van de eerste stap van een taak die (voor het gemak) de standaard naam ' JobStep ' heeft. step_name is nvarchar (128).

[ **\@ command_type =** ] command_type  
Het type opdracht dat wordt uitgevoerd door deze jobstep. command_type is nvarchar (50), met een standaard waarde van TSql, wat inhoudt dat de waarde van de @command_type para meter een T-SQL-script is.

Indien opgegeven, moet de waarde TSql zijn.

[ **\@ command_source =** ] command_source  
Het type locatie waar de opdracht wordt opgeslagen. command_source is nvarchar (50), met een standaard waarde van inline, wat inhoudt dat de waarde van de @command_source para meter de letterlijke tekst van de opdracht is.

Indien opgegeven, moet de waarde inline zijn.

[ **\@ Command =** ] ' opdracht '  
De opdracht moet een geldig T-SQL-script zijn en vervolgens worden uitgevoerd door deze taak stap. de opdracht is nvarchar (max), met een standaard waarde van NULL.

[ **\@ credential_name =** ] credential_name  
De naam van de data base-scoped referentie opgeslagen in deze taak beheer database die wordt gebruikt om verbinding te maken met elk van de doel databases in de doel groep wanneer deze stap wordt uitgevoerd. credential_name is nvarchar (128).

[ **\@ target_group_name =** ] ' doel-group_name '  
De naam van de doel groep die de doel databases bevat waarop de taak stap wordt uitgevoerd. target_group_name is nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
De vertraging vóór de eerste nieuwe poging, als de taak stap mislukt tijdens de eerste uitvoerings poging. initial_retry_interval_seconds is int, met de standaard waarde van 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
De maximale vertraging tussen nieuwe pogingen. Als de vertraging tussen nieuwe pogingen groter is dan deze waarde, wordt deze waarde in plaats daarvan beperkt. maximum_retry_interval_seconds is int, met de standaard waarde van 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
De vermenigvuldiger die moet worden toegepast op de vertraging voor nieuwe pogingen als de uitvoering van meerdere taak stappen mislukt. Als bijvoorbeeld de eerste nieuwe poging een vertraging van 5 seconde had en de uitstel-vermenigvuldiger 2,0 is, heeft de tweede nieuwe poging een vertraging van 10 seconden en de derde nieuwe poging heeft een vertraging van 20 seconden. retry_interval_backoff_multiplier is echt, met de standaard waarde van 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Het aantal keren dat de uitvoering opnieuw moet worden uitgevoerd als de eerste poging mislukt. Als de retry_attempts waarde bijvoorbeeld 10 is, is er 1 eerste poging en tien nieuwe pogingen, met een totaal van 11 pogingen. Als de laatste poging tot opnieuw proberen mislukt, wordt de uitvoering van de taak beëindigd met een levens cyclus van mislukt. retry_attempts is int, met de standaard waarde van 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
De maximale hoeveelheid tijd die is toegestaan om de stap uit te voeren. Als deze tijd wordt overschreden, wordt de uitvoering van de taak beëindigd met een levens cyclus van out. step_timeout_seconds is int, met de standaard waarde van 43.200 seconden (12 uur).

[ **\@ output_type =** ] output_type  
Als dit niet het geval is, is dit het type bestemming waarnaar de eerste resultatenset van de opdracht wordt geschreven. output_type is nvarchar (50), met een standaard waarde van NULL.

Indien opgegeven, moet de waarde SqlDatabase zijn.

[ **\@ output_credential_name =** ] output_credential_name  
Als dit niet het geval is, wordt de naam van de data base-scoped referentie die wordt gebruikt om verbinding te maken met de uitvoer doel database. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_credential_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ output_subscription_id =** ] output_subscription_id  
Beschrijving vereist.

[ **\@ output_resource_group_name =** ] output_resource_group_name  
Beschrijving vereist.

[ **\@ output_server_name =** ] output_server_name  
Als dit niet het geval is, wordt de volledig gekwalificeerde DNS-naam van de server die de uitvoer doel database bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_server_name is nvarchar (256), met een standaard waarde van NULL.

[ **\@ output_database_name =** ] output_database_name  
Als dit niet het geval is, is dit de naam van de data base die de uitvoer doel tabel bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_database_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ output_schema_name =** ] output_schema_name  
Als dit niet het geval is, is dit de naam van het SQL-schema dat de uitvoer doel tabel bevat. Als output_type gelijk is aan SqlDatabase, is de standaard waarde dbo. output_schema_name is nvarchar (128).

[ **\@ output_table_name =** ] output_table_name  
Als dit niet het geval is, is dit de naam van de tabel waarnaar de eerste resultatenset van de opdracht wordt geschreven. Als de tabel nog niet bestaat, wordt deze gemaakt op basis van het schema van de geretourneerde resultatenset. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_table_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ job_version =** ] job_version uitvoer  
Uitvoer parameter waaraan het nieuwe taak versie nummer wordt toegewezen. job_version is int.

[ **\@ max_parallelism =** ] max_parallelism uitvoer  
Het maximale parallelle niveau per elastische pool. Als deze is ingesteld, wordt de taak stap beperkt tot een maximum van zoveel data bases per elastische pool. Dit geldt voor elke elastische pool die rechtstreeks is opgenomen in de doel groep of die zich binnen een server bevindt die deel uitmaakt van de doel groep. max_parallelism is int.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Wanneer sp_add_jobstep slaagt, wordt het huidige versie nummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt. Als de taak momenteel wordt uitgevoerd, wordt de nieuwe stap niet in die uitvoering opgenomen.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:  

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Hiermee wordt een taak stap bijgewerkt.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak waartoe de stap behoort. job_name is nvarchar (128).

[ **\@ step_id =** ] step_id  
Het id-nummer van de taak stap die moet worden gewijzigd. Er moet een step_id of step_name worden opgegeven. step_id is een int.

[ **\@ step_name =** ] step_name  
De naam van de stap die moet worden gewijzigd. Er moet een step_id of step_name worden opgegeven. step_name is nvarchar (128).

[ **\@ new_id =** ] new_id  
Het nieuwe Volg nummer van de reeks-id voor de taak stap. De identificatie nummers van de stappen beginnen bij 1 en worden verhoogd zonder onderbrekingen. Als een stap wordt gewijzigd, worden andere stappen automatisch opnieuw genummerd.

[ **\@ new_name =** ] new_name  
De nieuwe naam van de stap. new_name is nvarchar (128).

[ **\@ command_type =** ] command_type  
Het type opdracht dat wordt uitgevoerd door deze jobstep. command_type is nvarchar (50), met een standaard waarde van TSql, wat inhoudt dat de waarde van de @command_type para meter een T-SQL-script is.

Indien opgegeven, moet de waarde TSql zijn.

[ **\@ command_source =** ] command_source  
Het type locatie waar de opdracht wordt opgeslagen. command_source is nvarchar (50), met een standaard waarde van inline, wat inhoudt dat de waarde van de @command_source para meter de letterlijke tekst van de opdracht is.

Indien opgegeven, moet de waarde inline zijn.

[ **\@ Command =** ] ' opdracht '  
De opdracht/opdrachten moeten een geldig T-SQL-script zijn en worden vervolgens uitgevoerd door deze taak stap. de opdracht is nvarchar (max), met een standaard waarde van NULL.

[ **\@ credential_name =** ] credential_name  
De naam van de data base-scoped referentie opgeslagen in deze taak beheer database die wordt gebruikt om verbinding te maken met elk van de doel databases in de doel groep wanneer deze stap wordt uitgevoerd. credential_name is nvarchar (128).

[ **\@ target_group_name =** ] ' doel-group_name '  
De naam van de doel groep die de doel databases bevat waarop de taak stap wordt uitgevoerd. target_group_name is nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
De vertraging vóór de eerste nieuwe poging, als de taak stap mislukt tijdens de eerste uitvoerings poging. initial_retry_interval_seconds is int, met de standaard waarde van 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
De maximale vertraging tussen nieuwe pogingen. Als de vertraging tussen nieuwe pogingen groter is dan deze waarde, wordt deze waarde in plaats daarvan beperkt. maximum_retry_interval_seconds is int, met de standaard waarde van 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
De vermenigvuldiger die moet worden toegepast op de vertraging voor nieuwe pogingen als de uitvoering van meerdere taak stappen mislukt. Als bijvoorbeeld de eerste nieuwe poging een vertraging van 5 seconde had en de uitstel-vermenigvuldiger 2,0 is, heeft de tweede nieuwe poging een vertraging van 10 seconden en de derde nieuwe poging heeft een vertraging van 20 seconden. retry_interval_backoff_multiplier is echt, met de standaard waarde van 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Het aantal keren dat de uitvoering opnieuw moet worden uitgevoerd als de eerste poging mislukt. Als de retry_attempts waarde bijvoorbeeld 10 is, is er 1 eerste poging en tien nieuwe pogingen, met een totaal van 11 pogingen. Als de laatste poging tot opnieuw proberen mislukt, wordt de uitvoering van de taak beëindigd met een levens cyclus van mislukt. retry_attempts is int, met de standaard waarde van 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
De maximale hoeveelheid tijd die is toegestaan om de stap uit te voeren. Als deze tijd wordt overschreden, wordt de uitvoering van de taak beëindigd met een levens cyclus van out. step_timeout_seconds is int, met de standaard waarde van 43.200 seconden (12 uur).

[ **\@ output_type =** ] output_type  
Als dit niet het geval is, is dit het type bestemming waarnaar de eerste resultatenset van de opdracht wordt geschreven. Als u de waarde van output_type weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_type is nvarchar (50), met een standaard waarde van NULL.

Indien opgegeven, moet de waarde SqlDatabase zijn.

[ **\@ output_credential_name =** ] output_credential_name  
Als dit niet het geval is, wordt de naam van de data base-scoped referentie die wordt gebruikt om verbinding te maken met de uitvoer doel database. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_credential_name weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_credential_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ output_server_name =** ] output_server_name  
Als dit niet het geval is, wordt de volledig gekwalificeerde DNS-naam van de server die de uitvoer doel database bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_server_name weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_server_name is nvarchar (256), met een standaard waarde van NULL.

[ **\@ output_database_name =** ] output_database_name  
Als dit niet het geval is, is dit de naam van de data base die de uitvoer doel tabel bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_database_name weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_database_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ output_schema_name =** ] output_schema_name  
Als dit niet het geval is, is dit de naam van het SQL-schema dat de uitvoer doel tabel bevat. Als output_type gelijk is aan SqlDatabase, is de standaard waarde dbo. Als u de waarde van output_schema_name weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_schema_name is nvarchar (128).

[ **\@ output_table_name =** ] output_table_name  
Als dit niet het geval is, is dit de naam van de tabel waarnaar de eerste resultatenset van de opdracht wordt geschreven. Als de tabel nog niet bestaat, wordt deze gemaakt op basis van het schema van de geretourneerde resultatenset. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_server_name weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op (lege teken reeks). output_table_name is nvarchar (128), met een standaard waarde van NULL.

[ **\@ job_version =** ] job_version uitvoer  
Uitvoer parameter waaraan het nieuwe taak versie nummer wordt toegewezen. job_version is int.

[ **\@ max_parallelism =** ] max_parallelism uitvoer  
Het maximale parallelle niveau per elastische pool. Als deze is ingesteld, wordt de taak stap beperkt tot een maximum van zoveel data bases per elastische pool. Dit geldt voor elke elastische pool die rechtstreeks is opgenomen in de doel groep of die zich binnen een server bevindt die deel uitmaakt van de doel groep. Als u de waarde van max_parallelism weer opnieuw wilt instellen op NULL, stelt u de waarde van deze para meter in op-1. max_parallelism is int.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Alle uitvoeringen van de taak worden niet beïnvloed. Wanneer sp_update_jobstep slaagt, wordt het versie nummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Hiermee verwijdert u een taak stap van een taak.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak waaruit de stap wordt verwijderd. job_name is nvarchar (128), zonder standaard waarde.

[ **\@ step_id =** ] step_id  
Het id-nummer van de taak stap die moet worden verwijderd. Er moet een step_id of step_name worden opgegeven. step_id is een int.

[ **\@ step_name =** ] step_name  
De naam van de stap die moet worden verwijderd. Er moet een step_id of step_name worden opgegeven. step_name is nvarchar (128).

[ **\@ job_version =** ] job_version uitvoer  
Uitvoer parameter waaraan het nieuwe taak versie nummer wordt toegewezen. job_version is int.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Alle uitvoeringen van de taak worden niet beïnvloed. Wanneer sp_update_jobstep slaagt, wordt het versie nummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt.

De andere taak stappen worden automatisch opnieuw genummerd om de lege ruimte te vullen met de stap verwijderde taak.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

De uitvoering van een taak wordt gestart.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak waaruit de stap wordt verwijderd. job_name is nvarchar (128), zonder standaard waarde.

[ **\@ job_execution_id =** ] job_execution_id uitvoer  
Uitvoer parameter waaraan de taak uitvoering-ID wordt toegewezen. job_version is uniqueidentifier.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Geen.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Hiermee stopt u de uitvoering van een taak.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_execution_id =** ] job_execution_id  
Het id-nummer van de taak uitvoering die moet worden gestopt. job_execution_id is uniqueidentifier, met de standaard waarde NULL.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Geen.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Hiermee voegt u een doel groep toe.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ target_group_name =** ] target_group_name  
De naam van de doel groep die u wilt maken. target_group_name is nvarchar (128), zonder standaard waarde.

[ **\@ target_group_id =** ] target_group_id uitvoer het id-nummer van de doel groep die is toegewezen aan de taak als deze is gemaakt. target_group_id is een uitvoer variabele van het type UniqueIdentifier, met de standaard waarde NULL.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Doel groepen bieden een eenvoudige manier om een taak te richten op een verzameling data bases.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Hiermee verwijdert u een doel groep.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argumenten

[ **\@ target_group_name =** ] target_group_name  
De naam van de doel groep die u wilt verwijderen. target_group_name is nvarchar (128), zonder standaard waarde.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Geen.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Hiermee voegt u een Data Base of een groep data bases toe aan een doel groep.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumenten

[ **\@ target_group_name =** ] target_group_name  
De naam van de doel groep waaraan het lid wordt toegevoegd. target_group_name is nvarchar (128), zonder standaard waarde.

[ **\@ membership_type =** ] membership_type  
Hiermee geeft u op of het lid van de doel groep wordt opgenomen of uitgesloten. target_group_name is nvarchar (128), waarbij de standaard waarde ' include ' is. Geldige waarden voor membership_type zijn ' include ' of ' exclude '.

[ **\@ target_type =** ] target_type  
Het type doel database of verzameling data bases, inclusief alle data bases op een server, alle data bases in een elastische pool, alle data bases in een Shard-kaart of een afzonderlijke data base. target_type is nvarchar (128), zonder standaard waarde. Geldige waarden voor target_type zijn ' SqlServer ', ' SqlElasticPool ', ' SqlDatabase ' of ' SqlShardMap '.

[ **\@ refresh_credential_name =** ] refresh_credential_name  
De naam van de data base-Scope referentie. refresh_credential_name is nvarchar (128), zonder standaard waarde.

[ **\@ server_name =** ] server_name  
De naam van de server die moet worden toegevoegd aan de opgegeven doel groep. server_name moet worden opgegeven als target_type SqlServer is. server_name is nvarchar (128), zonder standaard waarde.

[ **\@ database_name =** ] database_name  
De naam van de data base die moet worden toegevoegd aan de opgegeven doel groep. database_name moet worden opgegeven als target_type is ingesteld op SqlDatabase. database_name is nvarchar (128), zonder standaard waarde.

[ **\@ elastic_pool_name =** ] elastic_pool_name  
De naam van de elastische pool die moet worden toegevoegd aan de opgegeven doel groep. elastic_pool_name moet worden opgegeven als target_type is ingesteld op SqlElasticPool. elastic_pool_name is nvarchar (128), zonder standaard waarde.

[ **\@ shard_map_name =** ] shard_map_name  
De naam van de Shard-toewijzings groep die moet worden toegevoegd aan de opgegeven doel groep. elastic_pool_name moet worden opgegeven als target_type is ingesteld op SqlShardMap. shard_map_name is nvarchar (128), zonder standaard waarde.

[ **\@ target_id =** ] target_group_id uitvoer  
Het doel-id-nummer dat is toegewezen aan het lid van de doel groep als dit is gemaakt, wordt toegevoegd aan de doel groep. target_id is een uitvoer variabele van het type UniqueIdentifier, met de standaard waarde NULL.
Retour code waarden 0 (geslaagd) of 1 (mislukt)

#### <a name="remarks"></a>Opmerkingen

Een taak wordt uitgevoerd op alle afzonderlijke data bases binnen een server of in een elastische pool op het moment van uitvoering, wanneer een server of elastische pool wordt opgenomen in de doel groep.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden

In het volgende voor beeld worden alle data bases in de services Londen en NewYork toegevoegd aan de groeps servers die klant gegevens onderhouden. In dit geval ElasticJobs moet u verbinding maken met de Jobs-data base die is opgegeven bij het maken van de taak agent.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net';
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Hiermee verwijdert u een lid van een doel groep uit een doel groep.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argumenten

[ @target_group_name =] ' target_group_name '  
De naam van de doel groep waaruit het lid van de doel groep moet worden verwijderd. target_group_name is nvarchar (128), zonder standaard waarde.

[ @target_id =] target_id  
 Het doel-id-nummer dat is toegewezen aan het lid van de doel groep dat moet worden verwijderd. target_id is een uniqueidentifier met een standaard waarde van NULL.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Doel groepen bieden een eenvoudige manier om een taak te richten op een verzameling data bases.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden

In het volgende voor beeld wordt de server Londen verwijderd van de groeps servers die klant gegevens onderhouden. In dit geval ElasticJobs moet u verbinding maken met de Jobs-data base die is opgegeven bij het maken van de taak agent.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net';

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid;
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Hiermee verwijdert u de geschiedenis records voor een taak.

#### <a name="syntax"></a>Syntaxis

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenten

[ **\@ job_name =** ] job_name  
De naam van de taak waarvoor de geschiedenis records moeten worden verwijderd. job_name is nvarchar (128), met een standaard waarde van NULL. Er moet een job_id of job_name worden opgegeven, maar deze kunnen niet worden opgegeven.

[ **\@ job_id =** ] job_id  
 Het taak identificatienummer van de taak voor de records die moeten worden verwijderd. job_id is uniqueidentifier, met een standaard waarde van NULL. Er moet een job_id of job_name worden opgegeven, maar deze kunnen niet worden opgegeven.

[ **\@ oldest_date =** ] oldest_date  
 De oudste record die in de geschiedenis moet worden bewaard. oldest_date is DATETIME2, met een standaard waarde van NULL. Als oldest_date is opgegeven, verwijdert sp_purge_jobhistory alleen records die ouder zijn dan de opgegeven waarde.

#### <a name="return-code-values"></a>Retour code waarden

0 (geslaagd) of 1 (fout)

#### <a name="remarks"></a>Opmerkingen

Doel groepen bieden een eenvoudige manier om een taak te richten op een verzameling data bases.

#### <a name="permissions"></a>Machtigingen

Standaard kunnen leden van de vaste serverrol sysadmin deze opgeslagen procedure uitvoeren. Hiermee wordt een gebruiker beperkt tot het bewaken van taken, kunt u de gebruiker een deel van de volgende databaserol geven in de data base van de taak agent die is opgegeven bij het maken van de taak agent:

- jobs_reader

Zie de sectie machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken te bewerken van taken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden

In het volgende voor beeld worden alle data bases in de services Londen en NewYork toegevoegd aan de groeps servers die klant gegevens onderhouden. In dit geval ElasticJobs moet u verbinding maken met de Jobs-data base die is opgegeven bij het maken van de taak agent.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Taak weergaven

De volgende weer gaven zijn beschikbaar in de [Jobs-data base](job-automation-overview.md#elastic-job-database).

|Weergave  |Description  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Taak uitvoerings geschiedenis weer geven.      |
|[functies](#jobs-view)     |   Hiermee worden alle taken weer gegeven.      |
|[job_versions](#job_versions-view)     |   Alle taak versies worden weer gegeven.      |
|[jobsteps](#jobsteps-view)     |     Hiermee worden alle stappen in de huidige versie van elke taak weer gegeven.    |
|[jobstep_versions](#jobstep_versions-view)     |     Toont alle stappen in alle versies van elke taak.    |
|[target_groups](#target_groups-view)     |      Toont alle doel groepen.   |
|[target_group_members](#target_group_members-view)     |   Toont alle leden van alle doel groepen.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions weer gave

[Jobs]. [job_executions]

Taak uitvoerings geschiedenis weer geven.

|Kolomnaam | Gegevenstype | Beschrijving |
|---------|---------|---------|
|**job_execution_id** | uniqueidentifier | Unieke ID van een exemplaar van de uitvoering van een taak.
|**job_name** | nvarchar (128) | De naam van de taak.
|**job_id** | uniqueidentifier | De unieke ID van de taak.
|**job_version** | int | De versie van de taak (automatisch bijgewerkt telkens wanneer de taak wordt gewijzigd).
|**step_id** |int | Unieke id (voor deze taak) voor de stap. NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**is_active** | bit | Geeft aan of de gegevens actief of inactief zijn. 1 geeft actieve taken aan, en 0 geeft aan dat deze niet actief is.
|**duur** | nvarchar (50) | Waarde die de status van de taak aangeeft: ' gemaakt ', ' wordt uitgevoerd ', ' mislukt ', ' geslaagd ', ' overgeslagen ', ' SucceededWithSkipped '|
|**create_time**| DATETIME2 (7) | De datum en tijd waarop de taak is gemaakt.
|**start_time** | DATETIME2 (7) | De datum en tijd waarop de taak is gestart. NULL als de taak nog niet is uitgevoerd.
|**end_time** | DATETIME2 (7) | De datum en tijd waarop de taak is uitgevoerd. NULL als de taak nog niet is uitgevoerd of de uitvoering nog niet is voltooid.
|**current_attempts** | int | Aantal keren dat de stap opnieuw is uitgevoerd. De bovenliggende taak is 0, de uitvoering van onderliggende taken is 1 of meer op basis van het uitvoerings beleid.
|**current_attempt_start_time** | DATETIME2 (7) | De datum en tijd waarop de taak is gestart. NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**last_message** | nvarchar (max) | Taak-of stap geschiedenis bericht.
|**target_type** | nvarchar (128) | Type doel database of verzameling data bases, inclusief alle data bases in een server, alle data bases in een elastische pool of een Data Base. Geldige waarden voor target_type zijn ' SqlServer ', ' SqlElasticPool ' of ' SqlDatabase '. NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**target_id** | uniqueidentifier | De unieke ID van het lid van de doel groep.  NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**target_group_name** | nvarchar (128) | De naam van de doel groep. NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**target_server_name** | nvarchar (256)  | De naam van de server die deel uitmaakt van de doel groep. Alleen opgegeven als de target_type SqlServer is. NULL geeft aan dat de uitvoering van de bovenliggende taak is.
|**target_database_name** | nvarchar (128) | De naam van de data base die is opgenomen in de doel groep. Wordt alleen opgegeven als target_type is ingesteld op SqlDatabase. NULL geeft aan dat de uitvoering van de bovenliggende taak is.

### <a name="jobs-view"></a>taak weergave

[Jobs]. functies

Hiermee worden alle taken weer gegeven.

|Kolomnaam | Gegevenstype |Beschrijving|
|------|------|-------|
|**job_name** | nvarchar (128) | De naam van de taak.|
|**job_id**| uniqueidentifier |De unieke ID van de taak.|
|**job_version** |int |De versie van de taak (automatisch bijgewerkt telkens wanneer de taak wordt gewijzigd).|
|**beschrijvingen** |nvarchar (512)| Beschrijving voor de taak. Ingeschakelde bit: geeft aan of de taak is ingeschakeld of uitgeschakeld. 1 geeft ingeschakelde taken aan en 0 duidt op uitgeschakelde taken.|
|**schedule_interval_type**|nvarchar (50) |Waarde die aangeeft wanneer de taak moet worden uitgevoerd: ' Once ', ' minutes ', ' hours ', ' Days ', ' weken ', ' months '
|**schedule_interval_count**|int|Aantal schedule_interval_type Peri Oden dat moet worden uitgevoerd tussen elke uitvoering van de taak.|
|**schedule_start_time**|DATETIME2 (7)|De datum en tijd waarop de taak voor het laatst is gestart.|
|**schedule_end_time**|DATETIME2 (7)|De datum en tijd waarop de taak de laatste keer is uitgevoerd.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions weer gave

[Jobs]. [job_versions]

Alle taak versies worden weer gegeven.

|Kolomnaam|Gegevenstype|Beschrijving|
|------|------|-------|
|**job_name**|nvarchar (128)|De naam van de taak.|
|**job_id**|uniqueidentifier|De unieke ID van de taak.|
|**job_version**|int|De versie van de taak (automatisch bijgewerkt telkens wanneer de taak wordt gewijzigd).|

### <a name="jobsteps-view"></a>jobsteps weer geven

[Jobs]. [jobsteps]

Hiermee worden alle stappen in de huidige versie van elke taak weer gegeven.

|Kolomnaam|Gegevenstype|Beschrijving|
|------|------|-------|
|**job_name**|nvarchar (128)|De naam van de taak.|
|**job_id**|uniqueidentifier|De unieke ID van de taak.|
|**job_version**|int|De versie van de taak (automatisch bijgewerkt telkens wanneer de taak wordt gewijzigd).|
|**step_id**|int|Unieke id (voor deze taak) voor de stap.|
|**step_name**|nvarchar (128)|Unieke naam (voor deze taak) voor de stap.|
|**command_type**|nvarchar (50)|Type opdracht dat moet worden uitgevoerd in de taak stap. Voor v1 moet waarde gelijk aan en standaard ingesteld op TSql.|
|**command_source**|nvarchar (50)|Locatie van de opdracht. Voor v1 is ' inline ' de standaard waarde en alleen geaccepteerde waarden.|
|**cmd**|nvarchar (max)|De opdrachten die door elastische taken moeten worden uitgevoerd via command_type.|
|**credential_name**|nvarchar (128)|De naam van de data base-bereik referentie die wordt gebruikt om de taak uit te voeren.|
|**target_group_name**|nvarchar (128)|De naam van de doel groep.|
|**target_group_id**|uniqueidentifier|De unieke ID van de doel groep.|
|**initial_retry_interval_seconds**|int|De vertraging voor de eerste nieuwe poging. De standaardwaarde is 1.|
|**maximum_retry_interval_seconds**|int|De maximale vertraging tussen nieuwe pogingen. Als de vertraging tussen nieuwe pogingen groter is dan deze waarde, wordt deze waarde in plaats daarvan beperkt. De standaard waarde is 120.|
|**retry_interval_backoff_multiplier**|werkelijk|De vermenigvuldiger die moet worden toegepast op de vertraging voor nieuwe pogingen als de uitvoering van meerdere taak stappen mislukt. De standaard waarde is 2,0.|
|**retry_attempts**|int|Het aantal nieuwe pogingen om te gebruiken als deze stap mislukt. De standaard waarde is 10, waarmee wordt aangegeven dat er geen nieuwe pogingen moeten worden gedaan.|
|**step_timeout_seconds**|int|De hoeveelheid tijd in minuten tussen nieuwe pogingen. De standaard waarde is 0, wat een interval van 0 minuten aangeeft.|
|**output_type**|nvarchar (11)|Locatie van de opdracht. In de huidige preview is ' inline ' de standaard waarde en alleen geaccepteerde waarden.|
|**output_credential_name**|nvarchar (128)|De naam van de referenties die moeten worden gebruikt om verbinding te maken met de doel server voor het opslaan van de resultatenset.|
|**output_subscription_id**|uniqueidentifier|De unieke ID van het abonnement van de doel-server\database voor de resultaten die zijn ingesteld voor de uitvoering van de query.|
|**output_resource_group_name**|nvarchar (128)|De naam van de resource groep waarin de doel server zich bevindt.|
|**output_server_name**|nvarchar (256)|De naam van de doel server voor de resultatenset.|
|**output_database_name**|nvarchar (128)|De naam van de doel database voor de resultatenset.|
|**output_schema_name**|nvarchar (max)|De naam van het doel schema. De standaard waarde is dbo, indien niet opgegeven.|
|**output_table_name**|nvarchar (max)|De naam van de tabel voor het opslaan van de resultaten die zijn ingesteld op basis van de query resultaten. De tabel wordt automatisch gemaakt op basis van het schema van de resultatenset als deze nog niet bestaat. Het schema moet overeenkomen met het schema van de resultatenset.|
|**max_parallelism**|int|Het maximum aantal data bases per elastische pool waarin de taak stap op een bepaald moment wordt uitgevoerd. De standaard waarde is NULL, wat betekent dat er geen limiet is. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions weer gave

[Jobs]. [jobstep_versions]

Toont alle stappen in alle versies van elke taak. Het schema is identiek aan [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups weer gave

[Jobs]. [target_groups]

Een lijst met alle doel groepen.

|Kolomnaam|Gegevenstype|Beschrijving|
|-----|-----|-----|
|**target_group_name**|nvarchar (128)|De naam van de doel groep, een verzameling data bases.
|**target_group_id**|uniqueidentifier|De unieke ID van de doel groep.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members weer gave

[Jobs]. [target_group_members]

Toont alle leden van alle doel groepen.

|Kolomnaam|Gegevenstype|Beschrijving|
|-----|-----|-----|
|**target_group_name**|nvarchar (128|De naam van de doel groep, een verzameling data bases. |
|**target_group_id**|uniqueidentifier|De unieke ID van de doel groep.|
|**membership_type**|int|Hiermee geeft u op of het lid van de doel groep wordt opgenomen of uitgesloten in de doel groep. Geldige waarden voor target_group_name zijn ' include ' of ' exclude '.|
|**target_type**|nvarchar (128)|Type doel database of verzameling data bases, inclusief alle data bases in een server, alle data bases in een elastische pool of een Data Base. Geldige waarden voor target_type zijn ' SqlServer ', ' SqlElasticPool ', ' SqlDatabase ' of ' SqlShardMap '.|
|**target_id**|uniqueidentifier|De unieke ID van het lid van de doel groep.|
|**refresh_credential_name**|nvarchar (128)|De naam van de data base-scoped referentie die wordt gebruikt om verbinding te maken met het doel groepslid.|
|**subscription_id**|uniqueidentifier|De unieke ID van het abonnement.|
|**resource_group_name**|nvarchar (128)|De naam van de resource groep waarin het lid van de doel groep zich bevindt.|
|**server_name**|nvarchar (128)|De naam van de server die deel uitmaakt van de doel groep. Alleen opgegeven als de target_type SqlServer is. |
|**database_name**|nvarchar (128)|De naam van de data base die is opgenomen in de doel groep. Wordt alleen opgegeven als target_type is ingesteld op SqlDatabase.|
|**elastic_pool_name**|nvarchar (128)|De naam van de elastische pool die deel uitmaakt van de doel groep. Wordt alleen opgegeven als target_type is ingesteld op SqlElasticPool.|
|**shard_map_name**|nvarchar (128)|De naam van de Shard-kaarten die zijn opgenomen in de doel groep. Wordt alleen opgegeven als target_type is ingesteld op SqlShardMap.|

## <a name="resources"></a>Resources

- ![Onderwerp koppeling pictogram](/sql/database-engine/configure-windows/media/topic-link.gif "Pictogram koppeling onderwerp") [Transact-SQL-syntaxis conventies](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Volgende stappen

- [Elastische taken maken en beheren met PowerShell](elastic-jobs-powershell-create.md)
- [Autorisatie en machtigingen](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)

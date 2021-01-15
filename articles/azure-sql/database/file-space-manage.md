---
title: Azure SQL Database bestands ruimte beheer
description: Op deze pagina wordt beschreven hoe u de bestands ruimte beheert met één en gegroepeerde Data bases in Azure SQL Database, en vindt u code voorbeelden om te bepalen of u een enkele of een gegroepeerde Data Base moet verkleinen en hoe u een verkleinings bewerking voor een Data Base kunt uitvoeren.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 12/22/2020
ms.openlocfilehash: 08cab806d6ad8b75821a92994dde0fa07db8b960
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233590"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Bestands ruimte voor data bases in Azure SQL Database beheren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel worden verschillende soorten opslag ruimte voor data bases in Azure SQL Database beschreven en de stappen die kunnen worden uitgevoerd wanneer de toegewezen bestands ruimte expliciet moet worden beheerd.

> [!NOTE]
> Dit artikel is niet van toepassing op Azure SQL Managed Instance.

## <a name="overview"></a>Overzicht

Met Azure SQL Database zijn er werkbelasting patronen waarbij de toewijzing van onderliggende gegevens bestanden voor data bases groter kan worden dan de hoeveelheid gebruikte gegevens pagina's. Dit probleem kan optreden als er meer ruimte wordt gebruikt en er tegelijkertijd gegevens worden verwijderd. De reden hiervoor is dat de toegewezen bestands ruimte niet automatisch wordt vrijgemaakt wanneer gegevens worden verwijderd.

Mogelijk moet u in de volgende scenario's het gebruik van bestandsruimte bewaken en gegevensbestanden verkleinen:

- Sta groei van gegevens in een elastische pool toe als de toegewezen bestandsruimte voor de databases de maximale grootte van de pool bereikt.
- Sta toe dat de maximale grootte van één database of elastische pool wordt verkleind.
- Sta toe dat databases en elastische pools afzonderlijk naar een andere servicelaag of prestatielaag kunnen worden omgezet met een kleinere maximale grootte.

### <a name="monitoring-file-space-usage"></a>Bestands ruimte gebruik bewaken

De meeste metrische gegevens over opslag ruimte worden weer gegeven in het Azure Portal en de volgende Api's meten alleen de grootte van de gebruikte data pagina's:

- Api's met metrische gegevens op basis van Azure Resource Manager, waaronder Power shell [Get-Metrics](/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

De volgende Api's meten echter ook de grootte van de ruimte die is toegewezen voor data bases en elastische Pools:

- T-SQL:  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Gegevens bestanden verkleinen

Azure SQL Database verkleint gegevens bestanden niet automatisch om ongebruikte toegewezen ruimte te claimen vanwege de mogelijke gevolgen voor de prestaties van de data base.  Klanten kunnen gegevens bestanden echter verkleint via self-service tegelijk, door de stappen te volgen die worden beschreven in [herclaimen ongebruikte toegewezen ruimte](#reclaim-unused-allocated-space).

> [!NOTE]
> In tegens telling tot gegevens bestanden worden logboek bestanden met Azure SQL Database automatisch verkleind omdat deze bewerking geen invloed heeft op de prestaties van de data base.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Wat is de typen opslag ruimte voor een Data Base?

Meer informatie over de volgende hoeveel heden voor opslag ruimte zijn belang rijk voor het beheren van de bestands ruimte van een Data Base.

|Aantal data bases|Definitie|Opmerkingen|
|---|---|---|
|**Gebruikte gegevens ruimte**|De hoeveelheid ruimte die wordt gebruikt voor het opslaan van database gegevens in 8 KB-pagina's.|Over het algemeen neemt de gebruikte ruimte toe (afname) op INSERT (deletes). In sommige gevallen wordt de gebruikte ruimte niet gewijzigd op invoeg-of verwijderingen, afhankelijk van de hoeveelheid en het patroon van de gegevens die zijn betrokken bij de bewerking en eventuele fragmentatie. Als u bijvoorbeeld één rij uit elke gegevens pagina verwijdert, wordt de gebruikte ruimte niet noodzakelijkerwijs kleiner.|
|**Toegewezen gegevens ruimte**|De hoeveelheid geformatteerde bestands ruimte die beschikbaar is gesteld voor het opslaan van database gegevens.|De hoeveelheid toegewezen ruimte neemt automatisch toe, maar verlaagt nooit na het verwijderen. Dit gedrag zorgt ervoor dat toekomstige toevoegingen sneller zijn omdat de ruimte niet opnieuw moet worden geformatteerd.|
|**Toegewezen gegevens ruimte, maar niet gebruikt**|Het verschil tussen de hoeveelheid toegewezen gegevens ruimte en de gebruikte gegevens ruimte.|Deze hoeveelheid vertegenwoordigt de maximale hoeveelheid beschik bare ruimte die kan worden vrijgemaakt door database gegevens bestanden te verkleinen.|
|**Maximale grootte van gegevens**|De maximale hoeveelheid ruimte die kan worden gebruikt voor het opslaan van database gegevens.|De toegewezen hoeveelheid gegevens ruimte kan niet groter zijn dan de maximale grootte van de gegevens.|

In het volgende diagram ziet u de relatie tussen de verschillende typen opslag ruimte voor een Data Base.

![typen opslag ruimte en relaties](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Een query uitvoeren op één Data Base voor informatie over de opslag ruimte

De volgende query's kunnen worden gebruikt om de hoeveelheid opslag ruimte voor één Data Base te bepalen.  

### <a name="database-data-space-used"></a>Gebruikte ruimte voor database gegevens

Wijzig de volgende query om de hoeveelheid gebruikte database gegevens ruimte te retour neren.  De eenheden van het query resultaat zijn in MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Toegewezen en ongebruikte toegewezen ruimte voor database gegevens

Gebruik de volgende query om de hoeveelheid toegewezen database gegevensruimte en de hoeveelheid niet-gebruikte ruimte te retour neren.  De eenheden van het query resultaat zijn in MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Maximale grootte van database gegevens

Wijzig de volgende query om de maximale grootte van de database gegevens te retour neren.  De eenheden van het query resultaat bevinden zich in bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Wat is de typen opslag ruimte voor een elastische pool?

Meer informatie over de volgende hoeveel heden voor opslag ruimte zijn belang rijk voor het beheren van de bestands ruimte van een elastische pool.

|Hoeveelheid elastische Pools|Definitie|Opmerkingen|
|---|---|---|
|**Gebruikte gegevens ruimte**|De som van de gegevens ruimte die wordt gebruikt door alle data bases in de elastische pool.||
|**Toegewezen gegevens ruimte**|De som van de gegevens ruimte die wordt toegewezen door alle data bases in de elastische pool.||
|**Toegewezen gegevens ruimte, maar niet gebruikt**|Het verschil tussen de hoeveelheid toegewezen gegevens ruimte en de gegevens ruimte die wordt gebruikt door alle data bases in de elastische pool.|Deze hoeveelheid vertegenwoordigt de maximale hoeveelheid ruimte die is toegewezen voor de elastische pool en die kan worden vrijgemaakt door database gegevens bestanden te verkleinen.|
|**Maximale grootte van gegevens**|De maximale hoeveelheid gegevens ruimte die door de elastische pool kan worden gebruikt voor alle data bases.|De toegewezen ruimte voor de elastische pool mag niet groter zijn dan de maximale grootte van de elastische pool.  Als dit probleem zich voordoet, kan de toegewezen ruimte die niet wordt gebruikt, worden vrijgemaakt door de gegevens bestanden van de data base te verkleinen.|

> [!NOTE]
> Het fout bericht ' de elastische pool heeft de opslag limiet bereikt ' geeft aan dat aan de database objecten voldoende ruimte is toegewezen om te voldoen aan de opslag limiet van de elastische groep, maar er mogelijk ongebruikte ruimte is in de toewijzing van de gegevens ruimte. U kunt de opslag limiet van de elastische pool verg Roten of als oplossing voor de korte termijn, waarbij u gegevens ruimte vrijmaakt met behulp van de sectie [**ongebruikte toegewezen ruimte opnieuw claimen**](#reclaim-unused-allocated-space) hieronder. U moet ook rekening houden met de potentiële negatieve invloed op de prestaties van het verkleinen van database bestanden, zie de sectie [**indexen opnieuw samen stellen**](#rebuild-indexes) hieronder.

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Een elastische pool doorzoeken op informatie over de opslag ruimte

De volgende query's kunnen worden gebruikt om de hoeveelheid opslagruimte voor een elastische pool te bepalen.  

### <a name="elastic-pool-data-space-used"></a>Gebruikte gegevens ruimte elastische pool

Wijzig de volgende query om de hoeveelheid gebruikt gegevens ruimte van elastische groepen te retour neren.  De eenheden van het query resultaat zijn in MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Gegevens ruimte voor elastische Pools toegewezen en ongebruikte toegewezen ruimte

Wijzig de volgende voor beelden om een tabel te retour neren met een lijst met de toegewezen ruimte en de ongebruikte toegewezen ruimte voor elke data base in een elastische pool. De tabel bewaart data bases van deze data bases met de grootste hoeveelheid ongebruikte toegewezen ruimte op de minimale hoeveelheid ongebruikte toegewezen ruimte.  De eenheden van het query resultaat zijn in MB.  

De query resultaten voor het bepalen van de toegewezen ruimte voor elke data base in de pool kunnen samen worden toegevoegd om de totale toegewezen ruimte voor de elastische pool te bepalen. De toegewezen elastische groeps ruimte mag niet groter zijn dan de maximale grootte van de elastische pool.  

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

Voor het Power shell-script is SQL Server Power shell-module vereist. Zie [Power shell-module downloaden](/sql/powershell/download-sql-server-ps-module) om te installeren.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

De volgende scherm afbeelding is een voor beeld van de uitvoer van het script:

![voor beeld van een elastische groep met toegewezen ruimte en ongebruikte ruimte](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maximale grootte van elastische groeps gegevens

Wijzig de volgende T-SQL-query om de laatste vastgelegde maximale grootte van elastische Pools-gegevens te retour neren.  De eenheden van het query resultaat zijn in MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Ongebruikte toegewezen ruimte opnieuw claimen

> [!NOTE]
> Het verkleinen van opdrachten de prestaties van de data base tijdens het uitvoeren en indien mogelijk moet worden uitgevoerd tijdens peri Oden van weinig gebruik.

### <a name="dbcc-shrink"></a>DBCC Shrink

Zodra de data bases zijn geïdentificeerd voor het vrijmaken van ongebruikte toegewezen ruimte, wijzigt u de naam van de data base in de volgende opdracht om de gegevens bestanden voor elke Data Base te verkleinen.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1');
```

Het verkleinen van opdrachten de prestaties van de data base tijdens het uitvoeren en indien mogelijk moet worden uitgevoerd tijdens peri Oden van weinig gebruik.  

U moet ook rekening houden met de potentiële negatieve invloed op de prestaties van het verkleinen van database bestanden, zie de sectie [**indexen opnieuw samen stellen**](#rebuild-indexes) hieronder.

Zie [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)voor meer informatie over deze opdracht.

### <a name="auto-shrink"></a>Automatisch verkleinen

U kunt automatisch verkleinen ook inschakelen voor een Data Base.  Automatisch verkleinen vermindert de complexiteit van bestands beheer en is minder belang rijk voor de prestaties van de data base dan `SHRINKDATABASE` of `SHRINKFILE` .  Automatisch verkleinen kan bijzonder nuttig zijn voor het beheren van elastische Pools met veel data bases.  Automatisch verkleinen kan echter minder effectief zijn bij het vrijmaken van bestands ruimte dan `SHRINKDATABASE` en `SHRINKFILE` .
Automatisch verkleinen is standaard uitgeschakeld, zoals aanbevolen voor de meeste data bases. Zie [overwegingen voor AUTO_SHRINK](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)voor meer informatie.

Als u automatisch verkleinen wilt inschakelen, wijzigt u de naam van de data base in de volgende opdracht.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON;
```

Zie [Data Base set](/sql/t-sql/statements/alter-database-transact-sql-set-options) Options (Engelstalig) voor meer informatie over deze opdracht.

### <a name="rebuild-indexes"></a>Indexen opnieuw samen stellen

Nadat database gegevensbestanden zijn verkleind, kunnen de indexen gefragmenteerd raken en de effectiviteit van de prestatie optimalisatie verliezen. Als er sprake is van verminderde prestaties, kunt u database indexen opnieuw samen stellen. Zie [indexen opnieuw indelen en opnieuw samen stellen](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)voor meer informatie over fragmentatie en het opnieuw opbouwen van indexen.

## <a name="next-steps"></a>Volgende stappen

- Zie voor informatie over de maximale grootte van data bases:
  - [Azure SQL Database op vCore gebaseerde inkoop model limieten voor één data base](resource-limits-vcore-single-databases.md)
  - [Resourcelimieten voor individuele databases met behulp van het DTU-aankoopmodel](resource-limits-dtu-single-databases.md)
  - [Azure SQL Database op vCore gebaseerde inkoop model limieten voor elastische Pools](resource-limits-vcore-elastic-pools.md)
  - [Bronnen limieten voor elastische Pools met behulp van het DTU-gebaseerd inkoop model](resource-limits-dtu-elastic-pools.md)
- Zie SHRINKDATABASE voor meer informatie over de `SHRINKDATABASE` opdracht [](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Zie [indexen opnieuw indelen en opnieuw samen stellen](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)voor meer informatie over fragmentatie en het opnieuw opbouwen van indexen.
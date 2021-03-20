---
title: Resourcebeheer in dichte elastische pools
description: Reken bronnen beheren in Azure SQL Database elastische Pools met veel data bases.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 40b6c5a86184860cf3e7a9840f980706485ae977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572242"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Resourcebeheer in dichte elastische pools
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database [elastische Pools](./elastic-pool-overview.md) is een kosteneffectieve oplossing voor het beheren van veel data bases met een wisselend resource gebruik. Alle data bases in een elastische pool delen dezelfde toewijzing van bronnen, zoals CPU, geheugen, worker-threads, opslag ruimte, tempdb, op de veronderstelling dat **slechts een subset van data bases op een bepaald moment reken bronnen gebruikt**. Deze veronderstelling maakt het mogelijk dat elastische Pools rendabel zijn. In plaats van te betalen voor alle resources die elke afzonderlijke data base mogelijk nodig heeft, betalen klanten voor een veel kleinere set resources, gedeeld onder alle data bases in de groep.

## <a name="resource-governance"></a>Resourcebeheer

Voor het delen van resources moet het systeem het resource gebruik zorgvuldig beheren om het effect ' ruis in de buur ' te minimaliseren, waarbij een Data Base met een hoog Resource verbruik van invloed is op andere data bases in dezelfde elastische groep. Tegelijkertijd moet het systeem voldoende bronnen bieden voor functies als hoge Beschik baarheid en herstel na nood gevallen (HADR), back-up en herstel, bewaking, query Store, automatische afstemming, enzovoort.

Azure SQL Database haalt deze doel stellingen op met behulp van meerdere middelen beheer mechanismen, waaronder Windows- [taak objecten](/windows/win32/procthread/job-objects) voor het resource beheer op proces niveau, Windows [File Server Resource Manager (FSRM)](/windows-server/storage/fsrm/fsrm-overview) voor opslag quotum beheer en een aangepaste en uitgebreide versie van SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) voor het implementeren van resource governance binnen SQL database.

Het primaire ontwerp doel van elastische Pools is de kosten effectief. Daarom kunnen klanten met opzet _dense_ Pools maken, dat wil zeggen Pools met het aantal data bases dat het toegestane maximum heeft bereikt, maar met een gemiddelde toewijzing van reken bronnen. Om dezelfde reden worden niet alle resources die nodig zijn voor de interne processen door het systeem gereserveerd, maar is het delen van resources tussen interne processen en werk belastingen van de gebruiker mogelijk.

Met deze aanpak kunnen klanten compacte elastische Pools gebruiken om de prestaties en aanzienlijke kosten besparingen te bereiken. Als de werk belasting voor veel data bases in een compacte pool echter voldoende intensief is, wordt de bron conflicten aanzienlijk. Bron conflicten verminderen de prestaties van de gebruikers belasting en kunnen een negatieve invloed hebben op interne processen.

> [!IMPORTANT]
> In compacte Pools met veel actieve data bases is het wellicht niet haalbaar om het aantal data bases in de pool te verhogen tot de maximum waarden die zijn gedocumenteerd voor [DTU](resource-limits-dtu-elastic-pools.md) -en [vCore](resource-limits-vcore-elastic-pools.md) elastische Pools.
>
> Het aantal data bases dat in compacte Pools kan worden geplaatst zonder dat bronnen conflicten en prestatie problemen worden veroorzaakt, is afhankelijk van het aantal gelijktijdig actieve data bases en het Resource verbruik door de werk belasting van gebruikers in elke Data Base. Dit nummer kan in de loop van de tijd worden gewijzigd wanneer de werk belasting van de gebruiker verandert.

Als bronnen conflicten optreden in een compacte, verpakte pool, kunnen klanten een of meer van de volgende acties kiezen om deze te beperken:

- De werk belasting van de query afstemmen om het Resource verbruik te verminderen of Resource verbruik gedurende een bepaalde periode over meerdere data bases te verdelen.
- Verminder de densiteit van groepen door sommige data bases te verplaatsen naar een andere groep of door deze zelfstandige data bases te maken.
- Schaal de pool omhoog om meer resources te verkrijgen.

Zie voor meer informatie over het implementeren van de laatste twee acties, verderop in dit artikel aanbevolen [operationele aanbevelingen](#operational-recommendations) . Het verminderen van de conflicten tussen resources en interne processen en het systeem kan op betrouw bare wijze het verwachte service niveau beheren.

## <a name="monitoring-resource-consumption"></a>Resource verbruik bewaken

Om te voor komen dat de prestaties afnemen als gevolg van bron conflicten, moeten klanten die gebruikmaken van compacte elastische Pools het Resource verbruik proactief bewaken en tijd best rijken wanneer het verg Roten van de bron conflicten van invloed is op werk belastingen. Doorlopende bewaking is belang rijk omdat het resource gebruik in een pool in de loop van de tijd verandert, als gevolg van wijzigingen in de werk belasting van de gebruiker, wijzigingen in de gegevens volumes en distributie, wijzigingen in de geheugen densiteit en wijzigingen in de Azure SQL Database-Service.

Azure SQL Database biedt diverse metrische gegevens die relevant zijn voor dit type bewaking. Het overschrijden van de aanbevolen gemiddelde waarde voor elke metriek duidt op bron conflicten in de groep en moet worden aangepakt met een van de eerder genoemde acties.

|Naam van metrische gegevens|Beschrijving|Aanbevolen gemiddelde waarde|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|CPU-gebruik van het SQL-proces dat is gekoppeld aan een elastische pool, zoals gemeten door het onderliggende besturings systeem. Beschikbaar in de weer gave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in elke Data Base en in de [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weer gave in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` en kunnen worden weer gegeven in azure Portal. Deze waarde is hetzelfde voor elke data base in dezelfde elastische pool.|Minder dan 70%. Incidentele korte pieken tot 90% kunnen acceptabel zijn.|
|`max_worker_percent`|Gebruik van [worker-thread]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . Voor elke data base in de groep, evenals voor de groep zelf. Er zijn verschillende limieten voor het aantal werkthreads op database niveau en op groeps niveau, daarom is het raadzaam deze metrische gegevens op beide niveaus te controleren. Beschikbaar in de weer gave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in elke Data Base en in de [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weer gave in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` en kunnen worden weer gegeven in azure Portal.|Minder dan 80%. Pieken tot 100% leidt ertoe dat verbindings pogingen en query's mislukken.|
|`avg_data_io_percent`|IOPS-gebruik voor lezen en schrijven fysieke IO. Voor elke data base in de groep, evenals voor de groep zelf. Er zijn verschillende limieten voor het aantal IOPS op database niveau en op groeps niveau, daarom is het raadzaam deze metrische gegevens op beide niveaus te controleren. Beschikbaar in de weer gave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in elke Data Base en in de [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weer gave in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` en kunnen worden weer gegeven in azure Portal.|Minder dan 80%. Incidentele korte pieken tot 100% kunnen acceptabel zijn.|
|`avg_log_write_percent`|Doorvoer gebruik voor schrijf-i/o van transactie Logboeken. Voor elke data base in de groep, evenals voor de groep zelf. Er zijn verschillende limieten voor de door Voer van het logboek op database niveau en op het niveau van de groep, daarom is het raadzaam deze metrische gegevens op beide niveaus te controleren. Beschikbaar in de weer gave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in elke Data Base en in de [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weer gave in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` en kunnen worden weer gegeven in azure Portal. Wanneer deze metriek zich in de buurt van 100% bevindt, worden alle database wijzigingen (INVOEG-, UPDATE-, DELETE-en MERGe-instructies geselecteerd... IN, BULK INSERT, enz.) is langzamer.|Minder dan 90%. Incidentele korte pieken tot 100% kunnen acceptabel zijn.|
|`oom_per_second`|De frequentie van OOM-fouten (out-of-Memory) in een elastische pool. Dit is een indicatie van de geheugen belasting. Beschikbaar in de weer gave [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) . Bekijk voor [beelden](#examples) voor een voorbeeld query om deze metrische gegevens te berekenen.|0|
|`avg_storage_percent`|Totale opslag ruimte die wordt gebruikt door gegevens in alle data bases binnen een elastische pool. Bevat geen lege ruimte in database bestanden. Beschikbaar in de weer gave [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` en kunnen worden weer gegeven in azure Portal.|Minder dan 80%. Kan 100% voor Pools benaderen zonder gegevens groei.|
|`avg_allocated_storage_percent`|Totale opslag ruimte die wordt gebruikt door database bestanden in opslag in alle data bases binnen een elastische pool. Bevat lege ruimte in database bestanden. Beschikbaar in de weer gave [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in de- `master` Data Base. Deze metrische gegevens worden ook verzonden naar Azure Monitor, waar ze worden [genoemd](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` en kunnen worden weer gegeven in azure Portal.|Minder dan 90%. Kan 100% voor Pools benaderen zonder gegevens groei.|
|`tempdb_log_used_percent`|Ruimte gebruik van het transactie logboek in de- `tempdb` Data Base. Hoewel tijdelijke objecten die zijn gemaakt in de ene data base niet zichtbaar zijn in andere data bases in dezelfde elastische pool, `tempdb` is een gedeelde resource voor alle data bases in dezelfde groep. Een langlopende of zwevende trans actie in `tempdb` begonnen vanuit een data base in de groep kan een groot deel van het transactie logboek verbruiken en fouten veroorzaken voor query's in andere data bases in dezelfde groep. Afgeleid van [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) en [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) weer gaven. Deze metrische gegevens worden ook verzonden naar Azure Monitor en kunnen worden weer gegeven in Azure Portal. Zie voor [beelden](#examples) voor een voorbeeld query om de huidige waarde van deze metriek te retour neren.|Minder dan 50%. Incidentele pieken tot 80% zijn acceptabel.|
|||

Naast deze metrische gegevens bevat Azure SQL Database een weer gave die de werkelijke governance limieten van resources retourneert, evenals aanvullende weer gaven die de statistieken van het resource gebruik op het niveau van de resource groep retour neren, en op het niveau van de werkbelasting groep.

|Weergave naam|Beschrijving|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Retourneert werkelijke configuratie-en capaciteits instellingen die worden gebruikt door resource governance mechanismen in de huidige data base of elastische pool.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Retourneert informatie over de huidige status van de resource groep, de huidige configuratie van resource groepen en de statistieken van de cumulatieve resource groep.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Retourneert cumulatieve statistieken over werkbelasting groepen en de huidige configuratie van de werkbelasting groep. Deze weer gave kan worden gekoppeld aan sys.dm_resource_governor_resource_pools in de `pool_id` kolom om informatie over de resource groep op te halen.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Retourneert statistieken over het gebruik van de resource groep voor de afgelopen 32 minuten. Elke rij vertegenwoordigt een interval van 20 seconden. De `delta_` kolommen retour neren de wijziging in elke statistiek tijdens het interval.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Retourneert statistieken over het verbruik van werk belasting groepen voor de afgelopen 32 minuten. Elke rij vertegenwoordigt een interval van 20 seconden. De `delta_` kolommen retour neren de wijziging in elke statistiek tijdens het interval.|
|||

Deze weer gaven kunnen worden gebruikt voor het bewaken van het resource gebruik en het oplossen van problemen met bron conflicten in bijna realtime. De werk belasting van de gebruiker op de primaire en lees bare secundaire replica's, met inbegrip van geo-replica's, wordt geclassificeerd in de `SloSharedPool1` resource groep en `UserPrimaryGroup.DBId[N]` de werkbelasting groep waar `N` de data base-id-waarde staat.

Naast het bewaken van het huidige resource gebruik, kunnen klanten die gebruikmaken van een compacte pool, historische resource gebruiks gegevens in een afzonderlijk gegevens archief onderhouden. Deze gegevens kunnen worden gebruikt in voorspellende analyse om het resource gebruik proactief te beheren op basis van historische en seizoen trends.

## <a name="operational-recommendations"></a>Operationele aanbevelingen

**Geef voldoende resource ruimte**. Als er sprake is van conflicterende bronnen en prestaties, kan de beperking ertoe leiden dat sommige data bases worden verplaatst van de betreffende elastische pool of dat u de pool omhoog kunt schalen, zoals eerder is aangegeven. Voor deze acties zijn echter extra reken resources vereist om te volt ooien. Met name voor Premium en Bedrijfskritiek Pools moeten voor deze acties alle gegevens worden overgedragen voor de data bases die worden verplaatst of voor alle data bases in de elastische pool als de pool omhoog wordt geschaald. Gegevens overdracht is een langlopende en resource-intensieve bewerking. Als de pool zich al onder een hoge bron belasting bevindt, wordt de prestaties nog verder verslechterd door de beperkende bewerking zelf. In uitzonderlijke gevallen is het niet mogelijk om bron conflicten op te lossen via data base verplaatsen of pool scale-up omdat de vereiste resources niet beschikbaar zijn. In dit geval kan de werk belasting van de betrokken elastische groep tijdelijk worden verminderd.

Klanten die gebruikmaken van een compacte pool, moeten trends in het resource gebruik nauw keurig bewaken, zoals eerder beschreven, en de actie beperken terwijl de metrische gegevens binnen de aanbevolen bereiken blijven en er nog voldoende resources zijn in de elastische pool.

Het resource gebruik is afhankelijk van meerdere factoren die in de loop van de tijd worden gewijzigd voor elke Data Base en elke elastische pool. Het bereiken van een optimale prijs-prestatie verhouding in compacte Pools vereist doorlopende bewaking en herverdeling, waarmee data bases van meer gebruikte Pools worden verplaatst naar minder gebruikte Pools en nieuwe Pools kunnen worden gemaakt voor een verhoogde werk belasting.

**Geen ' hot ' data bases verplaatsen**. Als bron conflicten op groeps niveau voornamelijk worden veroorzaakt door een klein aantal zeer gebruikte data bases, kan het voor komen dat deze data bases worden verplaatst naar een minder gebruikte pool of dat ze zelfstandige data bases kunnen maken. Dit wordt echter niet aanbevolen, omdat een Data Base veel gebruikt wordt, omdat de verplaatsings bewerking verdere prestaties verslechteren, zowel voor de data base die wordt verplaatst als voor de hele groep. Wacht in plaats daarvan tot hoog gebruik, of verplaats minder gebruikte data bases in plaats daarvan de resource druk op het niveau van de groep te ontlasten. Maar het verplaatsen van data bases met een zeer laag gebruik biedt in dit geval geen voor deel, omdat het het resource gebruik niet aanmerkelijk vermindert op het niveau van de groep.

**Nieuwe data bases in een quarantaine groep maken**. In scenario's waarin regel matig nieuwe data bases worden gemaakt, zoals toepassingen die gebruikmaken van het model Tenant-per-data base, is er een risico dat een nieuwe data base in een bestaande elastische pool onverwacht aanzienlijke bronnen verbruikt en van invloed is op andere data bases en interne processen in de groep. U kunt dit risico beperken door een afzonderlijke quarantaine groep te maken met een ruime toewijzing van resources. Gebruik deze groep voor nieuwe data bases met nog onbekende patronen voor Resource verbruik. Zodra een data base in deze groep voor een bedrijfs cyclus is geduurd, zoals een week of maand, en het Resource verbruik bekend is, kan deze worden verplaatst naar een pool met voldoende capaciteit om dit extra resource gebruik te kunnen verwerken.

**Bewaak zowel gebruikte als toegewezen ruimte**. Wanneer de toegewezen pool ruimte (totale grootte van alle database bestanden in de opslag voor alle data bases in een pool) de maximale pool grootte bereikt, kunnen er fouten optreden met betrekking tot onvoldoende ruimte. Als de trends van de toegewezen ruimte hoog zijn en op schema zijn om de maximale groeps grootte te bereiken, zijn de volgende opties voor risico beperking:
- Verplaats sommige data bases uit de groep om de totale hoeveelheid toegewezen ruimte te beperken
- Database bestanden [verkleinen](file-space-manage.md) om de lege toegewezen ruimte in bestanden te verminderen
- De pool omhoog schalen naar een service doelstelling met een grotere maximum pool grootte

Als er groeps ruimte (totale grootte van gegevens in alle data bases in een pool, exclusief lege ruimte in bestanden), trends hoog is en de maximale groeps grootte bereikt, kunt u de volgende beperkende opties gebruiken:
- Verplaats enkele data bases uit de groep om de totale gebruikte ruimte te beperken
- Gegevens verplaatsen (archiveren) buiten de data base of gegevens verwijderen die niet meer nodig zijn
- [Gegevens compressie](/sql/relational-databases/data-compression/data-compression) implementeren
- De pool omhoog schalen naar een service doelstelling met een grotere maximum pool grootte

**Vermijd het gebruik van servers met hoge dichtheid**. Azure SQL Database [ondersteunt](./resource-limits-logical-server.md) maxi maal 5000 data bases per server. Klanten die gebruikmaken van elastische Pools met duizenden data bases, kunnen overwegen om meerdere elastische Pools op één server te plaatsen, met het totale aantal data bases tot de ondersteunde limiet. Servers met veel duizenden data bases maken echter operationele uitdagingen. Bewerkingen waarvoor het opsommen van alle data bases op een server is vereist, bijvoorbeeld het weer geven van data bases in de portal, verloopt langzamer. Operationele fouten, zoals een onjuiste wijziging van aanmeldingen op server niveau of firewall regels, zijn van invloed op een groter aantal data bases. Voor onbedoeld verwijderen van de server is hulp nodig van Microsoft Ondersteuning om data bases op de verwijderde server te herstellen, waardoor er een langdurige storing optreedt voor alle betrokken data bases.

Het is raadzaam het aantal data bases per server te beperken tot een lager nummer dan het maximum aantal dat wordt ondersteund. In veel gevallen is het gebruik van Maxi maal 1000-2000 data bases per server optimaal. Om de kans op onbedoeld verwijderen van de server te verminderen, wordt u aangeraden een [verwijderings vergrendeling](../../azure-resource-manager/management/lock-resources.md) op de server of de resource groep te plaatsen.

In het verleden waren bepaalde scenario's met betrekking tot het verplaatsen van data bases in, uit of tussen elastische Pools op dezelfde server sneller dan bij het verplaatsen van data bases tussen servers. Op dit moment wordt de uitvoering van alle data bases op dezelfde snelheid uitgevoerd, ongeacht de bron-en doel server.

## <a name="examples"></a>Voorbeelden

### <a name="monitoring-memory-utilization"></a>Geheugen gebruik bewaken

Deze query berekent de `oom_per_second` metrische gegevens voor elke resource groep, in de afgelopen 32 minuten. Deze query kan worden uitgevoerd in elke data base in een elastische pool.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Gebruik van controle `tempdb` logboek ruimte

Met deze query wordt de huidige waarde van de `tempdb_log_used_percent` metriek geretourneerd, waarbij het relatieve gebruik van het transactie logboek wordt weer gegeven `tempdb` ten opzichte van de Maxi maal toegestane grootte. Deze query kan worden uitgevoerd in elke data base in een elastische pool.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Volgende stappen

- Zie elastische Pools voor een inleiding tot elastische Pools, [waarmee u meerdere data bases in Azure SQL database kunt beheren en schalen](./elastic-pool-overview.md).
- Zie [bewaking en afstemming]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), en [controle en prestaties afstemmen](./monitor-tune-overview.md)voor informatie over het afstemmen van query-workloads om het resource gebruik te verminderen.
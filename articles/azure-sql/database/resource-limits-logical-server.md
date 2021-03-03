---
title: Resource limieten voor logische servers in azure
description: Dit artikel bevat een overzicht van de resource limieten voor de logische server in azure die wordt gebruikt door Azure SQL Database en Azure Synapse Analytics. Het bevat ook informatie over wat er gebeurt wanneer deze resource limieten worden bereikt of overschreden.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: 34613633b6b27fc3387e6a9fa63caf4a194ba963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691226"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Resource limieten voor Azure SQL Database en Azure Synapse Analytics-servers
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Dit artikel bevat een overzicht van de resource limieten voor de logische server die wordt gebruikt door Azure SQL Database en Azure Synapse Analytics. Het bevat informatie over wat er gebeurt wanneer deze resource limieten worden bereikt of overschreden en beschrijft de resource governance-mechanismen die worden gebruikt om deze limieten af te dwingen.

> [!NOTE]
> Zie [SQL database resource limieten voor beheerde instanties](../managed-instance/resource-limits.md)voor de limieten voor Azure SQL Managed instances.

## <a name="maximum-resource-limits"></a>Maximum aantal resource limieten

| Resource | Limiet |
| :--- | :--- |
| Data bases per server | 5000 |
| Standaard aantal servers per abonnement in een wille keurige regio | 20 |
| Maximum aantal servers per abonnement in een regio | 200 |  
| DTU/eDTU-quotum per server | 54.000 |  
| vCore quotum per Server/exemplaar | 540 |
| Maximum aantal groepen per server | Beperkt door het aantal Dtu's of vCores. Als bijvoorbeeld elke groep 1000 Dtu's is, kan een server 54 groepen ondersteunen.|
|||

> [!IMPORTANT]
> Wanneer het aantal data bases de limiet per server nadert, kunnen de volgende problemen optreden:
>
> - De latentie verhogen bij het uitvoeren van query's op de hoofd database.  Dit geldt ook voor weer gaven van gegevens over het resource gebruik, zoals sys.resource_stats.
> - Het verg Roten van latentie in beheer bewerkingen en het weer geven van portal-gezichts punten waarbij de data bases op de server worden geïnventariseerd.

> [!NOTE]
> Als u meer DTU/eDTU-quotum, vCore quotum of meer servers dan het standaard aantal wilt verkrijgen, moet u een nieuwe ondersteunings aanvraag indienen in de Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](quota-increase-request.md)voor meer informatie.

### <a name="storage-size"></a>Opslag grootte

Voor bron opslag van één data base, raadpleegt u op [DTU gebaseerde resource limieten](resource-limits-dtu-single-databases.md) of op [vCore gebaseerde resource limieten](resource-limits-vcore-single-databases.md) voor de maximale opslag ruimte per prijs categorie.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Wat er gebeurt wanneer de database resource limieten zijn bereikt

### <a name="compute-cpu"></a>Reken CPU

Wanneer het CPU-gebruik van de data base hoog wordt, wordt de query latentie verhoogd en kunnen query's zelfs een time-out opleveren. Onder deze omstandigheden kunnen query's in de wachtrij worden geplaatst door de service en worden resources opgegeven voor uitvoering omdat resources gratis worden.
Wanneer het gebruik van hoge berekeningen wordt tegengekomen, zijn de volgende opties voor risico beperking:

- De reken grootte van de data base of elastische pool verg Roten om de data base te voorzien van meer compute-resources. Zie bronnen van [één data base schalen](single-database-scale.md) en [elastische pool resources schalen](elastic-pool-scale.md).
- Query's optimaliseren om het CPU-resource gebruik van elke query te verminderen. Zie [Afstemmen van query's en query-hints](performance-guidance.md#query-tuning-and-hinting) voor meer informatie.

### <a name="storage"></a>Storage

Wanneer gebruikte database ruimte de maximale grootte bereikt, worden invoeg bewerkingen en updates die de omvang van de gegevens overschrijden, door data bases ingevoegd en bijgewerkt en ontvangen clients een [fout bericht](troubleshoot-common-errors-issues.md). De instructies SELECT en DELETE blijven slagen.

Wanneer u gebruik maakt van hoge ruimte, kunt u onder andere het volgende doen:

- De maximale grootte van de data base of elastische pool verg Roten of meer opslag toevoegen. Zie bronnen van [één data base schalen](single-database-scale.md) en [elastische pool resources schalen](elastic-pool-scale.md).
- Als de data base zich in een elastische pool bevindt, kan de data base buiten de pool worden geplaatst, zodat de opslag ruimte niet wordt gedeeld met andere data bases.
- Een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.
- Controleer of het gebruik van hoge ruimte wordt veroorzaakt door een piek in de grootte van de permanente versie opslag (PVS). PVS maakt deel uit van elke Data Base en wordt gebruikt om  [versneld database herstel](../accelerated-database-recovery.md)te implementeren. Zie [PVS Troubleshooting](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting)(Engelstalig) om de huidige grootte van de PVS te bepalen. Een veelvoorkomende reden voor grote PVS is een trans actie die lange tijd (uren) is geopend, waardoor het opruimen van oudere versies in PVS wordt voor komen.

### <a name="sessions-and-workers-requests"></a>Sessies en werk nemers (aanvragen)

Het maximum aantal sessies en werk rollen wordt bepaald door de servicelaag en de berekenings grootte (Dtu's/Edtu's of vCores). Nieuwe aanvragen worden afgewezen wanneer de limiet voor sessies of werk nemers is bereikt en clients ontvangen een fout bericht. Hoewel het aantal beschik bare verbindingen kan worden beheerd door de toepassing, is het aantal gelijktijdige werk nemers vaak moeilijker om te schatten en te beheren. Dit geldt met name voor piek belasting perioden wanneer database resource limieten zijn bereikt en werk nemers kunnen opvallen omdat er meer query's, grote blokkerende ketens of buitensporige query parallellisme worden uitgevoerd.

Wanneer u het gebruik van hoge sessies of werk nemers ondervindt, kunt u de volgende beperkende opties gebruiken:

- De servicelaag of de reken grootte van de data base of elastische pool wordt verhoogd. Zie bronnen van [één data base schalen](single-database-scale.md) en [elastische pool resources schalen](elastic-pool-scale.md).
- Het optimaliseren van query's om het resource gebruik van elke query te verminderen als de oorzaak van het verhoogde werk nemer is vanwege de conflicten voor reken bronnen. Zie [Afstemmen van query's en query-hints](performance-guidance.md#query-tuning-and-hinting) voor meer informatie.
- De instelling van de [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximale mate van parallellisme) verlagen.
- Optimaliseer de query werk belasting zodat het aantal keren en de duur van query's wordt beperkt. Zie [problemen met Azure SQL-blok kades begrijpen en oplossen](understand-resolve-blocking.md)voor meer informatie.

### <a name="memory"></a>Geheugen

In tegens telling tot andere resources (CPU, werk nemers, opslag), heeft het bereiken van de geheugen limiet geen negatieve invloed op de query prestaties en leidt dit niet tot fouten en fouten. Zoals beschreven in details over de [architectuur handleiding voor geheugen beheer](/sql/relational-databases/memory-management-architecture-guide), gebruikt de SQL server data base-engine vaak al het beschik bare geheugen. Geheugen wordt hoofd zakelijk gebruikt voor het opslaan van gegevens in de cache, om te voor komen dat u dure opslag toegang krijgt. Met een hoger geheugen gebruik wordt de query prestaties meestal verbeterd vanwege snellere lees bewerkingen van het geheugen, in plaats van langzamere Lees bewerkingen van de opslag.

Nadat de data base-engine is gestart, worden gegevens in het geheugen agressief opgeslagen in de data base-engine, omdat de werk belasting gegevens uit de opslag begint te lezen. Na deze eerste opvolgings periode is het gebruikelijk en verwacht dat de `avg_memory_usage_percent` kolommen en worden weer gegeven `avg_instance_memory_percent` in [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) sluitend of gelijk zijn aan 100%, met name voor data bases die niet inactief zijn en niet volledig in het geheugen passen.

Naast de gegevens cache wordt geheugen gebruikt in andere onderdelen van de data base-engine. Wanneer er een geheugen vraag is en alle beschik bare geheugen door de gegevens cache is gebruikt, wordt de grootte van de gegevens cache door de data base-engine dynamisch verkleind om geheugen beschikbaar te maken voor andere onderdelen, en wordt de gegevens cache dynamisch uitgebreid wanneer er andere onderdelen vrij zijn van geheugen.

In zeldzame gevallen kan een voldoende veeleisende werk belasting een ontoereikende geheugen fout veroorzaken, waardoor er onvoldoende geheugen beschikbaar is. Dit kan gebeuren op elk niveau van geheugen gebruik tussen 0% en 100%. Dit is waarschijnlijker voor kleinere reken grootten die proportionele kleinere geheugen limieten hebben en/of met werk belastingen met meer geheugen voor het verwerken van query's, zoals in [compacte elastische Pools](elastic-pool-resource-management.md).

Als er geheugen fouten optreden, zijn de volgende opties beschikbaar:
- De servicelaag of de reken grootte van de data base of elastische pool wordt verhoogd. Zie bronnen van [één data base schalen](single-database-scale.md) en [elastische pool resources schalen](elastic-pool-scale.md).
- Query's en configuratie optimaliseren om het geheugen gebruik te beperken. Algemene oplossingen worden beschreven in de volgende tabel.

|Oplossing|Beschrijving|
| :----- | :----- |
|De grootte van geheugen subsidies verminderen|Voor meer informatie over geheugen subsidies, zie het blog bericht [over SQL Server geheugen toekenning](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . Een gemeen schappelijke oplossing om te voor komen dat buitensporig grote geheugen subsidies worden gebruikt, houdt [Statistieken](/sql/relational-databases/statistics/statistics) up-to-date. Dit resulteert in een nauw keurige schatting van het geheugen verbruik door de query-engine, waardoor onnodig grote geheugen subsidies worden voor komen.</br></br>In data bases die gebruikmaken van het compatibiliteits niveau 140 en hoger, kan de data base-engine automatisch de grootte van het geheugen toewijzen met behulp van [feedback van geheugen toekenning in batch modus](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback) In data bases die gebruikmaken van het compatibiliteits niveau 150 en hoger, gebruikt de data base-engine ook [feedback over geheugen toekenning](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)in de rij, voor meer veelgebruikte query's in de rij-modus. Deze ingebouwde functionaliteit helpt bij het vermijden van geheugen fouten als gevolg van onnodig grote geheugen subsidies.|
|De grootte van de cache voor query plannen beperken|De data base-engine slaat query plannen op in het geheugen, om te voor komen dat een query plan wordt gecompileerd voor elke uitvoering van de query. Schakel de OPTIMIZE_FOR_AD_HOC_WORKLOADS [database bereik configuratie](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)in om te voor komen dat de cache van het query plan wordt veroorzaakt door schema's die slechts één keer worden gebruikt.|
|De grootte van het vergrendelings geheugen verminderen|De data base-engine gebruikt geheugen voor [vergren delingen](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Vermijd zo mogelijk grote trans acties die een groot aantal vergren delingen kunnen verkrijgen en het gebruik van hoog vergrendelings geheugen veroorzaken.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Resource verbruik door workloads van gebruikers en interne processen

Het CPU-en geheugen verbruik per werk belasting van de gebruiker in elke Data Base wordt gerapporteerd in de weer gaven [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , in `avg_cpu_percent` en `avg_memory_usage_percent` kolommen. Voor elastische Pools wordt het gebruik van resource op pool niveau gerapporteerd in de weer gave [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . Het CPU-verbruik van de werk belasting van de gebruiker wordt ook gerapporteerd via de `cpu_percent` Azure monitor metriek voor [afzonderlijke data bases](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) en [elastische Pools](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) op groeps niveau.

Azure SQL Database hebt reken resources nodig om basis service functies te implementeren, zoals hoge Beschik baarheid en herstel na nood gevallen, database back-up en herstel, bewaking, query Store, automatische afstemming, enzovoort. Het systeem stelt een bepaald beperkt deel van de algemene resources voor deze interne processen in met behulp van [resource governance](#resource-governance) -mechanismen, waardoor de rest van resources beschikbaar is voor werk belastingen van gebruikers. Wanneer interne processen geen reken resources gebruiken, worden ze beschikbaar gemaakt voor werk belastingen van gebruikers.

Het totale CPU-en geheugen verbruik per werk belasting van de gebruiker en interne processen worden gerapporteerd in de weer gaven [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , in `avg_instance_cpu_percent` en `avg_instance_memory_percent` kolommen. Deze gegevens worden ook gerapporteerd via de `sqlserver_process_core_percent` en `sqlserver_process_memory_percent` Azure monitor metrieken voor [afzonderlijke data bases](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) en [elastische Pools](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) op groeps niveau.

Een gedetailleerdere uitsplitsing van het recente verbruik van bronnen door werk belastingen van gebruikers en interne processen wordt gerapporteerd in de weer gaven [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) en [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Zie [resource governance](#resource-governance)voor meer informatie over resource groepen en werkbelasting groepen waarnaar wordt verwezen in deze weer gaven. Deze weer gaven rapporteren over het resource gebruik per werk belasting van de gebruiker en specifieke interne processen in de gekoppelde resource groepen en werkbelasting groepen.

In de context van het controleren van de prestaties en het oplossen van problemen is het belang rijk om zowel het **CPU-verbruik** van de gebruiker ( `avg_cpu_percent` , `cpu_percent` ) als het **totale CPU-verbruik** door werk belastingen van gebruikers en interne processen ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ) in overweging te nemen.

Het **CPU-verbruik** van de gebruiker wordt berekend als percentage van de werkbelasting limieten van de gebruiker in elke service doelstelling. **CPU-gebruik van gebruikers** bij 100% geeft aan dat de werk belasting van de gebruiker de limiet van de service doelstelling heeft bereikt. Wanneer het **totale CPU-verbruik** echter het bereik van 70-100% bereikt, is het mogelijk om de door Voer van de gebruiker te bekijken en de vertraging van query's te verg Roten, zelfs als gerapporteerd **CPU-verbruik** van de gebruiker aanzienlijk lager is dan 100%. Dit is waarschijnlijker bij het gebruik van kleinere service doelstellingen met een gemiddelde toewijzing van reken resources, maar relatief intensieve werk belastingen voor gebruikers, zoals in [compacte elastische Pools](elastic-pool-resource-management.md). Dit kan ook gebeuren met kleinere service doelstellingen wanneer interne processen tijdelijk extra bronnen nodig hebben, bijvoorbeeld bij het maken van een nieuwe replica van de data base.

Wanneer het **totale CPU-verbruik** hoog is, zijn de opties voor risico beperking hetzelfde als eerder vermeld en zijn de optimalisatie van de service doelstelling en/of de werk belasting van de gebruiker opgenomen.

## <a name="resource-governance"></a>Resourcebeheer

Azure SQL Database maakt gebruik van een resource governance-implementatie die is gebaseerd op SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor), gewijzigd en uitgebreid om uit te voeren in Azure SQL database, om resource limieten af te dwingen. In SQL Database, meerdere [resource groepen](/sql/relational-databases/resource-governor/resource-governor-resource-pool) en [werkbelasting groepen](/sql/relational-databases/resource-governor/resource-governor-workload-group), met resource limieten die zijn ingesteld op zowel groeps-als groeps niveau, bieden een [evenwichtige data base-as-a-Service](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). De werk belasting van gebruikers en interne workloads worden geclassificeerd in afzonderlijke resource groepen en werkbelasting groepen. De werk belasting van de gebruiker op de primaire en lees bare secundaire replica's, met inbegrip van geo-replica's, wordt geclassificeerd in de `SloSharedPool1` resource groep en `UserPrimaryGroup.DBId[N]` de werkbelasting groep waar `N` de data base-id-waarde staat. Daarnaast zijn er meerdere resource groepen en werkbelasting groepen voor verschillende interne workloads.

Naast het gebruik van Resource Governor voor het beheren van resources binnen het SQL-proces, gebruikt Azure SQL Database ook Windows- [taak objecten](/windows/win32/procthread/job-objects) voor het beheer van resource beheer op proces niveau en Windows [File Server Resource Manager (FSRM)](/windows-server/storage/fsrm/fsrm-overview) voor Storage quota management.

Azure SQL Database Resource governance is hiërarchisch van aard. Van boven naar beneden worden de limieten afgedwongen op het niveau van het besturings systeem en op het niveau van het opslag volume met behulp van de beheer mechanismen van het resource-en Resource Governor, op het niveau van de resource groep met behulp van Resource Governor en vervolgens op het niveau van de werkbelasting groep met behulp van Resource Governor. De beheer limieten voor resources die gelden voor de huidige data base of elastische pool, worden weer gegeven in de [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) weergave.

### <a name="data-io-governance"></a>Data IO governance

Data IO governance is een proces in Azure SQL Database dat wordt gebruikt voor het beperken van fysieke IO-Lees-en schrijf bewerkingen op gegevens bestanden van een Data Base. IOPS-limieten worden voor elk service niveau ingesteld om het effect "ruis op de buur" zo klein mogelijk te maken, om resource toewijzings verdeling in de multi tenant-service te bieden en om binnen de mogelijkheden van de onderliggende hardware en opslag te blijven.

Voor afzonderlijke data bases worden de limieten voor werkbelasting groepen toegepast op alle opslag-i/o-bewerkingen op de data base, terwijl de limieten voor de resource groep van toepassing zijn op alle opslag-i/o-data bases op dezelfde specifieke SQL-groep, inclusief `tempdb` Voor elastische Pools gelden werkbelasting groeps limieten voor elke data base in de pool, terwijl de limiet voor de resource groep van toepassing is op de volledige elastische pool, met inbegrip van de `tempdb` Data Base, die wordt gedeeld door alle data bases in de groep. Over het algemeen kunnen limieten voor resource groepen niet worden behaald door de werk belasting op basis van een Data Base (enkel of gegroepeerd), omdat de limieten voor werkbelasting groepen lager zijn dan de limieten voor de resource groep en het aantal IOPS/door Voer is beperkt. Groeps limieten kunnen echter worden bereikt door de gecombineerde werk belasting op meerdere data bases in dezelfde groep.

Als een query bijvoorbeeld 1000 IOPS genereert zonder IO resource governance, maar de maximale IOPS-limiet van de werkbelasting groep is ingesteld op 900 IOPS, kan de query niet meer dan 900 IOPS genereren. Als de maximale IOPS-limiet van de resource groep is ingesteld op 1500 IOPS en het totale aantal IO-bewerkingen van alle werkbelasting groepen die zijn gekoppeld aan de resource groep meer dan 1500 IOPS overschrijdt, kan de i/o van dezelfde query worden verminderd onder de limiet van 900 IOPS voor de werk groep.

De min/max-waarden voor IOPS en door Voer die door de [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) weer gave worden geretourneerd, fungeren als limieten/cap's, niet als garanties. Daarnaast garandeert resource governance geen specifieke opslag latentie. De best Haal bare latentie, IOPS en door Voer voor een bepaalde gebruikers werkbelasting zijn niet alleen afhankelijk van de beheer limieten voor i/o-resources, maar ook op basis van de combi natie van i/o-grootten en de mogelijkheden van de onderliggende opslag. SQL Database maakt gebruik van IOs die variëren van grootte tussen 512 KB en 4 MB. Met het oog op het afdwingen van IOPS-limieten wordt elke IO verwerkt ongeacht de grootte, met uitzonde ring van data bases met gegevens bestanden in Azure Storage. In dat geval wordt IOs groter dan 256 KB verwerkt als meerdere 256-KB IOs, om te worden uitgelijnd met Azure Storage IO-accounting.

Voor Basic-, Standard-en Algemeen-data bases, die gebruikmaken van gegevens bestanden in Azure Storage, `primary_group_max_io` kan de waarde mogelijk niet worden behaald als een Data Base niet genoeg gegevens bestanden bevat om cumulatief te bieden aan dit aantal IOPS, of als gegevens niet gelijkmatig over de bestanden worden gedistribueerd, of als het prestatie niveau van onderliggende blobs de limiet voor IOPS/door Voer overschrijdt. Op dezelfde manier, met Small log IOs gegenereerd door veelvuldige trans acties, `primary_max_log_rate` kan de waarde mogelijk niet worden behaald door een werk belasting als gevolg van de limiet voor IOPS voor de onderliggende Azure Storage blob. Voor data bases die gebruikmaken van Azure Premium Storage, Azure SQL Database gebruikt voldoende grote opslag-blobs om benodigde IOPS/door voer te verkrijgen, ongeacht de grootte van de data base. Voor grotere data bases worden meerdere gegevens bestanden gemaakt om het totale aantal IOPS/doorvoer capaciteit te verg Roten.

Waarden van resource gebruik, zoals `avg_data_io_percent` en `avg_log_write_percent` , gerapporteerd in de weer gaven [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)en [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , worden berekend als percentage van de maximale resource governance-limieten. Daarom is het mogelijk om te voor komen dat andere factoren dan de bron beheer limiet voor IOPS/door Voer, het afnemen van IOPS/door Voer, het afvlakken en latentie verhogen naarmate de werk belasting toeneemt, ook al is het gerapporteerde resource gebruik minder dan 100%.

Als u de IOPS, door Voer en latentie per database bestand voor lezen en schrijven wilt zien, gebruikt u de functie [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Deze functie behaalt alle i/o-bewerkingen op de data base, inclusief de i/o van de achtergrond die niet is verwerkt `avg_data_io_percent` , maar gebruikt IOPS en door Voer van de onderliggende opslag en kan invloed hebben op de waargenomen opslag latentie. De functie oppervlakken extra latentie die kan worden geïntroduceerd door i/o-resource beheer voor lees-en schrijf bewerkingen, respectievelijk in de `io_stall_queued_read_ms` `io_stall_queued_write_ms` kolommen en.

### <a name="transaction-log-rate-governance"></a>Beleid voor transactie logboek belasting

Het tarief voor transactie Logboeken is een proces in Azure SQL Database dat wordt gebruikt om een hoge opname snelheid te beperken voor werk belastingen, zoals bulksgewijs invoegen, selecteren en index builds. Deze limieten worden bijgehouden en afgedwongen op het subseconde op het niveau van de generatie van de logboek record, waardoor de door Voer wordt beperkt, ongeacht het aantal IOs-bestanden dat kan worden uitgegeven aan de hand van gegevens.  Tarieven voor het genereren van transactie logboeken worden momenteel lineair geschaald naar een punt dat is afhankelijk van de hardware, waarbij de maximale logboek snelheid van 96 MB/s met het vCore-aankoop model wordt toegestaan.

> [!NOTE]
> De daad werkelijke fysieke IOs-naar-transactie logboek bestanden zijn niet gebonden of beperkt.

De logboek tarieven zijn zodanig ingesteld dat ze in verschillende scenario's kunnen worden bereikt en onderhouden, terwijl het hele systeem de functionaliteit kan behouden met een geminimaliseerd effect op de belasting van de gebruiker. Log rate governance zorgt ervoor dat back-ups van transactie logboeken binnen de gepubliceerde herstel-Sla's blijven.  Dit bestuur voor komt ook een buitensporige achterstand op secundaire replica's.

Wanneer er logboek records worden gegenereerd, wordt elke bewerking geëvalueerd en beoordeeld of deze moet worden uitgesteld om een maximale gewenste logboek frequentie (MB/s per seconde) te behouden. De vertragingen worden niet toegevoegd wanneer de logboek records worden leeg gemaakt naar de opslag, en de log rate governance wordt toegepast tijdens het genereren van de logboek frequentie.

De werkelijke generatie tarieven voor logboek registratie die tijdens de uitvoering zijn opgelegd, kunnen ook worden beïnvloed door de feedback mechanismen, waardoor de toegestane logboek tarieven tijdelijk worden verminderd zodat het systeem kan stabiliseren. Beheer van de ruimte van het logboek bestand, voor komen dat de logboek ruimte wordt gebruikt en de replicatie mechanismen voor beschikbaarheids groepen kunnen de algehele systeem limieten tijdelijk verlagen.

De verkeers vormgeving van de logboek frequentie wordt geoppereerd via de volgende wacht typen (beschikbaar in de [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) weer gaven):

| Wacht type | Notities |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Data base beperken |
| POOL_LOG_RATE_GOVERNOR | Beperking van groepen |
| INSTANCE_LOG_RATE_GOVERNOR | Beperking op exemplaar niveau |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback beheer, fysieke replicatie van beschikbaarheids groepen in Premium/Bedrijfskritiek niet bijhouden |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback beheer, beperkende tarieven om te voor komen dat er een fout optreedt in de logboek ruimte |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Feedback beheer geo-replicatie, beperken van de logboek frequentie om te voor komen dat hoge gegevens latentie en niet-beschik baarheid van geo-inbaarten|
|||

Houd rekening met de volgende opties als er een frequentie limiet optreedt die de gewenste schaal baarheid belemmert:

- Schaal omhoog naar een hoger service niveau om de maximale logboek frequentie van 96 MB/s te verkrijgen, of schakel over naar een andere servicelaag. De [grootschalige](service-tier-hyperscale.md) -servicelaag levert een logboek frequentie van 100 MB per seconde, ongeacht het gekozen service niveau.
- Als gegevens die worden geladen tijdelijk zijn, zoals faserings gegevens in een ETL-proces, kan deze worden geladen in TempDB (dit is mini maal geregistreerd).
- Voor analytische scenario's laadt u in een geclusterde column Store-gedekte tabel. Dit reduceert de vereiste logboek frequentie vanwege compressie. Deze techniek verhoogt het CPU-gebruik en is alleen van toepassing op gegevens sets die profiteren van geclusterde column Store-indexen.

### <a name="storage-space-governance"></a>Beheer van opslag ruimte

In Premium-en Bedrijfskritiek-service lagen worden gegevens-en transactie logboek bestanden opgeslagen op het lokale SSD-volume van de computer die als host fungeert voor de data base of elastische pool. Dit biedt hoge IOPS en door Voer en een lage IO-latentie. De grootte van dit lokale volume is afhankelijk van de hardware-mogelijkheden en is eindig. Op een bepaalde computer wordt de lokale volume ruimte gebruikt door klant databases, waaronder `tempdb` het besturings systeem, de beheer software, het bewaken van gegevens, logboeken, enzovoort. Wanneer data bases worden gemaakt, verwijderd en het gebruik van de ruimte wordt verg root of verkleind, schommelt het lokale ruimte verbruik op een computer in de loop van de tijd. 

Als het systeem detecteert dat beschik bare vrije ruimte op een machine laag is en een Data Base of elastische pool het risico loopt dat er onvoldoende ruimte beschikbaar is, wordt de data base of elastische pool verplaatst naar een andere machine met voldoende beschik bare ruimte, waardoor de maximale grootte van de geconfigureerde service doelstelling kan worden overschreden. Deze verplaatsing vindt plaats op een online manier, op dezelfde manier als bij het schalen van de data base en heeft een vergelijk bare [invloed](single-database-scale.md#impact), inclusief een korte (seconden) failover aan het einde van de bewerking. Met deze failover worden open verbindingen beëindigd en worden er op dat moment back-uptransacties met mogelijk invloed op toepassingen die gebruikmaken van de data base.

Omdat gegevens fysiek naar een andere computer worden gekopieerd, kan het verplaatsen van grotere data bases veel tijd in beslag nemen. Als het gebruik van lokale ruimte door een grote gebruikers database of een elastische pool, of door de `tempdb` Data Base zeer snel groeit, neemt het risico op onvoldoende ruimte toe. Het systeem initieert de database verplaatsing op een evenwichtige manier om te voor komen dat er onvoldoende ruimte is en onnodig failovers te voor komen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [dtu's en edtu's](purchasing-models.md#dtu-based-purchasing-model)voor informatie over Dtu's en edtu's.
- Zie [tempdb in Azure SQL database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie over de limieten voor TempDB-grootte.
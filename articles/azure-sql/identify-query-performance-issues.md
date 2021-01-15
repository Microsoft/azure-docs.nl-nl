---
title: Typen problemen met de query prestaties in Azure SQL Database
description: In dit artikel vindt u meer informatie over de typen prestatie problemen met query's in Azure SQL Database en leert u hoe u query's kunt identificeren en oplossen met deze problemen
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217223"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Detecteerbare typen queryprestatieknelpunten in Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Wanneer u probeert een prestatie knelpunt op te lossen, begint u met het bepalen of het probleem zich voordoet wanneer de query wordt uitgevoerd in de actieve staat of in de wacht stand. Er zijn verschillende resoluties van toepassing, afhankelijk van deze bepaling. Gebruik het volgende diagram om inzicht te krijgen in de factoren die kunnen leiden tot een probleem met betrekking tot een actief of een wacht probleem. Problemen en oplossingen met betrekking tot elk type probleem worden beschreven in dit artikel.

U kunt Azure SQL Database [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) of SQL Server [dmv's](database/monitoring-with-dmvs.md) gebruiken om deze soorten prestatie knelpunten te detecteren.

![Status van werk belasting](./media/identify-query-performance-issues/workload-states.png)

**Uitvoerings** problemen: problemen die betrekking hebben op de prestaties zijn in het algemeen gerelateerd aan compilatie problemen die resulteren in een subquery plan of uitvoerings problemen die betrekking hebben op onvoldoende of overgebruikte resources.
**Problemen die te maken hebben met wachten** op het gebied van wachtende problemen:

- Vergren delingen (blok keren)
- I/O
- Conflicten met betrekking tot het gebruik van TempDB
- Wachten op geheugen toekenning

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Compilatie problemen die resulteren in een suboptimaal query plan

Een suboptimaal plan dat door SQL query Optimizer wordt gegenereerd, kan de oorzaak zijn van trage query prestaties. De SQL-query optimalisatie kan een suboptimaal plan produceren vanwege een ontbrekende index, verouderde statistieken, een onjuiste schatting van het aantal te verwerken rijen of een onnauwkeurige schatting van het vereiste geheugen. Als u weet dat de query sneller in het verleden of op een ander exemplaar is uitgevoerd, vergelijkt u de werkelijke uitvoerings plannen om te zien of ze verschillend zijn.

- Identificeer ontbrekende indexen met een van de volgende methoden:

  - Gebruik [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](database/database-advisor-implement-performance-recommendations.md) voor één en gepoolde data bases.
  - Dmv's. In dit voor beeld ziet u de impact van een ontbrekende index, het detecteren van [ontbrekende indexen](database/performance-guidance.md#identifying-and-adding-missing-indexes) met dmv's en de impact van het implementeren van de ontbrekende index aanbeveling.
- Probeer [query hints](/sql/t-sql/queries/hints-transact-sql-query)toe te passen, statistieken bij te [werken](/sql/t-sql/statements/update-statistics-transact-sql)of [indexen opnieuw](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) op te bouwen om het betere plan te krijgen. Schakel [automatische correctie van plannen](../azure-sql/database/automatic-tuning-overview.md) in Azure SQL database in om deze problemen automatisch te verhelpen.

  In dit [voor beeld](database/performance-guidance.md#query-tuning-and-hinting) ziet u de impact van een suboptimaal query plan als gevolg van een query met para meters, hoe u deze voor waarde detecteert en hoe u een query Hint gebruikt om op te lossen.

- Wijzig het database compatibiliteits niveau en implementatie van intelligente query verwerking. De SQL-query Optimizer kan een ander query plan genereren, afhankelijk van het compatibiliteits niveau voor uw data base. Hogere compatibiliteits niveaus bieden [intelligentere query's voor query verwerking](/sql/relational-databases/performance/intelligent-query-processing).

  - Zie voor meer informatie over het verwerken van query's [Query's verwerking architectuur hand leiding](/sql/relational-databases/query-processing-architecture-guide).
  - Zie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)voor informatie over het wijzigen van database compatibiliteits niveaus en lees meer over de verschillen tussen compatibiliteits niveaus.
  - Zie [schatting van kardinaliteit](/sql/relational-databases/performance/cardinality-estimation-sql-server) voor meer informatie over de schatting van de kardinaliteit

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Query's met suboptimale query execution plannen oplossen

In de volgende secties wordt beschreven hoe u query's kunt oplossen met een suboptimaal query-uitvoerings plan.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Query's met parameter gevoelige plan problemen (PSP)

Er treedt een fout op in het parameter gevoelige plan (PSP) wanneer het query optimalisatie programma een query-uitvoerings plan genereert dat optimaal is voor een specifieke parameter waarde (of een set waarden) en het schema in de cache vervolgens niet optimaal is voor parameter waarden die in opeenvolgende uitvoeringen worden gebruikt. Plannen die niet optimaal zijn, kunnen vervolgens problemen met de prestaties van query's veroorzaken en de door Voer van de algehele werk belasting verminderen.

Zie voor meer informatie over para meter-sniffing en query verwerking de [hand leiding](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)voor het verwerken van query's.

Diverse tijdelijke oplossingen kunnen PSP-problemen oplossen. Elke tijdelijke oplossing heeft voor-en nadelen:

- De query Hint [REcompile](/sql/t-sql/queries/hints-transact-sql-query) gebruiken bij elke uitvoering van de query. Deze tijdelijke oplossing verhandelt compilatie tijd en verhoogde CPU voor een betere plan kwaliteit. De `RECOMPILE` optie is vaak niet mogelijk voor werk belastingen die een hoge door Voer vereisen.
- Gebruik de query Hint [(Optimize for...)](/sql/t-sql/queries/hints-transact-sql-query) om de feitelijke parameter waarde te overschrijven met een typische parameter waarde die een plan produceert dat voldoende is voor de meeste mogelijkheden voor parameter waarden. Deze optie vereist een goed idee van de optimale parameter waarden en de bijbehorende plan kenmerken.
- Gebruik de [optie (optimaliseren voor onbekende)](/sql/t-sql/queries/hints-transact-sql-query) query hint om de werkelijke parameter waarde te overschrijven en gebruik in plaats daarvan het gemiddelde van de dichtheids vector te gebruiken. U kunt dit ook doen door de binnenkomende parameter waarden vast te leggen in lokale variabelen en vervolgens de lokale variabelen binnen de predikaten te gebruiken in plaats van de para meters zelf te gebruiken. Voor deze oplossing moet de gemiddelde densiteit *goed genoeg* zijn.
- Schakel para meter-sniffing helemaal uit met behulp van de query Hint [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) .
- Gebruik de [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) -query hint om hercompilaties in de cache te voor komen. Bij deze tijdelijke oplossing wordt ervan uitgegaan dat het goed gang bare plan al in de cache staat. U kunt ook automatische statistieken voor updates uitschakelen om de kans te verkleinen dat het goede plan wordt verwijderd en er wordt een nieuw, beschadigd plan gecompileerd.
- Dwing het plan expliciet met behulp van de query Hint [use plan](/sql/t-sql/queries/hints-transact-sql-query) door de query opnieuw te schrijven en de hint in de query tekst toe te voegen. Of stel een specifiek plan in met behulp van query Store of door [automatisch afstemmen](../azure-sql/database/automatic-tuning-overview.md)in te scha kelen.
- Vervang de ene procedure door een geneste set procedures die elk kan worden gebruikt op basis van voorwaardelijke logica en de bijbehorende parameter waarden.
- Alternatieven voor het uitvoeren van dynamische teken reeksen maken voor een statische procedure definitie.

Voor meer informatie over het oplossen van PSP-problemen raadpleegt u deze blog berichten:

- [Ik geur een para meter](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor versus dynamische SQL versus procedures versus plan kwaliteit voor query's met para meters](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Optimalisatie technieken voor SQL-query's in SQL Server: parameter sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilatie activiteit veroorzaakt door onjuiste parameterisering

Wanneer een query letterlijke waarden bevat, wordt de instructie automatisch door de data base-engine parameterizes of een gebruiker parameterizes de instructie expliciet om het aantal compilaties te verminderen. Een groot aantal compilaties voor een query met hetzelfde patroon, maar met verschillende letterlijke waarden kan leiden tot een hoog CPU-gebruik. Als u een query slechts gedeeltelijk para meters met letterlijke waarden, wordt de query niet meer door de data base-engine para meters.

Hier volgt een voor beeld van een gedeeltelijk Geparametriseerde query:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

In dit voor beeld `t1.c1` neemt `@p1` , maar `t2.c2` blijft GUID als letterlijke waarde. Als u in dit geval de waarde voor wijzigt `c2` , wordt de query behandeld als een andere query en wordt er een nieuwe compilatie uitgevoerd. Als u de compilaties in dit voor beeld wilt reduceren, zou u ook de GUID para meters.

Met de volgende query wordt het aantal query's per query-hash weer gegeven om te bepalen of een query juist is para meters:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Factoren die van invloed zijn op de wijzigingen in het query plan

Een hercompilatie van een query uitvoerings plan kan leiden tot een gegenereerd query plan dat verschilt van het oorspronkelijke schema van de cache. Een bestaand oorspronkelijk plan kan om verschillende redenen automatisch opnieuw worden gecompileerd:

- Naar wijzigingen in het schema wordt verwezen door de query
- Er wordt naar gegevens wijzigingen in de tabellen verwezen door de query
- De query context opties zijn gewijzigd

Een gecompileerd plan kan om verschillende redenen uit de cache worden uitgeworpen, zoals:

- Exemplaar wordt opnieuw opgestart
- Wijzigingen in database bereik configuratie
- Geheugendruk
- Expliciete aanvragen voor het wissen van de cache

Als u een RECOMPILE-Hint gebruikt, wordt er geen plan in de cache opgeslagen.

Een hercompilatie (of nieuwe compilatie na verwijdering van de cache) kan nog steeds leiden tot het genereren van een query-uitvoerings plan dat identiek is aan het origineel. Wanneer het plan wordt gewijzigd ten opzichte van het vorige of oorspronkelijke abonnement, zijn deze toelichtingen waarschijnlijk:

- **Gewijzigd fysiek ontwerp**: nieuwe indexen maken bijvoorbeeld effectiever de vereisten van een query. De nieuwe indexen kunnen worden gebruikt voor een nieuwe compilatie als de query optimalisatie besluit dat het gebruik van die nieuwe index betrouwbaarder is dan het gebruik van de gegevens structuur die oorspronkelijk was geselecteerd voor de eerste versie van de uitvoering van de query. Eventuele fysieke wijzigingen in de objecten waarnaar wordt verwezen, kunnen leiden tot een nieuwe plannings optie tijdens het compileren.

- **Verschillen in Server bronnen**: wanneer een plan in het ene systeem verschilt van het plan in een ander systeem, kan de beschik baarheid van resources, zoals het aantal beschik bare processors, invloed hebben op welk plan wordt gegenereerd. Als een systeem bijvoorbeeld meer processors heeft, kan een parallelle planning worden gekozen.

- **Andere statistieken**: de statistieken die zijn gekoppeld aan de objecten waarnaar wordt verwezen, zijn mogelijk gewijzigd of kunnen afwijken van de statistieken van het oorspronkelijke systeem. Als de statistieken veranderen en een hercompilatie plaatsvindt, gebruikt de query Optimizer de statistieken die beginnen wanneer ze zijn gewijzigd. De gegevens distributies en-frequenties van de herziene statistieken kunnen verschillen van die van de oorspronkelijke compilatie. Deze wijzigingen worden gebruikt voor het maken van kardinaliteit. (*Schattingen van kardinaliteit* zijn het aantal rijen dat naar verwachting door de logische query structuur loopt.) Wijzigingen in de kardinaliteit kunnen ertoe leiden dat u verschillende fysieke Opera tors en gekoppelde orders van bewerkingen kunt kiezen. Zelfs kleine wijzigingen in statistieken kunnen leiden tot een gewijzigd query-uitvoerings plan.

- Het **database compatibiliteits niveau of de Estimator-versie van de kardinaliteit is gewijzigd**: wijzigingen in het database compatibiliteits niveau kunnen nieuwe strategieën en functies mogelijk maken die kunnen leiden tot een ander uitvoerings plan voor query's. Buiten het database compatibiliteits niveau kan een uitgeschakelde of ingeschakelde tracerings vlag 4199 of een gewijzigde status van de database bereik configuratie QUERY_OPTIMIZER_HOTFIXES ook van invloed zijn op het uitvoerings plan van de query tijdens het compileren. Tracerings vlaggen 9481 (geforceerd verouderd CE) en 2312 (standaard-CE forceren) zijn ook van invloed op het plan.

## <a name="resource-limits-issues"></a>Problemen met resource limieten

Trage query prestaties die geen betrekking hebben op suboptimale query plannen en ontbrekende indexen, zijn over het algemeen gerelateerd aan onvoldoende of overgebruikte resources. Als het query plan optimaal is, kunnen de query (en de data base) de resource limieten voor de data base, de elastische pool of het beheerde exemplaar aanwijzen. Een voor beeld hiervan is een overbelasting doorvoer in het logboek voor het service niveau.

- Resource problemen detecteren met behulp van de Azure Portal: Zie voor meer informatie over het oplossen van resource limieten [SQL database resource controle](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Zie [database Advisor aanbevelingen](database/database-advisor-implement-performance-recommendations.md) voor de prestaties en [query prestaties](database/query-performance-insight-use.md)voor meer informatie over afzonderlijke data bases en elastische Pools.
- Bron limieten detecteren met behulp van [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Problemen met bronnen detecteren met [dmv's](database/monitoring-with-dmvs.md):

  - De [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) dmv retourneert CPU, I/O en geheugen verbruik voor de data base. Er bestaat één rij voor elk interval van 15 seconden, zelfs als er geen activiteit in de data base is. Historische gegevens worden één uur bewaard.
  - De [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) dmv retourneert het CPU-gebruik en de opslag gegevens voor Azure SQL database. De gegevens worden verzameld en samengevoegd in intervallen van vijf minuten.
  - [Veel afzonderlijke query's die cumulatief hoge CPU verbruiken](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Als u het probleem identificeert als onvoldoende resource, kunt u resources upgraden om de capaciteit van uw data base te verg Roten om de CPU-vereisten te absorberen. Zie voor meer informatie bronnen voor het [schalen van één data base in Azure SQL database](database/single-database-scale.md) en het [schalen van elastische pool resources in Azure SQL database](database/elastic-pool-scale.md). Zie [resource limieten voor service lagen](managed-instance/resource-limits.md#service-tier-characteristics) voor meer informatie over het schalen van een beheerd exemplaar

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestatie problemen als gevolg van een toegenomen hoeveelheid werk belasting

Een toename van het toepassings verkeer en het werkbelasting volume kan een groter CPU-gebruik veroorzaken. Maar u moet er wel voor zorgen dat u dit probleem correct kunt vaststellen. Wanneer u een probleem met een hoge CPU ziet, beantwoordt u deze vragen om te bepalen of de toename wordt veroorzaakt door wijzigingen in het werkbelasting volume:

- Zijn de query's van de toepassing de oorzaak van het probleem met de hoge CPU?
- Voor de [Top van CPU-verbruiks query's die u kunt herkennen](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Zijn er meerdere uitvoerings plannen gekoppeld aan dezelfde query? Als dat het geval is, waarom?
  - Voor query's met hetzelfde uitvoerings plan zijn de uitvoerings tijden consistent? Is het aantal uitvoeringen verhoogd? Als dit het geval is, worden er waarschijnlijk problemen met de werk belasting veroorzaakt.

Als het query-uitvoerings plan niet anders is uitgevoerd, maar het CPU-gebruik is verhoogd met het aantal uitvoeringen, is het prestatie probleem waarschijnlijk gerelateerd aan een verhoging van de werk belasting.

Het is niet altijd gemakkelijk om een wijziging van het werkbelasting volume te identificeren dat een CPU-probleem ondervindt. Houd rekening met de volgende factoren:

- **Resource gebruik gewijzigd**: u kunt bijvoorbeeld een scenario gebruiken waarbij het CPU-gebruik gedurende een lange periode is verhoogd tot 80 procent. Het CPU-gebruik betekent alleen dat het werkbelasting volume is gewijzigd. Regressies in het query-uitvoerings plan en wijzigingen in de gegevens distributie kunnen ook bijdragen aan meer resource gebruik, zelfs als de toepassing dezelfde werk belasting uitvoert.

- **Het uiterlijk van een nieuwe query**: een toepassing kan op verschillende tijdstippen een nieuwe set query's aansturen.

- **Een verhoging of afname van het aantal aanvragen**: dit scenario is de meest duidelijke maat regel voor een werk belasting. Het aantal query's komt niet altijd overeen met meer resource gebruik. Deze metriek is echter nog steeds een significant signaal, ervan uitgaande dat andere factoren ongewijzigd blijven.

Gebruik Intelligent Insights om de [werk belasting te verhogen](database/intelligent-insights-troubleshoot-performance.md#workload-increase) en [regressies te plannen](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemen die te maken hebben met wachten

Wanneer u een suboptimaal plan hebt geëlimineerd en te *wachten* op problemen die betrekking hebben op uitvoerings problemen, is het prestatie probleem in het algemeen de query's die waarschijnlijk wachten op een bepaalde resource. Er kunnen problemen met de wacht worden veroorzaakt door:

- **Blok keren**:

  Een query kan de vergren deling van objecten in de data base blok keren, terwijl andere gebruikers toegang proberen te krijgen tot dezelfde objecten. U kunt blokkerende query's identificeren met behulp van [dmv's](database/monitoring-with-dmvs.md#monitoring-blocked-queries) of [intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking). Zie [problemen met Azure SQL-blok kades begrijpen en oplossen](database/understand-resolve-blocking.md)voor meer informatie.
- **IO-problemen**

  Query's kunnen wachten tot de pagina's naar de gegevens of logboek bestanden zijn geschreven. In dit geval controleert u de `INSTANCE_LOG_RATE_GOVERNOR` , `WRITE_LOG` of wacht op de `PAGEIOLATCH_*` statistieken in de DMV. Zie Dmv's gebruiken om [io-prestatie problemen te identificeren](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemen met TempDB**

  Als de werk belasting tijdelijke tabellen gebruikt of als er sprake is van TempDB in de plannen, hebben de query's mogelijk een probleem met TempDB-door voer. Zie Dmv's gebruiken voor de [identiteit van tempdb-problemen](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Geheugen-gerelateerde problemen**

  Als de werk belasting onvoldoende geheugen heeft, kan het verwachting van de pagina verloren gaan of kunnen de query's minder geheugen krijgen dan nodig is. In sommige gevallen worden met ingebouwde intelligentie in query Optimizer problemen met het geheugen opgelost. Zie Dmv's gebruiken om [problemen met geheugen toekenning te identificeren](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Methoden voor het weer geven van categorieën met de hoogste wacht tijd

Deze methoden worden vaak gebruikt om de hoogste categorieën van wacht typen weer te geven:

- Intelligent Insights gebruiken om query's te identificeren die de prestaties verslechteren als gevolg van [verhoogde wacht tijden](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Gebruik [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) om gedurende een bepaalde periode wacht statistieken voor elke query te vinden. In query Store worden wachtende typen gecombineerd in wachtende categorieën. U kunt de toewijzing van wachtende Categorieën vinden door te wachten op typen in [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Gebruik [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) om informatie te retour neren over alle wacht tijden die worden aangetroffen door threads die worden uitgevoerd tijdens een query bewerking. U kunt deze geaggregeerde weer gave gebruiken om prestatie problemen vast te stellen met Azure SQL Database en ook met specifieke query's en batches. Query's kunnen wachten op resources, wachten op wacht rijen of externe wacht tijden.
- Gebruik [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) om informatie te retour neren over de wachtrij met taken die op een bepaalde resource wachten.

In scenario's met een hoge CPU worden in de query Store-en wait-statistieken mogelijk geen CPU-gebruik weer gegeven als:

- Query's met een hoog CPU-verbruik worden nog steeds uitgevoerd.
- De query's met een hoog CPU-verbruik worden uitgevoerd tijdens een failover.

Dmv's die query Store bijhouden en wachten op statistieken tonen alleen resultaten voor voltooide en time-out query's. Er worden geen gegevens weer gegeven voor de instructies die momenteel worden uitgevoerd tot de instructies zijn voltooid. Gebruik de weer gave dynamisch beheer [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) voor het bijhouden van query's die momenteel worden uitgevoerd en de bijbehorende werk tijd.

> [!TIP]
> Aanvullende hulpprogram ma's:
>
> - [TigerToolbox-wacht tijden en latches](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van SQL Database bewaking en afstemming](database/monitor-tune-overview.md)
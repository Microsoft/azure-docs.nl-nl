---
title: Aanbevolen procedures voor een toegewezen SQL-groep (voorheen SQL DW)
description: Aanbevelingen en aanbevolen procedures voor het ontwikkelen van oplossingen voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d62ea594916dbf5656569b7e1a3afe9ee81d8f89
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685290"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Aanbevolen procedures voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics

Dit artikel bevat een verzameling aanbevolen procedures om u te helpen optimaal te profiteren van uw [specifieke SQL-groep (voorheen SQL DW)-](sql-data-warehouse-overview-what-is.md) implementatie.  Het doel van dit artikel is om u een aantal basis richtlijnen te geven en belang rijke gebieden van focus te markeren.  

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over kostenverlaging via onderbreking en schaling.

## <a name="maintain-statistics"></a>Statistieken bijhouden

Een toegewezen SQL-groep (voorheen SQL DW) kan zo worden geconfigureerd dat er automatisch statistieken worden gedetecteerd en gemaakt voor kolommen.  De query plannen die door de Optimizer zijn gemaakt, zijn alleen net zo goed als de beschik bare statistieken.  

We raden u aan AUTO_CREATE_STATISTICS voor uw data bases in te scha kelen en de statistieken dagelijks of na elke belasting te laten bijwerken om ervoor te zorgen dat statistieken voor kolommen die worden gebruikt in uw query's altijd up-to-date zijn.

Als u vindt dat het te lang duurt om al uw statistieken bij te werken, wilt u wellicht beter selecteren welke kolommen regel matig statistieken moeten bijwerken. Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken.

> [!TIP]
> U profiteert het meest voor deel door bijgewerkte statistieken te hebben over kolommen die deel uitmaken van de samen voegingen, kolommen die worden gebruikt in de component WHERE en kolommen in GROUP BY.

Zie ook [tabel statistieken beheren](sql-data-warehouse-tables-statistics.md), [Statistieken maken](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Gebruik DMV’s om uw query’s te controleren en te optimaliseren

Een toegewezen SQL-groep (voorheen SQL DW) heeft verschillende Dmv's die kunnen worden gebruikt voor het bewaken van de uitvoering van query's.  U kunt de [werk belasting controleren met dmv's](sql-data-warehouse-manage-monitor.md) -artikel Details stapsgewijze instructies voor het bekijken van de details van een query die wordt uitgevoerd.  

Als u snel query’s wilt vinden in de DMV's, kunt u proberen de optie LABEL te gebruiken bij uw query’s.

Zie ook [uw workload controleren met dmv's](sql-data-warehouse-manage-monitor.md), [Label](sql-data-warehouse-develop-label.md), [Option](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>De query prestaties afstemmen met nieuwe product uitbreidingen

- [Prestaties afstemmen met gerealiseerde weergaven](performance-tuning-materialized-views.md)
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](performance-tuning-ordered-cci.md)
- [Prestaties afstemmen door resultatensets op te slaan in de cache](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>INSERT-instructie in batches groeperen

Een eenmalige belasting naar een kleine tabel met een INSERT-instructie of zelfs een periodiek opnieuw laden van een zoek opdracht kan goed worden uitgevoerd voor uw behoeften, met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

Als u echter gedurende de dag duizenden of zelfs miljoenen rijen moet laden, voldoen afzonderlijke INSERT-instructies mogelijk niet meer.  U kunt uw processen ook zo instellen dat ze naar een bestand schrijven, en een ander proces instellen dat het bestand periodiek laadt.

Zie ook [Invoegen](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase gebruiken om snel gegevens te laden en te exporteren

Een toegewezen SQL-groep (voorheen SQL DW) ondersteunt het laden en exporteren van gegevens via verschillende hulpprogram ma's, waaronder Azure Data Factory, poly base en BCP.  Voor kleine hoeveelheden gegevens waarbij prestaties niet belangrijk zijn, kunnen al deze toepassingen aan uw vereisten voldoen.  Wanneer u echter grote volumes aan gegevens laadt of exporteert of snelle prestaties vereist zijn, is PolyBase de beste keuze.  

Poly Base is ontworpen om gebruik te maken van gedistribueerde aard van het systeem en zorgt ervoor dat gegevens sneller worden geladen en geëxporteerd dan andere hulp middelen.  PolyBase-loads kunnen worden uitgevoerd met behulp van CTAS of INSERT INTO.   

> [!TIP]
> Door CTAS te gebruiken, wordt de transactieregistratie geminimaliseerd, waardoor het de snelste manier is om uw gegevens te laden.

Azure Data Factory biedt ook ondersteuning voor poly base-belastingen en kan soort gelijke prestaties opleveren als CTAS.  PolyBase ondersteunt verschillende bestandsindelingen, waaronder GZip.  
  
> [!NOTE]
> Als u de door voer wilt maximaliseren bij het gebruik van gzip-tekst bestanden, moet u bestanden opdelen in 60 of meer bestanden om de parallelle kracht van uw belasting te maximaliseren.  Voor een snellere totale doorvoer, kunt u overwegen gegevens gelijktijdig te laden.

Zie ook [gegevens laden](design-elt-data-loading.md), [gids voor gebruik van poly base](guidance-for-loading-data.md), [toegewezen SQL-groep voor het laden van patronen en strategieën, het](/archive/blogs/sqlcat/) [laden van gegevens met Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), het [verplaatsen van gegevens met Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), het maken van een [externe BESTANDS indeling](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en het maken van een [tabel als Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Externe tabellen laden en vervolgens query’s uitvoeren

PolyBase, ook wel bekend als externe tabellen, kan de snelste manier zijn om gegevens te laden, maar het is niet optimaal voor query’s. Poly base-tabellen ondersteunen momenteel alleen Azure Blob-bestanden en Azure Data Lake opslag. Deze bestanden worden niet door rekenresources ondersteunt.  

Als gevolg hiervan kan een toegewezen SQL-groep dit werk niet verhelpen en daarom moet het hele bestand lezen door het naar TempDB te laden om de gegevens te kunnen lezen.  Indien u meerdere query’s uitvoert voor deze gegevens, kunt u beter de gegevens eenmalig laden en query’s op de lokale tabel toepassen.

Zie ook [hand leiding voor het gebruik van poly base](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash

Tabellen worden standaard gedistribueerd middels Round Robin.  Dit maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven bepalen hoe de tabellen moeten worden gedistribueerd.  Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  

Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  

Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  

> [!TIP]
> Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  

Raadpleeg de volgende koppelingen voor meer informatie over het selecteren van een distributie kolom kan de prestaties verbeteren en het definiëren van een gedistribueerde tabel in de WITH-component van uw CREATE TABLE-instructie.

Zie ook [tabel Overzicht](sql-data-warehouse-tables-overview.md), [tabel distributie](sql-data-warehouse-tables-distribute.md), [selecteren van tabel distributie](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Create Table als selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="do-not-over-partition"></a>Niet te veel partities maken

Hoewel het partitioneren van gegevens effectief kan zijn voor het onderhouden van uw gegevens via partitie wisseling of het optimaliseren van scans met behulp van partitie-eliminatie, waardoor er te veel partities zijn, kunnen de query's worden vertraagd.  Vaak is een strategie voor een zeer nauw keurige partitionering, die mogelijk goed werkt op SQL Server mogelijk niet goed werken in een toegewezen SQL-groep (voorheen SQL DW).  

Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  Houd er achter de schermen rekening mee dat exclusieve SQL pool uw gegevens naar 60-data bases partitioneert, dus als u een tabel met 100-partities maakt, resulteert dit in werkelijkheid in 6000 partities.  

Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  Overweeg een lagere granulatie dan die in SQL Server misschien wel effectief was.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [tabel partities](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren

De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  

Als u bijvoorbeeld een INVOEG actie hebt die u verwacht één uur te maken, moet u, indien mogelijk, de invoeging in vier delen opsplitsen, die elk in 15 minuten wordt uitgevoerd.  Maak gebruik van speciale minimale logboek registratie cases, zoals CTAS, AFKAP ping, DROP TABLE of INSERT to Empty Tables, om het terugdraai risico te verminderen.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel te verwijderen waarin de order_date zich in oktober 2001 bevonden, kunt u uw gegevens maandelijks partitioneren en vervolgens de partitie uitzetten met gegevens voor een lege partitie uit een andere tabel (Zie voor beelden van [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ).  

Voor niet-gepartitioneerde tabellen kunt u een CTAS gebruiken voor het schrijven van de gegevens die u in een tabel wilt houden, in plaats van verwijderen te gebruiken.  Als een CTAS dezelfde hoeveelheid tijd in beslag neemt, is het een veel veiliger bewerking om uit te voeren omdat het een minimale transactie logboek registratie heeft en zo nodig snel kan worden geannuleerd.

Zie ook [informatie over trans acties](sql-data-warehouse-develop-transactions.md), [optimalisatie van trans acties](sql-data-warehouse-develop-best-practices-transactions.md), [tabel partities](sql-data-warehouse-tables-partition.md), [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en [create table as select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>De grootte van het query resultaat verminderen

Deze stap helpt u bij het voor komen van problemen aan de client die worden veroorzaakt door grote query resultaten.  U kunt uw query bewerken om het aantal geretourneerde rijen te verminderen. Met de hulpprogram ma's voor het genereren van query's kunt u de syntaxis ' top N ' toevoegen aan elke query.  U kunt het query resultaat ook CETAS naar een tijdelijke tabel en vervolgens poly base exporteren gebruiken voor de Down Level verwerking.

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken

Bij het definiëren van uw DDL kunt u met het kleinste gegevens type dat uw gegevens ondersteunt, de query prestaties verbeteren.  Deze benadering is vooral belang rijk voor CHAR-en VARCHAR-kolommen.  

Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [tabel Overzicht](sql-data-warehouse-tables-overview.md), [tabel gegevens typen](sql-data-warehouse-tables-data-types.md), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Tijdelijke heap-tabellen gebruiken voor tijdelijke gegevens

Wanneer u tijdelijk gegevens overbrengt, wordt het algehele proces sneller gemaakt met behulp van een heap-tabel.  Als u alleen gegevens laadt om ze klaar te zetten voordat u meer transformaties uitvoert, kan het laden van de tabel naar een heap-tabel veel sneller zijn dan het downloaden van de gegevens naar een geclusterde columnstore-tabel.  

Daarnaast is het laden van gegevens naar een tijdelijke tabel ook veel sneller dan het laden van een tabel naar permanente opslag.  Tijdelijke tabellen beginnen met een ' # ' en zijn alleen toegankelijk voor de sessie waarmee deze is gemaakt, zodat ze alleen in beperkte scenario's kunnen werken.

Heap-tabellen worden gedefinieerd in het WITH-component van een CREATE TABLE-instructie.  Als u wel een tijdelijke tabel gebruikt, vergeet dan niet ook statistieken voor de tijdelijke tabel te maken.

Zie ook [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Create Table als selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren

Geclusterde column Store-indexen zijn een van de meest efficiënte manieren om uw gegevens op te slaan in een toegewezen SQL-groep.  Tabellen in een toegewezen SQL-groep worden standaard gemaakt als geclusterde column Store.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  

Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie de [oorzaken van slechte kwaliteit van Column store-index](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) in het artikel [tabel indexen](sql-data-warehouse-tables-index.md) voor stapsgewijze instructies voor het detecteren en verbeteren van segment kwaliteit voor geclusterde column Store-tabellen.  

Omdat column Store-segmenten van hoge kwaliteit belang rijk zijn, is het een goed idee om gebruikers-Id's te gebruiken die zich in de middel lange of grote resource klasse bevinden voor het laden van gegevens. Als u lagere [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) gebruikt, wilt u een grotere resource klasse toewijzen aan de gebruiker die u wilt laden.

Aangezien column Store-tabellen in het algemeen geen gegevens naar een gecomprimeerd column Store-segment pushen totdat er meer dan 1.000.000 rijen per tabel zijn en elke toegewezen SQL-groeps tabel is gepartitioneerd in 60-tabellen, is voor column Store-tabellen geen query nodig tenzij de tabel meer dan 60.000.000 rijen heeft.  Voor tabellen met minder dan 60 miljoen rijen is het meestal niet nodig om een columnstore-index te hebben.  Het is misschien ook niet verkeerd.  

Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, moet deze ten minste 6.000.000.000 rijen hebben om te profiteren van een geclusterde column Store (60 distributies *100 partities* 1.000.000 rijen).  

Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

> [!TIP]
> Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabel-indexen](sql-data-warehouse-tables-index.md), [Gids columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Columnstore-indexen herbouwen](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Een grotere resourceklasse gebruiken om queryprestaties te verbeteren

De toegewezen SQL-groep gebruikt resource groepen als manier om geheugen aan query's toe te wijzen.  Alle gebruikers worden uit het vak toegewezen aan de kleine resource klasse, die 100 MB aan geheugen per distributie toekent.  Omdat er altijd 60 distributies zijn en aan elke distributie een minimum van 100 MB is toegekend, is de totale geheugentoewijzing systeembreed 6000 MB, of net onder 6 GB.  

Bepaalde query’s, zoals grote samenvoegingen of loads naar geclusterde columnstore-tabellen, profiteren van grotere geheugentoewijzingen.  Sommige query's, zoals zuivere scans, kunnen geen voor deel opleveren.  Het gebruik van grotere resource klassen vermindert echter de gelijktijdigheid, dus u kunt dit in overweging nemen voordat u al uw gebruikers naar een grote resource klasse verplaatst.

Zie ook [resource klassen voor workload Management](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Een kleinere resourceklasse gebruiken om de gelijktijdigheid te verbeteren

Als u ziet dat gebruikers een lange vertraging hebben, kan het zijn dat uw gebruikers in grotere bron klassen worden uitgevoerd en veel gelijktijdigheids sleuven gebruiken waardoor andere query's in de wachtrij worden geplaatst.  Om te zien of er gebruikersquery’s in de wachtrij staan, voert u `SELECT * FROM sys.dm_pdw_waits` uit en kijkt u of er rijen worden geretourneerd.

Zie ook [resource klassen voor workload management](resource-classes-for-workload-management.md) [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="other-resources"></a>Meer informatie

Zie ook ons artikel [Problemen oplossen](sql-data-warehouse-troubleshoot.md) voor veelvoorkomende problemen en oplossingen.

Als u in dit artikel niet hebt gevonden wat u zoekt, kunt u het document ' zoeken naar docs ' aan de linkerkant van deze pagina gebruiken om alle Azure Synapse-documenten te doorzoeken.  De [pagina van micro soft Q&een vraag voor Azure Synapse](/answers/topics/azure-synapse-analytics.html) is een plek waar u vragen kunt stellen aan andere gebruikers en aan de Azure Synapse-Product groep. We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  

Als u liever vragen hebt over Stack Overflow, hebben we ook een [Azure Synapse stack overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Gebruik de feedback pagina van [Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) om functie aanvragen te maken.  Het toevoegen van aanvragen of het stemmen op andere aanvragen helpt ons prioriteit te geven aan bepaalde functies.
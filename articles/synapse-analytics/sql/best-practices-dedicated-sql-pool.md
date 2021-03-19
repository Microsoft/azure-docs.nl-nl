---
title: Aanbevolen procedures voor toegewezen SQL-groepen
description: Aanbevelingen en aanbevolen procedures die u moet weten tijdens het werken met toegewezen SQL-groepen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 03/17/2021
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b11a76be94fc52285482e13dadbc8c7c92af1374
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609349"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Aanbevolen procedures voor toegewezen SQL-groepen in azure Synapse Analytics

In dit artikel vindt u een aantal aanbevolen procedures om u te helpen bij het bezorgen van optimale prestaties voor specifieke SQL-groepen in azure Synapse Analytics. Hieronder vindt u basis richtlijnen en belang rijke gebieden waarop u zich kunt richten tijdens het bouwen van uw oplossing. In elke sectie maakt u kennis met een concept en verwijst u naar meer gedetailleerde artikelen over het concept.

## <a name="dedicated-sql-pools-loading"></a>Gereserveerde SQL-groepen laden

Raadpleeg de [richt lijnen voor het laden van gegevens](data-loading-best-practices.md)voor toegewezen SQL-groepen.

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie [Compute beheren](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over het verminderen van de kosten door te onderbreken en schalen.

## <a name="maintain-statistics"></a>Statistieken bijhouden

Exclusieve SQL-groep kan zo worden geconfigureerd dat er automatisch statistieken worden gedetecteerd en gemaakt voor kolommen.  De query plannen die door de Optimizer zijn gemaakt, zijn alleen net zo goed als de beschik bare statistieken.  

We raden u aan AUTO_CREATE_STATISTICS voor uw data bases in te scha kelen en de statistieken dagelijks of na elke belasting te laten bijwerken om ervoor te zorgen dat statistieken voor kolommen die worden gebruikt in uw query's altijd up-to-date zijn.

Als u de onderhouds tijd voor de statistieken wilt verkorten, moet u selectief weten welke kolommen statistieken hebben of het meest frequent bijwerken. Stel dat u datum kolommen wilt bijwerken waar nieuwe waarden dagelijks kunnen worden toegevoegd. Focus op het hebben van statistieken voor kolommen die deel uitmaken van samen voegingen, kolommen die worden gebruikt in de WHERE-component en kolommen in GROUP BY.

Meer informatie over statistieken vindt u in de artikelen [tabel statistieken beheren](develop-tables-statistics.md), [Statistieken maken](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)en [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

## <a name="tune-query-performance-with-new-product-enhancements"></a>De query prestaties afstemmen met nieuwe product uitbreidingen

- [Prestaties afstemmen met gerealiseerde weergaven](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [Prestaties afstemmen door resultatensets op te slaan in de cache](../sql-data-warehouse/performance-tuning-result-set-caching.md)


## <a name="group-insert-statements-into-batches"></a>INSERT-instructie in batches groeperen

Een eenmalige belasting naar een kleine tabel met een INSERT-instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')` mogelijk de beste benadering is afhankelijk van uw behoeften. Als u echter duizenden of miljoenen rijen per dag moet laden, is het waarschijnlijk dat Singleton-INVOEG bladen niet optimaal zijn.

Een manier om dit probleem op te lossen is het ontwikkelen van een proces dat naar een bestand schrijft en vervolgens een ander proces om dit bestand periodiek te laden. Raadpleeg het artikel [Insert](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) voor meer informatie.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase gebruiken om snel gegevens te laden en te exporteren

De toegewezen SQL-groep ondersteunt het laden en exporteren van gegevens via verschillende hulpprogram ma's, waaronder Azure Data Factory, poly base en BCP.  Voor kleine hoeveelheden gegevens waarbij prestaties niet belangrijk zijn, kunnen al deze toepassingen aan uw vereisten voldoen.  

> [!NOTE]
> Poly Base is de beste keuze wanneer u grote hoeveel heden gegevens laadt of exporteert, of als u snellere prestaties nodig hebt.

PolyBase-loads kunnen worden uitgevoerd met behulp van CTAS of INSERT INTO. CTAS vermindert de transactie logboek registratie en is de snelste manier om uw gegevens te laden. Azure Data Factory biedt ook ondersteuning voor poly base-belastingen en kan de prestaties verzorgen die vergelijkbaar zijn met CTAS. Poly Base biedt ondersteuning voor verschillende bestands indelingen, waaronder gzip-bestanden.

Als u de door voer wilt maximaliseren bij het gebruik van gzip-tekst bestanden, moet u bestanden opdelen in 60 of meer bestanden om de parallelle kracht van uw belasting te maximaliseren. Voor een snellere totale doorvoer, kunt u overwegen gegevens gelijktijdig te laden. Meer informatie over de onderwerpen die relevant zijn voor deze sectie is opgenomen in de volgende artikelen:

- [Gegevens laden](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Gids voor gebruik van PolyBase](data-loading-best-practices.md)
- [Gereserveerde patronen en strategieën voor het laden van een SQL-groep](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Gegevens laden met Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Gegevens verplaatsen met Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Externe tabellen laden en vervolgens query’s uitvoeren

Poly Base is niet optimaal voor query's. Poly base-tabellen voor toegewezen SQL-groepen ondersteunen momenteel alleen Azure Blob-bestanden en Azure Data Lake opslag. Er zijn geen reken resources die deze bestanden kunnen herstellen. Als gevolg hiervan kunnen toegewezen SQL-groepen dit werk niet offloaden en het hele bestand lezen door het te laden in TempDB zodat de gegevens kunnen worden gelezen.

Als u verschillende query's hebt voor het uitvoeren van query's op deze gegevens, is het beter om deze gegevens één keer te laden en query's te laten gebruiken voor de lokale tabel. Meer informatie over poly Base is opgenomen in de  [hand leiding voor het gebruik van poly base](data-loading-best-practices.md) -artikel.

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash

Tabellen worden standaard gedistribueerd middels Round Robin.   Met deze standaard instelling kunnen gebruikers eenvoudig tabellen gaan maken zonder te hoeven bepalen hoe hun tabellen moeten worden gedistribueerd. Round Robin-tabellen kunnen voor sommige werk belastingen voldoende worden uitgevoerd. Maar in de meeste gevallen levert een distributie kolom betere prestaties.  

Het meest voorkomende voor beeld van een tabel die wordt gedistribueerd door een kolom die een Round Robin-tabel uitvoert, is wanneer twee grote feiten tabellen zijn gekoppeld.  

Als u bijvoorbeeld een tabel Orders hebt die wordt gedistribueerd door order_id, en een transactie tabel die ook wordt gedistribueerd door order_id, wanneer u uw orders tabel toevoegt aan de transactie tabel op order_id, wordt deze query een Pass Through-query. Gegevens verplaatsings bewerkingen worden vervolgens geëlimineerd. Minder stappen betekent een snellere query. Minder gegevensverplaatsing maakt query’s ook sneller.

> [!TIP]
> Wanneer u een gedistribueerde tabel laadt, moeten uw inkomende gegevens niet worden gesorteerd op de distributie sleutel. Als u dit doet, worden uw belasting vertraagd.

De onderstaande koppelingen vindt u meer informatie over het verbeteren van de prestaties via het selecteren van een distributie kolom. Daarnaast vindt u hier informatie over het definiëren van een gedistribueerde tabel in de WITH-component van uw CREATE TABLE-instructie:

- [Tabel overzicht](develop-tables-overview.md)
- [Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabeldistributie selecteren](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Niet te veel partities maken

Hoewel het partitioneren van gegevens effectief kan zijn voor het onderhouden van uw gegevens via partitie wisseling of het optimaliseren van scans met behulp van partitie-eliminatie, waardoor er te veel partities zijn, kunnen de query's worden vertraagd.  Vaak is het mogelijk dat een hoge granulatie strategie die goed werkt op SQL Server wellicht niet goed werkt op een toegewezen SQL-groep.  

Het gebruik van te veel partities kan de effectiviteit van geclusterde column Store-indexen verminderen als elke partitie minder dan 1.000.000 rijen heeft. met toegewezen SQL-groepen worden uw gegevens automatisch gepartitioneerd in 60-data bases. Als u dus een tabel met 100-partities maakt, is het resultaat 6000 partities. Elke werk belasting verschilt, dus het beste is om te experimenteren met partitioneren om te zien wat het beste werkt voor uw werk belasting.  

U kunt het beste een granulatie gebruiken die lager is dan wat u met SQL Server hebt geïmplementeerd. U kunt bijvoorbeeld wekelijkse of maandelijkse partities gebruiken in plaats van dagelijks partities.

Meer informatie over partitionering vindt u in het [tabel Artikel partitioneren](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren

INSERT-, UPDATE-en DELETE-instructies worden uitgevoerd in een trans actie. Als ze mislukken, moeten ze worden teruggedraaid. Als u de kans op een lange terugdraai actie wilt reduceren, minimaliseert u waar mogelijk de transactie grootten.  Het minimaliseren van transactie grootten kan worden uitgevoerd door INSERT-, UPDATE-en DELETE-instructies samen te delen in delen. Als u bijvoorbeeld een INVOEG actie hebt die u verwacht één uur te maken, kunt u het invoegen in vier delen opsplitsen. Elke uitvoering wordt vervolgens verkort tot 15 minuten.  

> [!TIP]
> Maak gebruik van speciale minimale logboek registratie cases, zoals CTAS, AFKAP ping, DROP TABLE of INSERT to Empty Tables om het terugdraai risico te verminderen.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren voor het verwijderen van alle rijen in een tabel waarin de order_date zich in oktober 2001 bevonden, kunt u uw gegevens maandelijks partitioneren. Vervolgens kunt u de partitie met gegevens voor een lege partitie uit een andere tabel deactiveren (Zie ALTER TABLE-voor beelden).  

Voor niet-gepartitioneerde tabellen kunt u een CTAS gebruiken voor het schrijven van de gegevens die u in een tabel wilt houden, in plaats van verwijderen te gebruiken.  Als een CTAS even lang duurt, is het veel veiliger om uit te voeren omdat het een minimale transactie logboek registratie heeft en zo nodig snel kan worden geannuleerd.

Meer informatie over de inhoud die betrekking heeft op deze sectie is opgenomen in de volgende artikelen:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Inzicht krijgen in transacties](develop-transactions.md)
- [Transacties optimaliseren](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabel partities](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>De grootte van het query resultaat verminderen

Het verminderen van de grootte van query resultaten helpt u bij het voor komen van problemen aan de client zijde die worden veroorzaakt door grote query resultaten.  U kunt uw query bewerken om het aantal geretourneerde rijen te verminderen. Met de hulpprogram ma's voor het genereren van query's kunt u de syntaxis ' top N ' toevoegen aan elke query.  U kunt het query resultaat ook CETAS naar een tijdelijke tabel en vervolgens poly base exporteren gebruiken voor de Down Level verwerking.

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken

Gebruik bij het definiëren van uw DDL het kleinste gegevens type dat uw gegevens ondersteunt, waardoor de query prestaties worden verbeterd.  Deze aanbeveling is vooral belang rijk voor CHAR-en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer bovendien kolommen als VARCHAR wanneer dat nodig is, in plaats van NVARCHAR te gebruiken.

Raadpleeg het [tabel Overzicht](develop-tables-overview.md), de [tabel gegevens typen](develop-tables-data-types.md)en [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) artikelen voor een uitgebreidere beoordeling van essentiële concepten die relevant zijn voor de bovenstaande informatie.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Tijdelijke heap-tabellen gebruiken voor tijdelijke gegevens

Wanneer u tijdelijk gegevens op toegewezen SQL-groepen overbrengt, maken heap-tabellen het algehele proces doorgaans sneller.  Als u alleen gegevens laadt om deze te stageen voordat u meer trans formaties uitvoert, is het laden van de tabel naar een heap-tabel sneller dan het laden van de gegevens naar een geclusterde column Store-tabel.  

Het laden van gegevens naar een tijdelijke tabel wordt ook veel sneller geladen dan het laden van een tabel naar permanente opslag.  Tijdelijke tabellen beginnen met een ' # ' en zijn alleen toegankelijk voor de sessie die de tabel heeft gemaakt. Daarom kunnen ze alleen worden gebruikt in beperkte scenario's. Heap-tabellen worden gedefinieerd in het WITH-component van een CREATE TABLE-instructie.  Als u wel een tijdelijke tabel gebruikt, vergeet dan niet ook statistieken voor de tijdelijke tabel te maken.

Raadpleeg de [tijdelijke tabellen](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true), en [Create Table als artikelen selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) voor meer informatie.

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren

Geclusterde column Store-indexen zijn een van de meest efficiënte manieren om uw gegevens op te slaan in een toegewezen SQL-groep.  Tabellen in een toegewezen SQL-groep worden standaard gemaakt als geclusterde column Store.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  

De segment kwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde Rijg groep. Zie de [oorzaken van slechte kwaliteit van Column store-index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) in het artikel [tabel indexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stapsgewijze instructies voor het detecteren en verbeteren van segment kwaliteit voor geclusterde column Store-tabellen.  

Omdat column Store-segmenten van hoge kwaliteit belang rijk zijn, is het een goed idee om gebruikers-Id's te gebruiken die zich in de middel lange of grote resource klasse bevinden voor het laden van gegevens. Als u lagere [Data Warehouse-eenheden](resource-consumption-models.md) gebruikt, wilt u een grotere resource klasse toewijzen aan de gebruiker die u wilt laden.

Column Store-tabellen pushen doorgaans geen gegevens naar een gecomprimeerd column Store-segment totdat er meer dan 1.000.000 rijen per tabel zijn. Elke toegewezen SQL-groeps tabel is gepartitioneerd in 60 tabellen. Daarom profiteren column Store-tabellen niet voor een query tenzij de tabel meer dan 60.000.000 rijen heeft.  

> [!TIP]
> Voor tabellen met minder dan 60.000.000 rijen is het mogelijk dat een column store-index niet de optimale oplossing is.  

Als u uw gegevens partitioneert, moet elke partitie 1.000.000 rijen hebben om te profiteren van een geclusterde column store-index.  Voor een tabel met 100-partities moet er ten minste 6.000.000.000 rijen zijn om te profiteren van een geclusterde column Store (60 distributies *100 partities* 1.000.000 rijen).  

Als uw tabel niet 6.000.000.000 rijen bevat, hebt u twee belang rijke opties. Verminder het aantal partities of overweeg in plaats daarvan een heap-tabel te gebruiken.  Het is ook mogelijk dat u experimenteert om te zien of er betere prestaties kunnen worden verkregen door gebruik te maken van een heap-tabel met secundaire indexen in plaats van een column Store-tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  Meer informatie over tabel-en column Store-indexen vindt u in de volgende artikelen:
- [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Hand leiding voor column Store-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [Columnstore-indexen herbouwen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) 
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Een grotere resourceklasse gebruiken om queryprestaties te verbeteren

SQL-Pools gebruiken resource groepen als manier om geheugen aan query's toe te wijzen. In eerste instantie worden alle gebruikers toegewezen aan de kleine resource klasse, die 100 MB aan geheugen per distributie toekent.  Er zijn altijd 60 distributies. Elke distributie krijgt een minimum van 100 MB. De totale geheugen toewijzing voor het hele systeem is 6.000 MB of minder dan 6 GB.  

Bepaalde query’s, zoals grote samenvoegingen of loads naar geclusterde columnstore-tabellen, profiteren van grotere geheugentoewijzingen.  Sommige query's, zoals zuivere scans, zien geen voor deel. Het gebruik van grotere bron klassen heeft invloed op gelijktijdigheid. Het is dus belang rijk dat u deze feiten in acht houdt voordat u al uw gebruikers naar een grote resource klasse verplaatst.

Raadpleeg het artikel [resource klassen voor workload Management](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie over resource klassen.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Een kleinere resource klasse gebruiken om gelijktijdigheid te verg Roten

Als u een lange vertraging in de gebruikers query's ziet, kunnen uw gebruikers in grotere bron klassen worden uitgevoerd. Met dit scenario wordt het verbruik van gelijktijdigheids sleuven verhoogd. Dit kan ertoe leiden dat andere query's in de wachtrij worden geplaatst.  Als u wilt bepalen of gebruikers query's in de wachtrij staan, voert `SELECT * FROM sys.dm_pdw_waits` u uit om te zien of er rijen worden geretourneerd.

De [resource klassen voor workload Management](../sql-data-warehouse/resource-classes-for-workload-management.md) en [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true) artikelen bevatten meer informatie.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Gebruik DMV’s om uw query’s te controleren en te optimaliseren

Toegewezen SQL-groepen hebben verschillende Dmv's die kunnen worden gebruikt voor het bewaken van de uitvoering van query's.  In het onderstaande controle artikel vindt u stapsgewijze instructies voor het weer geven van Details van een query die wordt uitgevoerd.  Als u snel query’s wilt vinden in de DMV's, kunt u proberen de optie LABEL te gebruiken bij uw query’s. Raadpleeg de artikelen in de onderstaande lijst voor meer gedetailleerde informatie:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [ADRES](develop-label.md)
- [SELECTEERT](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Volgende stappen

Zie ook het artikel [over probleem oplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor veelvoorkomende problemen en oplossingen.

Als u informatie nodig hebt die niet in dit artikel wordt vermeld, zoekt u op de [pagina van micro soft Q&een vraag naar Azure Synapse](/answers/topics/azure-synapse-analytics.html) is een plek waar u vragen kunt stellen aan andere gebruikers en aan de Azure Synapse Analytics-product groep.  

We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  Als u liever vragen hebt over Stack Overflow, hebben we ook een [Azure Synapse Analytics stack overflow-forum](https://stackoverflow.com/questions/tagged/azure-synapse).

Gebruik voor functie aanvragen de feedback pagina van [Azure Synapse Analytics](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Door uw aanvragen toe te voegen of door andere aanvragen te stemmen, kunnen we zich richten op de meeste functies in de aanvraag.
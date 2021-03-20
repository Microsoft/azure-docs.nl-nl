---
title: Best practices voor ontwikkeling
description: Aanbevelingen en aanbevolen procedures die u moet kennen bij het ontwikkelen van oplossingen voor Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc101e6bb743b9290593937a4d1bac656430b438
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685307"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Aanbevolen procedures voor het ontwikkelen van de Synapse SQL-groep

In dit artikel worden richt lijnen en aanbevolen procedures beschreven voor het ontwikkelen van uw SQL-groeps oplossing.

## <a name="tune-query-performance-with-new-product-enhancements"></a>De query prestaties afstemmen met nieuwe product uitbreidingen

- [Prestaties afstemmen met gerealiseerde weergaven](performance-tuning-materialized-views.md)
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](performance-tuning-ordered-cci.md)
- [Prestaties afstemmen door resultatensets op te slaan in de cache](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie het artikel [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het verminderen van de kosten door te onderbreken en schalen.

## <a name="maintain-statistics"></a>Statistieken bijhouden

SQL-groep kan zo worden geconfigureerd dat er automatisch statistieken worden gedetecteerd en gemaakt voor kolommen.  De query plannen die door de Optimizer zijn gemaakt, zijn alleen net zo goed als de beschik bare statistieken.  

We raden u aan AUTO_CREATE_STATISTICS voor uw data bases in te scha kelen en de statistieken dagelijks of na elke belasting te laten bijwerken om ervoor te zorgen dat statistieken voor kolommen die worden gebruikt in uw query's altijd up-to-date zijn.

Als u vindt dat het te lang duurt om al uw statistieken bij te werken, wilt u wellicht beter selecteren welke kolommen regel matig statistieken moeten bijwerken. Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken.

> [!TIP]
> U profiteert het meest voor deel door bijgewerkte statistieken te hebben voor kolommen die deel uitmaken van koppelingen, kolommen die worden gebruikt in de component WHERE en in GROUP BY.

Zie ook [tabel statistieken beheren](sql-data-warehouse-tables-statistics.md), [Statistieken maken](sql-data-warehouse-tables-statistics.md)en [Statistieken bijwerken](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash

Tabellen worden standaard gedistribueerd middels Round Robin.  Met dit ontwerp kunnen gebruikers gemakkelijk aan de slag gaan met het maken van tabellen zonder te hoeven bepalen hoe hun tabellen moeten worden gedistribueerd.  

Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  

Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  

Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  De volgende artikelen bevatten meer informatie over het verbeteren van de prestaties door een distributie kolom te selecteren en een gedistribueerde tabel te definiëren in de WITH-component van uw instructie CREATE TABLES.

Zie ook [tabel Overzicht](sql-data-warehouse-tables-overview.md), [tabel distributie](sql-data-warehouse-tables-distribute.md), [tabel distributie](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](sql-data-warehouse-tables-overview.md)en Create Table selecteren [als selecteren](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Niet te veel partities maken

Hoewel het partitioneren van gegevens effectief kan zijn voor het onderhouden van uw gegevens via partitie wisseling of het optimaliseren van scans met behulp van partitie-eliminatie, waardoor er te veel partities zijn, kunnen de query's worden vertraagd.  

Vaak is het mogelijk dat een hoogwaardige strategie voor het partitioneren van partities die goed werkt op SQL Server wellicht niet goed werkt voor de SQL-groep.  Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  

Houd er achter de schermen rekening mee dat de SQL-pool uw gegevens naar 60 data bases partitioneert, dus als u een tabel met 100-partities maakt, resulteert dit in een van de meeste 6000-partities.  Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  

> [!TIP]
> U kunt een lagere granulatie gebruiken dan in SQL Server.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [tabel partities](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren

De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  

Als u bijvoorbeeld een INVOEG actie hebt, die u verwacht één uur te maken, moet u, indien mogelijk, de toevoeging opsplitsen in vier delen, die elk in 15 minuten worden uitgevoerd.  Maak gebruik van speciale minimale logboek registratie cases, zoals CTAS, AFKAP ping, DROP TABLE of INSERT to Empty Tables, om het terugdraai risico te verminderen.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  

Voor niet-gepartitioneerde tabellen kunt u een CTAS gebruiken voor het schrijven van de gegevens die u in een tabel wilt houden, in plaats van verwijderen te gebruiken.  Als een CTAS dezelfde hoeveelheid tijd in beslag neemt, is het een veel veiliger bewerking om uit te voeren omdat het een minimale transactie logboek registratie heeft en zo nodig snel kan worden geannuleerd.

Zie ook [informatie over trans acties](sql-data-warehouse-develop-transactions.md), [optimalisatie van trans acties](sql-data-warehouse-develop-best-practices-transactions.md), [tabel partities](sql-data-warehouse-tables-partition.md), [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)en [create table as select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken

Bij het definiëren van uw DDL kunt u met het kleinste gegevens type dat uw gegevens ondersteunt, de query prestaties verbeteren.  Deze benadering is vooral belang rijk voor CHAR-en VARCHAR-kolommen.  

Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [tabel Overzicht](sql-data-warehouse-tables-overview.md), [tabel gegevens typen](sql-data-warehouse-tables-data-types.md)en [Create Table](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren

Geclusterde column Store-indexen zijn een van de meest efficiënte manieren om uw gegevens in de SQL-groep op te slaan.  Tabellen in SQL-groep worden standaard gemaakt als geclusterde column Store.  

> [!NOTE]
> Voor optimale prestaties van query's op column Store-tabellen is een goede segment kwaliteit van belang.  

Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  

Zie de [oorzaken van slechte kwaliteit van Column store-index](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) in het artikel [tabel indexen](sql-data-warehouse-tables-index.md) voor stapsgewijze instructies voor het detecteren en verbeteren van segment kwaliteit voor geclusterde column Store-tabellen.  

Omdat column Store-segmenten van hoge kwaliteit belang rijk zijn, is het een goed idee om gebruikers-Id's te gebruiken die zich in de middel lange of grote resource klasse bevinden voor het laden van gegevens. Als u lagere [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) gebruikt, wilt u een grotere resource klasse toewijzen aan de gebruiker die u wilt laden.

Aangezien column Store-tabellen doorgaans geen gegevens naar een gecomprimeerd column Store-segment pushen totdat er meer dan 1.000.000 rijen per tabel zijn en elke SQL-groeps tabel is gepartitioneerd in 60 tabellen, hebben de column Store-tabellen doorgaans geen voor waarde voor een query, tenzij de tabel meer dan 60.000.000 rijen heeft.  

Voor een tabel met minder dan 60.000.000 rijen is het wellicht niet zinvol om een column store-index te hebben.  Het is misschien ook niet verkeerd.  

Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, moet deze ten minste 6.000.000.000 rijen hebben om te profiteren van een geclusterde column Store (60 distributies *100 partities* 1.000.000 rijen).  

Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

> [!TIP]
> Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [tabel indexen](sql-data-warehouse-tables-index.md), [Column Store-indexen, gids](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Column Store-indexen opnieuw samen stellen](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Volgende stappen

Als u in dit artikel niet vindt wat u zoekt, kunt u het document ' zoeken naar docs ' aan de linkerkant van deze pagina gebruiken om alle Azure Synapse-documenten te doorzoeken.  

De [pagina van micro soft Q&een vraag voor Azure Synapse](/answers/topics/azure-synapse-analytics.html) is een plek waar u vragen kunt stellen aan andere gebruikers en aan de Azure Synapse-Product groep.  We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  

Als u liever vragen hebt over Stack Overflow, hebben we ook een [Azure Synapse Analytics stack overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Gebruik de pagina met [feedback over Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) om functie aanvragen te maken.  Het toevoegen van aanvragen of het stemmen op andere aanvragen helpt ons prioriteit te geven aan bepaalde functies.
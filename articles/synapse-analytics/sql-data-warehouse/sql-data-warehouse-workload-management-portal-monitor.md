---
title: Bewaking van werk belasting beheer Portal
description: Richt lijnen voor het bewaken van werk belasting beheer Portal in azure Synapse Analytics.
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
ms.openlocfilehash: eb7dcb9a384360d698c49a97f649bf75588d94f7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574233"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics-werk belasting Beheerportal bewaking

In dit artikel wordt uitgelegd hoe u resource gebruik en query activiteit van [werkbelasting groepen](sql-data-warehouse-workload-isolation.md#workload-groups) bewaakt.
Zie het artikel aan de slag [met azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor meer informatie over het configureren van de Azure Metrics Explorer.  Zie de sectie [resource gebruik](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) in de Azure Synapse Analytics-bewakings documentatie voor meer informatie over het bewaken van het gebruik van systeem bronnen.
Er zijn twee verschillende categorieën met metrische gegevens over werkbelasting groepen voor het bewaken van workload Management: resource toewijzing en query activiteit.  Deze metrische gegevens kunnen worden gesplitst en gefilterd op werkbelasting groep.  De metrische gegevens kunnen worden gesplitst en gefilterd op basis van de definitie van het systeem (resource class werk belasting groepen) of door de gebruiker gedefinieerde (gemaakt door een gebruiker met de syntaxis van de [WERKBELASTING groep maken](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ).

## <a name="workload-management-metric-definitions"></a>Metrische definities van workload Management

|Naam meetwaarde                    |Description  |Aggregatietype |
|-------------------------------|-------------|-----------------|
|Effectief cap-resource percentage | *Effectief cap resource percent* is een vaste limiet voor het percentage resources dat door de werkbelasting groep toegankelijk is, waarbij rekening wordt gehouden met een *effectief min resource percentage* dat is toegewezen aan andere werkbelasting groepen. De *werkelijke Cap-resource procentwaarde* wordt geconfigureerd met behulp `CAP_PERCENTAGE_RESOURCE` van de para meter in de syntaxis voor het maken van een [werkbelasting groep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  De geldige waarde wordt hier beschreven.<br><br>Als een werkbelasting groep bijvoorbeeld `DataLoads` is gemaakt met `CAP_PERCENTAGE_RESOURCE` = 100 en een andere werkbelasting groep wordt gemaakt met een effectief min resource percentage van 25%, is het percentage van de *effectief cap-resource* voor de `DataLoads` werkbelasting groep 75%.<br><br>Het *resource percentage van de effectief cap* bepaalt de bovengrens van gelijktijdigheid (en dus mogelijk door Voer) die een werkbelasting groep kan behalen.  Als er nog een extra door Voer is vereist dan wat momenteel wordt gerapporteerd door het *effectiefste Cap-percentage voor resources* , verhoogt u de `CAP_PERCENTAGE_RESOURCE` , verlaagt u de `MIN_PERCENTAGE_RESOURCE` andere werkbelasting groepen of verkleint u het exemplaar om meer resources toe te voegen.  Het verminderen van de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan gelijktijdigheid verhogen, maar kan de algemene door Voer niet verhogen.| Min, Gem, Max |
|Effectief min resource percentage |*Effectief min resource percentage* is het minimale percentage resources dat voor de werkbelasting groep is gereserveerd en geïsoleerd, waarbij rekening wordt gehouden met het minimum service niveau.  De werkelijke minimale resource procentuele waarde wordt geconfigureerd met behulp `MIN_PERCENTAGE_RESOURCE` van de para meter in de syntaxis [groep maken](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  De geldige waarde wordt [hier](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)beschreven.<br><br>Gebruik het aggregatie type Sum wanneer deze metriek niet is gefilterd en ongesplitst om de totale isolatie van de werk belasting te bewaken die op het systeem is geconfigureerd.<br><br>Het *werkelijke minimale resource percentage* bepaalt de laagste grens van gegarandeerde gelijktijdigheid (en dus gegarandeerde door Voer) die een werkbelasting groep kan belasten.  Als er extra gegarandeerde resources nodig zijn dan wordt gerapporteerd door de *werkelijke minimale resource procentuele* waarde, verhoogt u de `MIN_PERCENTAGE_RESOURCE` para meter die is geconfigureerd voor de werkbelasting groep.  Het verminderen van de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan gelijktijdigheid verhogen, maar kan de algemene door Voer niet verhogen. |Min, Gem, Max|
|Actieve query's voor werkbelasting groep  |Deze metriek rapporteert de actieve query's binnen de werkbelasting groep.  Als u deze metrische waarde onfilterd en ongesplitst gebruikt, worden alle actieve query's weer gegeven die op het systeem worden uitgevoerd.|Sum         |
|Toewijzing van werkbelasting groep op Maxi maal resource percentage |Met deze metriek wordt het percentage toegewezen resources weer gegeven ten opzichte van het *resource percentage van de effectief cap* per werkbelasting groep.  Deze metrische gegevens bieden het effectief gebruik van de werkbelasting groep.<br><br>Overweeg een werkbelasting groep `DataLoads` met een *effectief cap-resource percentage* van 75% en een `REQUEST_MIN_RESOURCE_GRANT_PERCENT` geconfigureerd op 25%.  De *werkbelasting groep toegewezen door de maximale waarde voor resource percentage* gefilterd op `DataLoads` zou 33% zijn (25%/75%) Als er één query werd uitgevoerd in deze werkbelasting groep.<br><br>Gebruik deze metrische gegevens om het gebruik van een werkbelasting groep te identificeren.  Een waarde dicht bij 100% geeft aan dat alle resources die beschikbaar zijn voor de werkbelasting groep, worden gebruikt.  Daarnaast zou de *werkbelasting groep query's metrieken* voor dezelfde werkbelasting groep met een waarde die groter is dan nul zou aangeven dat de werkbelasting groep extra resources zou gebruiken als ze worden toegewezen.  Als deze metriek echter voortdurend laag is en de *werkbelasting groep actieve query's* laag is, wordt de werkbelasting groep niet gebruikt.  Deze situatie is vooral problematisch als een *effectief cap-resource percentage* groter is dan nul, omdat dit zou wijzen op [isolatie van werk belastingen](#underutilized-workload-isolation).|Min, Gem, Max |
|Toewijzing van werkbelasting groep per systeem percentage | Met deze metriek wordt het percentage toegewezen resources ten opzichte van het hele systeem weer gegeven.<br><br>Overweeg een werkbelasting groep `DataLoads` met een configuratie die is `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ingesteld op 25%.  De waarde *van de werkbelasting groep per systeem percentage* gefilterd op `DataLoads` zou 25% zijn (25%/100%) Als er één query werd uitgevoerd in deze werkbelasting groep.|Min, Gem, Max |
|Time-outs query werkbelasting groep |Query's voor de werkbelasting groep waarvoor een time-out is opgetreden.  Querytime-time-outs die door deze metrische gegevens worden gerapporteerd, worden slechts eenmaal gestart nadat de query is uitgevoerd (de wacht tijd is niet opgenomen als gevolg van vergren deling of het wachten op de bron).<br><br>Querytime-out wordt geconfigureerd met behulp `QUERY_EXECUTION_TIMEOUT_SEC` van de para meter in de syntaxis voor het maken van een [werkbelasting groep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Het verhogen van de waarde kan het aantal querytime-outs verminderen.<br><br>U kunt de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para meter voor de werkbelasting groep verhogen om de hoeveelheid time-outs te verminderen en meer resources per query toe te wijzen.  Houd er rekening mee `REQUEST_MIN_RESOURCE_GRANT_PERCENT` dat de hoeveelheid gelijktijdigheid voor de werkbelasting groep wordt verminderd. |Sum |
|Werkbelasting groep in wachtrij geplaatste query's | Query's voor de werkbelasting groep die momenteel in de wachtrij staan om de uitvoering te starten.  Query's kunnen een wachtrij zijn omdat ze wachten op resources of vergren delingen.<br><br>Query's kunnen om talloze redenen worden gewacht.  Als het systeem overbelast is en de gelijktijdigheids vraag groter is dan beschikbaar is, worden query's in de wachtrij geplaatst.<br><br>Overweeg om meer resources aan de werkbelasting groep toe te voegen door de `CAP_PERCENTAGE_RESOURCE` para meter in de instructie [Create workload Group](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) te verhogen.  Als dit `CAP_PERCENTAGE_RESOURCE` groter is dan het *werkelijke Cap-percentage* van de resource, is de geconfigureerde werk belasting isolatie voor een andere werkbelasting groep van invloed op de resources die zijn toegewezen aan deze werkbelasting groep.  Overweeg `MIN_PERCENTAGE_RESOURCE` om andere werkbelasting groepen te verlagen of het exemplaar omhoog te schalen om meer resources toe te voegen. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Bewakings scenario's en acties

Hieronder vindt u een reeks grafiek configuraties voor het markeren van het metrische gebruik van workload Management voor het oplossen van problemen samen met gekoppelde acties om het probleem op te lossen.

### <a name="underutilized-workload-isolation"></a>Isolatie van werk belasting ondergebruikt

Houd rekening met de volgende groeps beleidsobjecten en classificatie configuratie waarbij een werkbelasting groep `wgPriority` met de naam wordt gemaakt en *TheCEO* `membername` wordt toegewezen aan de werk belasting met behulp van de `wcCEOPriority` classificatie werk belasting.  `wgPriority`Voor de werkbelasting groep is 25% werk belasting isolatie geconfigureerd ( `MIN_PERCENTAGE_RESOURCE` = 25).  Elke query die wordt verzonden door *TheCEO* , krijgt 5% van de systeem bronnen ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

De onderstaande grafiek wordt als volgt geconfigureerd:<br>
Metriek 1: *effectief min resource percentage* (gem. aggregatie `blue line` )<br>
Metriek 2: *toewijzing van werkbelasting groepen op systeem percentage* (gem. aggregatie `purple line` )<br>
Filter: [werkbelasting groep] = `wgPriority`<br>
![In de scherm afbeelding ziet u een grafiek met de twee metrische gegevens en het filter.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
In de grafiek wordt weer gegeven dat met 25% workload-isolatie slechts 10% wordt gebruikt bij gemiddelde.  In dit geval kan de `MIN_PERCENTAGE_RESOURCE` waarde van de para meter worden verlaagd tot 10 of 15 en kunnen andere werk belastingen op het systeem de bronnen gebruiken.

### <a name="workload-group-bottleneck"></a>Bottleneck van werkbelasting groep

Houd rekening met de volgende werkbelasting groep en classificatie configuratie waarbij een werkbelasting groep `wgDataAnalyst` met de naam wordt gemaakt en de *DataAnalyst* `membername` wordt toegewezen aan deze met behulp van de classificatie van de `wcDataAnalyst` werk belasting.  `wgDataAnalyst`Voor de werkbelasting groep is 6% isolatie van de werk belasting geconfigureerd ( `MIN_PERCENTAGE_RESOURCE` = 6) en een resource limiet van 9% ( `CAP_PERCENTAGE_RESOURCE` = 9).  Elke query die wordt verzonden door de *DataAnalyst* , krijgt 3% systeem bronnen ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

De onderstaande grafiek wordt als volgt geconfigureerd:<br>
Metriek 1: *percentage van effectief cap-resource* (gem. aggregatie `blue line` )<br>
Metriek 2: *toewijzing van werkbelasting groepen op Maxi maal resource percentage* (gem. aggregatie `purple line` )<br>
Metric 3: *werkbelasting groep query's in wachtrij* (Sum Aggregation, `turquoise line` )<br>
Filter: [werkbelasting groep] = `wgDataAnalyst`<br>
![In de scherm afbeelding ziet u een grafiek met de drie metrische gegevens en het filter.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
In de grafiek wordt weer gegeven dat de werkbelasting groep met een limiet van 9% op resources is 90% + gebruikt (van de *werkbelasting groep toewijzing op basis van het percentage metrische resources*).  Er is een stabiele wachtrij met query's, zoals wordt weer gegeven in de *groep workload query's* in de wachtrij.  In dit geval is het verhogen van de `CAP_PERCENTAGE_RESOURCE` naar een hogere waarde dan 9% dat er meer query's gelijktijdig kunnen worden uitgevoerd.  Het verg Roten `CAP_PERCENTAGE_RESOURCE` van de veronderstelt dat er voldoende bronnen beschikbaar zijn en niet worden geïsoleerd door andere werkbelasting groepen.  Controleer of de limiet is verhoogd door de *werkelijke Cap-waarde voor het resource percentage* te controleren.  Als u meer door voer wilt doen, kunt u ook overwegen `REQUEST_MIN_RESOURCE_GRANT_PERCENT` om de waarde te verhogen naar een hoger dan 3.  Het verg Roten van het `REQUEST_MIN_RESOURCE_GRANT_PERCENT` uitvoeren van query's kan leiden tot snellere uitvoering.

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: Isolatie van werkbelastingen configureren met behulp van T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [Werkbelasting groep maken (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [CLASSIFICATIE van werk belasting maken (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Resource gebruik bewaken](sql-data-warehouse-concept-resource-utilization-query-activity.md)

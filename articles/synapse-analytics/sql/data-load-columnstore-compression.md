---
title: Prestaties van columnstore-index verbeteren
description: Verminder de geheugenvereisten of verhoog het beschikbare geheugen om het aantal rijen te maximaliseren dat een columnstore-index in elke rijgroep comprimeert.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a452808b1c349e2aec91759675e269e3680f0598
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567958"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>Kwaliteit van rowgroup maximaliseren voor columnstore-indexprestaties

De kwaliteit van de rijgroep wordt bepaald door het aantal rijen in een rijgroep. Door het beschikbare geheugen te vergroten, kan het aantal rijen dat een columnstore-index comprimeert in elke rijgroep worden gemaximaliseerd.  Gebruik deze methoden om de compressiesnelheden en queryprestaties voor columnstore-indexen te verbeteren.

## <a name="why-the-rowgroup-size-matters"></a>Waarom de grootte van de rijgroep belangrijk is

Omdat een columnstore-index een tabel scant door kolomsegmenten van afzonderlijke rijgroepen te scannen, verbetert het maximaliseren van het aantal rijen in elke rijgroep de queryprestaties. Wanneer rijgroepen een groot aantal rijen hebben, verbetert de gegevenscompressie, wat betekent dat er minder gegevens van de schijf moeten worden gelezen.

Zie Columnstore Indexes Guide (Handleiding [columnstore-indexen) voor meer informatie over rijgroepen.](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)

## <a name="target-size-for-rowgroups"></a>Doelgrootte voor rijgroepen

Voor de beste queryprestaties is het doel om het aantal rijen per rijgroep in een columnstore-index te maximaliseren. Een rijgroep kan maximaal 1.048.576 rijen hebben. Het is geen probleem om niet het maximum aantal rijen per rijgroep te hebben. Columnstore-indexen leveren goede prestaties wanneer rijgroepen ten minste 100.000 rijen hebben.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rijgroepen kunnen worden afgekort tijdens compressie

Tijdens het bulksgewijs laden of herbouwen van columnstore-indexen is er soms onvoldoende geheugen beschikbaar om alle rijen te comprimeren die voor elke rijgroep zijn aangewezen. Wanneer er geheugendruk is, worden met columnstore-indexen de grootte van de rijgroepen kleiner, zodat compressie in de columnstore kan slagen.

Wanneer er onvoldoende geheugen is om ten minste 10.000 rijen in elke rijgroep te comprimeren, wordt er een fout gegenereerd.

Zie Bulksgewijs laden in een geclusterde columnstore-index voor meer informatie over [bulksgewijs laden.](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?view=azure-sqldw-latest#bulk&preserve-view=true)

## <a name="how-to-monitor-rowgroup-quality"></a>Kwaliteit van rijgroepen bewaken

De DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) bevat de weergavedefinitie die overeenkomt met SQL DB) die nuttige informatie beschikbaar maakt, zoals het aantal rijen in rijgroepen en de reden voor het inkorten als er is ingekort. U kunt de volgende weergave maken als een handige manier om een query uit te voeren op deze DMV om informatie op te halen over het inkorten van rijgroepen.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

De trim_reason_desc geeft aan of de rijgroep is ingekort (trim_reason_desc = NO_TRIM impliceert dat er geen inkorting is en de rijgroep van optimale kwaliteit is). De volgende trim-redenen wijzen op voortijdige inkorting van de rijgroep:

- BULKLOAD: Deze trimreden wordt gebruikt wanneer de binnenkomende batch met rijen voor de belasting minder dan 1 miljoen rijen heeft. De engine maakt gecomprimeerde rijgroepen als er meer dan 100.000 rijen worden ingevoegd (in plaats van in te voegen in het Delta-opslagopslag), maar stelt de reden voor het inkorten in op BULKLOAD. In dit scenario kunt u overwegen om de batchbelasting te verhogen om meer rijen op te nemen. Ook moet u uw partitieschema opnieuw beoordelen om ervoor te zorgen dat het niet te gedetailleerd is, omdat rijgroepen geen partitiegrenzen kunnen overspannen.
- MEMORY_LIMITATION: voor het maken van rijgroepen met 1 miljoen rijen is een bepaalde hoeveelheid werkgeheugen vereist door de engine. Wanneer het beschikbare geheugen van de laadsessie kleiner is dan het vereiste werkgeheugen, worden rijgroepen voortijdig ingekort. In de volgende secties wordt uitgelegd hoe u het vereiste geheugen kunt schatten en meer geheugen kunt toewijzen.
- DICTIONARY_SIZE: Deze reden voor het inkorten van rijen geeft aan dat het inkorten van rijgroepen is opgetreden omdat er ten minste één tekenreekskolom met brede en/of tekenreeksen met hoge kardinaliteit is. De grootte van de woordenlijst is beperkt tot 16 MB in het geheugen en zodra deze limiet is bereikt, wordt de rijgroep gecomprimeerd. Als u in deze situatie komt, kunt u overwegen om de problematische kolom te isoleren in een afzonderlijke tabel.

## <a name="how-to-estimate-memory-requirements"></a>Geheugenvereisten schatten

Het maximaal vereiste geheugen voor het comprimeren van één rijgroep is ongeveer als volgt:

- 72 MB +
- \#\* \# rijenkolommen \* 8 bytes +
- \#rijen \* \# met korte tekenreekskolommen \* van 32 bytes +
- \#long-string-columns \* 16 MB voor compressiewoordenlijst

> [!NOTE]
> In kolommen met korte tekenreeksen worden tekenreeksgegevenstypen van <= 32 bytes en voor kolommen met lange tekenreeksen worden tekenreeksgegevenstypen van > 32 bytes gebruikt.

Lange tekenreeksen worden gecomprimeerd met een compressiemethode die is ontworpen voor het comprimeren van tekst. Deze compressiemethode maakt gebruik van *een woordenlijst voor* het opslaan van tekstpatronen. De maximale grootte van een woordenlijst is 16 MB. Er is slechts één woordenlijst voor elke lange tekenreekskolom in de rijgroep.

Zie voor een uitgebreide bespreking van de vereisten voor columnstore-geheugen de video [Synapse SQL schalen: configuratie en richtlijnen.](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)

## <a name="ways-to-reduce-memory-requirements"></a>Manieren om geheugenvereisten te verminderen

Gebruik de volgende technieken om de geheugenvereisten voor het comprimeren van rijgroepen in columnstore-indexen te verminderen.

### <a name="use-fewer-columns"></a>Minder kolommen gebruiken

Ontwerp indien mogelijk de tabel met minder kolommen. Wanneer een rijgroep in de columnstore wordt gecomprimeerd, comprimeert de columnstore-index elk kolomsegment afzonderlijk. Daarom nemen de geheugenvereisten voor het comprimeren van een rijgroep toe naarmate het aantal kolommen toeneemt.

### <a name="use-fewer-string-columns"></a>Minder tekenreekskolommen gebruiken

Kolommen met tekenreeksgegevenstypen vereisen meer geheugen dan numerieke en datumgegevenstypen. Als u de geheugenvereisten wilt verminderen, kunt u tekenreekskolommen uit feitentabellen verwijderen en deze in kleinere dimensietabellen plaatsen.

Aanvullende geheugenvereisten voor tekenreekscompressie:

- Tekenreeksgegevenstypen van maximaal 32 tekens kunnen 32 extra bytes per waarde vereisen.
- Tekenreeksgegevenstypen met meer dan 32 tekens worden gecomprimeerd met behulp van woordenlijstmethoden.  Elke kolom in de rijgroep kan tot 16 MB extra vereisen om de woordenlijst te bouwen.

### <a name="avoid-over-partitioning"></a>Overpartities voorkomen

Columnstore-indexen maken een of meer rijgroepen per partitie. Voor datawarehousing in Azure Synapse Analytics neemt het aantal partities snel toe omdat de gegevens worden gedistribueerd en elke distributie wordt gepartitiefd. Als de tabel te veel partities heeft, zijn er mogelijk onvoldoende rijen om de rijgroepen te vullen. Het gebrek aan rijen leidt niet tot geheugendruk tijdens compressie, maar leidt wel tot rijgroepen die niet de beste prestaties van columnstore-query's bereiken.

Een andere reden om overpartities te voorkomen, is dat er geheugenoverhead is voor het laden van rijen in een columnstore-index op een gepartitiesteerde tabel. Tijdens een belasting kunnen veel partities de binnenkomende rijen ontvangen, die in het geheugen worden opgeslagen totdat elke partitie voldoende rijen heeft om te worden gecomprimeerd. Te veel partities zorgen voor extra geheugendruk.

### <a name="simplify-the-load-query"></a>De laadquery vereenvoudigen

De database deelt de geheugen-toekenning voor een query tussen alle operators in de query. Wanneer een laadquery complexe sorteert en joins heeft, wordt het geheugen dat beschikbaar is voor compressie verminderd.

Ontwerp de laadquery om u alleen te richten op het laden van de query. Als u transformaties op de gegevens wilt uitvoeren, moet u deze afzonderlijk van de laadquery uitvoeren. Faseer bijvoorbeeld de gegevens in een heap-tabel, voer de transformaties uit en laad vervolgens de faseringstabel in de columnstore-index. 

### <a name="adjust-maxdop"></a>MAXDOP aanpassen

Elke distributie comprimeert rijgroepen parallel in de columnstore wanneer er meer dan één CPU-kern beschikbaar is per distributie. Voor de parallellelisme zijn extra geheugenbronnen vereist, wat kan leiden tot geheugendruk en het inkorten van rijgroepen.

Als u de geheugendruk wilt verminderen, kunt u de MAXDOP-queryhint gebruiken om af te dwingen dat de laadbewerking wordt uitgevoerd in seriële modus binnen elke distributie.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Manieren om meer geheugen toe te wijzen

De DWU-grootte en de resourceklasse van de gebruiker bepalen samen hoeveel geheugen beschikbaar is voor een gebruikersquery. Als u de geheugen-toekenning voor een belastingquery wilt verhogen, kunt u het aantal DWUs verhogen of de resourceklasse verhogen.

- Zie Prestaties schalen om de [DWE Hoe kan ik te verhogen.](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Zie Voorbeeld van een gebruikersresourceklasse wijzigen om de resourceklasse voor een query [te wijzigen.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class)

## <a name="next-steps"></a>Volgende stappen

Zie prestatieoverzicht voor meer manieren om de prestaties in Synapse SQL [te verbeteren.](../overview-terminology.md)


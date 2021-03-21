---
title: Prestaties van Column store-index verbeteren voor toegewezen SQL-groep
description: Verminder de geheugen vereisten of verg root het beschik bare geheugen om het aantal rijen binnen elke Rijg roep in de toegewezen SQL-groep te maximaliseren.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b15f71698af8c340b58f3399390857790313b8ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585482"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maximale Rijg roep-kwaliteit voor column Store-indexen in toegewezen SQL-groep 

De kwaliteit van de Rijg roep wordt bepaald door het aantal rijen in een Rijg roep. Het verg Roten van het beschik bare geheugen kan het aantal rijen dat een column store-index in elke Rijg roep wordt gecomprimeerd, maximaliseren.  Gebruik deze methoden voor het verbeteren van de compressie frequentie en de query prestaties voor column Store-indexen.

## <a name="why-the-rowgroup-size-matters"></a>Waarom de Rijg roep grootte belang rijk is

Omdat een column store-index een tabel scant door kolom segmenten van afzonderlijke Rijg roepen te scannen, kunt u het aantal rijen in elke Rijg roep verhogen om de query prestaties te verbeteren.

Wanneer Rijg roepen een groot aantal rijen heeft, verbetert de gegevens compressie dat betekent dat er minder gegevens kunnen worden gelezen van de schijf.

Zie [Column Store-indexen gids](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor meer informatie over Rijg roepen.

## <a name="target-size-for-rowgroups"></a>Doel grootte voor Rijg roepen

Voor de beste query prestaties is het doel het aantal rijen per Rijg roep in een column store-index te maximaliseren. Een Rijg roep kan Maxi maal 1.048.576 rijen bevatten.

Het is niet mogelijk om het maximum aantal rijen per Rijg roep te hebben. Column Store-indexen bieden goede prestaties wanneer Rijg roepen ten minste 100.000 rijen hebben.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rijg roepen kan worden bijgesneden tijdens compressie

Tijdens het opnieuw opbouwen van bulksgewijs laden of column store-index is er mogelijk onvoldoende geheugen beschikbaar om alle opgegeven rijen voor elke Rijg roep te comprimeren. Wanneer geheugen druk aanwezig is, knipt column Store-indexen de Rijg roep-grootten, zodat compressie naar de column Store kan slagen.

Als er onvoldoende geheugen is om ten minste 10.000 rijen in elke Rijg roep te comprimeren, wordt er een fout gegenereerd.

Zie [bulksgewijs laden in een geclusterde column store-index](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)voor meer informatie over bulksgewijs laden.

## <a name="how-to-monitor-rowgroup-quality"></a>Rijg roep-kwaliteit bewaken

De DMV-sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bevat de weergave definitie die overeenkomt met de SQL-data base) waarmee nuttige informatie wordt weer gegeven, zoals het aantal rijen in Rijg roepen en de reden voor het verkleinen van de gegevens, als deze is bijgesneden.

U kunt de volgende weer gave maken als een handige manier om een query uit te geven op deze DMV om informatie op te halen over Rijg roep-bijsnijden.

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

De trim_reason_desc geeft aan of de Rijg roep is bijgesneden (trim_reason_desc = NO_TRIM impliceert dat er geen beperking is en dat de groep een optimale kwaliteit heeft). De volgende redenen voor het verkorten van de Rijg roep zijn:

- BULKLOAD: deze trim reden wordt gebruikt wanneer de inkomende batch rijen voor de belasting minder dan 1.000.000 rijen bevat. De-engine maakt gecomprimeerde Rijg roepen als er meer dan 100.000 rijen worden ingevoegd (in plaats van in het Delta archief in te voegen), maar de reden voor het verkleinen van de groep wordt ingesteld op BULKLOAD. In dit scenario kunt u de batch-belasting verg Roten om meer rijen op te nemen. U kunt ook het partitie schema opnieuw evalueren om er zeker van te zijn dat het niet te nauw keurig is als Rijg roepen geen partitie grenzen kunnen omvatten.
- MEMORY_LIMITATION: om Rijg roepen te maken met 1.000.000 rijen, wordt een bepaalde hoeveelheid werk geheugen vereist door de engine. Wanneer het beschik bare geheugen van de laad sessie kleiner is dan het vereiste werk geheugen, worden Rijg roepen voor tijdig afgekapt. In de volgende secties wordt uitgelegd hoe u het vereiste geheugen kunt schatten en meer geheugen kunt toewijzen.
- DICTIONARY_SIZE: deze trim reden geeft aan dat de Rijg roep is inge kort omdat er ten minste één teken reeks kolom is met een brede en/of hoge kardinaliteit teken reeksen. De grootte van de woorden lijst is beperkt tot 16 MB in het geheugen en zodra deze limiet is bereikt, wordt de groep gecomprimeerd. Als u in deze situatie wordt uitgevoerd, kunt u overwegen om de problematische kolom in een afzonderlijke tabel te isoleren.

## <a name="how-to-estimate-memory-requirements"></a>Geheugen vereisten schatten

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Het Maxi maal vereiste geheugen voor het comprimeren van één Rijg roep is ongeveer

- 72 MB +
- \#rijen \* \# kolommen \* 8 bytes +
- \#rijen \* \# korte teken reeks-kolommen \* 32 bytes +
- \#lange-teken reeks-kolommen \* 16 MB voor de compressie woordenlijst

> [!NOTE]
> Korte teken reeks-kolommen gebruiken teken reeks gegevens typen van <= 32 bytes en lange teken reeks kolommen gebruiken teken reeks gegevens typen van > 32 bytes.

Lange teken reeksen worden gecomprimeerd met een compressie methode die is ontworpen voor het comprimeren van tekst. Deze compressie methode maakt gebruik van een *woorden lijst* om tekst patronen op te slaan. De maximale grootte van een woorden lijst is 16 MB. Er is slechts één woorden lijst voor elke lange teken reeks kolom in de Rijg roep.

Zie voor een uitgebreide beschrijving van Column Store-geheugen vereisten de video- [toegewezen SQL-pool schalen: configuratie en begeleiding](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Manieren om geheugen vereisten te reduceren

Gebruik de volgende technieken om de geheugen vereisten voor het comprimeren van Rijg roepen naar column Store-indexen te verminderen.

### <a name="use-fewer-columns"></a>Minder kolommen gebruiken

Ontwerp, indien mogelijk, de tabel met minder kolommen. Wanneer een Rijg roep wordt gecomprimeerd naar de column Store, comprimeert de column store-index elk kolom segment afzonderlijk.

Als zodanig worden de geheugen vereisten voor het comprimeren van een Rijg roep verhoogd naarmate het aantal kolommen toeneemt.

### <a name="use-fewer-string-columns"></a>Minder teken reeks kolommen gebruiken

Kolommen met teken reeks gegevens typen vereisen meer geheugen dan numerieke en datum gegevens typen. Als u de geheugen vereisten wilt reduceren, kunt u overwegen om teken reeks kolommen uit feiten tabellen te verwijderen en ze in kleinere dimensie tabellen te plaatsen.

Aanvullende geheugen vereisten voor teken reeks compressie:

- Teken reeks gegevens typen tot 32 tekens kunnen 32 extra bytes per waarde vereisen.
- Teken reeks gegevens typen met meer dan 32 tekens worden gecomprimeerd met woordenboek methoden.  Voor elke kolom in de Rijg roep kan 16 MB extra worden vereist om de woorden lijst samen te stellen.

### <a name="avoid-over-partitioning"></a>Vermijd overschrijdende partitionering

Column Store-indexen maken een of meer Rijg roepen per partitie. Voor een toegewezen SQL-groep in azure Synapse Analytics neemt het aantal partities toe, omdat de gegevens worden gedistribueerd en elke distributie wordt gepartitioneerd.

Als de tabel te veel partities heeft, zijn er mogelijk niet voldoende rijen om de Rijg roepen in te vullen. Het ontbreken van rijen maakt geen geheugen druk tijdens compressie. Maar het resulteert in Rijg roepen die de beste prestaties van Column Store-query's niet behalen.

Een andere reden om te voor komen dat u partitioneert, is er een geheugen overhead voor het laden van rijen in een column store-index in een gepartitioneerde tabel.

Tijdens het laden kunnen veel partities de binnenkomende rijen ontvangen die in het geheugen worden bewaard totdat elke partitie voldoende rijen heeft om te worden gecomprimeerd. Als er te veel partities zijn, wordt extra geheugen druk gemaakt.

### <a name="simplify-the-load-query"></a>Vereenvoudig de laad query

De data base deelt de geheugen toekenning voor een query tussen alle Opera tors in de query. Wanneer een laad query complexe sorteringen en samen voegingen heeft, wordt het geheugen dat beschikbaar is voor compressie, gereduceerd.

Ontwerp de laad query zodanig dat deze alleen gericht is op het laden van de query. Als u trans formaties voor de gegevens wilt uitvoeren, moet u ze gescheiden van de laad query uitvoeren. U kunt bijvoorbeeld de gegevens in een heap-tabel faseren, de trans formaties uitvoeren en de faserings tabel vervolgens laden in de column store-index.

> [!TIP]
> U kunt de gegevens ook eerst laden en vervolgens het MPP-systeem gebruiken om de gegevens te transformeren.

### <a name="adjust-maxdop"></a>MAXDOP aanpassen

Elke distributie comprimeert Rijg roepen in de column Store parallel wanneer er meer dan één CPU-kern per distributie beschikbaar is.

De parallelle uitvoering vereist extra geheugen bronnen, wat kan leiden tot geheugen druk en rijg roep.

Als u de geheugen druk wilt reduceren, kunt u de MAXDOP-query Hint gebruiken om de laad bewerking af te dwingen in de seriële modus binnen elke distributie.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Manieren om meer geheugen toe te wijzen

De grootte van de DWU en de resource klasse van de gebruiker bepalen samen hoeveel geheugen beschikbaar is voor een gebruikers query.

Als u de geheugen toekenning voor een laad query wilt verg Roten, kunt u het aantal Dwu's verhogen of de resource klasse verg Roten.

- Zie [Hoe kan ik schaal prestaties](quickstart-scale-compute-portal.md) om de dwu's te verg Roten?
- Als u de resource klasse voor een query wilt wijzigen, raadpleegt u [een voor beeld van een gebruikers resource klasse wijzigen](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van prestaties](cheat-sheet.md)voor meer manieren om de prestaties van een toegewezen SQL-groep te verbeteren.

---
title: Knoop punten – grootschalige (Citus)-Azure Database for PostgreSQL
description: Meer informatie over de typen knoop punten en tabellen in een server groep in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314824"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Knoop punten en tabellen in Azure Database for PostgreSQL – grootschalige (Citus)

## <a name="nodes"></a>Knooppunten

Met het hosting type grootschalige (Citus) kunnen Azure Database for PostgreSQL-servers (knoop punten) worden gecoördineerd met elkaar in een architectuur met het gedeelde Nothing. De knoop punten in een server groep bevatten gezamenlijk meer gegevens en gebruiken meer CPU-kernen dan op één server mogelijk zijn. Met de architectuur kan de data base ook worden geschaald door meer knoop punten toe te voegen aan de Server groep.

### <a name="coordinator-and-workers"></a>Coördinator en werk nemers

Elke server groep heeft een coördinator knooppunt en meerdere werk rollen. Toepassingen sturen hun query's naar het coördinator knooppunt, die het doorstuurt naar de relevante werk nemers en de resultaten samenvoegt. Toepassingen kunnen niet rechtstreeks verbinding maken met werk nemers.

Met grootschalige (Citus) kan de database beheerder tabellen *distribueren* , waarbij verschillende rijen op verschillende werk knooppunten worden opgeslagen. Gedistribueerde tabellen zijn de belangrijkste tot grootschalige (Citus)-prestaties. Als het niet lukt om tabellen te distribueren, worden ze volledig op het coördinator knooppunt uitgevoerd en kunnen ze niet profiteren van parallellisme op meerdere machines.

Voor elke query op gedistribueerde tabellen stuurt de coördinator deze om naar één worker-knoop punt, of parallelizes deze op verschillende manieren, afhankelijk van het feit of de vereiste gegevens zich op één knoop punt of meerdere hebben voordoen. De coördinator beslist wat er moet gebeuren met tabellen met advies voor de meta gegevens. Deze tabellen volgen de DNS-namen en de status van worker-knoop punten en de distributie van gegevens tussen knoop punten.

## <a name="table-types"></a>Tabeltypen

Er zijn drie typen tabellen in een grootschalige-Server groep (Citus), die elk anders op knoop punten zijn opgeslagen en die voor verschillende doel einden worden gebruikt.

### <a name="type-1-distributed-tables"></a>Type 1: gedistribueerde tabellen

Het eerste type, de meest voorkomende, is gedistribueerde tabellen. Ze lijken normale tabellen te zijn voor SQL-instructies, maar ze zijn horizon taal gepartitioneerd tussen werk knooppunten. Dit betekent dat de rijen van de tabel worden opgeslagen op verschillende knoop punten, in fragment tabellen met de naam Shards.

Grootschalige (Citus) voert niet alleen SQL maar DDL-instructies uit in een cluster.
Als u het schema van een gedistribueerde tabel trapsgewijs wijzigt, worden de Shards van de tabel bijgewerkt voor alle werk nemers.

#### <a name="distribution-column"></a>Distributie kolom

Grootschalige (Citus) maakt gebruik van een algoritme sharding om rijen aan Shards toe te wijzen. De toewijzing wordt deterministisch gemaakt op basis van de waarde van een tabel kolom die de kolom distributie heet. De Cluster beheerder moet deze kolom aanwijzen bij het distribueren van een tabel.
Het is belang rijk dat u de juiste keuze maakt voor prestaties en functionaliteit.

### <a name="type-2-reference-tables"></a>Type 2: verwijzings tabellen

Een verwijzings tabel is een type gedistribueerde tabel waarvan de volledige inhoud in één Shard is geconcentreerd. De Shard wordt gerepliceerd op elke werk nemer. Query's op elke werk nemer hebben lokaal toegang tot de referentie gegevens, zonder de netwerk belasting van het aanvragen van rijen van een ander knoop punt. Verwijzings tabellen hebben geen distributie kolom, omdat er geen onderscheid hoeft te worden gemaakt tussen afzonderlijke Shards per rij.

Verwijzings tabellen zijn doorgaans klein en worden gebruikt voor het opslaan van gegevens die relevant zijn voor query's die worden uitgevoerd op een worker-knoop punt. Een voor beeld is opsommings waarden, zoals order statussen of product categorieën.

### <a name="type-3-local-tables"></a>Type 3: lokale tabellen

Wanneer u grootschalige (Citus) gebruikt, is het coördinator knooppunt waarmee u verbinding maakt, een normale PostgreSQL-data base. U kunt gewone tabellen maken op de coördinator en ervoor kiezen deze niet te Shard.

Een goede kandidaat voor lokale tabellen is kleine beheer tabellen die geen deel uitmaken van samenvoeg query's. Een voor beeld is een tabel met gebruikers voor het aanmelden en verifiëren van toepassingen.

## <a name="shards"></a>Shards

In de vorige sectie wordt beschreven hoe gedistribueerde tabellen worden opgeslagen als Shards op worker-knoop punten. In deze sectie worden meer technische details besproken.

De `pg_dist_shard` meta gegevens tabel in de coördinator bevat een rij voor elk Shard van elke gedistribueerde tabel in het systeem. De rij komt overeen met een Shard-ID met een bereik van gehele getallen in een hash-ruimte (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Als het coördinator knooppunt wil bepalen welke Shard een rij bevat `github_events` , wordt de waarde van de kolom Distribution in de rij hashes. Vervolgens controleert het knoop punt welk Shard \' s-bereik de gehashte waarde bevat. De bereiken worden gedefinieerd zodat de afbeelding van de hash-functie de niet-aaneengesloten samen voeging is.

### <a name="shard-placements"></a>Shard plaatsen

Stel dat Shard 102027 is gekoppeld aan de betreffende rij. De rij wordt in een tabel met `github_events_102027` de naam in een van de werk rollen gelezen of geschreven. Welke werk nemer? Dat wordt volledig bepaald door de meta gegevens tabellen. De toewijzing van Shard aan worker wordt de Shard-plaatsing genoemd.

Het coördinator knooppunt schrijft query's opnieuw in fragmenten die verwijzen naar de specifieke tabellen zoals `github_events_102027` en voert deze fragmenten uit op de juiste werk rollen. Hier volgt een voor beeld van een query die wordt uitgevoerd achter de schermen om het knoop punt te vinden dat Shard ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Volgende stappen

- Het [type van de toepassing bepalen](concepts-hyperscale-app-type.md) om gegevens modellering voor te bereiden

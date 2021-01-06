---
title: Tabel grootte bepalen-grootschalige (Citus)-Azure Database for PostgreSQL
description: De werkelijke grootte van gedistribueerde tabellen in een grootschalige (Citus)-Server groep zoeken
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937612"
---
# <a name="determine-table-and-relation-size"></a>De grootte van de tabel en de relatie bepalen

De gebruikelijke manier om tabel grootten te vinden in PostgreSQL, `pg_total_relation_size` , aanzienlijk onder-rapporteert de grootte van gedistribueerde tabellen op grootschalige (Citus).
Al deze functie doet zich voor op een Citus-Server groep (grootschalige) om de grootte van de tabellen op het coördinator knooppunt te onthullen.  In werkelijkheid bevinden de gegevens in gedistribueerde tabellen zich op de worker-knoop punten (in Shards), niet op de coördinator. Een werkelijke meting van de grootte van een gedistribueerde tabel wordt verkregen als een som van Shard-grootten. Grootschalige (Citus) biedt hulp functies om deze informatie op te vragen.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Functie</th>
<th>Retouren</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Grootte van de werkelijke gegevens in de tabel (de '<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">hoofd Fork</a>').</li>
<li>Een relatie kan de naam van een tabel of index zijn.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>toewijzing van de grootte van de <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">vrije ruimte</a></li>
<li>grootte van de <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">zichtbaarheids kaart</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>grootte van indices</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Deze functies zijn vergelijkbaar met drie van de standaard functies voor PostgreSQL- [object grootte](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE), behalve als ze geen verbinding kunnen maken met een knoop punt, er een fout optreedt.

## <a name="example"></a>Voorbeeld

U kunt als volgt de grootten van alle gedistribueerde tabellen weer geven:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Uitvoer:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het schalen van een server groep](howto-hyperscale-scale-grow.md) om meer gegevens te bewaren.
* Onderscheid maken tussen [tabel typen](concepts-hyperscale-nodes.md) in een grootschalige (Citus)-Server groep.

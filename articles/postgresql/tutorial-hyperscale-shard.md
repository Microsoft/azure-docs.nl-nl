---
title: 'Zelfstudie: Shardgegevens op werkknooppunten - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: In deze zelfstudie ziet u hoe u gedistribueerde tabellen maakt en de bijbehorende gegevens visualiseert met Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: bc93c3643e329879e5118d1cfb61a356442df808
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618389"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Zelfstudie: Shardgegevens op werkknooppunten in Azure Database for PostgreSQL - Hyperscale (Citus)

In deze zelfstudie gebruikt u Azure Database for PostgreSQL - Hyperscale (Citus) om te leren hoe u de volgende bewerkingen uitvoert:

> [!div class="checklist"]
> * Shards met hashverdeling maken
> * Zien waar tabelshards worden geplaatst
> * Ongelijke distributie identificeren
> * Beperkingen voor gedistribueerde tabellen maken
> * Query's uitvoeren op gedistribueerde gegevens

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie is een actieve Hyperscale (Citus)-servergroep met twee werkknooppunten vereist. Als u geen actieve servergroep hebt, volgt u de zelfstudie [Servergroep maken](tutorial-hyperscale-server-group.md) en keert u vervolgens terug naar dit item.

## <a name="hash-distributed-data"></a>Gegevens met hashverdeling

Het distribueren van tabelrijen over verschillende PostgreSQL-servers is een belangrijke techniek voor schaalbare query's in Hyperscale (Citus). Meerdere knooppunten kunnen samen meer gegevens bevatten dan een traditionele database, en kunnen in veel gevallen werkrol-CPU's parallel gebruiken om query's uit te voeren.

In de sectie met vereisten hebben we een Hyperscale (Citus)-servergroep met twee werkknooppunten gemaakt.

![coördinator en twee werkrollen](tutorial-hyperscale-shard/nodes.png)

Met de tabellen met metagegevens van het coördinatorknooppunt kunnen werkrollen en gedistribueerde gegevens worden bijgehouden. We kunnen de actieve werkrollen controleren in de tabel [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Knooppuntnamen in Hyperscale (Citus) zijn interne IP-adressen in een virtueel netwerk. De werkelijke adressen die u ziet, kunnen afwijken.

### <a name="rows-shards-and-placements"></a>Rijen, shards en plaatsingen

Als u de CPU- en opslagresources van werkknooppunten wilt gebruiken, moet u de tabelgegevens distribueren over de hele servergroep.  Als u een tabel distribueert, wordt elke rij toegewezen aan een logische groep genaamd een *shard.* We gaan nu een tabel maken en deze distribueren:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Met Hyperscale (Citus) wordt elke rij toegewezen aan een shard op basis van de waarde in de *distributiekolom*. In dit geval is deze waarde opgegeven als `email`. Elke rij komt precies in één shard, en elke shard kan meerdere rijen bevatten.

![gebruikerstabel met rijen die verwijzen naar shards](tutorial-hyperscale-shard/table.png)

`create_distributed_table()` maakt standaard 32 shards, zoals we kunnen zien als we de metagegevenstabel [pg_dist_shard](reference-hyperscale-metadata.md#shard-table) meetellen:

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Hyperscale (Citus) maakt gebruik van de tabel `pg_dist_shard` om rijen toe te wijzen aan shards, op basis van een hash van de waarde in de distributiekolom. De hashdetails zijn niet belangrijk voor deze zelfstudie. Wat belangrijk is, is dat we een query kunnen uitvoeren om te zien welke waarden zijn toegewezen aan welke shard-id's:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Het toewijzen van rijen aan shards gebeurt alleen op basis van logica. Shards moeten worden toegewezen aan specifieke werkknooppunten voor opslag, in wat *shardplaatsing* wordt genoemd in Hyperscale (Citus).

![shards die zijn toegewezen aan werkrollen](tutorial-hyperscale-shard/shard-placement.png)

We kunnen de shardplaatsing in [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table) bekijken.
Door deze te koppelen aan de andere tabellen met metagegevens die we hebben gezien, wordt duidelijk waar elke shard zich bevindt.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Ongelijkheid in gegevens

De uitvoering van een servergroep is het meest efficiënt wanneer u gegevens gelijkmatig verdeeld over werkknooppunten, en wanneer u gerelateerde gegevens bij dezelfde werkrol plaatst. In deze sectie richten we ons op het eerste gedeelte, de uniformiteit van plaatsing.

Ter demonstratie maken we voorbeeldgegevens voor de tabel `users`:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Als we shardgrootten willen zien, kunnen we [functies voor tabelgrootte](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) uitvoeren voor de shards.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

We zien dat de shards even groot zijn. We hebben al gezien dat plaatsingen gelijkmatig zijn gedistribueerd over werkrollen. We kunnen dus afleiden dat de werkknooppunten ongeveer hetzelfde aantal rijen bevatten.

De rijen in ons voorbeeld `users` zijn gelijkmatig gedistribueerd vanwege eigenschappen van de distributiekolom `email`.

1. Het aantal e-mailadressen is groter dan of gelijk aan het aantal shards
2. Het aantal rijen per e-mailadres is vergelijkbaar (in ons geval is er precies één rij per adres, omdat we het e-mailadres in een sleutel hebben gedeclareerd)

Een willekeurige tabel en distributiekolom waarbij een van de eigenschappen mislukt, bevat uiteindelijk ongelijke gegevensgrootten in werkrollen, dit wordt *ongelijkheid in gegevens* genoemd.

### <a name="add-constraints-to-distributed-data"></a>Beperkingen toevoegen aan gedistribueerde gegevens

Met Hyperscale (Citus) kunt u profiteren van de veiligheid van een relationele database, inclusief [databasebeperkingen](https://www.postgresql.org/docs/current/ddl-constraints.html).
Er is echter een limiet. Vanwege de aard van gedistribueerde systemen wordt in Hyperscale (Citus) niet kruislings verwezen naar beperkingen van uniekheid of referentiële integriteit tussen werkknooppunten.

Laten we onze voorbeeldtabel `users` erbij nemen met een gerelateerde tabel.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

Voor efficiëntiedoeleinden hebben we `books` op dezelfde manier gedistribueerd als `users`: via het e-mailadres van de eigenaar. Distributie op basis van vergelijkbare kolomwaarden heet [colocatie](concepts-hyperscale-colocation.md).

We hebben boeken zonder problemen met een refererende sleutel gedistribueerd naar gebruikers, omdat de sleutel zich bij een distributiekolom bevond. We zouden echter problemen ondervinden om van `isbn` een sleutel te maken:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

In een gedistribueerde tabel is van de unieke modulo van kolommen de distributiekolom maken het beste wat we kunnen doen:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Met de bovenstaande beperking wordt alleen de ISBN uniek gemaakt per gebruiker. Een andere optie is om van boeken een [referentietabel](howto-hyperscale-modify-distributed-tables.md#reference-tables) te maken, in plaats van een gedistribueerde tabel, en een afzonderlijke gedistribueerde tabel te maken die boeken koppelt aan gebruikers.

## <a name="query-distributed-tables"></a>Query uitvoeren op gedistribueerde tabellen

In de vorige secties hebben we gezien hoe gedistribueerde tabelrijen in shards worden geplaatst op werkknooppunten. Meestal hoeft u niet te weten hoe of waar gegevens zijn opgeslagen in een servergroep. Hyperscale (Citus) heeft een gedistribueerde query-uitvoerder waarmee normale SQL-query's automatisch worden opgesplitst. De query's worden parallel uitgevoerd op werkknooppunten dicht bij de gegevens.

We kunnen bijvoorbeeld een query uitvoeren om de gemiddelde leeftijd van gebruikers te vinden, waarbij we de gedistribueerde tabel `users` behandelen alsof het een normale tabel is in de coördinator.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![query op shards via de coördinator](tutorial-hyperscale-shard/query-fragments.png)

Achter de schermen wordt met de Hyperscale (Citus)-uitvoerder een afzonderlijke query gemaakt voor elke shard. Deze wordt uitgevoerd op de werkrollen en het resultaat wordt gecombineerd. U kunt deze zien met behulp van de PostgreSQL-opdracht EXPLAIN:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

De uitvoer toont een voorbeeld van een uitvoeringsplan voor een *queryfragment* dat wordt uitgevoerd voor shard 102040 (de tabel `users_102040` op werkrol 10.0.0.21). De andere fragmenten worden niet weergegeven omdat ze vergelijkbaar zijn. We zien dat op het werkknooppunt de shardtabellen worden gescand en de samenvoeging toegepast. Op het coördinatorknooppunt worden samenvoegingen gecombineerd voor het uiteindelijke resultaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een gedistribueerde tabel gemaakt, en informatie gekregen over de bijbehorende shards en plaatsingen. U hebt gezien dat het gebruik van beperkingen voor uniekheid en refererende sleutels een uitdaging vormen, en ten slotte hoe gedistribueerde query's werken op hoog niveau.

* Meer informatie over Hyperscale (Citus)-[tabeltypen](concepts-hyperscale-nodes.md)
* Krijg meer tips over het [kiezen van een distributiekolom](concepts-hyperscale-choose-distribution-column.md)
* Leer wat de voordelen zijn van [colocatie van tabellen](concepts-hyperscale-colocation.md)

---
title: Systeem tabellen – grootschalige (Citus)-Azure Database for PostgreSQL
description: Meta gegevens voor uitvoering van gedistribueerde query's
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652845"
---
# <a name="system-tables-and-views"></a>Systeem tabellen en-weer gaven

Grootschalige (Citus) maakt en onderhoudt speciale tabellen die informatie bevatten over gedistribueerde gegevens in de Server groep. Het coördinator knooppunt raadpleegt deze tabellen bij het plannen van het uitvoeren van query's op de worker-knoop punten.

## <a name="coordinator-metadata"></a>Meta gegevens van coördinator

Grootschalige (Citus) verdeelt elke gedistribueerde tabel in meerdere logische Shards op basis van de distributie kolom. De coördinator onderhoudt vervolgens meta gegevens tabellen om statistieken en informatie over de status en locatie van deze Shards bij te houden.

In deze sectie beschrijven we elk van deze meta gegevens tabellen en hun bijbehorende schema's.
U kunt deze tabellen weer geven en er query's op uitvoeren met SQL nadat u zich hebt aangemeld bij het coördinator knooppunt.

> [!NOTE]
>
> Grootschalige (Citus)-Server groepen waarop oudere versies van de Citus-engine worden uitgevoerd, bieden mogelijk niet alle tabellen die hieronder worden weer gegeven.

### <a name="partition-table"></a>Partitie tabel

De \_ tabel PG dist \_ Partition bevat meta gegevens over welke tabellen in de Data Base worden gedistribueerd. Voor elke gedistribueerde tabel wordt ook informatie over de distributie methode en gedetailleerde informatie over de distributie kolom opgeslagen.

| Naam         | Type     | Beschrijving                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | De gedistribueerde tabel waaraan deze rij overeenkomt. Deze waarde verwijst naar de kolom relfilenode in de tabel pg_class systeem catalogus.                                                                                                                   |
| partmethod   | char     | De methode die wordt gebruikt voor partitionering/distributie. De waarden van deze kolom die overeenkomen met verschillende distributie methoden worden toegevoegd: ' a ', hash: ' h ', referentie tabel: ' n '                                                                          |
| partkey      | tekst     | Gedetailleerde informatie over de kolom distributie, met inbegrip van kolom nummer, type en andere relevante informatie.                                                                                                                                      |
| colocationid | geheel getal  | De groep met co-locaties waarvan deze tabel deel uitmaakt. Met tabellen in dezelfde groep kunnen gelijktijdige samen voegingen en gedistribueerde rollups onder andere optimalisaties worden uitgevoerd. Deze waarde verwijst naar de kolom colocationid in de tabel pg_dist_colocation.                      |
| repmodel     | char     | De methode die wordt gebruikt voor gegevens replicatie. De waarden van deze kolom die overeenkomen met verschillende replicatie methoden zijn: Citus op basis van een instructie: ' c ', postgresql streaming-replicatie: ', twee fasen door voeren (voor referentie tabellen): 't ' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Shard-tabel

De \_ tabel pag dist \_ Shard bevat meta gegevens over afzonderlijke Shards van een tabel. Pg_dist_shard bevat informatie over de Shards van de gedistribueerde tabel en statistieken over de kolom distributie voor Shards.
Voor het toevoegen van gedistribueerde tabellen komen deze statistieken overeen met de minimum-en maximum waarden van de distributie kolom. Voor gedistribueerde hash-tabellen zijn dit hash-token bereik dat aan die Shard is toegewezen. Deze statistieken worden gebruikt voor het weggooien van ongerelateerde Shards tijdens SELECT-query's.

| Naam          | Type     | Beschrijving                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | De gedistribueerde tabel waaraan deze rij overeenkomt. Deze waarde verwijst naar de kolom relfilenode in de tabel pg_class systeem catalogus.                                                          |
| shardid       | bigint   | Wereld wijd unieke id die is toegewezen aan deze Shard.                                                                                                                                           |
| shardstorage  | char     | Type opslag dat wordt gebruikt voor deze Shard. In de onderstaande tabel worden verschillende opslag typen besproken.                                                                                               |
| shardminvalue | tekst     | Voor het toevoegen van gedistribueerde tabellen, minimale waarde van de kolom distributie in deze Shard (inclusief). Voor hash-gedistribueerde tabellen is de minimale hash-token waarde toegewezen aan die Shard (inclusief). |
| shardmaxvalue | tekst     | Voor het toevoegen van gedistribueerde tabellen, de maximum waarde van de kolom distributie in deze Shard (inclusief). Voor hash-gedistribueerde tabellen is de maximale hash-token waarde toegewezen aan die Shard (inclusief). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Shard-opslag typen

De kolom shardstorage in PG \_ dist \_ Shard geeft het type opslag dat wordt gebruikt voor de Shard. Hieronder vindt u een beknopt overzicht van de verschillende Shard-opslag typen en de bijbehorende representatie.

| Opslagtype | Waarde Shardstorage | Beschrijving                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABEL        | t                | Geeft aan dat Shard gegevens opslaat die horen bij een normale gedistribueerde tabel.         |
| KOLOM     | g                | Hiermee wordt aangegeven dat in Shard kolom gegevens worden opgeslagen. (Gebruikt door gedistribueerde cstore_fdw tabellen) |
| BUITENLAND      | ls                | Geeft aan dat Shard afwijkende gegevens opslaat. (Gebruikt door gedistribueerde file_fdw tabellen)    |

### <a name="shard-placement-table"></a>Tabel met Shard-plaatsing

De \_ tabel pag dist \_ placement houdt de locatie van Shard-replica's op worker-knoop punten bij. Elke replica van een Shard die is toegewezen aan een specifiek knoop punt, wordt een Shard-plaatsing genoemd. Deze tabel bevat informatie over de status en locatie van elke Shard-plaatsing.

| Naam        | Type   | Beschrijving                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | De Shard-id die is gekoppeld aan deze plaatsing. Deze waarde verwijst naar de kolom shardid in de tabel pg_dist_shard Catalog.             |
| shardstate  | int    | Hiermee wordt de status van deze plaatsing beschreven. In de volgende sectie worden verschillende Shard-statussen besproken.                                         |
| shardlength | bigint | Voor het toevoegen van gedistribueerde tabellen, de grootte van de Shard-plaatsing op het worker-knoop punt in bytes. Voor hash-gedistribueerde tabellen is nul.            |
| placementid | bigint | Unieke, automatisch gegenereerde id voor elke afzonderlijke plaatsing.                                                                           |
| groep     | int    | Hiermee wordt een groep van één primaire server en nul of meer secundaire servers aangegeven wanneer het replicatie model voor streaming wordt gebruikt. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Shard

Grootschalige (Citus) beheert de status van Shard per locatie. Als een plaatsing het systeem in een inconsistente status plaatst, wordt deze door Citus automatisch gemarkeerd als niet-beschikbaar. De plaatsings status wordt vastgelegd in de tabel pg_dist_shard_placement in de kolom shardstate. Hier volgt een beknopt overzicht van verschillende Shard-plaatsings statussen:

| Status naam | Waarde Shardstate | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VOLTOOID  | 1                | De status van de nieuwe Shards wordt gemaakt in. Shard-plaatsingen in deze status worden beschouwd als up-to-date en worden gebruikt bij het plannen en uitvoeren van query's.                                                                                                                                                                                                                                                                                 |
| INACTIEVE   | 3                | Shard-plaatsingen in deze status worden als inactief beschouwd omdat deze niet meer synchroon zijn met andere replica's van dezelfde Shard. De status kan optreden als het toevoegen, wijzigen (invoegen, bijwerken, verwijderen) of een DDL-bewerking mislukt voor deze plaatsing. De query planner negeert plaatsingen in deze status tijdens de planning en uitvoering. Gebruikers kunnen de gegevens in deze Shards synchroniseren met een voltooide replica als een achtergrond activiteit. |
| TO_DELETE  | 4                | Als Citus een Shard-plaatsing probeert te verwijderen als reactie op een master_apply_delete_command aanroep en mislukt, wordt de plaatsing verplaatst naar deze status. Gebruikers kunnen deze Shards vervolgens verwijderen als een volgende achtergrond activiteit.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabel worker-knoop punt

De \_ tabel pag dist- \_ knoop punt bevat informatie over de worker-knoop punten in het cluster.

| Naam             | Type    | Beschrijving                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeId           | int     | Automatisch gegenereerde id voor een afzonderlijk knoop punt.                                                                                                                                          |
| groep          | int     | Id die wordt gebruikt om een groep van één primaire server en nul of meer secundaire servers aan te duiden wanneer het replicatie model voor streaming wordt gebruikt. Dit is standaard hetzelfde als het nodeId.         |
| nodename         | tekst    | De hostnaam of het IP-adres van het PostgreSQL worker-knoop punt.                                                                                                                                     |
| nodeport         | int     | Het poort nummer waarop het PostgreSQL-werk knooppunt luistert.                                                                                                                              |
| noderack         | tekst    | Beschrijving Informatie over de plaatsing van rekken voor het worker-knoop punt.                                                                                                                                 |
| hasmetadata      | booleaans | Gereserveerd voor intern gebruik.                                                                                                                                                                 |
| IsActive         | booleaans | Hiermee wordt aangegeven of het knoop punt actief Shard plaatsen accepteert.                                                                                                                                     |
| noderole         | tekst    | Of het knoop punt een primair of secundair is                                                                                                                                                 |
| nodecluster      | tekst    | De naam van het cluster dat dit knoop punt bevat                                                                                                                                               |
| shouldhaveshards | booleaans | Als false is ingesteld, wordt Shards buiten het knoop punt verplaatst tijdens het herverdelen, en worden Shards van nieuwe gedistribueerde tabellen niet op het knoop punt geplaatst, tenzij ze zijn gekoppeld met Shards al |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabel met gedistribueerde objecten

De \_ tabel Citus.pg dist \_ object bevat een lijst met objecten zoals typen en functies die zijn gemaakt op het coördinator knooppunt en door gegeven aan worker-knoop punten. Wanneer een beheerder nieuwe worker-knoop punten aan het cluster toevoegt, maakt grootschalige (Citus) automatisch kopieën van de gedistribueerde objecten op de nieuwe knoop punten (in de juiste volg orde om aan object afhankelijkheden te voldoen).

| Naam                        | Type    | Beschrijving                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | nogmaals     | Klasse van het gedistribueerde object                      |
| object                       | nogmaals     | Object-ID van het gedistribueerde object                  |
| objsubid                    | geheel getal | De object SubID van het gedistribueerde object, bijvoorbeeld attnum |
| type                        | tekst    | Onderdeel van het stabiele adres dat wordt gebruikt tijdens het bijwerken van pagina's   |
| object_names                | tekst []  | Onderdeel van het stabiele adres dat wordt gebruikt tijdens het bijwerken van pagina's   |
| object_args                 | tekst []  | Onderdeel van het stabiele adres dat wordt gebruikt tijdens het bijwerken van pagina's   |
| distribution_argument_index | geheel getal | Alleen geldig voor gedistribueerde functies/procedures      |
| colocationid                | geheel getal | Alleen geldig voor gedistribueerde functies/procedures      |

\"Stabiele adressen \" identificeren objecten onafhankelijk van een specifieke server. Grootschalige (Citus) traceert objecten tijdens een PostgreSQL-upgrade met behulp van stabiele adressen die zijn gemaakt met de functie [ \_ \_ object identificeren \_ als \_ adres ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) van de pagina.

Hier volgt \' een voor beeld van hoe `create_distributed_function()` vermeldingen aan de `citus.pg_dist_object` tabel worden toegevoegd:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabel met co-locatie groep

De \_ tabel pag dist co \_ -locatie bevat informatie over welke tabellen \' Shards moeten worden geplaatst of moeten [](concepts-hyperscale-colocation.md)worden gecombineerd.
Wanneer twee tabellen zich in dezelfde groep grootschalige (Citus) bevinden, zorgt u ervoor dat Shards met dezelfde partitie waarden op dezelfde worker-knoop punten wordt geplaatst.
Met co-locatie kunt u deel nemen aan optimalisaties, bepaalde gedistribueerde rollups en ondersteuning voor refererende sleutels. De Shard-co-locatie wordt afgeleid wanneer het Shard telt, replicatie factoren en partitie kolom typen allemaal overeenkomen tussen twee tabellen; u kunt echter een aangepaste groep voor samen stellen opgeven wanneer u een gedistribueerde tabel wilt maken, indien gewenst.

| Naam                   | Type | Beschrijving                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | De unieke id voor de groep met de co-locatie waarmee deze rij overeenkomt.          |
| shardcount             | int  | Aantal Shard voor alle tabellen in deze groep met co-locaties                          |
| replicationfactor      | int  | Replicatie factor voor alle tabellen in deze groep met co-locaties.                  |
| distributioncolumntype | nogmaals  | Het type van de distributie kolom voor alle tabellen in deze groep met co-locaties. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Strategie tabel van de herbalancer

In deze tabel worden strategieën gedefinieerd die [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) kunnen gebruiken om te bepalen waar Shards moet worden verplaatst.

| Naam                           | Type    | Beschrijving                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | booleaans | Hiermee wordt aangegeven of rebalance_table_shards standaard deze strategie moet kiezen. Citus_set_default_rebalance_strategy gebruiken om deze kolom bij te werken             |
| shard_cost_function            | regproc | Id voor een functie van kosten, die een shardid moet hebben als bigint, en het principe van een kosten als type Real te retour neren                                |
| node_capacity_function         | regproc | Id voor een capaciteits functie, die een nodeId moet maken als int en het begrip van de capaciteit van het knoop punt als het type werkelijk wilt retour neren                          |
| shard_allowed_on_node_function | regproc | Id voor een functie die shardid bigint heeft gegeven en nodeidarg int retourneert een Booleaanse waarde voor of Citus de Shard op het knoop punt mag opslaan |
| default_threshold              | float4  | De drempel waarde voor het gebruik van een knoop punt is te vol of te leeg, wat bepaalt wanneer het rebalance_table_shards Shards moet verplaatsen                    |
| minimum_threshold              | float4  | Een beveiliging om te voor komen dat het argument van de drempel waarde van rebalance_table_shards () te laag wordt ingesteld                                                  |

Een grootschalige (Citus)-installatie wordt geleverd met de volgende strategieën in de tabel:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

De standaard strategie, `by_shard_count` , wijst elke Shard dezelfde kosten toe. Het effect hiervan is het egaliseren van het aantal Shard tussen knoop punten. Met de andere vooraf gedefinieerde strategie worden de `by_disk_size` kosten toegewezen aan elke Shard die overeenkomt met de grootte van de schijf in bytes plus die van de Shards die ermee zijn verbonden. De schijf grootte wordt berekend op basis van `pg_total_relation_size` , dus bevat indices. Deze strategie probeert op elk knoop punt dezelfde schijf ruimte te krijgen. Let op de drempel waarde van 0,1. dit voor komt onnodige Shard-verplaatsing, veroorzaakt door onbeduidende verschillen in schijf ruimte.

#### <a name="creating-custom-rebalancer-strategies"></a>Aangepaste strategieën voor de herbalancer maken

Hier volgen enkele voor beelden van functies die kunnen worden gebruikt in nieuwe Shard-strategieën en die zijn geregistreerd in de [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) met de functie [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Patroon voor uitzonde ring van knooppunt capaciteit instellen op hostnaam:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Herverdeling op basis van het aantal query's dat naar een Shard gaat, zoals gemeten door de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Het isoleren van een specifieke Shard (10000) in een knoop punt (adres \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Query statistieken tabel

Grootschalige (Citus) biedt `citus_stat_statements` statistieken over hoe query's worden uitgevoerd en voor wie. Dit is \' vergelijkbaar met (en kan worden gekoppeld aan) de weer gave [PG \_ stat- \_ instructies](https://www.postgresql.org/docs/current/static/pgstatstatements.html) in postgresql, waarmee statistieken over de query snelheid worden bijgehouden.

Met deze weer gave kunt u query's traceren voor de oorspronkelijke tenants in een toepassing met meerdere tenants, waarmee u kunt bepalen wanneer Tenant isolatie moet worden uitgevoerd.

| Naam          | Type   | Beschrijving                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | id (geschikt voor pg_stat_statements deelname)                                   |
| userid        | nogmaals    | gebruiker die de query heeft uitgevoerd                                                           |
| dbid          | nogmaals    | data base-exemplaar van coördinator                                                 |
| query         | tekst   | query reeks geanonimiseerd                                                          |
| Voer      | tekst   | Citus-uitvoerder gebruikt: Adaptief, realtime, taak tracering, router of INSERT-SELECT |
| partition_key | tekst   | waarde van de kolom distributie in query's die door de router worden uitgevoerd, else NULL               |
| rpc's         | bigint | aantal keren dat de query is uitgevoerd                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Resultaten:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Waarschuwingen

-   De gegevens over de statistieken worden niet gerepliceerd en er zijn nog geen \' data bases of failover van het database systeem binnengehaald
-   Houdt een beperkt aantal query's bij, ingesteld door de `pg_stat_statements.max` GUC (standaard 5000)
-   Als u de tabel wilt afkappen, gebruikt u de `citus_stat_statements_reset()` functie

### <a name="distributed-query-activity"></a>Gedistribueerde query activiteit

Grootschalige (Citus) biedt speciale weer gaven voor het bekijken van query's en vergren delingen in het hele cluster, waaronder Shard-specifieke query's die intern worden gebruikt voor het bouwen van resultaten voor gedistribueerde query's.

-   **Citus \_ dist \_ stat- \_ activiteit**: toont de gedistribueerde query's die op alle knoop punten worden uitgevoerd. Een superset van `pg_stat_activity` , bruikbare waar de laatste is.
-   **Citus \_ worker- \_ \_ activiteit**: toont query's op werk rollen, inclusief fragment query's voor afzonderlijke Shards.
-   **\_ \_ wacht tijden voor Citus-vergren deling**: geblokkeerde query's in het cluster.

De eerste twee weer gaven bevatten alle kolommen met de [PG \_ stat- \_ activiteit](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) plus de host/poort van de werk nemer die de query heeft gestart en de host/poort van het coördinator knooppunt van het cluster.

U kunt bijvoorbeeld de rijen in een gedistribueerde tabel tellen:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

De query wordt weer gegeven in `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Voor deze query is informatie van alle Shards vereist. Sommige informatie is in Shard `users_table_102038` , die in wordt opgeslagen `localhost:9700` . We kunnen een query zien die toegang heeft tot de Shard door de `citus_worker_stat_activity` weer gave te bekijken:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

In het veld worden de `query` gegevens weer gegeven die worden gekopieerd uit de Shard die moeten worden geteld.

> [!NOTE]
> Als een router query (bijvoorbeeld één Tenant in een toepassing met meerdere tenants) selecteert u
> * VAN de tabel waar tenant_id = X ') wordt uitgevoerd zonder een transactie blok, worden de kolommen van de hoofd \_ query- \_ hostnaam \_ en de hoofd \_ query \_ hosten \_ Null in de Citus- \_ werk nemer \_ stat- \_ activiteit.

Om te zien hoe `citus_lock_waits` Works werkt, kunnen we hand matig een vergrendelings situatie genereren. Eerst hebben we \' een test tabel ingesteld op basis van de coördinator:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Daarna kunt u met twee sessies op de coördinator deze volg orde van de instructies uitvoeren:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

In de `citus_lock_waits` weer gave wordt de situatie weer gegeven.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

In dit voor beeld zijn de query's afkomstig van de coördinator, maar de weer gave kan ook de vergren deling weer geven tussen query's die afkomstig zijn van werk rollen (uitgevoerd met grootschalige (Citus) MX voor instantie).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over hoe sommige [grootschalige (Citus)-functies](reference-hyperscale-functions.md) systeem tabellen wijzigen
* Bekijk de concepten van [knoop punten en tabellen](concepts-hyperscale-nodes.md)

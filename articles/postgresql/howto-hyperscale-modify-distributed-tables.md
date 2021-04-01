---
title: Gedistribueerde tabellen wijzigen-grootschalige (Citus)-Azure Database for PostgreSQL
description: SQL-opdrachten voor het maken en wijzigen van gedistribueerde tabellen-grootschalige (Citus) met behulp van de Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026434"
---
# <a name="distribute-and-modify-tables"></a>Tabellen distribueren en bewerken

## <a name="distributing-tables"></a>Tabellen distribueren

Als u een gedistribueerde tabel wilt maken, moet u eerst het tabel schema definiëren. U kunt dit doen door een tabel te definiëren met behulp van de [Create Table](http://www.postgresql.org/docs/current/static/sql-createtable.html) -instructie op dezelfde manier als met een gewone postgresql-tabel.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Vervolgens kunt u de \_ functie gedistribueerde \_ tabel maken () gebruiken om de kolom tabel distributie op te geven en de worker-Shards te maken.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

De functie aanroep informeert grootschalige (Citus) dat de \_ tabel github Events moet worden gedistribueerd in de \_ kolom opslag plaats id (door hashing van de kolom waarde). Met de functie wordt ook Shards gemaakt op de worker-knoop punten met behulp van de Citus. Shard \_ Count-en Citus. Shard- \_ configuratie waarden voor de replicatie \_ factor.

Hiermee maakt u een totaal van Citus. Shard aantal \_ Shards, waarbij elke Shard eigenaar is van een deel van een hash-ruimte en wordt gerepliceerd op basis van de standaard waarde voor de configuratie factor Citus. Shard voor \_ replicatie \_ . De Shard-replica's die zijn gemaakt op de werk nemer hebben hetzelfde tabel schema, dezelfde index en beperkings definities als de tabel op de coördinator. Zodra de replica's zijn gemaakt, slaat de functie alle gedistribueerde meta gegevens op in de coördinator.

Aan elke gemaakte Shard wordt een unieke Shard-ID toegewezen en alle replica's hebben dezelfde Shard-ID. Shards worden op het worker-knoop punt weer gegeven als gewone PostgreSQL-tabellen met de naam \' TableName \_ shardid \' , waarbij TableName de naam van de gedistribueerde tabel is, en Shard-ID de unieke id is toegewezen. U kunt verbinding maken met de post gres instanties van de werk nemer om opdrachten te bekijken of uit te voeren op afzonderlijke Shards.

U bent nu klaar om gegevens in te voegen in de gedistribueerde tabel en query's uit te voeren. U kunt ook meer te weten komen over de UDF die in deze sectie wordt gebruikt in de [tabel-en SHARD DDL](reference-hyperscale-functions.md#table-and-shard-ddl) -verwijzing.

### <a name="reference-tables"></a>Verwijzings tabellen

Met de bovenstaande methode worden tabellen gedistribueerd naar meerdere horizontale Shards.  Een andere mogelijkheid is om tabellen te distribueren in één Shard en de Shard naar elk worker-knoop punt te repliceren. Tabellen die op deze manier worden gedistribueerd, worden *referentie tabellen genoemd.* Ze worden gebruikt voor het opslaan van gegevens die regel matig moeten worden geopend door meerdere knoop punten in een cluster.

Algemene kandidaten voor referentie tabellen zijn onder andere:

-   Kleinere tabellen die moeten worden gekoppeld aan grotere gedistribueerde tabellen.
-   Tabellen in apps met meerdere tenants die geen kolom Tenant-ID of zijn \' t koppelen aan een Tenant. (Of tijdens migratie, zelfs voor sommige tabellen die aan een Tenant zijn gekoppeld.)
-   Tabellen die unieke beperkingen nodig hebben in meerdere kolommen en die klein genoeg zijn.

Stel bijvoorbeeld dat een multi tenant-site voor meerdere pachters BTW moet berekenen voor trans acties in een van de winkels. Belasting gegevens hebben \' t specifiek voor elke Tenant. Het is zinvol om deze in een gedeelde tabel te plaatsen. Een Amerikaanse naslag tabel kan er als volgt uitzien:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Query's, zoals het berekenen van de belasting voor een winkel wagen, kunnen worden toegevoegd aan de `states` tabel zonder netwerk overhead en kunnen een refererende sleutel toevoegen aan de status code voor een betere validatie.

Naast het distribueren van een tabel als één gerepliceerd Shard, `create_reference_table` markeert de UDF deze als een verwijzings tabel in de meta gegevens tabellen van grootschalige (Citus). Grootschalige (Citus) voert automatisch tweephase door voeringen ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) uit voor wijzigingen in tabellen die op deze manier zijn gemarkeerd. Dit biedt krachtige consistentie garanties.

Als u een gedistribueerde tabel hebt met een Shard aantal van één, kunt u deze bijwerken naar een herkende verwijzings tabel als volgt:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Zie de [zelf studie multi tenant-data base](tutorial-design-database-hyperscale-multi-tenant.md)voor een ander voor beeld van het gebruik van verwijzings tabellen.

### <a name="distributing-coordinator-data"></a>Coördinator gegevens distribueren

Als een bestaande PostgreSQL-data base wordt geconverteerd naar het coördinator knooppunt voor een grootschalige-cluster (Citus), kunnen de gegevens in de tabellen op efficiënte wijze worden gedistribueerd en met een minimale onderbreking voor een toepassing.

De `create_distributed_table` functie die eerder is beschreven, werkt zowel in lege als niet-lege tabellen, en voor de laatste worden tabel rijen automatisch in het cluster gedistribueerd. U weet zeker dat er gegevens worden gekopieerd door de aanwezigheid van het bericht, \" Let op: gegevens kopiëren uit lokale tabel \. ... \" Bijvoorbeeld:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Schrijf bewerkingen voor de tabel worden geblokkeerd wanneer de gegevens worden gemigreerd en wachtende schrijf bewerkingen worden verwerkt als gedistribueerde query's zodra de functie is door gegeven. (Als de functie mislukt, worden de query's opnieuw lokaal.) Lees bewerkingen kunnen als normaal blijven functioneren en worden gedistribueerde query's zodra de functie wordt door gegeven.

Bij het distribueren van tabellen A en B, waarbij een refererende sleutel is voor B, moet u eerst de sleutel doel tabel B distribueren. Als u het in de verkeerde volg orde uitvoert, treedt er een fout op:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Als het niet mogelijk is om in de juiste volg orde te distribueren, verwijdert u de refererende sleutels, distribueert u de tabellen en maakt u de refererende sleutels opnieuw.

Wanneer u gegevens migreert vanuit een externe data base, zoals van Amazon RDS naar grootschalige (Citus), maakt u eerst de gedistribueerde grootschalige-(Citus)-tabellen via `create_distributed_table` en kopieert u de gegevens naar de tabel.
Het kopiëren naar gedistribueerde tabellen voor komt dat er bijna geen ruimte meer is op het coördinator knooppunt.

## <a name="colocating-tables"></a>Tabellen worden verplaatst

Co-locatie betekent het plaatsen van verwante informatie op dezelfde machines. Hiermee kunnen efficiënte query's worden uitgevoerd, terwijl de horizontale schaal baarheid voor de hele gegevensset wordt benut. Zie voor meer informatie co- [locatie](concepts-hyperscale-colocation.md).

Tabellen worden in groepen opgenomen. Als u de groeps toewijzing voor de colocatie van een tabel hand matig wilt beheren, gebruikt u de optionele `colocate_with` para meter van `create_distributed_table` . Als u \' geen andere locatie van een tabel \' s wilt, dan laat u deze para meter weg. De standaard instelling is de waarde `'default'` , waarmee de tabel wordt gegroepeerd met een andere tabel met een andere standaard locatie, met hetzelfde type voor de distributie kolom, het aantal Shard en de replicatie factor. Als u deze impliciete locatie wilt verstoren of bijwerken, kunt u gebruiken `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Als een nieuwe tabel niet is gerelateerd aan anderen in de groep impliciete groepen, geeft u op `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Het splitsen van niet-gerelateerde tabellen in hun eigen groep met co-locaties verbetert de [Shard](howto-hyperscale-scale-rebalance.md) -prestaties, omdat Shards in dezelfde groep samen moeten worden verplaatst.

Wanneer tabellen inderdaad verwant zijn (bijvoorbeeld wanneer ze worden gekoppeld), kan het zinvol zijn om ze expliciet te verplaatsen. De toename van de juiste co-locatie is belang rijker dan de overhead van de herverdeling.

Als u meerdere tabellen expliciet wilt verplaatsen, distribueert u er een en plaatst u de andere in de bijbehorende groep. Bijvoorbeeld:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Informatie over co-locatie groepen wordt opgeslagen in de tabel [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) , terwijl [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) laat zien welke tabellen aan welke groepen zijn toegewezen.

## <a name="dropping-tables"></a>Tabellen verwijderen

U kunt de standaard opdracht PostgreSQL DROP TABLE gebruiken om uw gedistribueerde tabellen te verwijderen. Net als bij normale tabellen verwijdert DROP TABLE alle indexen, regels, triggers en beperkingen die voor de doel tabel bestaan. Daarnaast wordt de Shards op de worker-knoop punten neergezet en worden de meta gegevens opgeschoond.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Tabellen wijzigen

Met grootschalige (Citus) worden veel soorten DDL-instructies automatisch door gegeven.
Als u een gedistribueerde tabel op het coördinator knooppunt wijzigt, worden Shards ook op de werk nemers bijgewerkt. Andere DDL-instructies vereisen hand matige doorgifte en bepaalde andere worden niet toegestaan, zoals een distributie kolom.
Als u probeert DDL uit te voeren die niet in aanmerking komt voor automatische doorgifte, wordt er een fout gegenereerd en worden de tabellen op het coördinator knooppunt ongewijzigd gelaten.

Hier volgt een verwijzing naar de categorieën DDL-instructies die worden door gegeven.
Automatische doorgifte kan worden ingeschakeld of uitgeschakeld met een [configuratie parameter](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Kolommen toevoegen/wijzigen

Met grootschalige (Citus) worden de meeste [gewijzigde tabel](https://www.postgresql.org/docs/current/static/ddl-alter.html) opdrachten automatisch door gegeven. Het toevoegen van kolommen of het wijzigen van de standaard waarden werkt zoals in een PostgreSQL-data base met één computer:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Er zijn belang rijke wijzigingen in een bestaande kolom, zoals het wijzigen van de naam of het gegevens type ervan. Het gegevens type van de [kolom distributie](concepts-hyperscale-nodes.md#distribution-column) kan echter niet worden gewijzigd.
Deze kolom bepaalt hoe tabel gegevens worden gedistribueerd via het grootschalige (Citus)-cluster en het gegevens type moet worden gewijzigd.

Als u dit probeert te doen, treedt er een fout op:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Beperkingen toevoegen/verwijderen

Met grootschalige (Citus) kunt u de veiligheid van een relationele data base blijven gebruiken, inclusief database beperkingen (Zie de PostgreSQL [docs](https://www.postgresql.org/docs/current/static/ddl-constraints.html)).
Vanwege de aard van gedistribueerde systemen, hebben grootschalige (Citus) geen referentie beperkings beperkingen of referentiële integriteit tussen werk knooppunten.

Als u een refererende sleutel wilt instellen tussen naast elkaar gedistribueerde tabellen, neemt u altijd de kolom distributie op in de sleutel. Met inbegrip van de distributie kolom, kan de sleutel verbinding worden gemaakt.

In deze situaties kunnen refererende sleutels worden gemaakt:

-   tussen twee lokale (niet-gedistribueerde) tabellen,
-   tussen twee referentie tabellen,
-   tussen twee [naast](concepts-hyperscale-colocation.md) elkaar gedistribueerde tabellen wanneer de sleutel de kolom distributie bevat, of
-   Als een gedistribueerde tabel die verwijst naar een [verwijzings tabel](concepts-hyperscale-nodes.md#type-2-reference-tables)

Refererende sleutels van verwijzings tabellen naar gedistribueerde tabellen worden niet ondersteund.

> [!NOTE]
>
> Primaire sleutels en uniekheids beperkingen moeten de kolom distributie bevatten. Door ze toe te voegen aan een niet-distributie kolom, wordt een fout gegenereerd

In dit voor beeld ziet u hoe u primaire en refererende sleutels maakt in gedistribueerde tabellen:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

U kunt ook de kolom distributie in beperkingen van uniekheid toevoegen:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Niet-null-beperkingen kunnen worden toegepast op elke kolom (distributie of niet) omdat er geen lookups tussen werk nemers nodig zijn.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>GEEN geldige beperkingen gebruiken

In sommige gevallen kan het handig zijn om beperkingen voor nieuwe rijen af te dwingen, terwijl bestaande niet-conforme rijen ongewijzigd blijven. Grootschalige (Citus) ondersteunt deze functie voor controle beperkingen en refererende sleutels, met behulp van PostgreSQL \' s is \" geen geldige \" beperkings aanwijzing.

Denk bijvoorbeeld aan een toepassing waarin gebruikers profielen worden opgeslagen in een [verwijzings tabel](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

In de loop van de tijd dat enkele niet-adressen in de tabel worden weer geven.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

We willen graag de adressen valideren, maar PostgreSQL staat Norma liter niet toe om een controle beperking toe te voegen die voor bestaande rijen mislukt. Dit betekent echter wel dat een beperking *is* gemarkeerd als ongeldig:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Nieuwe rijen zijn nu beveiligd.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Later kan een database beheerder tijdens niet-piek uren proberen om de ongeldige rijen te herstellen en de beperking opnieuw te valideren.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

De PostgreSQL-documentatie bevat meer informatie over ongeldige en validatie beperkingen in de sectie [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Indices toevoegen/verwijderen

Grootschalige (Citus) ondersteunt het toevoegen en verwijderen van [indices](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Bij het toevoegen van een index wordt een schrijf vergrendeling uitgevoerd. Dit kan ongewenst zijn in een \" systeem-of-record met meerdere tenants. \" Als u de uitval tijd van toepassingen wilt minimaliseren, maakt u de index in plaats daarvan [tegelijk](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Voor deze methode is meer werk in totaal vereist dan het bouwen van een standaard index en het duurt langer om het proces te volt ooien. Omdat het echter mogelijk is dat normale bewerkingen worden voortgezet terwijl de index is gebouwd, is deze methode handig voor het toevoegen van nieuwe indexen in een productie omgeving.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```

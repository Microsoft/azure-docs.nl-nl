---
title: SQL-functies – grootschalige (Citus)-Azure Database for PostgreSQL
description: Functies in de grootschalige (Citus) SQL-API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026230"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Functies in de grootschalige (Citus) SQL-API

Deze sectie bevat referentie-informatie voor de door de gebruiker gedefinieerde functies van grootschalige (Citus). Met deze functies kunt u extra gedistribueerde functionaliteit bieden aan grootschalige (Citus) dan de standaard-SQL-opdrachten.

> [!NOTE]
>
> Grootschalige (Citus)-Server groepen waarop oudere versies van de Citus-engine worden uitgevoerd, bieden mogelijk niet alle onderstaande functies.

## <a name="table-and-shard-ddl"></a>DDL voor tabel en Shard

### <a name="create_distributed_table"></a>\_gedistribueerde \_ tabel maken

De \_ functie gedistribueerde \_ tabel maken () wordt gebruikt voor het definiëren van een gedistribueerde tabel en het maken van de Shards als het een hash-gedistribueerde tabel is. Deze functie maakt deel uit van een tabel naam, de kolom distributie en een optionele distributie methode en voegt de juiste meta gegevens in om de tabel als gedistribueerd te markeren. De functie wordt standaard ingesteld op hash-distributie als er geen distributie methode is opgegeven. Als de tabel hash-Distributed is, wordt met de functie ook Shards gemaakt op basis van het aantal Shard en de configuratie waarden van de Shard-replicatie factor. Als de tabel rijen bevat, worden deze automatisch gedistribueerd naar werk knooppunten.

Deze functie vervangt het gebruik van \_ stramien \_ gedistribueerde \_ tabel maken (), gevolgd door de \_ \_ hoofdwerk nemer \_ Shards maken ().

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** naam van de tabel die moet worden gedistribueerd.

**distributie \_ kolom:** de kolom waarop de tabel moet worden gedistribueerd.

**distributie \_ type:** (optioneel) de methode volgens welke de tabel moet worden gedistribueerd. Toegestane waarden zijn Append of hash, met de standaard waarde hash.

**Ga naar \_ :** (optioneel) Neem de huidige tabel op in de groep voor co-locaties van een andere tabel. Standaard worden tabellen naast elkaar geplaatst wanneer ze worden gedistribueerd door kolommen van hetzelfde type, hetzelfde Shard aantal hebben en dezelfde replicatie factor hebben. Mogelijke waarden voor `colocate_with` zijn `default` , `none` om een nieuwe groep met co-locaties te starten of de naam van een andere tabel om deze tabel te vinden.  (Zie [Table](concepts-hyperscale-colocation.md)-co-locatie.)

Houd er wel voor dat de standaard waarde van de `colocate_with` impliciete co-locatie is. De co- [locatie](concepts-hyperscale-colocation.md) kan een fantastische ding zijn wanneer tabellen aan elkaar zijn gerelateerd of worden gekoppeld.  Als twee tabellen echter geen verband houden met het gebruik van hetzelfde gegevens type voor hun distributie kolommen, kunnen ze per ongeluk de prestaties tijdens het [herverdelen van Shard](howto-hyperscale-scale-rebalance.md)afnemen.  De tabel Shards wordt onnodig verplaatst in een \" trapsgewijze verplaatsing.\"

Als een nieuwe gedistribueerde tabel niet is gerelateerd aan andere tabellen, kunt u het beste opgeven `colocate_with => 'none'` .

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de Data Base \_ gedistribueerd dat de tabel github-gebeurtenissen moet worden verdeeld op hash in de \_ kolom opslag plaats id.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>\_verwijzings \_ tabel maken

De \_ functie verwijzings \_ tabel maken () wordt gebruikt om een kleine verwijzing of dimensie tabel te definiëren. Deze functie neemt de naam van een tabel en maakt een gedistribueerde tabel met slechts één Shard, gerepliceerd naar elk worker-knoop punt.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** naam van de kleine dimensie of verwijzings tabel die moet worden gedistribueerd.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de data base waarvan de tabel van de bron is gedefinieerd als een verwijzings tabel.

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>bijwerken \_ naar \_ verwijzings \_ tabel

De \_ functie upgrade naar \_ verwijzings \_ tabel () gebruikt een bestaande gedistribueerde tabel met een Shard aantal van één en voert een upgrade uit voor een herkende verwijzings tabel. Na het aanroepen van deze functie is de tabel zo ingesteld dat deze is gemaakt met [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** naam van de gedistribueerde tabel (met Shard Count = 1) die wordt gedistribueerd als een verwijzings tabel.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de data base waarvan de tabel van de bron is gedefinieerd als een verwijzings tabel.

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>tabellen die worden \_ \_ geplaatst markeren

De functie voor het markeren \_ \_ van tabellen () heeft een gedistribueerde tabel (de bron) en een lijst met andere personen (de doelen), en plaatst de doelen in dezelfde groep voor samen werking als de bron. Als de bron zich nog niet in een groep bevindt, maakt deze functie er een en wijst deze toe aan de bron en de doelen.

Tabellen die u wilt verplaatsen, zouden moeten worden uitgevoerd tijdens de distributie tijd van de tabel via de `colocate_with` para meter van [create_distributed_table](#create_distributed_table), maar `mark_tables_colocated` Dit kan, indien nodig, later worden verzorgd.

#### <a name="arguments"></a>Argumenten

**bron \_ tabel \_ naam:** naam van de gedistribueerde tabel waarvan de groep waaraan de doelen worden toegewezen, moet overeenkomen.

**doel \_ tabel \_ namen:** matrix met namen van de gedistribueerde doel tabellen, mag niet leeg zijn. Deze gedistribueerde tabellen moeten overeenkomen met de bron tabel in:

> -   distributie methode
> -   type distributie kolom
> -   replicatie type
> -   aantal Shard

Als geen van bovenstaande van toepassing is, wordt er een fout gegenereerd door grootschalige (Citus). Bijvoorbeeld om tabellen te zoeken `apples` en `oranges` waarvan de distributie kolom typen verschillen in:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In dit voor beeld wordt `products` en `line_items` in dezelfde groep met één locatie als `stores` . In het voor beeld wordt ervan uitgegaan dat deze tabellen allemaal worden gedistribueerd op een kolom met een overeenkomend type, waarschijnlijk een \" archief-id.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>\_gedistribueerde \_ functie maken

Hiermee wordt een functie van het coördinator knooppunt door gegeven aan werk nemers en gemarkeerd voor gedistribueerde uitvoering. Wanneer een gedistribueerde functie wordt aangeroepen in de coördinator, gebruikt grootschalige (Citus) de waarde van het \" distributie argument \" om een worker-knoop punt te kiezen voor het uitvoeren van de functie. Wanneer de functie wordt uitgevoerd op werk nemers, wordt de parallelle factor verhoogd en kan de code dichter bij de gegevens in Shards worden gebracht voor een lagere latentie.

Het zoekpad voor post gres wordt niet door gegeven aan werk nemers tijdens de uitvoering van gedistribueerde functies, dus de gedistribueerde functie code moet de namen van database objecten volledig kwalificeren. Ook meldingen die door de functies worden verzonden, worden niet weer gegeven aan de gebruiker.

#### <a name="arguments"></a>Argumenten

**functie \_ naam:** naam van de functie die moet worden gedistribueerd. De naam moet de parameter typen van de functie tussen haakjes bevatten, omdat meerdere functies dezelfde naam kunnen hebben in PostgreSQL. Bijvoorbeeld, `'foo(int)'` wijkt af van `'foo(int, text)'` .

**naam distributie- \_ ARG \_ :** (optioneel) de argument naam die moet worden gedistribueerd. Voor het gemak (of als de functie argumenten geen namen hebben), is een positie aanduiding toegestaan, zoals `'$1'` . Als deze para meter niet is opgegeven, wordt de functie benoemd door `function_name` alleen op de werk nemers gemaakt. Als worker-knoop punten in de toekomst worden toegevoegd, wordt de functie ook automatisch gemaakt.

**Ga verder \_ met:** (optioneel) wanneer de gedistribueerde functie leest of schrijft naar een gedistribueerde tabel (of in het algemeen een groep met co-locaties), zorg ervoor dat deze tabel een naam met behulp van de `colocate_with` para meter. Vervolgens wordt elke aanroep van de functie uitgevoerd op het worker-knoop punt met de relevante Shards.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Meta gegevens/configuratie-informatie

### <a name="master_get_table_metadata"></a>\_ \_ meta gegevens van hoofd tabel ophalen \_

De \_ \_ functie voor het ophalen \_ van meta gegevens van de hoofd tabel () kan worden gebruikt om distributie-gerelateerde meta gegevens voor een gedistribueerde tabel te retour neren. Deze meta gegevens bevatten de relatie-ID, het opslag type, de distributie methode, de distributie kolom, het aantal replicaties, de maximale Shard-grootte en het Shard-plaatsings beleid voor de tabel. Achter de kaften zoekt deze functie naar grootschalige-meta gegevens tabellen (Citus) om de vereiste gegevens op te halen en voegt deze toe aan een tuple voordat deze wordt geretourneerd aan de gebruiker.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** naam van de gedistribueerde tabel waarvoor u meta gegevens wilt ophalen.

#### <a name="return-value"></a>Retourwaarde

Een tuple met de volgende informatie:

**logische \_ relid:** OID van de gedistribueerde tabel. Deze verwijst naar de kolom relfilenode in de catalogus tabel van de PG- \_ klasse System.

**\_ type onderdeel opslag \_ :** het type opslag dat wordt gebruikt voor de tabel. Mogelijk ' (standaard tabel), ' f ' (refererende tabel) of ' c ' (kolom in tabel).

**onderdeel \_ methode:** de distributie methode die wordt gebruikt voor de tabel. Kan ' a ' (toevoegen) of ' h ' (hash) zijn.

**onderdeel \_ sleutel:** de kolom distributie voor de tabel.

**\_ aantal onderdelen replica's \_ :** huidig aantal Shard-replicaties.

**grootte van onderdeel \_ \_ :** huidige maximale grootte van Shard in bytes.

**onderdeel \_ plaatsings \_ beleid:** Shard-plaatsings beleid dat wordt gebruikt voor het plaatsen van de Shards van de tabel. Kan 1 (lokaal-knoop punt-eerste) of 2 (Round-Robin) zijn.

#### <a name="example"></a>Voorbeeld

In het onderstaande voor beeld worden de meta gegevens van de tabel voor de tabel github-gebeurtenissen opgehaald en weer gegeven \_ .

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>\_Shard \_ -id \_ voor \_ distributie \_ kolom ophalen

Grootschalige (Citus) wijst elke rij van een gedistribueerde tabel toe aan een Shard op basis van de waarde van de kolom verdeling en de distributie methode van de tabel. In de meeste gevallen is de precieze toewijzing een Details op laag niveau die de beheerder van de data base kan negeren. Het kan echter nuttig zijn om de Shard van een rij te bepalen, ofwel voor hand matige onderhouds taken voor de data base of alleen om te voldoen aan Curiosity. De `get_shard_id_for_distribution_column` functie levert deze informatie voor zowel hash-als bereik-gedistribueerde tabellen en naslag tabellen. Het werkt niet voor de toevoeg distributie.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** de gedistribueerde tabel.

**distributie \_ waarde:** de waarde van de kolom distributie.

#### <a name="return-value"></a>Retourwaarde

De Shard ID grootschalige (Citus) wordt gekoppeld aan de waarde van de distributie kolom voor de opgegeven tabel.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>kolom \_ \_ naam naar kolom \_

Zet de kolom om `partkey` `pg_dist_partition` in een tekst kolom naam. De vertaling is handig om de distributie kolom van een gedistribueerde tabel te bepalen.

Zie [een distributie kolom kiezen](concepts-hyperscale-choose-distribution-column.md)voor een gedetailleerder onderwerp.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** de gedistribueerde tabel.

**\_ var- \_ tekst kolom:** de waarde van `partkey` in de `pg_dist_partition` tabel.

#### <a name="return-value"></a>Retourwaarde

De naam van de `table_name` distributie kolom.

#### <a name="example"></a>Voorbeeld

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Uitvoer:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>grootte van Citus- \_ relatie \_

De schijf ruimte ophalen die wordt gebruikt door de Shards van de opgegeven gedistribueerde tabel.
De schijf ruimte bevat de grootte van de \" hoofd Fork, \" maar sluit de toewijzing van de zichtbaarheids kaart en de vrije ruimte voor de Shards uit.

#### <a name="arguments"></a>Argumenten

**logicalrelid:** de naam van een gedistribueerde tabel.

#### <a name="return-value"></a>Retourwaarde

Grootte in bytes als een bigint.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>Citus- \_ tabel \_ grootte

De schijf ruimte ophalen die wordt gebruikt door de Shards van de opgegeven gedistribueerde tabel, met uitzonde ring van indexen (zoals pop-up, vrije ruimte kaart en zicht baarheids kaart).

#### <a name="arguments"></a>Argumenten

**logicalrelid:** de naam van een gedistribueerde tabel.

#### <a name="return-value"></a>Retourwaarde

Grootte in bytes als een bigint.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>Citus \_ totale \_ grootte van relatie \_

De totale schijf ruimte ophalen die wordt gebruikt door de Shards van de opgegeven gedistribueerde tabel, inclusief alle indexen en pop-upgegevens.

#### <a name="arguments"></a>Argumenten

**logicalrelid:** de naam van een gedistribueerde tabel.

#### <a name="return-value"></a>Retourwaarde

Grootte in bytes als een bigint.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>Citus \_ stat- \_ instructies \_ opnieuw instellen

Hiermee verwijdert u alle rijen uit [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Deze functie werkt onafhankelijk van `pg_stat_statements_reset()` . Roep beide functies aan om alle statistieken opnieuw in te stellen.

#### <a name="arguments"></a>Argumenten

N.v.t.

#### <a name="return-value"></a>Retourwaarde

Geen

## <a name="server-group-management-and-repair"></a>Beheer en reparatie van Server groep

### <a name="master_copy_shard_placement"></a>Shard-plaatsing van hoofd \_ kopiëren \_ \_

Als een Shard-plaatsing niet kan worden bijgewerkt tijdens een wijzigings opdracht of een DDL-bewerking, wordt deze als inactief gemarkeerd. De \_ Shard- \_ functie voor het kopiëren van hoofd kopieën \_ kan vervolgens worden aangeroepen om een inactieve Shard-plaatsing te herstellen met gegevens uit een gezonde plaatsing.

Als u een Shard wilt herstellen, breekt de functie eerst de onjuiste Shard-plaatsing en maakt deze opnieuw met behulp van het schema op de coördinator. Zodra de Shard-plaatsing is gemaakt, kopieert de functie gegevens van de gezonde plaatsing en werkt de meta gegevens bij om de nieuwe Shard-plaatsing als in orde te markeren. Deze functie zorgt ervoor dat de Shard wordt beschermd tegen elke gelijktijdige wijziging tijdens het herstel.

#### <a name="arguments"></a>Argumenten

**Shard \_ -id:** id van de Shard die moet worden hersteld.

**naam van bron \_ knooppunt \_ :** DNS-naam van het knoop punt waarop de plaats van de gezonde Shard aanwezig is ( \" bron \" knooppunt).

**bron \_ knooppunt \_ poort:** de poort op het bron werk knooppunt waarop de database server luistert.

**doel \_ knooppunt \_ naam:** DNS-naam van het knoop punt waarop de ongeldige Shard-plaatsing zich bevindt ( \" doel \" knooppunt).

**doel \_ knooppunt \_ poort:** de poort op het doel werk knooppunt waarop de database server luistert.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In het onderstaande voor beeld wordt een inactieve Shard-plaatsing hersteld van Shard 12345, dat aanwezig is op de database server die wordt uitgevoerd op ' beschadigde \_ host ' op poort 5432. Om het te herstellen, worden er gegevens gebruikt van een gezonde Shard-plaatsing die aanwezig is op de server die wordt uitgevoerd op ' goede \_ host ' op de poort
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_Shard- \_ \_ plaatsing Master verplaatsing

Met deze functie wordt een gegeven Shard (en Shards naast elkaar) verplaatst van het ene knoop punt naar het andere. Deze wordt doorgaans indirect gebruikt tijdens het herverdelen van Shard in plaats van dat ze rechtstreeks worden aangeroepen door een database beheerder.

Er zijn twee manieren om de gegevens te verplaatsen: blok keren of niet blok keren. De blokkerende aanpak houdt in dat tijdens het verplaatsen van alle wijzigingen in de Shard worden onderbroken.
De tweede manier, waarmee wordt voor komen dat Shard-schrijf bewerkingen worden geblokkeerd, is afhankelijk van post gres 10 logische replicatie.

Na een geslaagde verplaatsings bewerking worden de Shards in het bron knooppunt verwijderd. Als de verplaatsing op geen enkele wijze mislukt, wordt door deze functie een fout gegenereerd en blijven de bron-en doel knooppunten ongewijzigd.

#### <a name="arguments"></a>Argumenten

**Shard \_ -id:** id van de Shard die moet worden verplaatst.

**naam van bron \_ knooppunt \_ :** DNS-naam van het knoop punt waarop de plaats van de gezonde Shard aanwezig is ( \" bron \" knooppunt).

**bron \_ knooppunt \_ poort:** de poort op het bron werk knooppunt waarop de database server luistert.

**doel \_ knooppunt \_ naam:** DNS-naam van het knoop punt waarop de ongeldige Shard-plaatsing zich bevindt ( \" doel \" knooppunt).

**doel \_ knooppunt \_ poort:** de poort op het doel werk knooppunt waarop de database server luistert.

**Shard- \_ overdrachts \_ modus:** (optioneel) Geef de replicatie methode op, of u postgresql logische replicatie of een Kopieer opdracht voor meerdere werk nemers wilt gebruiken. De mogelijke waarden zijn:

> -   `auto`: Replica-identiteit vereisen als logische replicatie mogelijk is, anders verouderd gedrag gebruiken (bijvoorbeeld voor Shard-reparatie, PostgreSQL 9,6). Dit is de standaardwaarde.
> -   `force_logical`: Gebruik logische replicatie, zelfs als de tabel geen replica-id heeft. Alle gelijktijdige bijwerk-en verwijderings instructies voor de tabel mislukken tijdens de replicatie.
> -   `block_writes`: Gebruik COPY (blokkerende schrijf bewerkingen) voor tabellen die geen primaire sleutel of replica-id hebben.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>herverdeling van \_ tabel \_ Shards

De functie rebalancing \_ Table \_ Shards () verplaatst Shards van de opgegeven tabel om ze gelijkmatig te verdelen over de werk nemers. De functie berekent eerst de lijst met verplaatsingen die moeten worden gemaakt om ervoor te zorgen dat de Server groep binnen de opgegeven drempel waarde wordt verdeeld. Vervolgens worden de Shard-locaties één voor één van het bron knooppunt naar het doel knooppunt verplaatst en worden de bijbehorende Shard-meta gegevens bijgewerkt om de verplaatsing weer te geven.

Aan elke Shard worden kosten toegewezen bij het bepalen of Shards \" gelijkmatig worden verdeeld. \" Elke Shard heeft standaard dezelfde kosten (een waarde van 1), zodat de distributie om de kosten voor de verschillende werk nemers te egaliseren hetzelfde is als het aantal Shards op elk gewenst punt wordt geegaliseerd. De constante kosten strategie wordt aangeroepen \" door \_ \_ het aantal Shard \" en is de standaard strategie voor herverdeling.

De standaard strategie is afhankelijk van de volgende omstandigheden:

*  De Shards hebben ongeveer dezelfde grootte
*  De Shards krijgen ongeveer dezelfde hoeveelheid verkeer
*  Worker-knoop punten hebben dezelfde grootte/type
*  Shards zijn niet vastgemaakt aan bepaalde werk nemers

Als een van deze hypo theses niet aanwezig is, kan de standaard herverdeling resulteren in een beschadigd plan. In dit geval kunt u de strategie aanpassen met behulp van de `rebalance_strategy` para meter.

Het is raadzaam om [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) aan te roepen voordat u een herverdelings \_ tabel \_ Shards uitvoert om te zien en te controleren welke acties moeten worden uitgevoerd.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** (optioneel) de naam van de tabel waarvan Shards opnieuw moet worden gebalanceerd. Zo niet, dan worden alle bestaande groepen met co-locaties opnieuw gebalanceerd.

**drempel waarde:** (optioneel) een drijvende komma tussen 0,0 en 1,0 die de maximale verschil verhouding van het knooppunt gebruik van het gemiddelde gebruik aangeeft. Als u bijvoorbeeld 0,1 opgeeft, wordt de Shard opnieuw geprobeerd om alle knoop punten te verdelen zodat hetzelfde aantal Shards ± 10% wordt gehouden.
Met name probeert de Shard-herbalancer het gebruik van alle worker-knoop punten te convergeren tot het gemiddelde gebruik (1-drempel) \* \_ \. . (1
+ drempel) \* gemiddeld \_ gebruiks bereik.

**maximum aantal \_ Shard \_ verplaatst:** (optioneel) het Maxi maal te verplaatsen Shards.

**uitgesloten \_ Shard- \_ lijst:** (optioneel) id's van Shards die niet mogen worden verplaatst tijdens het opnieuw verdelen.

**Shard- \_ overdrachts \_ modus:** (optioneel) Geef de replicatie methode op, of u postgresql logische replicatie of een Kopieer opdracht voor meerdere werk nemers wilt gebruiken. De mogelijke waarden zijn:

> -   `auto`: Replica-identiteit vereisen als logische replicatie mogelijk is, anders verouderd gedrag gebruiken (bijvoorbeeld voor Shard-reparatie, PostgreSQL 9,6). Dit is de standaardwaarde.
> -   `force_logical`: Gebruik logische replicatie, zelfs als de tabel geen replica-id heeft. Alle gelijktijdige bijwerk-en verwijderings instructies voor de tabel mislukken tijdens de replicatie.
> -   `block_writes`: Gebruik COPY (blokkerende schrijf bewerkingen) voor tabellen die geen primaire sleutel of replica-id hebben.

**\_ alleen afvoer:** (optioneel) als deze optie waar is, verplaatst u Shards uit worker-knoop punten die zijn `shouldhaveshards` ingesteld op False in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table); Verplaats geen andere Shards.

**strategie voor opnieuw verdelen \_ :** (optioneel) de naam van een strategie in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Als dit argument wordt wegge laten, wordt met de functie de standaard strategie gekozen, zoals aangegeven in de tabel.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt geprobeerd de Shards van de tabel github-gebeurtenissen te herverdelen \_ binnen de standaard drempel waarde.

```postgresql
SELECT rebalance_table_shards('github_events');
```

In dit voor beeld wordt een poging gedaan om de GitHub \_ -gebeurtenissen tabel opnieuw te verdelen zonder Shards te verplaatsen met de id 1 en 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>Shards-plan voor \_ herverdeling van \_ tabel ophalen \_ \_

De geplande Shard-verplaatsingen van [rebalance_table_shards](#rebalance_table_shards) uitvoeren zonder ze uit te voeren.
Hoewel het onwaarschijnlijk is, kan het Shards-plan voor het opnieuw \_ verdelen van \_ \_ \_ een iets ander plan worden uitgevoerd dan wat een rebalance \_ Table Shards- \_ aanroep met dezelfde argumenten zal doen. Ze worden niet tegelijkertijd uitgevoerd, dus feiten over de Server groep, \- bijvoorbeeld schijf ruimte, \- kunnen verschillen tussen de aanroepen.

#### <a name="arguments"></a>Argumenten

Dezelfde argumenten als herverdeling \_ Table \_ Shards: relatie, drempel waarde, maximum aantal \_ Shard \_ verplaatst, \_ lijst met uitgesloten Shards \_ en \_ alleen afvoer. Zie de documentatie van die functie voor de argumenten ' betekenisf '.

#### <a name="return-value"></a>Retourwaarde

Tuples met deze kolommen:

-   **tabel \_ naam**: de tabel waarvan Shards zou worden verplaatst
-   **shardid**: de Shard in kwestie
-   **\_ grootte van Shard**: grootte in bytes
-   **SourceName**: hostnaam van het bron knooppunt
-   **sourceport**: poort van het bron knooppunt
-   **TargetName**: de hostnaam van het doel knooppunt
-   **targetport**: poort van het doel knooppunt

### <a name="get_rebalance_progress"></a>\_voortgang van herverdeling ophalen \_

Zodra een herverdeling van een Shard begint, `get_rebalance_progress()` geeft de functie de voortgang weer van elke betrokken Shard. Het controleert de verplaatsingen die worden gepland en uitgevoerd door `rebalance_table_shards()` .

#### <a name="arguments"></a>Argumenten

N.v.t.

#### <a name="return-value"></a>Retourwaarde

Tuples met deze kolommen:

-   **sessie-id**: post gres PID van de monitor voor opnieuw verdelen
-   **tabel \_ naam**: de tabel waarvan de Shards worden verplaatst
-   **shardid**: de Shard in kwestie
-   **\_ grootte van Shard**: grootte in bytes
-   **SourceName**: hostnaam van het bron knooppunt
-   **sourceport**: poort van het bron knooppunt
-   **TargetName**: de hostnaam van het doel knooppunt
-   **targetport**: poort van het doel knooppunt
-   **voortgang**: 0 = wachten om te worden verplaatst; 1 = verplaatsen; 2 = volt ooien

#### <a name="example"></a>Voorbeeld

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>Citus-strategie voor opnieuw \_ \_ verdelen toevoegen \_

Een rij toevoegen aan [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argumenten

Zie voor meer informatie over deze argumenten de overeenkomstige kolom waarden in `pg_dist_rebalance_strategy` .

**naam:** id voor de nieuwe strategie

**\_ \_ functie Shard Cost:** identificeert de functie die wordt gebruikt om de \" kosten \" van elke Shard te bepalen

**\_ functie knooppunt capaciteit \_ :** identificeert de functie om de capaciteit van het knoop punt te meten

**Shard \_ toegestaan \_ op \_ knooppunt \_ functie:** identificeert de functie die bepaalt welke Shards kan worden geplaatst op welke knoop punten

**standaard \_ drempelwaarde:** een drijvende-komma drempel die afstemt op de nauw keurigheid van de cumulatieve Shard-kosten tussen knoop punten

**minimale \_ drempel waarde:** (optioneel) een kolom voor beveiliging die de toegestane minimum waarden voor het argument drempel van de herverdelings \_ tabel \_ Shards () bevat. De standaard waarde is 0

#### <a name="return-value"></a>Retourwaarde

N.v.t.

### <a name="citus_set_default_rebalance_strategy"></a>Citus \_ instellen \_ standaard strategie voor opnieuw \_ verdelen \_

Werk de [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) tabel bij, waarbij u de strategie wijzigt met de naam van het argument als standaard gekozen bij het herverdelen van Shards.

#### <a name="arguments"></a>Argumenten

**naam:** de naam van de strategie in de pag/of-strategie voor de verdeling van pagina's \_ \_ \_

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>\_Statistieken voor externe \_ verbinding \_ Citus

De \_ \_ \_ functie statistieken voor externe verbinding () Citus toont het aantal actieve verbindingen met elk knoop punt op afstand.

#### <a name="arguments"></a>Argumenten

N.v.t.

#### <a name="example"></a>Voorbeeld

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>\_ \_ knoop punt voor hoofd afvoer

Met de \_ functie hoofd afvoer \_ knooppunt () verplaatst u Shards uit het aangewezen knoop punt en naar andere knoop punten die zijn `shouldhaveshards` ingesteld op True in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Roep de functie aan voordat u een knoop punt uit de Server groep verwijdert en de fysieke server van het knoop punt uitschakelt.

#### <a name="arguments"></a>Argumenten

**knooppuntnaam:** De hostnaam van het knoop punt dat moet worden geleegd.

**nodeport:** Het poort nummer van het knoop punt dat moet worden leeg gesteld.

**Shard- \_ overdrachts \_ modus:** (optioneel) Geef de replicatie methode op, of u postgresql logische replicatie of een Kopieer opdracht voor meerdere werk nemers wilt gebruiken. De mogelijke waarden zijn:

> -   `auto`: Replica-identiteit vereisen als logische replicatie mogelijk is, anders verouderd gedrag gebruiken (bijvoorbeeld voor Shard-reparatie, PostgreSQL 9,6). Dit is de standaardwaarde.
> -   `force_logical`: Gebruik logische replicatie, zelfs als de tabel geen replica-id heeft. Alle gelijktijdige bijwerk-en verwijderings instructies voor de tabel mislukken tijdens de replicatie.
> -   `block_writes`: Gebruik COPY (blokkerende schrijf bewerkingen) voor tabellen die geen primaire sleutel of replica-id hebben.

**strategie voor opnieuw verdelen \_ :** (optioneel) de naam van een strategie in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Als dit argument wordt wegge laten, wordt met de functie de standaard strategie gekozen, zoals aangegeven in de tabel.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="example"></a>Voorbeeld

Hier volgen de typische stappen voor het verwijderen van één knoop punt (bijvoorbeeld ' 10.0.0.1 ' op een standaard PostgreSQL-poort):

1.  Het knoop punt leeg te laten.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Wachten tot de opdracht is voltooid

3.  Het knoop punt verwijderen

Wanneer u meerdere knoop punten verwerkt, is het raadzaam om [rebalance_table_shards](#rebalance_table_shards) in plaats daarvan te gebruiken. Als u dit wel doet, kan grootschalige (Citus) vooruit plannen en Shards het minimale aantal keren worden verplaatst.

1.  Voer uit voor elk knoop punt dat u wilt verwijderen:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Deze allemaal tegelijk verwerken met [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Wacht tot de herverdeling van het verwerkings stop is voltooid

4.  De knoop punten verwijderen

### <a name="replicate_table_shards"></a>\_tabel \_ Shards repliceren

De functie gerepliceerde \_ tabel \_ Shards () repliceert de onder-gerepliceerde Shards van de opgegeven tabel. De functie berekent eerst de lijst met onder-gerepliceerde Shards en locaties van waaruit ze kunnen worden opgehaald voor replicatie. De functie kopieert vervolgens over die Shards en werkt de bijbehorende Shard-meta gegevens bij om de kopie weer te geven.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** de naam van de tabel waarvan de Shards moet worden gerepliceerd.

**Shard- \_ replicatie \_ factor:** (optioneel) de gewenste replicatie factor die voor elke Shard moet worden gerealiseerd.

**Max. aantal \_ Shard- \_ exemplaren:** (optioneel) het Maxi maal toegestane Shards dat moet worden gekopieerd om de gewenste replicatie factor te bereiken.

**uitgesloten \_ Shard- \_ lijst:** (optioneel) id's van Shards die niet moeten worden gekopieerd tijdens de replicatie bewerking.

#### <a name="return-value"></a>Retourwaarde

N.v.t.

#### <a name="examples"></a>Voorbeelden

In het volgende voor beeld wordt geprobeerd de Shards van de GitHub- \_ gebeurtenissen tabel te repliceren naar Shard- \_ replicatie \_ factor.

```postgresql
SELECT replicate_table_shards('github_events');
```

In dit voor beeld wordt geprobeerd de Shards van de GitHub- \_ gebeurtenissen tabel naar de gewenste replicatie factor te brengen met een maximum van 10 Shard-exemplaren. De herbalancer kopieert Maxi maal 10 Shards in de poging om de gewenste replicatie factor te bereiken.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>\_Tenant isoleren \_ voor \_ nieuwe \_ Shard

Deze functie maakt een nieuwe Shard om rijen te bewaren met een specifieke enkele waarde in de kolom distributie. Het is vooral handig voor het gebruik van multi-tenant grootschalige (Citus), waarbij een grote Tenant alleen kan worden geplaatst op een eigen Shard en uiteindelijk een eigen fysiek knoop punt.

#### <a name="arguments"></a>Argumenten

**tabel \_ naam:** de naam van de tabel om een nieuwe Shard op te halen.

**Tenant \_ -id:** de waarde van de kolom distributie die wordt toegewezen aan de nieuwe Shard.

**trapsgewijze \_ optie:** (optioneel) wanneer deze is ingesteld op \" Met CASCADE wordt \" ook een Shard geïsoleerd van alle tabellen in de [groeps deel](concepts-hyperscale-colocation.md)van de huidige tabel.

#### <a name="return-value"></a>Retourwaarde

**Shard \_ -id:** de functie retourneert de unieke id die is toegewezen aan de zojuist gemaakte Shard.

#### <a name="examples"></a>Voorbeelden

Maak een nieuwe Shard voor de regel items voor Tenant 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Volgende stappen

* Veel van de functies in dit artikel [meta gegevens tabellen](reference-hyperscale-metadata.md) van het systeem wijzigen
* [Server parameters](reference-hyperscale-parameters.md) bepalen het gedrag van sommige functies

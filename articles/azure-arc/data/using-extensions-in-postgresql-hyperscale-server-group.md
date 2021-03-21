---
title: PostgreSQL-extensies gebruiken
description: PostgreSQL-extensies gebruiken
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6586375d7db71274f40eb62aeb24f9daad0d7c2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688294"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL-extensies gebruiken in uw Azure-PostgreSQL grootschalige-Server groep

PostgreSQL is de beste optie wanneer u deze gebruikt met uitbrei dingen. Eigenlijk is een belang rijk element van onze eigen grootschalige-functionaliteit de door micro soft verschafte `citus` uitbrei ding die standaard wordt geïnstalleerd, waardoor post gres transparante gegevens kan Shard op meerdere knoop punten.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Ondersteunde extensies
De standaard [`contrib`](https://www.postgresql.org/docs/12/contrib.html) uitbreidingen en de volgende extensies zijn al geïmplementeerd in de containers van uw Azure-postgresql grootschalige-Server groep:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. De Citus-extensie door [Citus-gegevens](https://www.citusdata.com/) wordt standaard geladen omdat de grootschalige-functionaliteit naar de postgresql-engine brengt. Het verwijderen van de Citus-uitbrei ding van uw Azure Arc PostgreSQL grootschalige-Server groep wordt niet ondersteund.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Updates voor deze lijst worden geplaatst wanneer deze na verloop van tijd worden opgegroeid.

> [!IMPORTANT]
> Hoewel u de Server groep een andere uitbrei ding dan de hierboven vermelde extensie kunt geven, wordt deze niet opgeslagen in het systeem. Dit betekent dat deze niet beschikbaar is na het opnieuw opstarten van het systeem en dat u het opnieuw moet doen.

Deze hand leiding gaat in een scenario over het gebruik van twee van deze uitbrei dingen:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Welke extensies moeten worden toegevoegd aan de shared_preload_libraries en gemaakt?

|Uitbreidingen   |Moet worden toegevoegd aan shared_preload_libraries  |Moet worden gemaakt |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Nee       |Ja        |
|`pg_audit`     |Ja       |Ja        |
|`plpgsql`      |Ja       |Ja        |
|`postgis`      |Nee       |Ja        |
|`plv8`      |Nee       |Ja        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Extensies toevoegen aan de shared_preload_libraries
Raadpleeg de PostgreSQL [-documentatie voor](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)meer informatie over die shared_preload_libraries:
- Deze stap is niet nodig voor de uitbrei dingen die deel uitmaken van `contrib`
- deze stap is niet vereist voor uitbrei dingen die niet vooraf moeten worden geladen door shared_preload_libraries. Voor deze uitbrei dingen kunt u de volgende alinea een [extensie maken](https://docs.microsoft.com/azure/azure-arc/data/using-extensions-in-postgresql-hyperscale-server-group#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Een uitbrei ding toevoegen tijdens het maken van een server groep
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Een uitbrei ding toevoegen aan een exemplaar dat al bestaat
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>De lijst met uitbrei dingen weer geven die zijn toegevoegd aan shared_preload_libraries
Voer een van de volgende opdrachten uit.

### <a name="with-an-azdata-cli-command"></a>Met een azdata CLI-opdracht
```console
azdata arc postgres server show -n <server group name>
```
Schuif in de uitvoer en Let op de engine\extensions-secties in de specificaties van uw server groep. Bijvoorbeeld:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Met kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Schuif in de uitvoer en Let op de engine\extensions-secties in de specificaties van uw server groep. Bijvoorbeeld:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Extensies maken
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>De lijst met gemaakte extensies weer geven
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Een uitbrei ding verwijderen
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>De `PostGIS` uitbrei ding
U hoeft de extensie niet toe te voegen `PostGIS` aan de `shared_preload_libraries` .
Haal [voorbeeld gegevens](http://duspviz.mit.edu/tutorials/intro-postgis/) op uit het departement van de MIT van het stads onderzoek & planning. Voer uit `apt-get install unzip` om unzip naar behoefte te installeren.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

We gaan nu verbinding maken met de data base en de `PostGIS` uitbrei ding creëren:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Als u een van de uitbrei dingen in het pakket wilt gebruiken (bijvoorbeeld,,... `postgis` `postgis_raster` ), `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` moet u eerst de postgis-extensie maken en vervolgens de andere extensie maken. Bijvoorbeeld: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

En maak het schema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nu kunnen we combi neren `PostGIS` met de uitschaal functionaliteit door de coffee_shops tabel te verdelen:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Laten we een aantal gegevens laden:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Vul het `geom` veld in met de juiste versleutelde breedte graad en lengte graad in het `PostGIS` `geometry` gegevens type:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nu kunnen we de koffie winkels weer geven die het dichtst bij MIT (77 Massachusetts Ave op 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>De `pg_cron` uitbrei ding

Nu gaan we inschakelen `pg_cron` op onze postgresql-server groep door deze toe te voegen aan de shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

De Server groep wordt opnieuw gestart om de installatie van de extensies te volt ooien. Het kan 2 tot drie minuten duren.

Er kan nu opnieuw verbinding worden gemaakt en de `pg_cron` uitbrei ding te maken:

```sql
CREATE EXTENSION pg_cron;
```

In het kader van test doeleinden kunt u een tabel maken `the_best_coffee_shop` die een wille keurige naam uit onze eerdere `coffee_shops` tabel haalt en de tabel inhoud invoegt:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

We kunnen `cron.schedule` plus enkele SQL-instructies gebruiken om een wille keurige tabel naam op te halen (Let op het gebruik van een tijdelijke tabel om een gedistribueerd query resultaat op te slaan) en sla het op in `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

En nu, één keer per minuut, krijgen we een andere naam:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Raadpleeg het [Leesmij-bestand van pg_cron](https://github.com/citusdata/pg_cron) voor volledige informatie over de syntaxis.


## <a name="next-steps"></a>Volgende stappen
- Documentatie lezen op [`plv8`](https://plv8.github.io/)
- Documentatie lezen op [`PostGIS`](https://postgis.net/)
- Documentatie lezen op [`pg_cron`](https://github.com/citusdata/pg_cron)

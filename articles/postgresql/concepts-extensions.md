---
title: Extensies-Azure Database for PostgreSQL-één server
description: Meer informatie over de beschik bare post gres-uitbrei dingen in Azure Database for PostgreSQL-één server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: de395bed885804445146b01745edf5e93426cb13
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606314"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-extensies in Azure Database for PostgreSQL - individuele server
PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Extensies bundelen meerdere SQL-objecten in één pakket dat met één opdracht kan worden geladen in of verwijderd uit uw database. Nadat de gegevens in de database zijn geladen, functioneren de extensies als ingebouwde functies.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken
PostgreSQL-extensies moeten worden geïnstalleerd in uw Data Base voordat u ze kunt gebruiken. Als u een bepaalde extensie wilt installeren, voert u de opdracht [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) van het hulpprogramma psql uit om de verpakte objecten in uw database te laden.

Azure Database for PostgreSQL ondersteunt een subset van de sleutel uitbreidingen zoals hieronder wordt weer gegeven. Deze informatie is ook beschikbaar door te worden uitgevoerd `SELECT * FROM pg_available_extensions;` . Uitbrei dingen die niet in de lijst staan, worden niet ondersteund. U kunt geen eigen uitbrei ding maken in Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Post gres 11-extensies

De volgende uitbrei dingen zijn beschikbaar op Azure Database for PostgreSQL servers die post gres-versie 11 hebben. 

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypothetische indexen voor PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en Opera tors die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | statistieken van tuple-niveau weer geven|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | de gedeelde buffer cache controleren|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensie voor het beheren van gepartitioneerde tabellen op tijd of ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/java script (V8) betrouw bare procedure taal|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2, 1.7.4             | Schakelt schaal bare invoegen en complexe query's voor tijdreeks gegevens in|
> |[accenten opzeggen](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|

## <a name="postgres-10-extensions"></a>Post gres 10-extensies 

De volgende uitbrei dingen zijn beschikbaar op Azure Database for PostgreSQL servers die post gres versie 10 hebben.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | gegevens type voor automatisch versleutelde wacht woorden|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en Opera tors die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | statistieken van tuple-niveau weer geven|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | de gedeelde buffer cache controleren|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van gepartitioneerde tabellen op tijd of ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/java script (V8) betrouw bare procedure taal|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Schakelt schaal bare invoegen en complexe query's voor tijdreeks gegevens in|
> |[accenten opzeggen](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|

## <a name="postgres-96-extensions"></a>Post gres 9,6-extensies 

De volgende uitbrei dingen zijn beschikbaar op Azure Database for PostgreSQL servers met post gres-versie 9,6.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | gegevens type voor automatisch versleutelde wacht woorden|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en Opera tors die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | statistieken van tuple-niveau weer geven|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | de gedeelde buffer cache controleren|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van gepartitioneerde tabellen op tijd of ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/java script (V8) betrouw bare procedure taal|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Schakelt schaal bare invoegen en complexe query's voor tijdreeks gegevens in|
> |[accenten opzeggen](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|

## <a name="postgres-95-extensions"></a>Post gres 9,5-extensies

>[!NOTE]
> PostgreSQL-versie 9,5 is buiten gebruik gesteld.

De volgende uitbrei dingen zijn beschikbaar op Azure Database for PostgreSQL servers met post gres-versie 9,5.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | gegevens type voor automatisch versleutelde wacht woorden|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | gegevens type voor hiërarchische structuren in structuur|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en Opera tors die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | statistieken van tuple-niveau weer geven|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | de gedeelde buffer cache controleren|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van gepartitioneerde tabellen op tijd of ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[accenten opzeggen](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | Universele unieke id's (UUID) genereren|


## <a name="pg_stat_statements"></a>pg_stat_statements
De [uitbrei ding pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure database for postgresql-server, zodat u de uitvoerings statistieken van SQL-instructies kunt volgen.
De instelling `pg_stat_statements.track` , die bepaalt welke instructies door de uitbrei ding worden geteld, wordt standaard ingesteld op `top` , wat betekent dat alle instructies die rechtstreeks door clients worden uitgegeven, worden bijgehouden. De twee andere tracking niveaus zijn `none` en `all` . Deze instelling kan worden geconfigureerd als een server parameter via de [Azure Portal](./howto-configure-server-parameters-using-portal.md) of de [Azure cli](./howto-configure-server-parameters-using-cli.md).

Er is een verhouding tussen de informatie over het uitvoeren van query's pg_stat_statements biedt en de invloed op de prestaties van de server bij het vastleggen van elke SQL-instructie. Als u de extensie pg_stat_statements niet actief gebruikt, wordt u aangeraden `pg_stat_statements.track` om in te stellen op `none` . Houd er rekening mee dat sommige bewakings services van derden afhankelijk zijn van pg_stat_statements voor het leveren van query's met betrekking tot query prestaties, zodat u kunt controleren of dit het geval is voor u of niet.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
met [dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) en [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) kunt u verbinding maken met een postgresql-server of een andere Data Base op dezelfde server. De ontvangende server moet verbindingen vanaf de verzendende server via de Firewall toestaan. Als u deze uitbrei dingen gebruikt om verbinding te maken tussen Azure Database for PostgreSQL-servers, kunt u dit doen door de optie toegang tot Azure-Services toestaan in te stellen op aan. Dit is ook nodig als u de uitbrei dingen wilt gebruiken om naar dezelfde server te gaan. De instelling toegang tot Azure-Services toestaan vindt u op de pagina Azure Portal voor de post gres-server, onder verbindings beveiliging. Als u toegang tot Azure-Services toestaan inschakelt, worden alle Azure Ip's in de acceptatie lijst geplaatst.

Momenteel worden uitgaande verbindingen van Azure Database for PostgreSQL niet ondersteund, met uitzonde ring van verbindingen met andere Azure Database for PostgreSQL servers in dezelfde regio.

## <a name="uuid"></a>uuid
Als u van plan bent om te gebruiken `uuid_generate_v4()` vanuit de [uuid-ossp-extensie](https://www.postgresql.org/docs/current/uuid-ossp.html), kunt u overwegen om te vergelijken met `gen_random_uuid()` de [pgcrypto-extensie](https://www.postgresql.org/docs/current/pgcrypto.html) voor prestatie voordelen.

## <a name="pgaudit"></a>pgAudit
De [pgAudit-extensie](https://github.com/pgaudit/pgaudit/blob/master/README.md) biedt controle logboek registratie voor sessies en objecten. Ga naar het artikel over de [controle concepten](concepts-audit.md)voor meer informatie over het gebruik van deze uitbrei ding in azure database for PostgreSQL. 

## <a name="pg_prewarm"></a>pg_prewarm
Met de extensie pg_prewarm worden relationele gegevens in de cache geladen. Bij de voor bereiding van uw caches hebt u na het opnieuw opstarten betere reactie tijden bij de eerste uitvoering van uw query's. In post gres 10 en lager wordt de voor bereiding hand matig uitgevoerd met de [functie prewarm](https://www.postgresql.org/docs/10/pgprewarm.html).

In post gres 11 en hoger kunt u voor bereiding configureren om [automatisch](https://www.postgresql.org/docs/current/pgprewarm.html)te worden uitgevoerd. U moet pg_prewarm opnemen in de `shared_preload_libraries` lijst van de para meter en de server opnieuw opstarten om de wijziging toe te passen. Para meters kunnen worden ingesteld vanuit de sjabloon [Azure Portal](howto-configure-server-parameters-using-portal.md), [cli](howto-configure-server-parameters-using-cli.md), rest API of arm. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB is een Data Base met een tijd reeks die is verpakt als een uitbrei ding voor PostgreSQL. TimescaleDB biedt tijdgebonden analytische functies, optimalisaties en schaal baarheid van post gres voor werk belastingen in de tijd reeks.

Meer [informatie over TimescaleDB](https://docs.timescale.com/latest), een gedeponeerd merk van [tijdschaal, Inc.](https://www.timescale.com/). Azure Database for PostgreSQL biedt de TimescaleDB [Apache-2-editie](https://www.timescale.com/legal/licenses).

### <a name="installing-timescaledb"></a>TimescaleDB installeren
Als u TimescaleDB wilt installeren, moet u deze toevoegen aan de gedeelde vooraf geladen bibliotheken van de server. Voor een wijziging van de `shared_preload_libraries` para meter post gres moet de **server opnieuw worden opgestart** . U kunt para meters wijzigen met behulp van de [Azure Portal](howto-configure-server-parameters-using-portal.md) of de [Azure cli](howto-configure-server-parameters-using-cli.md).

Met behulp van de [Azure Portal](https://portal.azure.com/):

1. Selecteer uw Azure Database for PostgreSQL-server.

2. Selecteer op de zijbalk **server parameters**.

3. Zoek de `shared_preload_libraries` para meter.

4. Selecteer **TimescaleDB**.

5. Selecteer **Opslaan** om uw wijzigingen te bewaren. U ontvangt een melding zodra de wijziging is opgeslagen. 

6. Nadat de melding is ontvangen, start u de server **opnieuw** op om deze wijzigingen toe te passen. Zie [een Azure database for postgresql server opnieuw starten](howto-restart-server-portal.md)voor meer informatie over het opnieuw opstarten van een server.


U kunt nu TimescaleDB inschakelen in uw post gres-data base. Maak verbinding met de data base en voer de volgende opdracht uit:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Als er een fout optreedt, controleert u of u [de server opnieuw hebt opgestart na het](howto-restart-server-portal.md) opslaan van shared_preload_libraries. 

U kunt nu een [nieuwe](https://docs.timescale.com/getting-started/creating-hypertables) TimescaleDB-hypertable maken of [bestaande gegevens van de tijd reeks migreren in postgresql](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Een tijdschaal database herstellen
Als u een tijdschaal database wilt herstellen met pg_dump en pg_restore, moet u twee ondersteunende procedures uitvoeren in de doel database: `timescaledb_pre_restore()` en `timescaledb_post restore()` .

Bereid eerst de doel database voor:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

U kunt nu pg_dump uitvoeren op de oorspronkelijke data base en vervolgens pg_restore. Na het herstellen voert u de volgende opdracht uit in de herstelde data base:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Volgende stappen
Als u een uitbrei ding die u wilt gebruiken, niet ziet, laat het ons dan weten. Stem op bestaande aanvragen of maak nieuwe feedback aanvragen in ons [Feedback forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
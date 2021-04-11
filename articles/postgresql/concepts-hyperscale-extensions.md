---
title: Extensies – grootschalige (Citus)-Azure Database for PostgreSQL
description: Beschrijft de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van uitbrei dingen in Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012759"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-uitbrei dingen in Azure Database for PostgreSQL – grootschalige (Citus)

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw database uit te breiden met behulp van extensies. Met uitbrei dingen kunt u meerdere gerelateerde SQL-objecten bundelen in één pakket dat kan worden geladen of verwijderd uit uw data base met één opdracht. Nadat de gegevens in de database zijn geladen, kunnen de extensies functioneren als ingebouwde functies. Zie [pakket verwante objecten in een uitbrei ding](https://www.postgresql.org/docs/current/static/extend-extensions.html)voor meer informatie over postgresql-extensies.

## <a name="use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken

PostgreSQL-extensies moeten worden geïnstalleerd in uw Data Base voordat u ze kunt gebruiken. Als u een bepaalde extensie wilt installeren, voert u de opdracht [extensie maken](https://www.postgresql.org/docs/current/static/sql-createextension.html) uit vanuit het psql-hulp programma om de verpakte objecten in uw data base te laden.

Azure Database for PostgreSQL-grootschalige (Citus) ondersteunt momenteel een subset met sleutel uitbreidingen, zoals hier wordt weer gegeven. Extensies die niet worden vermeld, worden niet ondersteund. U kunt geen eigen uitbrei ding maken met Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensies die worden ondersteund door Azure Database for PostgreSQL

De volgende tabellen geven een lijst van de standaard PostgreSQL-extensies die momenteel door Azure Database for PostgreSQL worden ondersteund. Deze informatie is ook beschikbaar door te worden uitgevoerd `SELECT * FROM pg_available_extensions;` .

De versies van elke uitbrei ding die is geïnstalleerd in een server groep, kunnen soms verschillen op basis van de versie van PostgreSQL (11, 12 of 13). De lijst met tabellen extensie versies per database versie.

### <a name="citus-extension"></a>Citus-extensie

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Gedistribueerde data base Citus. | 9,5-1 | 9,5-1 | 10.0-2 |

### <a name="data-types-extensions"></a>Uitbrei dingen van gegevens typen

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Bevat een hoofdletter gevoelig type teken reeks. | 1.5 | 1.6 | 1.6 |
> | [kubus](https://www.postgresql.org/docs/current/static/cube.html) | Biedt een gegevens type voor multidimensionale kubussen. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Biedt een HyperLogLog-gegevens structuur. | 2.14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Biedt een gegevens type voor het opslaan van sets sleutel-waardeparen. | 1.5 | 1.6 | 1,7 |
> | [hebben](https://www.postgresql.org/docs/current/static/isn.html) | Biedt gegevens typen voor International product Numbering Standards. | 1.2 | 1.2 | 1.2 |
> | [laag](https://www.postgresql.org/docs/current/lo.html) | Large Object onderhoud. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Voorziet in een gegevens type voor hiërarchische structuren op boom structuur. | 1.1 | 1,1 | 1,2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Gegevens type voor de weer gave van lijn segmenten of drijvende-komma intervallen. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Gegevens type voor on line accumulatie van op rang gebaseerde statistieken, zoals quantiles en bijgesneden middelen. | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ voor de bovenste n JSONB. | 2.2.2 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Extensies voor zoeken in volledige tekst

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [dicteren \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Voorziet in een woordenlijst sjabloon voor tekst zoeken voor gehele getallen. | 1.0 | 1.0 | 1.0 |
> | [dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Zoek woordenlijst sjabloon voor tekst voor uitgebreide synoniemen verwerking. | 1.0 | 1.0 | 1.0 |
> | [accenten opzeggen](https://www.postgresql.org/docs/current/static/unaccent.html) | Een zoek woordenlijst voor tekst waarmee accenten (diakritische tekens) van lexemes worden verwijderd. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Extensies voor functies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Functies voor auto incrementele velden. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Biedt een manier om grote afstanden te berekenen op het Opper vlak van de aarde. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Biedt verschillende functies voor het bepalen van overeenkomsten en de afstand tussen teken reeksen. | 1.1 | 1.1 | 1.1 |
> | [\_gebruikers naam invoegen](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Functies voor het bijhouden wie een tabel heeft gewijzigd. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Geheel getal, aggregator en Enumerator (verouderd). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Biedt functies en Opera tors voor het bewerken van Null-vrije matrices van gehele getallen. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Functies voor het bijhouden van de tijd van de laatste wijziging. | 1.0 | 1.0 | 1.0 |
> | [PG- \_ deelman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hiermee beheert u gepartitioneerde tabellen op tijd of ID. | 4.1 | 4.4.1 | 4.4.1 |
> | [pag \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Voorziet in functies en Opera tors voor het bepalen van de gelijkenis van alfanumerieke tekst op basis van het afstemmen van Trigram. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Voorziet in cryptografische functies. | 1.3 | 1.3 | 1.3 |
> | [verfijnt](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Functies voor het implementeren van referentiële integriteit (verouderd). | 1.0 | 1.0 | 1.0 |
> | sessie \_ analyse | Functies voor het uitvoeren van query's op hstore-matrices. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Biedt functies voor het bewerken van hele tabellen, inclusief Kruistabel query's. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Geactiveerde wijzigings meldingen. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Functies voor het implementeren van tijd reizen. | 1.0 | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genereert Universele unieke id's (UUID). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Extensies van index typen

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [bloei](https://www.postgresql.org/docs/current/bloom.html) | Toegangs methode voor bloei-index op basis van een hand tekening bestand. | 1.0 | 1.0 | 1.0 |
> | [btree \_ eginnen](https://www.postgresql.org/docs/current/static/btree-gin.html) | Biedt een voor beeld van EGINNEN-operator klassen waarmee B-structuur-like-gedrag voor bepaalde gegevens typen kan worden geïmplementeerd. | 1.3 | 1.3 | 1.3 |
> | [btreee- \_ registratie](https://www.postgresql.org/docs/current/static/btree-gist.html) | Bevat de klassen van een categorie-operator die de B-structuur implementeert. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Taal extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Door PL/pgSQL geladen procedure taal. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Diverse uitbrei dingen

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Beheer functies voor PostgreSQL. | 2,0 | 2,0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Functies voor het controleren van relatie-integriteit. | 1,1 | 1,2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Een module die verbindingen met andere PostgreSQL-data bases in een database sessie ondersteunt. Zie de sectie ' dblink and postgres_fdw ' voor informatie over deze uitbrei ding. | 1.2 | 1.2 | 1.2 |
> | [bestands \_ fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Externe-gegevens wrapper voor toegang tot platte bestanden. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | De inhoud van database pagina's op een laag niveau controleren. | 1,7 | 1,7 | 1.8 |
> | [pag \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Biedt een manier om te controleren wat er gebeurt in de gedeelde buffer cache in realtime. | 1.3 | 1.3 | 1.3 |
> | [pag \_ cron](https://github.com/citusdata/pg_cron) | Job Scheduler voor PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [pag \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Bekijk de toewijzing van de vrije ruimte (FSM). | 1.2 | 1.2 | 1.2 |
> | [pag/ \_ warm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Biedt een manier om relatie gegevens in de buffer cache te laden. | 1.2 | 1.2 | 1.2 |
> | [PG \_ stat- \_ instructies](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Biedt een manier om uitvoerings statistieken op te sporen van alle SQL-instructies die worden uitgevoerd door een server. Zie de sectie ' pg_stat_statements ' voor informatie over deze uitbrei ding. | 1.6 | 1,7 | 1.8 |
> | [\_zicht baarheid van pagina's](https://www.postgresql.org/docs/current/pgvisibility.html) | Bekijk de informatie over de zichtbaarheids kaart (VM) en de zicht baarheid op pagina niveau. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Biedt een manier om vergrendelings informatie op rijniveau te tonen. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Biedt een manier om statistieken op tupleniveau weer te geven. | 1.5 | 1.5 | 1.5 |
> | [post gres \_ fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Externe-gegevens wrapper die wordt gebruikt voor toegang tot gegevens die zijn opgeslagen in externe PostgreSQL-servers. Zie de sectie ' dblink and postgres_fdw ' voor informatie over deze uitbrei ding.| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informatie over TLS/SSL-certificaten. | 1.2 | 1.2 | 1.2 |
> | [TSM \_ systeem \_ rijen](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Methode TABLESAMPLE, waarmee het aantal rijen als een limiet wordt geaccepteerd. | 1.0 | 1.0 | 1.0 |
> | [TSM \_ systeem \_ tijd](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-methode, waarmee de tijd in milliseconden als limiet wordt geaccepteerd. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-query's en XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS-extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** | **PG 11** | **PAG 12** | **PAG 13** |
> |---|---|---|---|---|
> | [Postgis](https://www.postgis.net/), postgis- \_ topologie, postgis \_ Tiger \_ geocodeer, postgis \_ sfcgal | Ruimtelijke en geografische objecten voor PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | adres \_ standaardiserer, adresseer \_ \_ data gegevens \_ | Wordt gebruikt om een adres te parseren in onderdeel elementen. Wordt gebruikt ter ondersteuning van de stap voor het normaliseren van het adres voor geocodering. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis \_ sfcgal | PostGIS SFCGAL-functies. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis \_ Tiger \_ geocodeer | PostGIS Tiger geocodeer en reverse geocodeer. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis- \_ topologie | Ruimtelijke typen en functies van de PostGIS-topologie. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
De [ \_ \_ uitbrei ding pag. instructies](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure database for postgresql-server, zodat u de uitvoerings statistieken van SQL-instructies kunt volgen.

De instelling `pg_stat_statements.track` bepaalt welke instructies worden geteld door de uitbrei ding. De standaard instelling `top` is, wat betekent dat alle instructies die rechtstreeks door clients worden uitgegeven, worden bijgehouden. De twee andere tracking niveaus zijn `none` en `all` . Deze instelling kan worden geconfigureerd als een server parameter via de [Azure Portal](./howto-configure-server-parameters-using-portal.md) of de [Azure cli](./howto-configure-server-parameters-using-cli.md).

Er is sprake van een afweging tussen de informatie over het uitvoeren van query's pg_stat_statements biedt en het effect op Server prestaties, terwijl elke SQL-instructie wordt geregistreerd. Als u de uitbrei ding pg_stat_statements niet actief gebruikt, wordt u aangeraden `pg_stat_statements.track` om in te stellen op `none` . Sommige bewakings services van derden zijn afhankelijk van pg_stat_statements om query prestaties te kunnen leveren, dus bevestig of dit het geval voor u is.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw

U kunt dblink en post gres \_ fdw gebruiken om verbinding te maken tussen een postgresql server en een andere Data Base op dezelfde server.  De ontvangende server moet verbindingen vanaf de verzendende server via de Firewall toestaan.  Als u deze uitbrei dingen wilt gebruiken om verbinding te maken tussen Azure Database for PostgreSQL servers of grootschalige (Citus)-Server groepen, stelt u **Azure-Services en-resources in staat om toegang te krijgen tot deze server groep (of server)** naar op.  U moet deze instelling ook inschakelen als u de uitbrei dingen wilt gebruiken om naar dezelfde server te gaan.
De instelling **Azure-Services en-resources toegang tot deze server groep toestaan vindt u** op de pagina Azure portal voor de Server groep grootschalige (Citus) onder **netwerken**.  Momenteel worden uitgaande verbindingen van Azure Database for PostgreSQL één server en grootschalige (Citus) niet ondersteund, met uitzonde ring van verbindingen met andere Azure Database for PostgreSQL servers en grootschalige (Citus)-Server groepen.

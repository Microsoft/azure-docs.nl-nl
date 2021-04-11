---
title: Extensies-Azure Database for PostgreSQL-flexibele server
description: Meer informatie over de beschik bare post gres-extensies in Azure Database for PostgreSQL-flexibele server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: e8f71eb120b86f35672c9123b52f7f19c9fee662
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608456"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL-extensies in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Extensies bundelen meerdere gerelateerde SQL-objecten in één pakket dat kan worden geladen of verwijderd uit uw data base met behulp van een opdracht. Nadat de gegevens in de database zijn geladen, functioneren de extensies als ingebouwde functies.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken
PostgreSQL-extensies moeten worden geïnstalleerd in uw Data Base voordat u ze kunt gebruiken. Voer de opdracht [extensie maken](https://www.postgresql.org/docs/current/sql-createextension.html) uit om een bepaalde uitbrei ding te installeren. Met deze opdracht worden de verpakte objecten in uw data base geladen.

Azure Database for PostgreSQL ondersteunt een subset van de sleutel uitbreidingen zoals hieronder wordt weer gegeven. Deze informatie is ook beschikbaar door te worden uitgevoerd `SHOW azure.extensions;` . Extensies die niet in dit document worden vermeld, worden niet ondersteund op Azure Database for PostgreSQL flexibele server. U kunt geen eigen uitbrei ding maken of laden in Azure Database for PostgreSQL.


## <a name="postgres-12-extensions"></a>Post gres 12-extensies

De volgende uitbrei dingen zijn beschikbaar in Azure Database for PostgreSQL flexibele servers die post gres-versie 12 hebben. 

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | functies voor het controleren van relatie-integriteit|
> |[bloei](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | toegangs methode voor bloei-hand tekening bestand op basis van index|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | tekst zoeken woordenlijst sjabloon voor uitgebreide synoniemen verwerking|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | geheel getal, aggregator en enumerator. Verouderd|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | de inhoud van database pagina's op een laag niveau controleren|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | de gedeelde buffer cache controleren|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | Bekijk de toewijzing van de vrije ruimte (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | Bekijk de informatie over de zichtbaarheids kaart (VM) en de zicht baarheid op pagina niveau|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | statistieken van tuple-niveau weer geven|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometrie, geografie |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | Raster typen en-functies van PostGIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informatie over SSL-certificaten|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-methode die het aantal rijen als een limiet accepteert|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Methode TABLESAMPLE waarmee tijd in milliseconden als limiet wordt geaccepteerd|
> |[accenten opzeggen](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|

## <a name="postgres-11-extensions"></a>Post gres 11-extensies

De volgende uitbrei dingen zijn beschikbaar in Azure Database for PostgreSQL flexibele servers die post gres-versie 11 hebben. 

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | functies voor het controleren van relatie-integriteit|
> |[bloei](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | toegangs methode voor bloei-hand tekening bestand op basis van index|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[kubus](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | tekst zoeken woordenlijst sjabloon voor uitgebreide synoniemen verwerking|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | geheel getal, aggregator en enumerator. Verouderd|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[hebben](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | de inhoud van database pagina's op een laag niveau controleren|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | de gedeelde buffer cache controleren|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | Bekijk de toewijzing van de vrije ruimte (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | Bekijk de informatie over de zichtbaarheids kaart (VM) en de zicht baarheid op pagina niveau|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | biedt controle functionaliteit|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | statistieken van tuple-niveau weer geven|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informatie over SSL-certificaten|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-methode die het aantal rijen als een limiet accepteert|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Methode TABLESAMPLE waarmee tijd in milliseconden als limiet wordt geaccepteerd|
> |[accenten opzeggen](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|


## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
met [dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) en [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) kunt u verbinding maken met een postgresql-server of een andere Data Base op dezelfde server. Flexibele server ondersteunt zowel binnenkomende als uitgaande verbindingen met een wille keurige PostgreSQL-server. De verzendende server moet uitgaande verbindingen met de ontvangende server toestaan. Op dezelfde manier moet de ontvangende server verbindingen met de verzendende server toestaan. 

U wordt aangeraden uw servers te implementeren met [VNet-integratie](concepts-networking.md) als u van plan bent om deze twee uitbrei dingen te gebruiken. Standaard maakt VNet-integratie verbindingen mogelijk tussen servers in het VNET. U kunt er ook voor kiezen om de toegang aan te passen met [VNet-netwerk beveiligings groepen](../../virtual-network/manage-network-security-group.md) .

## <a name="pg_prewarm"></a>pg_prewarm

Met de extensie pg_prewarm worden relationele gegevens in de cache geladen. Bij de voor bereiding van uw caches hebt u na het opnieuw opstarten betere reactie tijden bij de eerste uitvoering van uw query's. De automatische prewarme-functionaliteit is momenteel niet beschikbaar in Azure Database for PostgreSQL-flexibele server.

## <a name="pg_stat_statements"></a>pg_stat_statements
De [uitbrei ding pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure database for PostgreSQL flexibele server, zodat u de uitvoerings statistieken van SQL-instructies kunt volgen.
De instelling `pg_stat_statements.track` , die bepaalt welke instructies door de uitbrei ding worden geteld, wordt standaard ingesteld op `top` , wat betekent dat alle instructies die rechtstreeks door clients worden uitgegeven, worden bijgehouden. De twee andere tracking niveaus zijn `none` en `all` . Deze instelling kan worden geconfigureerd als een server parameter.

Er is een verhouding tussen de informatie over het uitvoeren van query's pg_stat_statements biedt en de invloed op de prestaties van de server bij het vastleggen van elke SQL-instructie. Als u de extensie pg_stat_statements niet actief gebruikt, wordt u aangeraden `pg_stat_statements.track` om in te stellen op `none` . Houd er rekening mee dat sommige bewakings services van derden afhankelijk zijn van pg_stat_statements voor het leveren van query's met betrekking tot query prestaties, zodat u kunt controleren of dit het geval is voor u of niet.


## <a name="next-steps"></a>Volgende stappen

Als u een uitbrei ding die u wilt gebruiken, niet ziet, laat het ons dan weten. Stem op bestaande aanvragen of maak nieuwe feedback aanvragen in ons [Feedback forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
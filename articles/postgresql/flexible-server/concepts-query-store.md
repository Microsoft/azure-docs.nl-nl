---
title: Query Store - Azure Database for PostgreSQL - Flex Server
description: In dit artikel wordt de functie Query Store in Azure Database for PostgreSQL - Flex Server beschreven.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559012"
---
# <a name="monitor-performance-with-query-store"></a>Prestaties bewaken met Query Store
**Van toepassing op:** Azure Database for PostgreSQL Flex Server versie 11 en hoger

De functie Query Store in Azure Database for PostgreSQL een manier om queryprestaties in de tijd bij te houden. Query Store vereenvoudigt het oplossen van prestatieproblemen door u te helpen snel de langst lopende en meest resource-intensieve query's te vinden. Query Store legt automatisch een geschiedenis van query's en runtimestatistieken vast en behoudt deze voor uw beoordeling. De gegevens worden gesegmenteerd op tijd, zodat u tijdelijke gebruikspatronen kunt zien. Gegevens voor alle gebruikers, databases en query's worden opgeslagen in een database met de **azure_sys** in het Azure Database for PostgreSQL exemplaar.

> [!IMPORTANT]
> Wijzig de **azure_sys** database of het schema ervan niet. Als u dit doet, werken Query Store en gerelateerde prestatiefuncties niet goed.
## <a name="enabling-query-store"></a>Query Store inschakelen
Query Store is een opt-in-functie en is dus niet standaard ingeschakeld op een server. Query Store is algemeen ingeschakeld of uitgeschakeld voor alle databases op een bepaalde server en kan niet per database worden in- of uitgeschakeld.
### <a name="enable-query-store-using-the-azure-portal"></a>Query Store inschakelen met behulp van de Azure Portal
1. Meld u aan bij Azure Portal en selecteer uw Azure Database for PostgreSQL server.
2. Selecteer **Serverparameters** in **de sectie** Instellingen van het menu.
3. Zoek naar de `pg_qs.query_capture_mode` parameter .
4. Stel de waarde in op `TOP` of `ALL` en sla **op.**
Wacht 20 minuten tot de eerste batch met gegevens is vastgelegd in database azure_sys.
## <a name="information-in-query-store"></a>Informatie in Query Store
Query Store heeft één winkel:
- Een runtime-statistiekenopslag voor het persistent maken van de gegevens van de queryuitvoeringsstatistieken.

Veelvoorkomende scenario's voor het gebruik van Query Store zijn:
- Bepalen hoe vaak een query is uitgevoerd in een bepaald tijdvenster
- Vergelijking van de gemiddelde uitvoeringstijd van een query in verschillende tijdvensters om grote delta's te zien
- Langstlopende query's in de afgelopen paar uur identificeren Om het ruimtegebruik te minimaliseren, worden de uitvoeringsstatistieken van de runtime in de runtime-statistiekenopslag geaggregeerd in een vast, configureerbaar tijdvenster. De informatie in deze winkels kan worden opgevraagd met behulp van weergaven.
## <a name="access-query-store-information"></a>Informatie over Query Store openen
Query Store-gegevens worden opgeslagen in azure_sys database op uw Postgres-server. De volgende query retourneert informatie over query's in Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Configuratie-opties
Wanneer Query Store is ingeschakeld, worden gegevens opgeslagen in aggregatievensters van 15 minuten, maximaal 500 afzonderlijke query's per venster. De volgende opties zijn beschikbaar voor het configureren van Query Store-parameters.

| **Parameter** | **Beschrijving** | **Standaard** | **Bereik**|
|---|---|---|---|
| pg_qs.query_capture_mode | Hiermee stelt u in welke instructies worden bijgespoord. | geen | none, top, all |
| pg_qs.max_query_text_length | Hiermee stelt u de maximale querylengte die kan worden opgeslagen. Langere query's worden afgekapt. | 6000 | 100 - 10.000 |
| pg_qs.retention_period_in_days | Hiermee stelt u de retentieperiode in. | 7 | 1 - 30 |
| pg_qs.track_utility | Hiermee stelt u in of de opdrachten van het hulpprogramma worden bijgespoord | op | aan, uit |

Gebruik de [Azure Portal](howto-configure-server-parameters-using-portal.md) om een andere waarde voor een parameter op te halen of in te stellen.

## <a name="views-and-functions"></a>Weergaven en functies
Query Store weergeven en beheren met behulp van de volgende weergaven en functies. Iedereen met de openbare postgreSQL-rol kan deze weergaven gebruiken om de gegevens in Query Store te bekijken. Deze weergaven zijn alleen beschikbaar in **azure_sys** database.
Query's worden genormaliseerd door te kijken naar hun structuur na het verwijderen van letterlijke en constanten. Als twee query's identiek zijn, met uitzondering van letterlijke waarden, hebben ze dezelfde queryId.
### <a name="query_storeqs_view"></a>query_store.qs_view
Deze weergave retourneert alle gegevens in Query Store. Er is één rij voor elke afzonderlijke database-id, gebruikers-id en query-id. 

|**Naam**   |**Type** | **Referenties**  | **Beschrijving**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Id uit de runtime_stats_entries tabel|
|user_id    |Oid    |pg_authid.oid  |OID van de gebruiker die de instructie heeft uitgevoerd|
|db_id  |Oid    |pg_database.oid    |OID van de database waarin de instructie is uitgevoerd|
|query_id   |bigint  || Interne hash-code, berekend vanuit de parsestructuur van de instructie|
|query_sql_text |Varchar(10000)  || Tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samen geclusterd; deze tekst is de tekst voor de eerste van de query's in het cluster.|
|plan_id    |bigint |   |Id van het plan dat overeenkomt met deze query, nog niet beschikbaar|
|start_tijd |tijdstempel  ||  Query's worden geaggregeerd door tijdsemmers. De tijdspanne van een bucket is standaard 15 minuten. Dit is de begintijd die overeenkomt met de tijdse bucket voor deze vermelding.|
|end_time   |tijdstempel  ||  Eindtijd die overeenkomt met de tijdse bucket voor dit item.|
|Oproepen  |bigint  || Aantal keren dat de query is uitgevoerd|
|total_time |dubbele precisie   ||  Totale uitvoeringstijd van query's, in milliseconden|
|min_time   |dubbele precisie   ||  Minimale uitvoeringstijd van query's, in milliseconden|
|max_time   |dubbele precisie   ||  Maximale uitvoeringstijd van query's, in milliseconden|
|mean_time  |dubbele precisie   ||  Gemiddelde uitvoeringstijd van query's, in milliseconden|
|stddev_time|   dubbele precisie    ||  Standaarddeviatie van de uitvoeringstijd van de query, in milliseconden |
|Rijen   |bigint ||  Totaal aantal rijen dat is opgehaald of beïnvloed door de instructie|
|shared_blks_hit|   bigint  ||  Totaal aantal gedeelde blokcachetreffers op de instructie|
|shared_blks_read|  bigint  ||  Totaal aantal gedeelde blokken dat door de instructie wordt gelezen|
|shared_blks_dirtied|   bigint   || Totaal aantal gedeelde blokken dat is geseed door de instructie |
|shared_blks_written|   bigint  ||  Totaal aantal gedeelde blokken geschreven door de instructie|
|local_blks_hit|    bigint ||   Het totale aantal lokale blokcachetreffers op de instructie|
|local_blks_read|   bigint   || Totaal aantal lokale blokken dat door de instructie wordt gelezen|
|local_blks_dirtied|    bigint  ||  Totaal aantal lokale blokken dat is geseed door de instructie|
|local_blks_written|    bigint  ||  Totaal aantal lokale blokken geschreven door de instructie|
|temp_blks_read |bigint  || Totaal aantal tijdelijke blokken dat door de instructie wordt gelezen|
|temp_blks_written| bigint   || Totaal aantal tijdelijke blokken dat is geschreven door de instructie|
|blk_read_time  |dubbele precisie    || Totale tijd dat de instructie blokken heeft gelezen, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
|blk_write_time |dubbele precisie    || Totale tijd die de instructie heeft besteed aan het schrijven van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_teksten_view
Deze weergave retourneert querytekstgegevens in Query Store. Er is één rij voor elke afzonderlijke query_text.

| **Naam** | **Type** | **Beschrijving** |
|--|--|--|
| query_text_id | bigint | Id voor de query_texts tabel |
| query_sql_text | Varchar(10000) | Tekst van een representatieve instructie. Verschillende query's met dezelfde structuur worden samen geclusterd; deze tekst is de tekst voor de eerste van de query's in het cluster. |

### <a name="functions"></a>Functions
`qs_reset` alle statistieken die tot nu toe door Query Store zijn verzameld, worden genegeerd. Deze functie kan alleen worden uitgevoerd door de serverbeheerdersrol.

`staging_data_reset` alle statistieken verwijderd die in het geheugen zijn verzameld door Query Store (dat wil zeggen, de gegevens in het geheugen die nog niet zijn leeg gemaakt in de database). Deze functie kan alleen worden uitgevoerd door de serverbeheerdersrol.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
- Als op een PostgreSQL-server de parameter default_transaction_read_only, worden er geen gegevens door Query Store vastleggen.
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [scenario's waarin Query Store met name nuttig kan zijn.](concepts-query-store-scenarios.md)
- Meer informatie over [best practices voor het gebruik van Query Store.](concepts-query-store-best-practices.md)

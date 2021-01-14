---
title: Query Store-Azure Database for MySQL
description: Meer informatie over de functie query Store in Azure Database for MySQL om de prestaties na verloop van tijd bij te houden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 82482b260233994672e603c16fe8cf919c92337f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201022"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Azure Database for MySQL prestaties bewaken met query Store

**Van toepassing op:** Azure Database for MySQL 5,7, 8,0

De functie query Store in Azure Database for MySQL biedt een manier om query prestaties na verloop van tijd bij te houden. Query Store vereenvoudigt het oplossen van problemen met prestaties, omdat u snel de langstlopende en meest tijdrovende query's kunt vinden. In de query Store wordt automatisch een geschiedenis van query's en runtime-statistieken vastgelegd en worden deze voor uw beoordeling bewaard. De gegevens worden op tijd Vensters gescheiden, zodat u de database gebruiks patronen kunt zien. Gegevens voor alle gebruikers, data bases en query's worden opgeslagen in de **MySQL** -schema database in het Azure database for MySQL-exemplaar.

## <a name="common-scenarios-for-using-query-store"></a>Algemene scenario's voor het gebruik van query Store

Query Store kan worden gebruikt in een aantal scenario's, waaronder de volgende:

- Teruggedraaide-query's detecteren
- Bepalen hoe vaak een query in een bepaald tijd venster is uitgevoerd
- De gemiddelde uitvoerings tijd van een query vergelijken in tijd Vensters om grote verschillen te bekijken

## <a name="enabling-query-store"></a>Query Store inschakelen

Query Store is een opt-in-functie, waardoor deze niet standaard actief is op een server. Het query archief is globaal ingeschakeld of uitgeschakeld voor alle data bases op een bepaalde server en kan niet worden in-of uitgeschakeld per data base.

### <a name="enable-query-store-using-the-azure-portal"></a>Query Store inschakelen met behulp van de Azure Portal

1. Meld u aan bij de Azure Portal en selecteer uw Azure Database for MySQL-server.
1. Selecteer **server parameters** in de sectie **instellingen** van het menu.
1. Zoek de para meter query_store_capture_mode.
1. Stel de waarde in op alles en **Sla** deze op.

Wachtende statistieken in het query archief inschakelen:

1. Zoek de para meter query_store_wait_sampling_capture_mode.
1. Stel de waarde in op alles en **Sla** deze op.

Maxi maal 20 minuten toestaan dat de eerste batch met gegevens persistent is in de MySQL-data base.

## <a name="information-in-query-store"></a>Informatie in query Store

Het query archief heeft twee winkels:

- Een runtime-statistieken Archief voor het persistent maken van de statistieken voor query-uitvoering.
- Een wacht statistieken Archief voor het persistent maken van gegevens over wacht tijden.

Om ruimte gebruik te minimaliseren, worden de runtime uitvoerings statistieken in de runtime-statistieken opslag geaggregeerd over een vast, configureerbaar tijd venster. De informatie in deze archieven is zichtbaar door query's uit te geven op de query Store-weer gaven.

De volgende query retourneert informatie over query's in query Store:

```sql
SELECT * FROM mysql.query_store;
```

Of deze query voor wachtende statistieken:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Wacht query's zoeken

> [!NOTE]
> Wacht statistieken mogen niet worden ingeschakeld tijdens piek uren van de werk belasting of voor onbepaalde tijd worden ingeschakeld voor gevoelige werk belastingen. <br>Voor workloads die worden uitgevoerd met een hoog CPU-gebruik of op servers die zijn geconfigureerd met een lagere vCores, moet u voorzichtig zijn bij het inschakelen van wacht statistieken. Deze moet niet voor onbepaalde tijd worden ingeschakeld.

Wacht gebeurtenis typen combi neren verschillende wacht gebeurtenissen in buckets op vergelijk bare wijze. Het query archief bevat het type wacht gebeurtenis, specifieke wacht gebeurtenis naam en de query in kwestie. Als u deze wacht gegevens wilt correleren met de statistieken voor de runtime van query's, betekent dit dat u meer inzicht krijgt in wat bijdragen aan de prestatie kenmerken van de query.

Hier volgen enkele voor beelden van hoe u meer inzicht kunt krijgen in uw werk belasting met behulp van de wacht statistieken in query Store:

| **Observ** | **Actie** |
|---|---|
|Wacht tijden met hoge vergren deling | Controleer de query teksten voor de betrokken query's en Identificeer de doel entiteiten. Zoek in query Store naar andere query's die dezelfde entiteit wijzigen, die regel matig wordt uitgevoerd en/of een hoge duur hebben. Nadat u deze query's hebt geïdentificeerd, kunt u overwegen om de toepassings logica te wijzigen om gelijktijdig gebruik te kunnen verbeteren of een minder beperkend isolatie niveau te gebruiken. |
|Hoge buffer-IO-wacht tijden | Zoek de query's met een groot aantal fysieke Lees bewerkingen in query Store. Als ze overeenkomen met de query's met hoge i/o-wacht tijden, kunt u een index voor de onderliggende entiteit introduceren, om te zoeken in plaats van scans. Hierdoor worden de i/o-overhead van de query's geminimaliseerd. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen voor deze server zijn die de query's optimaliseren. |
|Hoog geheugen wacht tijden | Zoek het hoogste geheugen gebruik query's in query Store. Deze query's vertragen waarschijnlijk verdere voortgang van de betrokken query's. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen zijn waarmee deze query's kunnen worden geoptimaliseerd. |

## <a name="configuration-options"></a>Configuratie-opties

Wanneer query Store is ingeschakeld, worden gegevens opgeslagen in een periode van 15 minuten voor aggregatie van Maxi maal 500 DISTINCT-query's per venster.

De volgende opties zijn beschikbaar voor het configureren van query Store-para meters.

| **Parameter** | **Beschrijving** | **Prijs** | **Bereik** |
|---|---|---|---|
| query_store_capture_mode | De functie query Store in-of uitschakelen op basis van de waarde. Opmerking: als performance_schema is uitgeschakeld, wordt performance_schema en een subset van de performance schema-instrumenten die voor deze functie zijn vereist query_store_capture_mode, ingeschakeld. | ALL | GEEN, ALLE |
| query_store_capture_interval | De interval voor het vastleggen van de query opslag in minuten. Hiermee kunt u het interval opgeven waarin de metrische gegevens van de query worden geaggregeerd | 15 | 5 - 60 |
| query_store_capture_utility_queries | In-of uitschakelen voor het vastleggen van alle hulp query's die in het systeem worden uitgevoerd. | NO | JA, NEE |
| query_store_retention_period_in_days | Tijd venster in dagen dat de gegevens in het query archief moeten worden bewaard. | 7 | 1 - 30 |

De volgende opties zijn specifiek van toepassing op wacht statistieken.

| **Parameter** | **Beschrijving** | **Prijs** | **Bereik** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Hiermee kunt u de wacht statistieken in-of uitschakelen. | GEEN | GEEN, ALLE |
| query_store_wait_sampling_frequency | Wijzigt de frequentie van wacht-sampling in seconden. 5 tot 300 seconden. | 30 | 5-300 |

> [!NOTE]
> Op dit moment is **query_store_capture_mode** vervangen door deze configuratie, wat betekent dat zowel **query_store_capture_mode** als **QUERY_STORE_WAIT_SAMPLING_CAPTURE_MODE** moeten worden ingeschakeld om alle wacht statistieken te kunnen gebruiken. Als **query_store_capture_mode** is uitgeschakeld, is de wacht tijd van de statistieken uitgeschakeld, omdat wacht tijden worden gebruikt voor de performance_schema ingeschakeld en de query_text vastgelegd door query Store.

Gebruik de [Azure Portal](howto-server-parameters.md) of [Azure cli](howto-configure-server-parameters-using-cli.md) om een andere waarde voor een para meter op te halen of in te stellen.

## <a name="views-and-functions"></a>Weer gaven en functies

Bekijk en beheer query Store met behulp van de volgende weer gaven en functies. Iedereen in de [publieke rol Select-bevoegdheid](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) kan deze weer gaven gebruiken om de gegevens in query Store te bekijken. Deze weer gaven zijn alleen beschikbaar in de **MySQL** -data base.

Query's worden genormaliseerd door de structuur te bekijken na het verwijderen van letterlijke waarden en constanten. Als twee query's identiek zijn, met uitzonde ring van letterlijke waarden, hebben ze dezelfde hash.

### <a name="mysqlquery_store"></a>mysql.query_store

In deze weer gave worden alle gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID en query-ID.

| **Naam** | **Gegevens type** | **IS_NULLABLE** | **Beschrijving** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | Naam van het schema |
| `query_id`| bigint (20) | NO| De unieke ID die voor de specifieke query is gegenereerd, als dezelfde query in een ander schema wordt uitgevoerd, wordt een nieuwe ID gegenereerd |
| `timestamp_id` | tijdstempel| NO| Tijds tempel waarin de query wordt uitgevoerd. Dit is gebaseerd op de configuratie van de query_store_interval|
| `query_digest_text`| LONGTEXT| NO| De genormaliseerde query tekst nadat alle letterlijke waarden zijn verwijderd|
| `query_sample_text` | LONGTEXT| NO| Eerste weer gave van de werkelijke query met letterlijke waarden|
| `query_digest_truncated` | bit| JA| Hiermee wordt aangegeven of de query tekst is afgekapt. De waarde is Ja als de query langer is dan 1 KB|
| `execution_count` | bigint (20)| NO| Het aantal keren dat de query is uitgevoerd voor deze tijds tempel-ID/tijdens de geconfigureerde interval periode|
| `warning_count` | bigint (20)| NO| Aantal waarschuwingen dat deze query heeft gegenereerd tijdens de interne|
| `error_count` | bigint (20)| NO| Aantal fouten dat deze query heeft gegenereerd tijdens het interval|
| `sum_timer_wait` | double| JA| Totale uitvoerings tijd van deze query tijdens het interval|
| `avg_timer_wait` | double| JA| Gemiddelde uitvoerings tijd voor deze query tijdens het interval|
| `min_timer_wait` | double| JA| Minimale uitvoerings tijd voor deze query|
| `max_timer_wait` | double| JA| Maximale uitvoerings tijd|
| `sum_lock_time` | bigint (20)| NO| De totale hoeveelheid tijd die is besteed aan alle vergren delingen voor deze uitvoering van deze query tijdens dit tijd venster|
| `sum_rows_affected` | bigint (20)| NO| Aantal rijen dat wordt beïnvloed|
| `sum_rows_sent` | bigint (20)| NO| Aantal rijen dat naar de client is verzonden|
| `sum_rows_examined` | bigint (20)| NO| Aantal onderzochte rijen|
| `sum_select_full_join` | bigint (20)| NO| Aantal volledige samen voegingen|
| `sum_select_scan` | bigint (20)| NO| Aantal geselecteerde scans |
| `sum_sort_rows` | bigint (20)| NO| Aantal gesorteerde rijen|
| `sum_no_index_used` | bigint (20)| NO| Aantal keren dat de query geen indexen heeft gebruikt|
| `sum_no_good_index_used` | bigint (20)| NO| Aantal keren dat de engine voor het uitvoeren van query's geen goede indexen heeft gebruikt|
| `sum_created_tmp_tables` | bigint (20)| NO| Totaal aantal tijdelijke tabellen gemaakt|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| Totaal aantal tijdelijke tabellen gemaakt op de schijf (genereert I/O)|
| `first_seen` | tijdstempel| NO| Het eerste exemplaar (UTC) van de query tijdens het aggregatie venster|
| `last_seen` | tijdstempel| NO| Het laatste exemplaar (UTC) van de query tijdens dit aggregatie venster|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Met deze weer gave worden wachtende gebeurtenis gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID, query-ID en gebeurtenis.

| **Naam**| **Gegevens type** | **IS_NULLABLE** | **Beschrijving** |
|---|---|---|---|
| `interval_start` | tijdstempel | NO| Begin van het interval (toename van 15 minuten)|
| `interval_end` | tijdstempel | NO| Einde van het interval (toename van 15 minuten)|
| `query_id` | bigint (20) | NO| Gegenereerde unieke ID voor de genormaliseerde query (uit query Store)|
| `query_digest_id` | varchar (32) | NO| De genormaliseerde query tekst na het verwijderen van alle letterlijke waarden (uit query Store) |
| `query_digest_text` | LONGTEXT | NO| Eerste weer gave van de werkelijke query met letterlijke waarden (uit query Store) |
| `event_type` | varchar (32) | NO| Categorie van de gebeurtenis wait |
| `event_name` | varchar (128) | NO| Naam van de gebeurtenis wait |
| `count_star` | bigint (20) | NO| Aantal wacht gebeurtenissen dat wordt voor bereid tijdens het interval voor de query |
| `sum_timer_wait_ms` | double | NO| Totale wacht tijd (in milliseconden) van deze query tijdens het interval |

### <a name="functions"></a>Functies

| **Naam**| **Beschrijving** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Hiermee worden alle gegevens van het query archief vóór de opgegeven tijds tempel verwijderd |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Verwijdert alle gebeurtenis gegevens na de opgegeven tijds tempel |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Verwijdert aanbevelingen waarvan de verval datum vóór de opgegeven tijds tempel ligt |

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

- Als een MySQL-server de para meter `default_transaction_read_only` op heeft, kan de query Store geen gegevens vastleggen.
- De functionaliteit van het query archief kan worden onderbroken als er lange Unicode-query's worden aangetroffen ( \> = 6000 bytes).
- De Bewaar periode voor wacht statistieken is 24 uur.
- Wacht statistieken gebruiken voor beeld om een fractie van gebeurtenissen vast te leggen. De frequentie kan worden gewijzigd met behulp van de para meter `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [query prestaties inzichten](concepts-query-performance-insight.md)

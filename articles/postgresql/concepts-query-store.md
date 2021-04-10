---
title: Query Store-Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie query Store in Azure Database for PostgreSQL-één server beschreven.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 1779df1c5f9baf2aa46ff809ecae9ec5e3cd7adb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581567"
---
# <a name="monitor-performance-with-the-query-store"></a>Prestaties bewaken met de query Store

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6 en hoger

De functie query Store in Azure Database for PostgreSQL biedt een manier om query prestaties na verloop van tijd bij te houden. Query Store vereenvoudigt het oplossen van problemen met prestaties, omdat u snel de langstlopende en meest tijdrovende query's kunt vinden. In de query Store wordt automatisch een geschiedenis van query's en runtime-statistieken vastgelegd en worden deze voor uw beoordeling bewaard. De gegevens worden op tijd Vensters gescheiden, zodat u de database gebruiks patronen kunt zien. Gegevens voor alle gebruikers, data bases en query's worden opgeslagen in een Data Base met de naam **azure_sys** in het Azure database for PostgreSQL-exemplaar.

> [!IMPORTANT]
> Wijzig de **azure_sys** data base of de bijbehorende schema's niet. Dit zorgt ervoor dat query Store en gerelateerde prestatie functies niet goed werken.

## <a name="enabling-query-store"></a>Query Store inschakelen
Query Store is een opt-in-functie, waardoor deze niet standaard actief is op een server. De Store is globaal ingeschakeld of uitgeschakeld voor alle data bases op een bepaalde server en kan niet worden in-of uitgeschakeld per data base.

### <a name="enable-query-store-using-the-azure-portal"></a>Query Store inschakelen met behulp van de Azure Portal
1. Meld u aan bij de Azure Portal en selecteer uw Azure Database for PostgreSQL-server.
2. Selecteer **server parameters** in de sectie **instellingen** van het menu.
3. Zoek de `pg_qs.query_capture_mode` para meter.
4. Stel de waarde in op `TOP` en **Sla** deze op.

Wachtende statistieken in het query archief inschakelen: 
1. Zoek de `pgms_wait_sampling.query_capture_mode` para meter.
1. Stel de waarde in op `ALL` en **Sla** deze op.


U kunt deze para meters ook instellen met behulp van de Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Wacht 20 minuten tot de eerste batch met gegevens is vastgelegd in database azure_sys.

## <a name="information-in-query-store"></a>Informatie in query Store
Het query archief heeft twee winkels:
- Een runtime-statistieken opslag voor het persistent maken van de statistieken voor query-uitvoering.
- Een wacht statistieken opslag voor permanente wacht statistieken.

Veelvoorkomende scenario's voor het gebruik van query Store zijn onder andere:
- Bepalen hoe vaak een query in een bepaald tijd venster is uitgevoerd
- De gemiddelde uitvoerings tijd van een query vergelijken in tijd Vensters om grote verschillen te bekijken
- Langst uitgevoerde query's in de afgelopen X uur identificeren
- Eerste N query's identificeren die op resources wachten
- Wat is de wacht aard van een bepaalde query?

Om ruimte gebruik te minimaliseren, worden de runtime uitvoerings statistieken in de runtime-statistieken opslag geaggregeerd over een vast, configureerbaar tijd venster. De informatie in deze archieven is zichtbaar door query's uit te geven op de query Store-weer gaven.

## <a name="access-query-store-information"></a>Informatie over toegang tot query Store

Query Store-gegevens worden opgeslagen in de azure_sys-Data Base op uw post gres-server. 

De volgende query retourneert informatie over query's in query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Of deze query voor wachtende statistieken:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Wacht query's zoeken
Wacht gebeurtenis typen combi neren verschillende wacht gebeurtenissen in buckets op vergelijk bare wijze. Het query archief bevat het type wacht gebeurtenis, specifieke wacht gebeurtenis naam en de query in kwestie. Als u deze wacht gegevens wilt correleren met de statistieken voor de runtime van query's, betekent dit dat u meer inzicht krijgt in wat bijdragen aan de prestatie kenmerken van de query.

Hier volgen enkele voor beelden van hoe u meer inzicht kunt krijgen in uw werk belasting met behulp van de wacht statistieken in query Store:

| **Observ** | **Actie** |
|---|---|
|Wacht tijden met hoge vergren deling | Controleer de query teksten voor de betrokken query's en Identificeer de doel entiteiten. Zoek in query Store naar andere query's die dezelfde entiteit wijzigen, die regel matig wordt uitgevoerd en/of een hoge duur hebben. Nadat u deze query's hebt geïdentificeerd, kunt u overwegen om de toepassings logica te wijzigen om gelijktijdig gebruik te kunnen verbeteren of een minder beperkend isolatie niveau te gebruiken.|
| Hoge buffer-IO-wacht tijden | Zoek de query's met een groot aantal fysieke Lees bewerkingen in query Store. Als ze overeenkomen met de query's met hoge i/o-wacht tijden, kunt u overwegen een index voor de onderliggende entiteit te introduceren, om te zoeken in plaats van scans. Hierdoor worden de i/o-overhead van de query's geminimaliseerd. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen voor deze server zijn die de query's optimaliseren.|
| Hoog geheugen wacht tijden | Zoek het hoogste geheugen gebruik query's in query Store. Deze query's vertragen waarschijnlijk verdere voortgang van de betrokken query's. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen zijn waarmee deze query's kunnen worden geoptimaliseerd.|

## <a name="configuration-options"></a>Configuratie-opties
Wanneer query Store is ingeschakeld, worden gegevens opgeslagen in een periode van 15 minuten voor aggregatie van Maxi maal 500 DISTINCT-query's per venster. 

De volgende opties zijn beschikbaar voor het configureren van query Store-para meters.

| **Parameter** | **Beschrijving** | **Prijs** | **Bereik**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Hiermee stelt u in welke instructies worden bijgehouden. | geen | geen, boven, alle |
| pg_qs pg_qs.max_query_text_length | Hiermee stelt u de maximale query lengte in die kan worden opgeslagen. Langere query's worden afgekapt. | 6000 | 100-10K |
| pg_qs pg_qs.retention_period_in_days | Hiermee stelt u de Bewaar periode. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Hiermee wordt ingesteld of hulp opdrachten worden bijgehouden | op | aan, uit |

De volgende opties zijn specifiek van toepassing op wacht statistieken.

| **Parameter** | **Beschrijving** | **Prijs** | **Bereik**|
|---|---|---|---|
| pgms_wait_sampling pgms_wait_sampling.query_capture_mode | Hiermee wordt ingesteld welke instructies worden bijgehouden voor wachtende statistieken. | geen | geen, alle|
| Pgms_wait_sampling Pgms_wait_sampling.history_period | Stel de frequentie in milliseconden in waarop wacht gebeurtenissen worden bemonsterd. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** vervangt **pgms_wait_sampling. query_capture_mode**. Als pg_qs. query_capture_mode geen is, heeft de instelling pgms_wait_sampling. query_capture_mode geen effect.


Gebruik de [Azure Portal](howto-configure-server-parameters-using-portal.md) of [Azure cli](howto-configure-server-parameters-using-cli.md) om een andere waarde voor een para meter op te halen of in te stellen.

## <a name="views-and-functions"></a>Weer gaven en functies
Bekijk en beheer query Store met behulp van de volgende weer gaven en functies. Iedereen in de open bare rol PostgreSQL kan deze weer gaven gebruiken om de gegevens in query Store te bekijken. Deze weer gaven zijn alleen beschikbaar in de **azure_sys** -data base.

Query's worden genormaliseerd door de structuur te bekijken na het verwijderen van letterlijke waarden en constanten. Als twee query's identiek zijn, met uitzonde ring van letterlijke waarden, hebben ze dezelfde hash.

### <a name="query_storeqs_view"></a>query_store query_store.qs_view
In deze weer gave worden alle gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID en query-ID. 

|**Naam**   |**Type** | **Referenties**  | **Beschrijving**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID uit de runtime_stats_entries tabel|
|user_id    |nogmaals    |pg_authid. OID  |OID van de gebruiker die de instructie heeft uitgevoerd|
|db_id  |nogmaals    |pg_database. OID    |De OID van de Data Base waarin de instructie is uitgevoerd|
|query_id   |bigint  || Interne hash-code, berekend op basis van de ontledings structuur van de instructie|
|query_sql_text |Varchar (10000)  || De tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samen geclusterd. deze tekst is de tekst voor de eerste van de query's in het cluster.|
|plan_id    |bigint |   |De ID van het abonnement dat is gekoppeld aan deze query en die nog niet beschikbaar is|
|start_tijd |tijdstempel  ||  Query's worden geaggregeerd op tijd-buckets: de tijds panne van een Bucket is standaard 15 minuten. Dit is de begin tijd die overeenkomt met het tijds interval voor deze vermelding.|
|end_time   |tijdstempel  ||  De eind tijd die overeenkomt met het tijds interval voor deze vermelding.|
|rpc's  |bigint  || Aantal keren dat de query is uitgevoerd|
|total_time |dubbele precisie   ||  Totale uitvoerings tijd van de query, in milliseconden|
|min_time   |dubbele precisie   ||  Minimale uitvoerings tijd van de query, in milliseconden|
|max_time   |dubbele precisie   ||  Maximale uitvoerings tijd van de query, in milliseconden|
|mean_time  |dubbele precisie   ||  Gemiddelde uitvoerings tijd van query's, in milliseconden|
|stddev_time|   dubbele precisie    ||  Standaard afwijking van de uitvoerings tijd van de query, in milliseconden |
|rijen   |bigint ||  Totaal aantal rijen dat is opgehaald of beïnvloed door de instructie|
|shared_blks_hit|   bigint  ||  Totaal aantal gedeelde blok cache treffers per instructie|
|shared_blks_read|  bigint  ||  Totaal aantal gedeelde blokken dat is gelezen door de instructie|
|shared_blks_dirtied|   bigint   || Totaal aantal gedeelde blokken dirtied door de instructie |
|shared_blks_written|   bigint  ||  Totaal aantal gedeelde blokken dat is geschreven door de instructie|
|local_blks_hit|    bigint ||   Totaal aantal lokale blok cache treffers per instructie|
|local_blks_read|   bigint   || Totaal aantal lokale blokken dat door de instructie is gelezen|
|local_blks_dirtied|    bigint  ||  Totaal aantal lokale blokken dirtied door de instructie|
|local_blks_written|    bigint  ||  Totaal aantal lokale blokken dat is geschreven door de instructie|
|temp_blks_read |bigint  || Totaal aantal tijdelijke blokken dat is gelezen door de instructie|
|temp_blks_written| bigint   || Totaal aantal tijdelijke blokken dat is geschreven door de instructie|
|blk_read_time  |dubbele precisie    || De totale tijd die de instructie heeft besteed aan het lezen van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
|blk_write_time |dubbele precisie    || De totale tijd die de instructie heeft besteed aan het schrijven van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
    
### <a name="query_storequery_texts_view"></a>query_store query_store.query_texts_view
Deze weer gave retourneert query tekst gegevens in query Store. Er is één rij voor elke afzonderlijke query_text.

| **Naam** | **Type** | **Beschrijving** |
|--|--|--|
| query_text_id | bigint | ID voor de query_texts tabel |
| query_sql_text | Varchar (10000) | De tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samen geclusterd. deze tekst is de tekst voor de eerste van de query's in het cluster. |

### <a name="query_storepgms_wait_sampling_view"></a>query_store query_store.pgms_wait_sampling_view
Met deze weer gave worden wachtende gebeurtenis gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID, query-ID en gebeurtenis.

| **Naam** | **Type** | **Referenties** | **Beschrijving** |
|--|--|--|--|
| user_id | nogmaals | pg_authid. OID | OID van de gebruiker die de instructie heeft uitgevoerd |
| db_id | nogmaals | pg_database. OID | De OID van de Data Base waarin de instructie is uitgevoerd |
| query_id | bigint |  | Interne hash-code, berekend op basis van de ontledings structuur van de instructie |
| event_type | tekst |  | Het type gebeurtenis waarvoor de back-end wacht |
| gebeurtenislog | tekst |  | De wacht gebeurtenis naam als de back-end momenteel wacht |
| rpc's | Geheel getal |  | Nummer van dezelfde gebeurtenis vastgelegd |

### <a name="functions"></a>Functions

Query_store Query_store.qs_reset () retourneert void

`qs_reset` Hiermee worden alle statistische gegevens verwijderd die tot nu toe zijn verzameld door de query Store. Deze functie kan alleen worden uitgevoerd door de rol Server beheerder.

Query_store Query_store.staging_data_reset () retourneert void

`staging_data_reset` Hiermee worden alle statistieken die in het geheugen zijn verzameld, verwijderd door de query Store (dat wil zeggen, de gegevens in het geheugen die nog niet zijn leeg gemaakt voor de data base). Deze functie kan alleen worden uitgevoerd door de rol Server beheerder.


## <a name="azure-monitor"></a>Azure Monitor
Azure Database for PostgreSQL is geïntegreerd met [Azure monitor Diagnostische instellingen](../azure-monitor/essentials/diagnostic-settings.md). Met Diagnostische instellingen kunt u uw post gres-Logboeken in JSON-indeling verzenden naar [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor analyse-en waarschuwings doeleinden, Event hubs voor streaming en Azure Storage voor archivering.

>[!IMPORTANT]
> Deze diagnostische functie voor is alleen beschikbaar in de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen.

### <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren
U kunt Diagnostische instellingen inschakelen voor uw post gres-server met behulp van de Azure Portal, CLI, REST API en Power shell. De logboek categorieën die moeten worden geconfigureerd, zijn **QueryStoreRuntimeStatistics** en **QueryStoreWaitStatistics**. 

Resource logboeken inschakelen met behulp van de Azure Portal:

1. Ga in de portal naar Diagnostische instellingen in het navigatie menu van uw post gres-server.
2. Selecteer diagnostische instelling toevoegen.
3. Geef deze instelling een naam.
4. Selecteer uw gewenste eind punt (opslag account, Event Hub, log Analytics).
5. Selecteer de logboek typen **QueryStoreRuntimeStatistics** en **QueryStoreWaitStatistics**.
6. Sla de instelling op.

Ga naar het [artikel Diagnostische instellingen](../azure-monitor/essentials/diagnostic-settings.md)om deze instelling in te scha kelen met behulp van Power shell, CLI of rest API.

### <a name="json-log-format"></a>JSON-logboek bestands indeling
In de volgende tabellen worden de velden voor de twee logboek typen beschreven. Afhankelijk van het uitvoer eindpunt dat u kiest, worden de opgenomen velden en de volg orde waarin ze worden weer gegeven.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Veld** | **Beschrijving** |
|---|---|
| TimeGenerated [UTC] | Tijds tempel voor het vastleggen van het logboek in UTC |
| ResourceId | De Azure-resource-URI van de post gres-server |
| Categorie | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Post gres-server naam | 
| runtime_stats_entry_id_s | ID uit de runtime_stats_entries tabel |
| user_id_s | OID van de gebruiker die de instructie heeft uitgevoerd |
| db_id_s | De OID van de Data Base waarin de instructie is uitgevoerd |
| query_id_s | Interne hash-code, berekend op basis van de ontledings structuur van de instructie |
| end_time_s | Eind tijd die overeenkomt met het tijds interval voor dit item |
| calls_s | Aantal keren dat de query is uitgevoerd |
| total_time_s | Totale uitvoerings tijd van de query, in milliseconden |
| min_time_s | Minimale uitvoerings tijd van de query, in milliseconden |
| max_time_s | Maximale uitvoerings tijd van de query, in milliseconden |
| mean_time_s | Gemiddelde uitvoerings tijd van query's, in milliseconden |
| ResourceGroup | De resource groep | 
| SubscriptionId | Uw abonnements-id |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Resource | Post gres-server naam |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Veld** | **Beschrijving** |
|---|---|
| TimeGenerated [UTC] | Tijds tempel voor het vastleggen van het logboek in UTC |
| ResourceId | De Azure-resource-URI van de post gres-server |
| Categorie | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | OID van de gebruiker die de instructie heeft uitgevoerd |
| db_id_s | De OID van de Data Base waarin de instructie is uitgevoerd |
| query_id_s | Interne hash-code van de query |
| calls_s | Nummer van dezelfde gebeurtenis vastgelegd |
| event_type_s | Het type gebeurtenis waarvoor de back-end wacht |
| event_s | De wacht gebeurtenis naam als de back-end momenteel wacht |
| start_time_t | Begin tijd van gebeurtenis |
| end_time_s | Eind tijd van gebeurtenis | 
| LogicalServerName_s | Post gres-server naam | 
| ResourceGroup | De resource groep | 
| SubscriptionId | Uw abonnements-id |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Resource | Post gres-server naam |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
- Als een PostgreSQL-server de para meter default_transaction_read_only heeft, kan de query Store geen gegevens vastleggen.
- De functionaliteit van het query archief kan worden onderbroken als er lange Unicode-query's worden aangetroffen (>= 6000 bytes).
- [Replica's lezen](concepts-read-replicas.md) query's opslaan gegevens van de primaire server repliceren. Dit betekent dat het query archief van een lees replica geen statistieken biedt over query's die worden uitgevoerd op de Lees replica.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [scenario's waarin query Store bijzonder nuttig kan zijn](concepts-query-store-scenarios.md).
- Meer informatie over [Best practices voor het gebruik van query Store](concepts-query-store-best-practices.md).

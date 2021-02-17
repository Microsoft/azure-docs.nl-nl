---
title: Azure-cache bewaken voor redis
description: Meer informatie over het controleren van de status en prestaties van uw Azure-cache voor redis-instanties
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0ff11c9601fb55e27d8780185d77c177e9d9201b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584644"
---
# <a name="monitor-azure-cache-for-redis"></a>Azure-cache bewaken voor redis

Azure cache voor redis maakt gebruik van [Azure monitor](../azure-monitor/index.yml) om verschillende opties te bieden voor het bewaken van uw cache-exemplaren. U kunt metrische gegevens weer geven, metrische grafieken vastmaken aan het start Board, het datum-en tijds bereik van bewakings grafieken aanpassen, metrische gegevens aan de grafieken toevoegen en verwijderen, en waarschuwingen instellen wanneer aan bepaalde voor waarden wordt voldaan. Met deze hulpprogram ma's kunt u de status van uw Azure-cache bewaken voor redis-instanties en helpt u bij het beheren van uw cache toepassingen.

Metrische gegevens voor Azure cache voor redis-instanties worden verzameld met de opdracht redis [info](https://redis.io/commands/info) ongeveer twee keer per minuut en automatisch gedurende 30 dagen opgeslagen (Zie [metrische gegevens van de export cache](#export-cache-metrics) om een ander Bewaar beleid te configureren), zodat ze kunnen worden weer gegeven in de grafieken metrische gegevens en geëvalueerd door waarschuwings regels. Zie [beschik bare metrische gegevens en rapportage-intervallen](#available-metrics-and-reporting-intervals)voor meer informatie over de verschillende info waarden die worden gebruikt voor elke cache-metriek.

<a name="view-cache-metrics"></a>

Als u de metrische gegevens van de cache wilt weer geven, [bladert](cache-configure.md#configure-azure-cache-for-redis-settings) u naar uw cache-exemplaar in het [Azure Portal](https://portal.azure.com).  Azure cache voor redis biedt een aantal ingebouwde grafieken op de Blade **overzicht** en de Blade **metrische gegevens van redis** . Elke grafiek kan worden aangepast door metrische gegevens toe te voegen of te verwijderen en het rapportage-interval te wijzigen.

![Er worden zes grafieken weer gegeven. Een van de gebruikers is cache treffers en cache missers in het afgelopen uur.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Vooraf geconfigureerde metrische grafieken weer geven

De Blade **overzicht** bevat de volgende vooraf geconfigureerde bewakings grafieken.

* [Bewakings grafieken](#monitoring-charts)
* [Gebruiks grafieken](#usage-charts)

### <a name="monitoring-charts"></a>Bewakings grafieken

De sectie **bewaking** op de Blade **overzicht** bevat **treffers en missers**, **ophalen en instellen**, **verbindingen** en **Totaal aantal opdrachten** grafieken.

![Bewakings grafieken](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gebruiks grafieken

De sectie **gebruik** in de Blade **overzicht** bevat **redis server belasting**, **geheugen gebruik**, **netwerk bandbreedte** en **CPU-gebruiks** grafieken, en geeft ook de **prijs categorie** voor het cache-exemplaar weer.

![Gebruiks grafieken](./media/cache-how-to-monitor/redis-cache-usage-part.png)

In de **prijs** categorie wordt de prijs categorie voor de cache weer gegeven. deze kan worden gebruikt om de cache te [schalen](cache-how-to-scale.md) naar een andere prijs categorie.

## <a name="view-metrics-with-azure-monitor"></a>Metrische gegevens weer geven met Azure monitor

Als u metrische gegevens voor redis wilt weer geven en aangepaste grafieken wilt maken met behulp van Azure Monitor, klikt u op **metrische gegevens** in het **menu resource** en past u uw grafiek aan met de gewenste metrische gegevens, het rapportage-interval, het grafiek type en nog veel meer.

![In het navigatie deel venster aan de linkerkant van contoso55 is metrische gegevens een optie onder bewaking en is gemarkeerd. Er is een lijst met metrische gegevens over metrische gegevens. Cache treffers en cache missers zijn geselecteerd.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Zie [overzicht van metrische gegevens in Microsoft Azure](../azure-monitor/data-platform.md)voor meer informatie over het werken met metrische gegevens met behulp van Azure monitor.

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Metrische gegevens van de cache exporteren

Standaard worden de metrische gegevens in de cache van Azure Monitor [30 dagen opgeslagen](../azure-monitor/essentials/data-platform-metrics.md) en vervolgens verwijderd. Als u de cache-metrische gegevens langer dan 30 dagen wilt behouden, kunt u [een opslag account aanwijzen](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) en een **Bewaar beleid (dagen)** voor uw cache-metrische gegevens opgeven. 

Een opslag account configureren voor de metrische gegevens van de cache:

1. Selecteer op de pagina **Azure-cache voor redis** , onder de kop **bewaking** , de optie **Diagnostische gegevens**.
2. Selecteer **+ Diagnostische instelling toevoegen**.
3. Geef de instellingen een naam.
4. Schakel **Archiveren naar een opslagaccount** in. Wanneer u Diagnostische gegevens naar een opslag account verzendt, worden de normale tarieven voor opslag en trans acties in rekening gebracht.
4. Selecteer **configureren** om het opslag account te kiezen waarin de metrische gegevens van de cache moeten worden opgeslagen.
5. Schakel onder het veld tabelkop **metrische gegevens** het selectie vakje in naast de regel items die u wilt opslaan, zoals **AllMetrics**. Geef een **Bewaar beleid (dagen)** op. De maximale Bewaar periode die u kunt opgeven, is **365 dagen**. Als u de metrische gegevens echter altijd wilt behouden, stelt u **bewaren (dagen)** in op **0**.
6. Klik op **Opslaan**.


![Redis diagnostische gegevens](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Naast het archiveren van uw cache-metrische gegevens naar opslag, kunt u [ze ook streamen naar een event hub of naar Azure monitor-logboeken verzenden](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Als u toegang wilt krijgen tot uw metrische gegevens, kunt u ze weer geven in de Azure Portal zoals eerder in dit artikel is beschreven. u kunt ze ook openen met behulp van de [Azure monitor metrische rest API](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Als u opslag accounts wijzigt, blijven de gegevens in het eerder geconfigureerde opslag account beschikbaar voor downloaden, maar wordt het niet weer gegeven in de Azure Portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Beschik bare metrische gegevens en rapportage-intervallen

Metrische cache gegevens worden gerapporteerd met behulp van verschillende rapportage intervallen, waaronder **afgelopen uur**, **vandaag**, in de **afgelopen week** en **aangepast**. De Blade **metrische gegevens** voor elk metrieke diagram geeft de gemiddelde, minimale en maximale waarde voor elke metriek in het diagram en sommige metrische gegevens geven een totaal weer voor het rapportage-interval. 

Elke metriek bevat twee versies. Eén meet waarde meet de prestaties voor de volledige cache en voor caches die gebruikmaken van [clusters](cache-how-to-premium-clustering.md), een tweede versie van de metrische waarde die in de naam is opgenomen, neemt `(Shard 0-9)` de prestaties van één Shard in een cache. Als een cache bijvoorbeeld vier Shards heeft, `Cache Hits` is het totale aantal treffers voor de hele cache en `Cache Hits (Shard 3)` zijn alleen de treffers voor die Shard van de cache.

> [!NOTE]
> Zelfs als de cache niet actief is zonder verbonden actieve client toepassingen, ziet u mogelijk bepaalde cache-activiteiten, zoals verbonden clients, geheugen gebruik en bewerkingen die worden uitgevoerd. Deze activiteit is normaal tijdens de bewerking van een Azure-cache voor redis-instantie.
> 
> 

| Metrisch | Beschrijving |
| --- | --- |
| Cachetreffers |Het aantal opzoek bewerkingen met geslaagde sleutels tijdens het opgegeven rapportage-interval. Dit nummer wordt toegewezen aan `keyspace_hits` via de opdracht redis [info](https://redis.io/commands/info) . |
| Cache latentie (preview-versie) | De latentie van de cache wordt berekend op basis van de latentie van het interknooppunt van de cache. Deze metriek wordt gemeten in micro seconden en heeft drie dimensies: `Avg` , `Min` en `Max` , die respectievelijk de gemiddelde, minimale en maximale latentie van de cache vertegenwoordigen tijdens het opgegeven rapportage-interval. |
| Cachemissers |Het aantal mislukte sleutel lookups tijdens het opgegeven rapportage-interval. Dit nummer wordt toegewezen aan `keyspace_misses` via de opdracht REDIS info. Missers in de cache hoeven niet te betekenen dat er een probleem is met de cache. Bij gebruik van het cache-leggings programmerings patroon wordt een toepassing bijvoorbeeld eerst in de cache voor een item weer gegeven. Als het item niet aanwezig is (niet in cache), wordt het item opgehaald uit de data base en de volgende keer toegevoegd aan de cache. Cache missers zijn normaal gedrag voor het cache-leggings programmerings patroon. Als het aantal missers in de cache hoger is dan verwacht, controleert u de toepassings logica waarmee de cache wordt gevuld en gelezen. Als items worden verwijderd uit de cache vanwege geheugen belasting, is er mogelijk een aantal cache missers, maar een betere metrische waarde voor het bewaken van de geheugen belasting is `Used Memory` of `Evicted Keys` . |
| Gelezen uit cache |De hoeveelheid gegevens die uit de cache is gelezen in mega bytes per seconde (MB/s) tijdens het opgegeven rapportage-interval. Deze waarde is afgeleid van de netwerk interface kaarten die ondersteuning bieden voor de virtuele machine die als host fungeert voor de cache en die niet redis specifiek is. **Deze waarde komt overeen met de netwerk bandbreedte die door deze cache wordt gebruikt. Als u waarschuwingen wilt instellen voor netwerk bandbreedte limieten aan de server zijde, maakt u deze met behulp van deze `Cache Read` teller. Zie [deze tabel](cache-planning-faq.md#azure-cache-for-redis-performance) voor de waargenomen bandbreedte limieten voor verschillende prijs categorieën en grootten voor de cache.** |
| Geschreven naar cache |De hoeveelheid gegevens die naar de cache wordt geschreven in mega bytes per seconde (MB/s) tijdens het opgegeven rapportage-interval. Deze waarde is afgeleid van de netwerk interface kaarten die ondersteuning bieden voor de virtuele machine die als host fungeert voor de cache en die niet redis specifiek is. Deze waarde komt overeen met de netwerk bandbreedte van gegevens die vanuit de client naar de cache worden verzonden. |
| Verbonden clients |Het aantal client verbindingen met de cache tijdens het opgegeven rapportage-interval. Dit nummer wordt toegewezen aan `connected_clients` via de opdracht REDIS info. Zodra de [verbindings limiet](cache-configure.md#default-redis-server-configuration) is bereikt, mislukken volgende Verbindings pogingen met de cache. Zelfs als er geen actieve client toepassingen zijn, kunnen er nog steeds enkele exemplaren van verbonden clients zijn vanwege interne processen en verbindingen. |
| CPU |Het CPU-gebruik van de Azure-cache voor de redis-server als een percentage tijdens het opgegeven rapportage-interval. Deze waarde wordt toegewezen aan het prestatie meter item van het besturings systeem `\Processor(_Total)\% Processor Time` . |
| Fouten | Specifieke fouten en prestatie problemen die de cache tijdens een opgegeven rapportage-interval kan ondervinden. Deze metriek heeft acht dimensies die verschillende fout typen vertegenwoordigen, maar kan in de toekomst meer worden toegevoegd. De volgende fout typen worden nu weer gegeven: <br/><ul><li>**Failover** : wanneer een storing optreedt in een cache (ondergeschikte onderliggende niveau)</li><li>**Dataloss** : wanneer er gegevens verloren gaan in de cache</li><li>**UnresponsiveClients** : wanneer de clients geen gegevens van de server snel genoeg lezen</li><li>**AOF** : wanneer er een probleem is met de AOF persistentie</li><li>**RDB** : wanneer er een probleem is met de RDB-persistentie</li><li>**Importeren** : wanneer er een probleem is met het importeren van RDB</li><li>**Exporteren** : wanneer er een probleem is met het exporteren van RDB</li></ul> |
| Verwijderde sleutels |Het aantal items dat uit de cache wordt verwijderd tijdens het opgegeven rapportage-interval vanwege de `maxmemory` limiet. Dit nummer wordt toegewezen aan `evicted_keys` via de opdracht REDIS info. |
| Verlopen sleutels |Het aantal items dat tijdens het opgegeven rapportage-interval is verlopen vanuit de cache. Deze waarde wordt toegewezen aan `expired_keys` via de opdracht REDIS info.|
| Ophalingen |Het aantal Get-bewerkingen uit de cache tijdens het opgegeven rapportage-interval. Deze waarde is de som van de volgende waarden uit de redis-info:,,, `cmdstat_get` `cmdstat_hget` ,, `cmdstat_hgetall` `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit` , en `cmdstat_getrange` , en is gelijk aan de som van cache treffers en missers tijdens het rapportage-interval. |
| Bewerkingen per seconde | Het totale aantal opdrachten dat per seconde door de cache server is verwerkt tijdens het opgegeven rapportage-interval.  Deze waarde wordt toegewezen aan instantaneous_ops_per_sec van de opdracht redis INFO. |
| Redis-server belasting |Het percentage cycli waarin de redis-server bezig is met verwerken en niet in afwachting van berichten die niet actief zijn. Als dit item 100 is, betekent dit dat de redis-server een prestatie plafond heeft bereikt en de CPU niet sneller kan werken. Als u een hoge redis-server belasting ziet, ziet u time-outuitzonderingen in de client. In dit geval moet u overwegen om uw gegevens naar meerdere caches te schalen of te partitioneren. |
| Sets |Het aantal ingestelde bewerkingen voor de cache tijdens het opgegeven rapportage-interval. Deze waarde is de som van de volgende waarden uit de redis info:,, `cmdstat_set` `cmdstat_hset` `cmdstat_hmset` , `cmdstat_hsetnx` , `cmdstat_lset` , `cmdstat_mset` , `cmdstat_msetnx` , `cmdstat_setbit` ,, en `cmdstat_setex` `cmdstat_setrange` `cmdstat_setnx` . |
| Totaal aantal sleutels  | Het maximum aantal sleutels in de cache gedurende de laatste rapportage periode. Dit nummer wordt toegewezen aan `keyspace` via de opdracht REDIS info. Als gevolg van een beperking van het onderliggende systeem voor metrische gegevens, voor caches waarvoor Clustering is ingeschakeld, retourneert Total Keys het maximum aantal sleutels van de Shard dat het maximum aantal sleutels tijdens het rapportage-interval heeft.  |
| Totaalaantal bewerkingen |Het totale aantal opdrachten dat door de cache server is verwerkt tijdens het opgegeven rapportage-interval. Deze waarde wordt toegewezen aan `total_commands_processed` via de opdracht REDIS info. Wanneer Azure cache voor redis alleen voor pub/sub wordt gebruikt, zijn er geen metrische gegevens voor `Cache Hits` , `Cache Misses` , `Gets` , of `Sets` , maar zijn er `Total Operations` metrische gegevens die het cache gebruik voor pub/sub-bewerkingen weer spie gelen. |
| Gebruikt geheugen |De hoeveelheid cache geheugen die wordt gebruikt voor sleutel/waarde-paren in de cache in MB tijdens het opgegeven rapportage-interval. Deze waarde wordt toegewezen aan `used_memory` via de opdracht REDIS info. Deze waarde bevat geen meta gegevens of fragmentatie. |
| Percentage gebruikt geheugen | Het percentage van het totale geheugen dat wordt gebruikt tijdens het opgegeven rapportage-interval.  Deze waarde verwijst naar de `used_memory` waarde van de opdracht REDIS info om het percentage te berekenen. |
| Gebruikte geheugen-RSS |De hoeveelheid cache geheugen die in MB wordt gebruikt tijdens het opgegeven rapportage-interval, inclusief fragmentatie en meta gegevens. Deze waarde wordt toegewezen aan `used_memory_rss` via de opdracht REDIS info. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Waarschuwingen

U kunt instellen dat u waarschuwingen ontvangt op basis van metrische gegevens en activiteitenlogboeken. Met Azure Monitor kunt u een waarschuwing zodanig configureren dat deze bij activering het volgende doet:

* Een e-mailmelding verzenden
* Een webhook aanroepen
* Een logische Azure-app aanroepen

Als u waarschuwings regels wilt configureren voor uw cache, klikt u op **waarschuwings regels** in het **menu resource**.

![Bewaking](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Zie [overzicht van waarschuwingen](../azure-monitor/alerts/alerts-classic-portal.md)voor meer informatie over het configureren en gebruiken van waarschuwingen.

## <a name="activity-logs"></a>Activiteitenlogboeken
Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-cache voor redis-exemplaren. Voorheen bekend als ' controle logboeken ' of ' operationele logboeken '. Met activiteiten Logboeken kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn gemaakt in uw Azure-cache voor redis-exemplaren. 

> [!NOTE]
> Activiteiten logboeken bevatten geen lees bewerkingen (GET).
>

Als u activiteiten logboeken voor uw cache wilt weer geven, klikt u op **activiteiten logboeken** in het **menu resource**.

Zie [overzicht van het Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over activiteiten Logboeken.
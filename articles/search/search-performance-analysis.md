---
title: Prestaties analyseren
titleSuffix: Azure Cognitive Search
description: NOG TE BEPALEN
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582286"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Prestaties analyseren in azure Cognitive Search

In dit artikel worden de hulpprogram ma's, gedrag en benaderingen beschreven voor het analyseren van de prestaties van query's en indexeren in Cognitive Search.

## <a name="develop-baseline-numbers"></a>Basislijn nummers ontwikkelen

Bij een grote implementatie is het essentieel om een prestatie benchmarking test van uw Cognitive Search-service uit te voeren voordat u deze implementeert in productie. U moet de belasting van de zoek query testen die u verwacht, maar ook de verwachte workloads voor gegevens opname (indien mogelijk) tegelijk uitvoeren. Met Bench Mark-nummers kunt u de juiste [Zoek categorie](search-sku-tier.md), [Service configuratie](search-capacity-planning.md)en verwachte [latentie van query's](search-performance-analysis.md#average-query-latency)valideren.

Voor het ontwikkelen van benchmarks wordt u aangeraden het [Azure-Search-performance test (github)](https://github.com/Azure-Samples/azure-search-performance-testing) tool te gebruiken.

U kunt de effecten van een gedistribueerde service architectuur isoleren door te testen op service configuraties van één replica en één partitie.

> [!NOTE]
> Voor de lagen geoptimaliseerd voor opslag (L1 en L2) moet u een lagere query door Voer en een hogere latentie dan de standaard lagen.
>

## <a name="use-diagnostic-logging"></a>Diagnostische logboek registratie gebruiken

Het belangrijkste hulp programma dat een beheerder in staat stelt om mogelijke prestatie problemen op te lossen, is [Diagnostische logboek registratie](search-monitor-logs.md) waarmee operationele gegevens en metrieken over uw zoek service worden verzameld. Diagnostische logboek registratie is ingeschakeld via [Azure monitor](../azure-monitor/overview.md). Er zijn kosten verbonden aan het gebruik van Azure Monitor en het opslaan van gegevens, maar als u deze voor uw service inschakelt, kan het een instrument zijn om prestatie problemen te onderzoeken.

Er zijn meerdere mogelijkheden voor latentie, hetzij tijdens een netwerk overdracht, hetzij tijdens het verwerken van inhoud in de app service-laag of op een zoek service. Een belang rijk voor deel van diagnostische logboek registratie is dat activiteiten worden vastgelegd in het perspectief van de zoek service. Dit betekent dat u met het logboek kunt bepalen of prestatie problemen worden veroorzaakt door problemen met de query of het indexeren of een ander fout punt.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Keten van geregistreerde gebeurtenissen" border="true":::

Diagnostische gegevens registratie biedt u opties voor het opslaan van vastgelegde informatie. We raden u aan [log Analytics](../azure-monitor/logs/log-analytics-overview.md) te gebruiken zodat u geavanceerde Kusto-query's kunt uitvoeren op de gegevens om veel vragen over het gebruik en de prestaties te beantwoorden. 

Op de pagina's van de zoek service Portal kunt u logboek registratie via **Diagnostische instellingen** inschakelen en vervolgens Kusto-query's voor log Analytics geven door **Logboeken** te kiezen. Zie [logboek gegevens verzamelen en analyseren](search-monitor-logs.md)voor meer informatie over het instellen van.

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Opties voor het menu logboek registratie" border="true":::

## <a name="throttling-behaviors"></a>Beperkings gedrag

Beperking treedt op wanneer de zoek service de limiet heeft bereikt van wat er kan worden verwerkt vanuit een resource perspectief. Beperking kan optreden tijdens query's of het indexeren. Vanaf de client zijde resulteert een API-aanroep in een 503 HTTP-antwoord wanneer dit is beperkt. Tijdens het indexeren is het ook mogelijk om een 207 HTTP-antwoord te ontvangen. Dit geeft aan dat een of meer items niet zijn geïndexeerd. Deze fout geeft aan dat de zoek service bijna de capaciteit krijgt. 

Als vuist regel kunt u het beste de hoeveelheid beperking bepalen die u ziet. Als bijvoorbeeld één zoek opdracht uit 500.000 wordt beperkt, is het mogelijk dat er geen grote hoeveelheid een probleem is. Als een groot percentage van query's echter wordt beperkt over een bepaalde periode, zou dit een grotere bezorgdheid zijn. Door te kijken naar een beperking van een bepaalde periode, is het ook handig om tijd-frames te identificeren waarbij beperking waarschijnlijker is en u te helpen bepalen hoe u het beste kunt doen.

Een eenvoudige oplossing voor de meeste beperkings problemen is het genereren van meer resources bij de zoek service (meestal replica's voor op query's gebaseerde beperking of partities voor op indexering gebaseerde beperking). Het verg Roten van replica's of partities voegt echter wel kosten toe. Daarom is het belang rijk om te weten waarom het beperken van de beperking plaatsvindt. Het onderzoeken van de voor waarden die ervoor zorgen dat het beperken van beperkingen wordt uitgelegd in de volgende secties. 

Hieronder ziet u een voor beeld van een Kusto-query die de uitsplitsing van HTTP-antwoorden van de zoek service die wordt geladen kan identificeren. Als er een query wordt uitgevoerd gedurende een periode van zeven dagen, ziet u in het gerenderde staaf diagram dat een relatief groot percentage van de zoek query's is beperkt, vergeleken met het aantal geslaagde (200) reacties.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Staaf diagram van HTTP-fout aantallen" border="true":::

Het controleren van de beperking over een bepaalde periode kan helpen om te bepalen hoe vaak de beperking kan optreden. In het onderstaande voor beeld wordt een tijdreeks diagram gebruikt om het aantal vertraagde query's weer te geven dat is opgetreden gedurende een opgegeven periode. In dit geval zijn de vertraagde query's gecorreleerd met de tijden in met de prestatie benchmarking.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Lijn diagram van vertraagde query's" border="true":::

## <a name="measure-individual-queries"></a>Afzonderlijke query's meten

In sommige gevallen kan het handig zijn om afzonderlijke query's te testen om te zien hoe ze worden uitgevoerd. Om dit te doen, is het belang rijk om te zien hoe lang het duurt voordat de zoek service het werk voltooit, en hoe lang het duurt om de aanvraag voor de retour actie van de client te maken en terug te gaan naar de client. De diagnostische logboeken kunnen worden gebruikt voor het opzoeken van afzonderlijke bewerkingen, maar het kan gemakkelijker zijn om dit allemaal te doen vanuit een client hulpprogramma, zoals een postman.

In het onderstaande voor beeld is een op REST gebaseerde zoek query uitgevoerd. Cognitive Search bevat in elke reactie het aantal milliseconden dat het heeft geduurd om de query te volt ooien, weer gegeven op het tabblad headers in ' verstreken tijd '. Naast de status boven aan het antwoord vindt u de retour duur. in dit geval 418 milliseconden. In de sectie resultaten is het tabblad headers gekozen. Als u deze twee waarden in de onderstaande afbeelding hebt gemarkeerd met een rood vak, zien we dat de zoek service 21 MS duurde om de zoek query te volt ooien en de volledige aanvraag voor de round-trip van de client 125 MS heeft geduurd. Door deze twee getallen af te trekken, kunnen we bepalen dat deze 104 MS meer tijd heeft geduurd om de zoek opdracht naar de zoek service te sturen en de zoek resultaten terug naar de client over te dragen.

Dit kan bijzonder nuttig zijn om te bepalen of er netwerk latenties of andere factoren van invloed zijn op de prestaties van query's.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Metrische gegevens van de query duur" border="true":::

## <a name="query-rates"></a>Query tarieven

Een mogelijke reden voor uw zoek service voor het beperken van aanvragen wordt veroorzaakt door het Sheer aantal query's dat wordt uitgevoerd, waarbij het volume wordt vastgelegd als query's per seconde (QPS) of query's per minuut (QPM). Als uw zoek service meer QPS ontvangt, duurt het doorgaans langer en langer om op deze query's te reageren totdat de query niet meer kan worden bewaard, zodat deze een beperking van 503 HTTP-antwoorden terugstuurt. 

De volgende Kusto-query toont een query volume zoals gemeten in QPM, samen met de gemiddelde duur van een query in milliseconden (AvgDurationMS) en het gemiddelde aantal documenten (AvgDocCountReturned) dat in elk van beide is geretourneerd.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Grafiek waarin query's per minuut worden weer gegeven" border="true":::

> [!TIP]
> Als u de gegevens achter deze grafiek wilt onthullen, verwijdert u de regel `| render timechart` en voert u de query opnieuw uit.

## <a name="impact-of-indexing-on-queries"></a>Gevolgen van het indexeren van query's

Een belang rijke factor waarmee u rekening moet houden bij prestaties, is dat het indexeren dezelfde resources als Zoek query's gebruikt. Als u een grote hoeveelheid inhoud wilt indexeren, kunt u verwachten dat de latentie groeit naarmate de service beide workloads probeert toe te passen.

Als query's worden vertraagd, bekijkt u de timing van de indexerings activiteit om te zien of deze samen vallen met een query degradatie. Bijvoorbeeld: een Indexeer functie voert een werkend werk per dag of per uur uit die overeenkomt met de verlaagde prestaties van de zoek query's. 

Deze sectie bevat een reeks query's waarmee u de zoek-en indexerings snelheden kunt visualiseren. Voor deze voor beelden wordt het tijds bereik ingesteld in de query. Zorg ervoor dat **in de query set** wordt aangegeven wanneer de query's worden uitgevoerd in azure Portal.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Tijds bereiken instellen in het query-hulp programma" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Gemiddelde latentie van Query's

In de onderstaande query wordt een interval grootte van 1 minuut gebruikt om de gemiddelde latentie van de zoek query's weer te geven. In de grafiek kunnen we zien dat de gemiddelde latentie laag is tot 5:17:45 en laatst tot 5:53pm.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Grafiek waarin de gemiddelde latentie van query's wordt weer gegeven" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Gemiddeld aantal Query's per minuut (QPM)

Met de volgende query kunt u het gemiddelde aantal query's per minuut bekijken om ervoor te zorgen dat er geen sprake is van een bepaalde Prikker in Search-aanvragen die de latentie mogelijk beïnvloeden. In de grafiek ziet u dat er enige afwijking is, maar niets om een piek op te geven in het aantal aanvragen.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Grafiek waarin gemiddeld aantal query's per minuut wordt weer gegeven" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Indexerings bewerkingen per minuut (OPM)

Hier gaan we kijken naar het aantal indexerings bewerkingen per minuut. In de grafiek kunnen we zien dat een grote hoeveelheid gegevens is geïndexeerd om 5:42 uur en beëindigd om 5:50pm. Deze indexering begon drie minuten voordat de zoek query's worden gestart en 3 minuten zijn geëindigd voordat de Zoek opdrachten niet meer zijn doorgevoerd.

Hier kunnen we zien dat het ongeveer drie minuten duurt voordat de zoek service genoeg bezig is met de indexering om te beginnen met de latentie van de zoek query. U kunt ook zien dat nadat het indexeren is voltooid, de zoek service binnen een paar drie minuten de volledige hoeveelheid werk van de zojuist geïndexeerde inhoud heeft voltooid, totdat de Zoek opdrachten niet meer kunnen worden doorgevoerd.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Grafiek met de bewerkingen van indexeren per minuut" border="true":::

## <a name="background-service-processing"></a>Achtergrond service verwerken

Het is niet ongebruikelijk om periodieke pieken in de query-of indexerings latentie weer te geven. Pieken kunnen optreden in reactie op het indexeren of hoge query tarieven, maar kunnen ook optreden tijdens samenvoeg bewerkingen. Zoek indexen worden opgeslagen in segmenten-of Shards. Het systeem voegt periodiek kleinere Shards samen tot grote Shards, waardoor de service prestaties kunnen worden geoptimaliseerd. Met deze samenvoeg bewerking worden ook documenten opgeschoond die eerder zijn gemarkeerd voor verwijdering uit de index, wat resulteert in het herstel van opslag ruimte. 

Het samen voegen van Shards is snel, maar ook veel resources, waardoor de prestaties van de service kunnen worden verminderd. Als er korte bursts van de latentie van query's worden weer geven en deze bursts samen vallen met recente wijzigingen in geïndexeerde inhoud, is het waarschijnlijk dat u het kenmerk van de latentie voor het samen voegen van Shard-bewerkingen. 

## <a name="next-steps"></a>Volgende stappen

Bekijk deze aanvullende artikelen met betrekking tot het analyseren van de prestaties van services.

+ [Tips voor prestaties](search-performance-tips.md)
+ [Een servicelaag kiezen](search-sku-tier.md)
+ [Capaciteit beheren](search-capacity-planning.md)

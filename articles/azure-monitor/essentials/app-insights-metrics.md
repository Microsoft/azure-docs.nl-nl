---
title: Metrische op logboek gebaseerd op basis van Azure-toepassing Insights | Microsoft Docs
description: Dit artikel bevat een overzicht van Azure-toepassing Insights-metrische gegevens met ondersteunde aggregaties en dimensies. De details over metrische gegevens op basis van een logboek bevatten de onderliggende Kusto-query-instructies.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.openlocfilehash: bae7980137c1da5e1755450863af4f9029d6ed62
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583459"
---
# <a name="application-insights-log-based-metrics"></a>Metrische gegevens op basis van het logboek Application Insights

Met Application Insights metrische gegevens op basis van een logboek kunt u de status van uw bewaakte apps analyseren, krachtige Dash boards maken en waarschuwingen configureren. Er zijn twee soorten metrische gegevens:

* [Metrische gegevens op basis](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) van een logboek achter de scène worden omgezet in [Kusto-query's](/azure/kusto/query/) van opgeslagen gebeurtenissen.
* [Standaard metrische gegevens](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) worden opgeslagen als vooraf samengestelde tijd reeksen.

Aangezien *standaard metrische gegevens* vooraf worden geaggregeerd tijdens het verzamelen, hebben ze betere prestaties op het moment van de query. Dit maakt ze een betere keuze voor het maken van Dash boards en in realtime waarschuwingen. De *metrische gegevens op basis van een logboeken* hebben meer dimensies, waardoor ze de superieure optie zijn voor het analyseren van analyses en ad-hoc diagnostiek. Gebruik de [naam ruimte kiezer](./metrics-getting-started.md#create-your-first-metric-chart) om te scha kelen tussen op logboek gebaseerde en standaard metrische gegevens in [Metrics Explorer](./metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Query's in dit artikel interpreteren en gebruiken

In dit artikel worden metrische gegevens weer gegeven met ondersteunde aggregaties en dimensies. De details over metrische gegevens op basis van een logboek bevatten de onderliggende Kusto-query-instructies. Voor het gemak gebruikt elke query standaard waarden voor de tijd granulatie, het grafiek type en soms het splitsen van de query in Log Analytics zonder dat u wijzigingen hoeft aan te brengen.

Wanneer u dezelfde metriek in [Metrics Explorer](./metrics-getting-started.md)uitzet, zijn er geen standaard waarden. de query wordt dynamisch aangepast op basis van de instellingen van de grafiek:

- Het geselecteerde **tijds bereik** wordt omgezet in een extra *Where Time Stamp...* -component, zodat alleen de gebeurtenissen uit het geselecteerde tijds bereik worden gekozen. Bijvoorbeeld een grafiek met gegevens gedurende de meest recente 24 uur, bevat de query *| waar tijds tempel > geleden (24 uur)*.

- De geselecteerde **tijd granulatie** wordt in het definitieve overzicht geplaatst *... per bak (Time Stamp, [time korrel])-* component.

- Alle geselecteerde **filter** dimensies worden vertaald in aanvullende *where* -componenten.

- De geselecteerde dimensie voor **gesplitste grafieken** wordt vertaald naar een extra samenvattings eigenschap. Als u bijvoorbeeld uw grafiek op *locatie* splitst en uitgeeft met een tijd granulatie van 5 minuten, wordt *de component samenvatten* samenvatten *... per bak (tijds tempel, 5 m), locatie*.

> [!NOTE]
> Als u geen ervaring hebt met de Kusto-query taal, kunt u beginnen met het kopiëren en plakken van Kusto-instructies in het query deel venster Log Analytics zonder dat u wijzigingen hoeft aan te brengen. Klik op **uitvoeren** om de basis grafiek weer te geven. U begint met het maken van de syntaxis van de query taal, maar u kunt nu kleine wijzigingen aanbrengen en de gevolgen van de wijziging bekijken. Het verkennen van uw eigen gegevens is een uitstekende manier om te beginnen met het realiseren van de volledige kracht van [log Analytics](../logs/log-analytics-tutorial.md) en [Azure monitor](../overview.md).

## <a name="availability-metrics"></a>Metrische beschikbaarheids gegevens

Met metrische gegevens in de beschikbaarheids categorie kunt u de status van uw webtoepassing zien, zoals wordt waargenomen door punten over de hele wereld. [Configureer de beschikbaarheids tests](../app/monitor-web-app-availability.md) om alle metrische gegevens uit deze categorie te gebruiken.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Beschik baarheid (availabilityResults/availabilityPercentage)
De metrische *beschikbaarheids* gegevens tonen het percentage van de webtest-runs waarvoor geen problemen zijn gedetecteerd. De laagst mogelijke waarde is 0, wat aangeeft dat alle uitvoeringen van de webtest zijn mislukt. De waarde van 100 betekent dat alle webtest uitvoeringen de validatie criteria heeft door gegeven.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Percentage|Gemiddeld|Uitvoerings locatie, naam van test|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duur van beschikbaarheids test (availabilityResults/duur)

De metrische gegevens voor de *beschikbaarheids test* geven aan hoe lang het duurt voordat de webtest wordt uitgevoerd. Voor de [webtests met meerdere stappen](../app/availability-multistep.md)weerspiegelt de metriek de totale uitvoerings tijd van alle stappen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Uitvoerings locatie, naam van test, resultaat van test

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Beschikbaarheids testen (availabilityResults/Count)

De metrische *beschikbaarheids tests* weerspiegelt het aantal van de webtests dat wordt uitgevoerd door Azure monitor.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Count|Count|Uitvoerings locatie, naam van test, resultaat van test|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metrische gegevens van de browser

De metrische gegevens van de browser worden verzameld door de Application Insights java script SDK van echte gebruikers browsers. Ze bieden fantastische inzichten in de ervaring van uw gebruikers met uw web-app. De metrische gegevens van de browser worden doorgaans niet bemonsterd. Dit betekent dat ze een grotere nauw keurigheid van de gebruiks cijfers bieden ten opzichte van metrische gegevens aan de server zijde die kunnen worden schuingetrokken door steek proeven.

> [!NOTE]
> Als u metrische gegevens voor de browser wilt verzamelen, moet uw toepassing worden geinstrumenteerd met de [Application Insights java script SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Laad tijd van browser pagina (browserTimings/totalDuration)

Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Verwerkings tijd van client (browserTiming/processingDuration)

Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Netwerk verbindings tijd voor laden van pagina (browserTimings/networkDuration)

Tijd tussen de gebruikers aanvraag en de netwerk verbinding. Inclusief DNS-Zoek-en transport verbinding.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Reactie tijd van ontvangen (browserTimings/receiveDuration)

Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Verzend aanvraag tijd (browserTimings/sendDuration)

Tijd tussen netwerk verbinding en ontvangst van de eerste byte.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metrische fout gegevens

De metrische gegevens in **fouten** tonen problemen met verwerkings aanvragen, afhankelijkheids aanroepen en uitzonde ringen.

### <a name="browser-exceptions-exceptionsbrowser"></a>Browser uitzonderingen (uitzonde ringen/browser)

Deze metriek weerspiegelt het aantal uitzonde ringen dat is veroorzaakt door de toepassings code die in de browser wordt uitgevoerd. Alleen uitzonde ringen die worden bijgehouden met een ```trackException()``` Application Insights-API-aanroep, zijn opgenomen in de metrische gegevens.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|Notities|
|---|---|---|---|
|Count|Count|Geen|Bij versie op basis van een logboek functie worden **Sum** -aggregatie gebruikt|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Mislukte afhankelijkheids aanroepen (afhankelijkheden/mislukt)

Het aantal mislukte afhankelijkheids aanroepen.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|Notities|
|---|---|---|---|
|Count|Count|Geen|Bij versie op basis van een logboek functie worden **Sum** -aggregatie gebruikt|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Uitzonde ringen (uitzonde ringen/aantal)

Telkens wanneer u een uitzonde ring registreert op Application Insights, wordt er een aanroep naar de [methode trackException ()](../app/api-custom-events-metrics.md#trackexception) van de SDK. De metriek uitzonde ringen toont het aantal geregistreerde uitzonde ringen.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|Notities|
|---|---|---|---|
|Count|Count|Rolnaam van Cloud, instantie van Cloud-rol, apparaattype|Bij versie op basis van een logboek functie worden **Sum** -aggregatie gebruikt|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Mislukte aanvragen (aanvragen/mislukt)

Het aantal bijgehouden server aanvragen dat is gemarkeerd als *mislukt*. Standaard markeert de Application Insights SDK automatisch elke server aanvraag die de 5xx of 4xx van HTTP-antwoorden als een mislukte aanvraag heeft geretourneerd. U kunt deze logica aanpassen door de eigenschap  *success* van het aanvraag-telemetrie in een [aangepaste telemetrie-initialisatie functie](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)te wijzigen.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|Notities|
|---|---|---|---|
|Count|Count|Cloud rolinstantie, naam van Cloud functie, reëel of synthetisch verkeer, prestaties aanvragen, respons code|Bij versie op basis van een logboek functie worden **Sum** -aggregatie gebruikt|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Server uitzonderingen (uitzonde ringen/server)

Met deze metriek wordt het aantal server uitzonderingen weer gegeven.

|Meeteenheid|Ondersteunde aggregaties|Vooraf samengestelde dimensies|Notities|
|---|---|---|---|
|Count|Count|Rolnaam van Cloud, instantie van Cloud-rol|Bij versie op basis van een logboek functie worden **Sum** -aggregatie gebruikt|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Prestatiemeteritems

Gebruik metrische gegevens in de categorie **prestatie meter items** om toegang te krijgen tot [systeem prestatie meter items die door Application Insights zijn verzameld](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Beschikbaar geheugen (Performance Counters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Uitzonderings frequentie (Performance Counters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Uitvoerings tijd van de HTTP-aanvraag (Performance Counters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Frequentie van HTTP-aanvragen (Performance Counters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-aanvragen in de toepassings wachtrij (Performance Counters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU van proces (Performance Counters/processCpuPercentage)

De metriek laat zien hoeveel van de totale processor capaciteit wordt verbruikt door het proces dat als host fungeert voor uw bewaakte app.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, min, Max|Cloud rolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>I/o-frequentie van processen (Performance Counters/processIOBytesPerSecond)

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes per seconde|Gemiddeld, min, Max|Cloud rolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Privé-bytes verwerken (Performance Counters/processPrivateBytes)

De hoeveelheid niet-gedeeld geheugen die het bewaakte proces voor de gegevens heeft toegewezen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes|Gemiddeld, min, Max|Cloud rolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processor tijd (Performance Counters/processorCpuPercentage)

CPU-gebruik door *alle* processen die worden uitgevoerd op het bewaakte Server exemplaar.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, min, Max|Cloud rolinstantie

>[!NOTE]
> De metrische processor tijd is niet beschikbaar voor de toepassingen die worden gehost in Azure-app Services. Gebruik de  [CPU](#process-cpu-performancecountersprocesscpupercentage) -metriek van het proces om het CPU-gebruik bij te houden van de webtoepassingen die worden gehost in app Services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metrische Server gegevens

### <a name="dependency-calls-dependenciescount"></a>Afhankelijkheids aanroepen (afhankelijkheden/aantal)

Deze metriek is gerelateerd aan het aantal afhankelijkheids aanroepen.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Duur van afhankelijkheid (afhankelijkheden/duur)

Deze metrische waarde verwijst naar de duur van afhankelijkheids aanroepen.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Server aanvragen (aanvragen/aantal)

Deze metriek weerspiegelt het aantal inkomende server aanvragen dat door uw webtoepassing is ontvangen.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Server reactietijd (aanvragen/duur)

Deze metriek weerspiegelt de tijd die nodig is voor het verwerken van binnenkomende aanvragen door de servers.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metrische gegevens over gebruik

### <a name="page-view-load-time-pageviewsduration"></a>Laad tijd pagina weergave (page views/duur)

Deze metrische waarde verwijst naar de hoeveelheid tijd die nodig was voor het laden van pagina weergave-gebeurtenissen.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Pagina weergaven (page views/aantal)

Het aantal pagina weergave-gebeurtenissen dat is geregistreerd met de TrackPageView () Application Insights-API.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessies (sessies/aantal)

Deze metrische waarde verwijst naar het aantal afzonderlijke sessie-Id's.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Traceringen (traceringen/aantal)

Het aantal trace-instructies dat is geregistreerd met de TrackTrace () Application Insights-API-aanroep.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Gebruikers (gebruikers/aantal)

Het aantal afzonderlijke gebruikers die toegang hebben tot uw toepassing. De nauw keurigheid van deze metriek kan aanzienlijk worden beïnvloed door het gebruik van een telemetrie-steek proef en filters.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Gebruikers, geverifieerd (gebruikers/geverifieerd)

Het aantal afzonderlijke gebruikers dat is geverifieerd in uw toepassing.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
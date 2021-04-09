---
title: Azure-toepassing Insights-metrische standaard waarden | Microsoft Docs
description: Dit artikel bevat een overzicht van Azure-toepassing Insights-metrische gegevens met ondersteunde aggregaties en dimensies.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572354"
---
# <a name="application-insights-standard-metrics"></a>Application Insights standaard metrische gegevens

Standaard metrische gegevens worden vooraf geaggregeerd tijdens het verzamelen. ze hebben betere prestaties op het moment van query's. Op die manier kunnen Dash boards en real-time waarschuwingen optimaal worden gekozen.

## <a name="availability-metrics"></a>Metrische beschikbaarheids gegevens

Met metrische gegevens in de beschikbaarheids categorie kunt u de status van uw webtoepassing zien, zoals wordt waargenomen door punten over de hele wereld. [Configureer de beschikbaarheids tests](../app/monitor-web-app-availability.md) om alle metrische gegevens uit deze categorie te gebruiken.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Beschik baarheid (availabilityResults/availabilityPercentage)
De metrische *beschikbaarheids* gegevens tonen het percentage van de webtest-runs waarvoor geen problemen zijn gedetecteerd. De laagst mogelijke waarde is 0, wat aangeeft dat alle uitvoeringen van de webtest zijn mislukt. De waarde van 100 betekent dat alle webtest uitvoeringen de validatie criteria heeft door gegeven.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Percentage|Gemiddeld|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Duur van beschikbaarheids test (availabilityResults/duur)

De metrische gegevens voor de *beschikbaarheids test* geven aan hoe lang het duurt voordat de webtest wordt uitgevoerd. Voor de [webtests met meerdere stappen](../app/availability-multistep.md)weerspiegelt de metriek de totale uitvoerings tijd van alle stappen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Milliseconden|Gemiddeld, min, Max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Beschikbaarheids testen (availabilityResults/Count)

De metrische *beschikbaarheids tests* weerspiegelt het aantal van de webtests dat wordt uitgevoerd door Azure monitor.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Count|Count|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Metrische gegevens van de browser

De metrische gegevens van de browser worden verzameld door de Application Insights java script SDK van echte gebruikers browsers. Ze bieden fantastische inzichten in de ervaring van uw gebruikers met uw web-app. De metrische gegevens van de browser worden doorgaans niet bemonsterd. Dit betekent dat ze een grotere nauw keurigheid van de gebruiks cijfers bieden ten opzichte van metrische gegevens aan de server zijde die kunnen worden schuingetrokken door steek proeven.

> [!NOTE]
> Als u metrische gegevens voor de browser wilt verzamelen, moet uw toepassing worden geinstrumenteerd met de [Application Insights java script SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Laad tijd van browser pagina (browserTimings/totalDuration)

|Meeteenheid|Ondersteunde aggregaties| Ondersteunde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Verwerkings tijd van client (browserTiming/processingDuration)

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Netwerk verbindings tijd voor laden van pagina (browserTimings/networkDuration)

|Meeteenheid|Ondersteunde aggregaties| Ondersteunde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Reactie tijd van ontvangen (browserTimings/receiveDuration)

|Meeteenheid|Ondersteunde aggregaties| Ondersteunde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

### <a name="send-request-time-browsertimingssendduration"></a>Verzend aanvraag tijd (browserTimings/sendDuration)

|Meeteenheid|Ondersteunde aggregaties| Ondersteunde dimensies|
|---|---|---|
|Milliseconden|Gemiddeld, min, Max|Geen|

## <a name="failure-metrics"></a>Metrische fout gegevens

De metrische gegevens in **fouten** tonen problemen met verwerkings aanvragen, afhankelijkheids aanroepen en uitzonde ringen.

### <a name="browser-exceptions-exceptionsbrowser"></a>Browser uitzonderingen (uitzonde ringen/browser)

Deze metriek weerspiegelt het aantal uitzonde ringen dat is veroorzaakt door de toepassings code die in de browser wordt uitgevoerd. Alleen uitzonde ringen die worden bijgehouden met een ```trackException()``` Application Insights-API-aanroep, zijn opgenomen in de metrische gegevens.

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies|
|---|---|---|---|
| Count | Count | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Mislukte afhankelijkheids aanroepen (afhankelijkheden/mislukt)

Het aantal mislukte afhankelijkheids aanroepen.

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
|Count|Count| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Uitzonde ringen (uitzonde ringen/aantal)

Telkens wanneer u een uitzonde ring registreert op Application Insights, wordt er een aanroep naar de [methode trackException ()](../app/api-custom-events-metrics.md#trackexception) van de SDK. De metriek uitzonde ringen toont het aantal geregistreerde uitzonde ringen.

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Mislukte aanvragen (aanvragen/mislukt)

Het aantal bijgehouden server aanvragen dat is gemarkeerd als *mislukt*. Standaard markeert de Application Insights SDK automatisch elke server aanvraag die de 5xx of 4xx van HTTP-antwoorden als een mislukte aanvraag heeft geretourneerd. U kunt deze logica aanpassen door de eigenschap  *success* van het aanvraag-telemetrie in een [aangepaste telemetrie-initialisatie functie](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)te wijzigen.


|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Server uitzonderingen (uitzonde ringen/server)

Met deze metriek wordt het aantal server uitzonderingen weer gegeven.

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Prestatiemeteritems

Gebruik metrische gegevens in de categorie **prestatie meter items** om toegang te krijgen tot [systeem prestatie meter items die door Application Insights zijn verzameld](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Beschikbaar geheugen (Performance Counters/availableMemory)

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
|Gegevens afhankelijk: Mega bytes, gigabytes|Gemiddeld, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Uitzonderings frequentie (Performance Counters/exceptionRate)

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
| Count | Gemiddeld, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Uitvoerings tijd van de HTTP-aanvraag (Performance Counters/requestExecutionTime)

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
| Milliseconden | Gemiddeld, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Frequentie van HTTP-aanvragen (Performance Counters/requestsPerSecond)

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
| Aanvragen per seconde | Gemiddeld, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-aanvragen in de toepassings wachtrij (Performance Counters/requestsInQueue)

|Meeteenheid|Ondersteunde aggregaties | Ondersteunde dimensies |
|---|---|---|---|
| Count | Gemiddeld, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU van proces (Performance Counters/processCpuPercentage)

De metriek laat zien hoeveel van de totale processor capaciteit wordt verbruikt door het proces dat als host fungeert voor uw bewaakte app.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>I/o-frequentie van processen (Performance Counters/processIOBytesPerSecond)

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes per seconde|Gemiddeld, min, Max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Privé-bytes verwerken (Performance Counters/processPrivateBytes)

De hoeveelheid niet-gedeeld geheugen die het bewaakte proces voor de gegevens heeft toegewezen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes|Gemiddeld, min, Max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processor tijd (Performance Counters/processorCpuPercentage)

CPU-gebruik door *alle* processen die worden uitgevoerd op het bewaakte Server exemplaar.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, min, Max|`Cloud role instance` |

>[!NOTE]
> De metrische processor tijd is niet beschikbaar voor de toepassingen die worden gehost in Azure-app Services. Gebruik de  [CPU](#process-cpu-performancecountersprocesscpupercentage) -metriek van het proces om het CPU-gebruik bij te houden van de webtoepassingen die worden gehost in app Services.

## <a name="server-metrics"></a>Metrische Server gegevens

### <a name="dependency-calls-dependenciescount"></a>Afhankelijkheids aanroepen (afhankelijkheden/aantal)

Deze metriek is gerelateerd aan het aantal afhankelijkheids aanroepen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Duur van afhankelijkheid (afhankelijkheden/duur)

Deze metrische waarde verwijst naar de duur van afhankelijkheids aanroepen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Tijd | Gemiddeld, min, Max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Aantal server aanvragen (aanvragen/aantal)

Deze metriek weerspiegelt het aantal inkomende server aanvragen dat door uw webtoepassing is ontvangen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Count | Gemiddeld | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Server aanvragen (aanvragen/aantal)

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Server reactietijd (aanvragen/duur)

Deze metriek weerspiegelt de tijd die nodig is voor het verwerken van binnenkomende aanvragen door de servers.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Tijd | Gemiddeld, min, Max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Metrische gegevens over gebruik

### <a name="page-view-load-time-pageviewsduration"></a>Laad tijd pagina weergave (page views/duur)

Deze metrische waarde verwijst naar de hoeveelheid tijd die nodig was voor het laden van pagina weergave-gebeurtenissen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Tijd | Gemiddeld, min, Max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Pagina weergaven (page views/aantal)

Het aantal pagina weergave-gebeurtenissen dat is geregistreerd met de TrackPageView () Application Insights-API.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Count | Count | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Traceringen (traceringen/aantal)

Het aantal trace-instructies dat is geregistreerd met de TrackTrace () Application Insights-API-aanroep.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [metrische gegevens op basis van een logboek en vooraf geaggregeerde metrieken](./pre-aggregated-metrics-log-metrics.md).
* [Op logboek gebaseerde metrische query's en definities](../essentials/app-insights-metrics.md).
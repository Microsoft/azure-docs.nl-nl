---
title: Azure-toepassing Insights-gegevens model | Microsoft Docs
description: Beschrijft de eigenschappen die zijn geëxporteerd uit continue export in JSON en gebruikt als filters.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: b4609d54c1c3c33a654dd58a3bceaca4974fda15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584220"
---
# <a name="application-insights-export-data-model"></a>Gegevens model Application Insights exporteren
Deze tabel bevat de eigenschappen van de telemetrie die van de [Application Insights](./app-insights-overview.md) sdk's naar de portal worden verzonden.
Deze eigenschappen worden weer gegeven in gegevens uitvoer van [doorlopend exporteren](export-telemetry.md).
Ze worden ook weer gegeven in eigenschaps filters in [metrische Explorer](../essentials/metrics-charts.md) en [Diagnostische Zoek opdrachten](./diagnostic-search.md).

Punten om te noteren:

* `[0]` in deze tabellen geeft u een punt op in het pad waar u een index wilt invoegen. maar dit is niet altijd 0.
* De tijds duur ligt in tienden van een micro seconde, dus 10000000 = = 1 seconde.
* Datums en tijden zijn UTC en worden uitgedrukt in de ISO-notatie `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Voorbeeld

```json
// A server report about an HTTP request
{
  "request": [
    {
      "urlData": { // derived from 'url'
        "host": "contoso.org",
        "base": "/",
        "hashTag": ""
      },
      "responseCode": 200, // Sent to client
      "success": true, // Default == responseCode<400
      // Request id becomes the operation id of child events
      "id": "fCOhCdCnZ9I=",  
      "name": "GET Home/Index",
      "count": 1, // 100% / sampling rate
      "durationMetric": {
        "value": 1046804.0, // 10000000 == 1 second
        // Currently the following fields are redundant:
        "count": 1.0,
        "min": 1046804.0,
        "max": 1046804.0,
        "stdDev": 0.0,
        "sampledValue": 1046804.0
      },
      "url": "/"
    }
  ],
  "internal": {
    "data": {
      "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
      "documentVersion": "1.61"
    }
  },
  "context": {
    "device": { // client browser
      "type": "PC",
      "screenResolution": { },
      "roleInstance": "WFWEB14B.fabrikam.net"
    },
    "application": { },
    "location": { // derived from client ip
      "continent": "North America",
      "country": "United States",
      // last octagon is anonymized to 0 at portal:
      "clientip": "168.62.177.0",
      "province": "",
      "city": ""
    },
    "data": {
      "isSynthetic": true, // we identified source as a bot
      // percentage of generated data sent to portal:
      "samplingRate": 100.0,
      "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
    },
    "user": {
      "isAuthenticated": false,
      "anonId": "us-tx-sn1-azr", // bot agent id
      "anonAcquisitionDate": "0001-01-01T00:00:00Z",
      "authAcquisitionDate": "0001-01-01T00:00:00Z",
      "accountAcquisitionDate": "0001-01-01T00:00:00Z"
    },
    "operation": {
      "id": "fCOhCdCnZ9I=",
      "parentId": "fCOhCdCnZ9I=",
      "name": "GET Home/Index"
    },
    "cloud": { },
    "serverDevice": { },
    "custom": { // set by custom fields of track calls
      "dimensions": [ ],
      "metrics": [ ]
    },
    "session": {
      "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
      "isFirst": true
    }
  }
}
```

## <a name="context"></a>Context
Alle soorten telemetrie zijn vergezeld van een context sectie. Niet al deze velden worden met elk gegevens punt verzonden.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| context. custom. Dimensions [0] |object [] |Sleutel-waarde teken reeks paren die zijn ingesteld met de para meter aangepaste eigenschappen. Maximum lengte van de sleutel 100, waarden met een maximale lengte van 1024. Meer dan 100 unieke waarden, de eigenschap kan worden doorzocht, maar kan niet worden gebruikt voor segmentatie. Maxi maal 200 sleutels per iKey. |
| context. custom. Metrics [0] |object [] |Sleutel-waardeparen ingesteld op basis van de para meter voor aangepaste metingen en door TrackMetrics. Maximum lengte van de sleutel 100, waarden kunnen numeriek zijn. |
| context. data. eventTime |tekenreeks |UTC |
| context. data. isSynthetic |booleaans |De aanvraag lijkt afkomstig te zijn van een bot of Webtest. |
| context. data. samplingRate |getal |Het percentage van de telemetrie dat is gegenereerd door de SDK en dat naar de portal wordt verzonden. Bereik 0.0-100,0. |
| context. apparaat |object |Client apparaat |
| context. device. browser |tekenreeks |IE, Chrome,... |
| context. device. browserVersion |tekenreeks |Chrome 48,0,... |
| context. device. deviceModel |tekenreeks | |
| context. device. DeviceName |tekenreeks | |
| context.device.id |tekenreeks | |
| context. apparaat. land instelling |tekenreeks |en-GB, de-DE,... |
| context. device. Network |tekenreeks | |
| context. device. oemName |tekenreeks | |
| context. device. OS |tekenreeks | |
| context. device. onmedium |tekenreeks |Hostbesturingssyteem |
| context. device. roleInstance |tekenreeks |ID van de server host |
| context. device. rolnaam |tekenreeks | |
| context. device. screenResolution |tekenreeks | |
| context. device. type |tekenreeks |PC, browser,... |
| context. locatie |object |Afgeleid `clientip` . |
| context. locatie. plaats |tekenreeks |Afgeleid van `clientip` , indien bekend |
| context. location. client |tekenreeks |De laatste Octagon is geanonimiseerd op 0. |
| context. locatie. continent |tekenreeks | |
| context. locatie. Country |tekenreeks | |
| context. location. provincie |tekenreeks |Staat of provincie |
| context.operation.id |tekenreeks |Items die hetzelfde zijn, `operation id` worden weer gegeven als verwante items in de portal. Normaal gesp roken de `request id` . |
| context.operation.name |tekenreeks |URL of aanvraag naam |
| context. Operation. parentId |tekenreeks |Hiermee staat u geneste gerelateerde items toe. |
| context.session.id |tekenreeks |`Id` van een groep bewerkingen uit dezelfde bron. Een periode van 30 minuten zonder een bewerking signaleert het einde van een sessie. |
| context. Session. isFirst |booleaans | |
| context. User. accountAcquisitionDate |tekenreeks | |
| context. User. accountId |tekenreeks | |
| context. User. anonAcquisitionDate |tekenreeks | |
| context. User. anonId |tekenreeks | |
| context. User. authAcquisitionDate |tekenreeks |[Geverifieerde gebruiker](./api-custom-events-metrics.md#authenticated-users) |
| context. User. authId |tekenreeks | |
| context. User. isAuthenticated |booleaans | |
| context. User. storeRegion |tekenreeks | |
| internal.data.documentVersion |tekenreeks | |
| internal.data.id |tekenreeks | `Unique id` die wordt toegewezen wanneer een item wordt opgenomen in Application Insights |

## <a name="events"></a>Gebeurtenissen
Aangepaste gebeurtenissen gegenereerd door [track Event ()](./api-custom-events-metrics.md#trackevent).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal gebeurtenissen [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| gebeurtenis [0] naam |tekenreeks |Gebeurtenis naam.  Maximale lengte van 250. |
| gebeurtenis [0] URL |tekenreeks | |
| gebeurtenis [0] urlData. base |tekenreeks | |
| gebeurtenis [0] urlData. host |tekenreeks | |

## <a name="exceptions"></a>Uitzonderingen
Rapporteert [uitzonde ringen](./asp-net-exceptions.md) op de server en in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| basicException [0]-Assembly |tekenreeks | |
| aantal basicException [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| basicException [0] exceptionGroup |tekenreeks | |
| basicException [0] exceptionType |tekenreeks | |
| basicException [0] failedUserCodeMethod |tekenreeks | |
| basicException [0] failedUserCodeAssembly |tekenreeks | |
| basicException [0] handledAt |tekenreeks | |
| basicException [0] hasFullStack |booleaans | |
| basicException [0] `id` |tekenreeks | |
| basicException [0]-methode |tekenreeks | |
| basicException [0] bericht |tekenreeks |Uitzonderings bericht. Maximale lengte van 10k. |
| basicException [0] outerExceptionMessage |tekenreeks | |
| basicException [0] outerExceptionThrownAtAssembly |tekenreeks | |
| basicException [0] outerExceptionThrownAtMethod |tekenreeks | |
| basicException [0] outerExceptionType |tekenreeks | |
| basicException [0] outerId |tekenreeks | |
| basicException [0] parsedStack [0] Assembly |tekenreeks | |
| basicException [0] parsedStack [0] bestands naam |tekenreeks | |
| basicException [0] parsedStack [0] niveau |geheel getal | |
| basicException [0] parsedStack [0] regel |geheel getal | |
| basicException [0] parsedStack [0]-methode |tekenreeks | |
| basicException [0] stack |tekenreeks |Maximale lengte 10k |
| basicException [0] typeName |tekenreeks | |

## <a name="trace-messages"></a>Berichten traceren
Verzonden door [TrackTrace](./api-custom-events-metrics.md#tracktrace)en door de [logboek registratie-adapters](./asp-net-trace-logs.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| bericht [0] logger logboek |tekenreeks | |
| bericht [0] para meters |tekenreeks | |
| bericht [0] RAW |tekenreeks |Het logboek bericht heeft een maximale lengte van 10k. |
| bericht [0] severityLevel |tekenreeks | |

## <a name="remote-dependency"></a>Externe afhankelijkheid
Verzonden door TrackDependency. Wordt gebruikt voor het rapporteren van de prestaties en het gebruik van [aanroepen naar afhankelijkheden](./asp-net-dependencies.md) in de server en Ajax-aanroepen in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| remoteDependency [0] async |booleaans | |
| remoteDependency [0] basenaam |tekenreeks | |
| remoteDependency [0] opdrachtnaam |tekenreeks |Bijvoorbeeld ' home/index ' |
| aantal remoteDependency [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |tekenreeks |HTTP, SQL,... |
| remoteDependency [0] durationMetric. waarde |getal |Tijd van oproep tot voltooiing van antwoord door afhankelijkheid |
| remoteDependency [0] `id` |tekenreeks | |
| remoteDependency [0] naam |tekenreeks |URL. Maximale lengte van 250. |
| remoteDependency [0] resultCode |tekenreeks |van HTTP-afhankelijkheid |
| remoteDependency [0] geslaagd |booleaans | |
| remoteDependency [0] type |tekenreeks |Http, SQL,... |
| remoteDependency [0] URL |tekenreeks |Maximale lengte van 2000 |
| remoteDependency [0] urlData. base |tekenreeks |Maximale lengte van 2000 |
| remoteDependency [0] urlData. hashTag |tekenreeks | |
| remoteDependency [0] urlData. host |tekenreeks |Maximale lengte van 200 |

## <a name="requests"></a>Aanvragen
Verzonden door [TrackRequest](./api-custom-events-metrics.md#trackrequest). De standaard modules gebruiken dit om de reactie tijd van de server te rapporteren, gemeten op de server.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal aanvragen [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld: 4 = &gt; 25%. |
| aanvraag [0] durationMetric. waarde |getal |Tijd van aanvraag die inkomt bij antwoord. 1e7 = = 1S |
| aanvraag [0] `id` |tekenreeks |`Operation id` |
| naam van aanvraag [0] |tekenreeks |GET/POST + URL-basis.  Maximale lengte van 250 |
| aanvraag [0] responseCode |geheel getal |HTTP-antwoord verzonden naar client |
| de aanvraag [0] is voltooid |booleaans |Standaard instelling = = (responseCode &lt; 400) |
| URL van aanvraag [0] |tekenreeks |Geen host |
| aanvraag [0] urlData. base |tekenreeks | |
| aanvraag [0] urlData. hashTag |tekenreeks | |
| aanvraag [0] urlData. host |tekenreeks | |

## <a name="page-view-performance"></a>Prestaties van pagina weergave
Verzonden door de browser. Hiermee wordt de tijd gemeten voor het verwerken van een pagina, van de gebruiker die de aanvraag initieert om volledig weer te geven (exclusief asynchrone AJAX-aanroepen).

Met context waarden wordt het client besturingssysteem en de browser versie weer gegeven.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| clientPerformance [0] clientProcess. waarde |geheel getal |De tijd vanaf het moment dat de HTML wordt ontvangen om de pagina weer te geven. |
| clientPerformance [0] naam |tekenreeks | |
| clientPerformance [0] networkConnection. waarde |geheel getal |De tijd die nodig is om een netwerk verbinding tot stand te brengen. |
| clientPerformance [0] receiveRequest. waarde |geheel getal |De tijd vanaf het einde van het verzenden van de aanvraag om de HTML in antwoord te ontvangen. |
| clientPerformance [0] sendRequest. waarde |geheel getal |De tijd die nodig is om de HTTP-aanvraag te verzenden. |
| clientPerformance [0] totale. waarde |geheel getal |De start tijd voor het verzenden van de aanvraag om de pagina weer te geven. |
| clientPerformance [0] URL |tekenreeks |URL van deze aanvraag |
| clientPerformance [0] urlData. base |tekenreeks | |
| clientPerformance [0] urlData. hashTag |tekenreeks | |
| clientPerformance [0] urlData. host |tekenreeks | |
| clientPerformance [0] urlData. Protocol |tekenreeks | |

## <a name="page-views"></a>Paginaweergaven
Verzonden door trackPageView () of [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal weer geven [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| weer gave [0] durationMetric. waarde |geheel getal |De waarde is optioneel ingesteld in trackPageView () of door startTrackPage ()-stopTrackPage (). Niet hetzelfde als clientPerformance-waarden. |
| naam van weer gave [0] |tekenreeks |Pagina titel.  Maximale lengte van 250 |
| URL van weer gave [0] |tekenreeks | |
| weer gave [0] urlData. base |tekenreeks | |
| weer gave [0] urlData. hashTag |tekenreeks | |
| weer gave [0] urlData. host |tekenreeks | |

## <a name="availability"></a>Beschikbaarheid
Rapporten over [Beschik baarheid van webtests](./monitor-web-app-availability.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| Beschik baarheid [0] availabilityMetric.name |tekenreeks |availability |
| Beschik baarheid [0] availabilityMetric. waarde |getal |1,0 of 0,0 |
| aantal Beschik baarheid [0] |geheel getal |100/([sampling](./sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| Beschik baarheid [0] dataSizeMetric.name |tekenreeks | |
| Beschik baarheid [0] dataSizeMetric. waarde |geheel getal | |
| Beschik baarheid [0] durationMetric.name |tekenreeks | |
| Beschik baarheid [0] durationMetric. waarde |getal |Duur van de test. 1e7 = = 1S |
| bericht Beschik baarheid [0] |tekenreeks |Diagnose van fouten |
| resultaat van Beschik baarheid [0] |tekenreeks |Geslaagd/mislukt |
| Beschik baarheid [0] runLocation |tekenreeks |Geo-bron van HTTP-aanvraag |
| Beschik baarheid [0] testnaam |tekenreeks | |
| Beschik baarheid [0] testRunId |tekenreeks | |
| Beschik baarheid [0] testTimestamp |tekenreeks | |

## <a name="metrics"></a>Metrische gegevens
Gegenereerd door TrackMetric ().

De metrische waarde is gevonden in context. custom. Metrics [0]

Bijvoorbeeld:

```json
{
  "metric": [ ],
  "context": {
  ...
    "custom": {
      "dimensions": [
        { "ProcessId": "4068" }
      ],
      "metrics": [
        {
          "dispatchRate": {
            "value": 0.001295,
            "count": 1.0,
            "min": 0.001295,
            "max": 0.001295,
            "stdDev": 0.0,
            "sampledValue": 0.001295,
            "sum": 0.001295
          }
        }
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>Metrische waarden
Metrische waarden, zowel in metrische rapporten als elders, worden gerapporteerd met een standaard-object structuur. Bijvoorbeeld:

```json
"durationMetric": {
  "name": "contoso.org",
  "type": "Aggregation",
  "value": 468.71603053650279,
  "count": 1.0,
  "min": 468.71603053650279,
  "max": 468.71603053650279,
  "stdDev": 0.0,
  "sampledValue": 468.71603053650279
}
```

Dit kan al dan niet worden gewijzigd in de toekomst, in alle waarden die zijn gerapporteerd vanuit de standaard SDK-modules `count==1` en alleen de `name` `value` velden en zijn nuttig. Wanneer u uw eigen TrackMetric-aanroepen schrijft waarin u de andere para meters hebt ingesteld, is de enige andere situatie waar ze verschillend zouden zijn.

Het doel van de andere velden is om toe te staan dat metrische gegevens in de SDK worden geaggregeerd, om het verkeer naar de portal te verminderen. U kunt bijvoorbeeld een gemiddelde berekenen van een aantal opeenvolgende Lees bewerking voordat u elk rapport met metrische gegevens verzendt. Vervolgens berekent u de minimum-, maximum-, standaard afwijking-en aggregatie waarde (Sum of average) en stelt u het aantal lees bewerkingen in dat door het rapport wordt vertegenwoordigd.

In de bovenstaande tabellen zijn het aantal zelden gebruikte velden, min, Max, stddev verwerken en sampledValue wegge laten.

In plaats van vooraf aggregatie van metrische gegevens, kunt u [steek proeven](./sampling.md) gebruiken als u het volume van de telemetrie wilt reduceren.

### <a name="durations"></a>Duur
Tenzij anders aangegeven, worden de duursen weer gegeven in tienden van een micro seconde, zodat 10000000,0 betekent 1 seconde.

## <a name="see-also"></a>Zie ook
* [Application Insights](./app-insights-overview.md)
* [Continue export](export-telemetry.md)
* [Codevoorbeelden](export-telemetry.md#code-samples)


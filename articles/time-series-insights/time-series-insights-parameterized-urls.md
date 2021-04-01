---
title: Aangepaste weer gaven met geparametriseerde Url's delen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het maken van geparametriseerde Url's voor het eenvoudig delen van aangepaste Explorer-weer gaven in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665324"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Een aangepaste weergave delen via een geparameteriseerde URL

Als u een aangepaste weer gave in Azure Time Series Insights Explorer wilt delen, kunt u programmatisch een geparametriseerde URL maken van de aangepaste weer gave.

Azure Time Series Insights Explorer ondersteunt URL-query parameters om weer gaven in de ervaring rechtstreeks vanuit de URL op te geven. U kunt bijvoorbeeld via de URL een doelomgeving, een zoekpredicaat en de gewenste tijdspanne opgeven. Wanneer een gebruiker de aangepaste URL selecteert, biedt de interface een koppeling rechtstreeks naar die asset in de Azure Time Series Insights Portal. Beleid voor gegevenstoegang wordt toegepast.

> [!TIP]
>
> * Bekijk de gratis [Azure time series Insights demo](https://insights.timeseries.azure.com/samples).
> * Lees de documentatie bij de bijbehorende [Azure time series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Omgevings-id

De parameter `environmentId=<guid>` geeft de id van de doelomgeving op. Het is een onderdeel van de FQDN van gegevens toegang en u vindt het in de rechter bovenhoek van het overzicht van de omgeving in het Azure Portal. Alles dat voorafgaat aan `env.timeseries.azure.com` .

Een voorbeeld van de parameter voor omgevings-id is `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tijd

U kunt absolute of relatieve tijdwaarden opgeven met een geparameteriseerde URL.

### <a name="absolute-time-values"></a>Absolute tijdwaarden

Voor absolute tijdwaarden gebruikt u de parameters `from=<integer>` en `to=<integer>`.

* `from=<integer>` is een waarde in JavaScript-milliseconden voor de begintijd van de tijdspanne voor zoeken.
* `to=<integer>` is een waarde in JavaScript-milliseconden voor de eindtijd van de tijdspanne voor zoeken.

> [!TIP]
> Als u eenvoudig datums wilt omzetten in Java script-milliseconden, probeert u de [epoche & Unix Time Stamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatieve tijdwaarden

Voor een relatieve tijd waarde gebruikt u `relativeMillis=<value>` , waarbij *waarde* in Java script-milliseconden is van de meest recente tijds tempel die is ontvangen van de API.

`&relativeMillis=3600000` geeft bijvoorbeeld de afgelopen 60 minuten aan gegevens weer.

Geaccepteerde waarden komen overeen met het menu **snelle tijd** van Azure time series Insights Explorer en bevatten het volgende:

* `1800000` (Afgelopen 30 minuten)
* `3600000` (Afgelopen 60 minuten)
* `10800000` (Afgelopen 3 uur)
* `21600000` (Afgelopen 6 uur)
* `43200000` (Afgelopen 12 uur)
* `86400000` (Afgelopen 24 uur)
* `604800000` (Afgelopen 7 dagen)
* `2592000000` (Afgelopen 30 uur)

### <a name="optional-parameters"></a>Optionele parameters

De `timeSeriesDefinitions=<collection of term objects>` para meter bepaalt de voor waarden die in een Azure time series Insights weer gave worden weer gegeven:

| Parameter | URL-item | Beschrijving |
| --- | --- | --- |
| **name** | `\<string>` | De naam van het *onderdeel*. |
| **splitBy** | `\<string>` | De naam van de kolom waarop moet worden *gesplitst*. |
| **measureName** | `\<string>` | De kolomnaam van de *meting*. |
| **predicaat** | `\<string>` | De *where*-component voor filteren aan de serverzijde. |
| **useSum** | `true` | Een optionele para meter waarmee het gebruik van een som voor uw meting wordt opgegeven. |

> [!NOTE]
> Als `Events` de geselecteerde **useSum** -meting is, is aantal standaard geselecteerd.  
> Als `Events` niet is geselecteerd, wordt standaard het gemiddelde geselecteerd. |

* `multiChartStack=<true/false>`Met het sleutel-waardepaar kan de grafiek worden gestapeld.
* Met het `multiChartSameScale=<true/false>` sleutel-waardepaar kunt u dezelfde schaal van de Y-as voor alle termen binnen een optionele para meter.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`U kunt de schuif regelaar voor het interval aanpassen om een meer gedetailleerde of vloeiendere weer gave van de grafiek te bieden.  
* Met de `timezoneOffset=<integer>` para meter kunt u de tijd zone voor de grafiek instellen zodat deze wordt weer gegeven als een offset naar UTC.

| Paar (s) | Description |
| --- | --- |
| `multiChartStack=false` | `true` is standaard ingeschakeld, dus door gegeven `false` aan stack. |
| `multiChartStack=false&multiChartSameScale=true` | Stapelen moet zijn ingeschakeld om dezelfde schaling van de Y-as te gebruiken voor verschillende onderdelen.  `false` `true` Deze functie is standaard ingeschakeld. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units = `days` ,,, `hours` `minutes` `seconds` , `milliseconds` .  Gebruik altijd een hoofdletter voor de eenheid. </br> Definieer het aantal eenheden door het gewenste gehele getal voor **timeBucketSize** door te geven.  |
| `timezoneOffset=-<integer>` | Het gehele getal is altijd in milliseconden. |

> [!NOTE]
> **time Bucket unit** waarden kunnen Maxi maal zeven dagen worden gesoepeld.
> **time zone offset** waarden zijn geen UTC of lokale tijd.

### <a name="examples"></a>Voorbeelden

Als u de definities van de tijd reeks wilt toevoegen aan een Azure Time Series Insights omgeving als een URL-para meter, voegt u het volgende toe:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Gebruik de voor beelden van Time Series-definities voor:

* De omgevings-ID
* De laatste 60 minuten aan gegevens
* De voor waarden (**F1PressureID**, **F2TempStation** en **F3VibrationPL**) waaruit de optionele para meters bestaan

U kunt de volgende geparametriseerde URL voor een weer gave maken:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure Time Series Insights Explorer-URL met para meter](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Bekijk de Explorer Live [met behulp van het bovenstaande URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) -voor beeld.

De URL hierboven wordt beschreven en geeft de weer gave Azure Time Series Insights Explorer met para meters.

* De para meters-predikaten.

  [![Azure Time Series Insights Explorer-predikaten met para meters.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* De gedeelde volledige grafiek weergave.

  [![De gedeelde volledige grafiek weergave.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [opvragen van gegevens met behulp van C#](time-series-insights-query-data-csharp.md).

* Meer informatie over de [Azure time series Insights Explorer](./time-series-insights-explorer.md).

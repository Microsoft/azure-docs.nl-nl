---
title: Azure-toepassing Insights-telemetrie in uw Java-Web-app filteren
description: Verminder telemetrie-verkeer door de gebeurtenissen die u niet hoeft te bewaken, te filteren.
ms.topic: conceptual
ms.date: 3/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 35a95ac32fc4390e08d3c7fee2b9f9ff52202e4b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573822"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Telemetrie filteren in uw Java-Web-app

> [!IMPORTANT]
> De benadering die in dit document wordt beschreven, wordt niet meer aanbevolen.
>
> De aanbevolen benadering voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Volg de richt lijnen voor [Application Insights Java 3,0-agent](./java-in-process-agent.md).

Filters bieden een manier om de telemetrie te selecteren die uw [Java-Web-app naar Application Insights verzendt](java-get-started.md). Er zijn een aantal out-of-the-box filters die u kunt gebruiken, en u kunt ook uw eigen aangepaste filters schrijven.

De out-of-the-box filters zijn:

* Ernst niveau van tracering
* Specifieke Url's, tref woorden of antwoord codes
* Snelle reacties-dat wil zeggen, aanvragen waarop uw app snel heeft gereageerd
* Specifieke gebeurtenis namen

> [!NOTE]
> Filters scheefen de metrische gegevens van uw app. U kunt bijvoorbeeld bepalen dat u bij het vaststellen van langzame reacties een filter wilt instellen om snelle reactie tijden te negeren. Maar u moet er rekening mee houden dat de gemiddelde reactie tijd die wordt gerapporteerd door Application Insights lager is dan de werkelijke snelheid en dat het aantal aanvragen kleiner is dan het werkelijke aantal.
> Als dit een probleem is, gebruikt u in plaats daarvan [steek proeven](./sampling.md) .

## <a name="setting-filters"></a>Filters instellen

Voeg in ApplicationInsights.xml een sectie toe, `TelemetryProcessors` zoals in dit voor beeld:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[Inspecteer de volledige set ingebouwde processors](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Ingebouwde filters

### <a name="metric-telemetry-filter"></a>Filter metrische telemetrie

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Door komma's gescheiden lijst met aangepaste metrische namen.


### <a name="page-view-telemetry-filter"></a>Telemetrie-filter pagina weergave

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -De duur verwijst naar de tijd die nodig is om de pagina te laden. Als deze instelling is ingesteld, worden pagina's die sneller zijn geladen dan deze keer niet gerapporteerd.
* `NotNeededNames` -Door komma's gescheiden lijst met pagina namen.
* `NotNeededUrls` -Door komma's gescheiden lijst met URL-fragmenten. Hiermee `"home"` filtert u bijvoorbeeld alle pagina's met ' Home ' in de URL.


### <a name="request-telemetry-filter"></a>Telemetrie-filter aanvragen


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Synthetisch bron filter

Filtert alle telemetrie die waarden hebben in de eigenschap SyntheticSource. Dit zijn onder andere aanvragen van bots, spiders en beschikbaarheids testen.

Telemetrie voor alle synthetische aanvragen filteren:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Telemetrie filteren op specifieke synthetische bronnen:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` -Door komma's gescheiden lijst met synthetische bron namen.

### <a name="telemetry-event-filter"></a>Telemetrie-gebeurtenis Filter

Filtert aangepaste gebeurtenissen (geregistreerd met behulp van [track Event ()](./api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` -Door komma's gescheiden lijst met gebeurtenis namen.


### <a name="trace-telemetry-filter"></a>Telemetrie-filter traceren

Filtert logboek traceringen (geregistreerd met behulp van [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) of een [logboek registratie van de Framework-logging](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` geldige waarden zijn:
  *  ALLE traceringen uit het filter uitschakelen
  *  TRACERen: geen filters. is gelijk aan traceer niveau
  *  INFO-traceer niveau uitfilteren
  *  WAARSCHUWEN-TRACERing en informatie filteren
  *  FOUT-filter voor waarschuwing, INFO, TRACERing
  *  KRITIEK: alle alle maar essentiële filters uitfilteren


## <a name="custom-filters"></a>Aangepaste filters

### <a name="1-code-your-filter"></a>1. de code van uw filter

Maak in uw code een klasse die het volgende implementeert `TelemetryProcessor` :

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

        /* Any parameters that are required to support the filter.*/
        private final String successful;

        /* Initializers for the parameters, named "setParameterName" */
        public void setNotNeeded(String successful)
        {
            this.successful = successful;
        }

        /* This method is called for each item of telemetry to be sent.
           Return false to discard it.
           Return true to allow other processors to inspect it. */
        @Override
        public boolean process(Telemetry telemetry) {
            if (telemetry == null) { return true; }
            if (telemetry instanceof RequestTelemetry)
            {
                RequestTelemetry requestTelemetry = (RequestTelemetry)    telemetry;
                return request.getSuccess() == successful;
            }
            return true;
        }
    }

```

### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. aanroepen van het filter in het configuratie bestand

In ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. het filter aanroepen (Java-lente)

Voor toepassingen op basis van het lente-Framework moeten aangepaste telemetrie-processors als een bonen worden geregistreerd in uw hoofd toepassings klasse. Ze worden vervolgens automatisch bekabeld wanneer de toepassing wordt gestart.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

U moet uw eigen filter parameters maken in `application.properties` en gebruikmaken van het externe configuratie raamwerk met lente opstarten om deze para meters door te geven aan uw aangepaste filter. 


## <a name="troubleshooting"></a>Problemen oplossen

*Mijn filter werkt niet.*

* Controleer of u geldige parameter waarden hebt ingevoerd. Bijvoorbeeld, de duur moet een geheel getal zijn. Ongeldige waarden zorgen ervoor dat het filter wordt genegeerd. Als uw aangepaste filter een uitzonde ring genereert van een constructor of set-methode, wordt deze genegeerd.

## <a name="next-steps"></a>Volgende stappen

* [Bemonsteren](./sampling.md) : beschouw de steek proeven als een alternatief die uw metrische gegevens niet scheef stelt.


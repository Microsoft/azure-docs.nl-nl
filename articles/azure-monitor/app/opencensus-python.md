---
title: Python-toepassingen bewaken met Azure Monitor | Microsoft Docs
description: Bevat instructies voor het in wire-up maken van OpenCensus Python met Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 92d954a865a2d4a8c55177b132139dcd7d0444ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515920"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Een Azure Monitor voor uw Python-toepassing instellen

Azure Monitor biedt ondersteuning voor gedistribueerde tracering, verzameling van metrische gegevens en logboekregistratie van Python-toepassingen via integratie [met OpenCensus.](https://opencensus.io) In dit artikel wordt u door het proces van het instellen van OpenCensus voor Python en het verzenden van uw bewakingsgegevens naar de Azure Monitor.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Python-installatie. In dit artikel [wordt Python 3.7.0](https://www.python.org/downloads/release/python-370/)gebruikt, hoewel andere versies waarschijnlijk met kleine wijzigingen werken. De SDK ondersteunt alleen Python v2.7 en v3.4-v3.7.
- Maak een Application Insights [resource](./create-new-resource.md). U krijgt uw eigen instrumentatiesleutel (ikey) toegewezen voor uw resource.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument met OpenCensus Python SDK voor Azure Monitor

Installeer het OpenCensus-Azure Monitor export:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> Bij `python -m pip install opencensus-ext-azure` de opdracht wordt ervan uitgenomen dat u een `PATH` omgevingsvariabele hebt ingesteld voor uw Python-installatie. Als u deze variabele nog niet hebt geconfigureerd, moet u het volledige mappad opgeven naar de locatie van het uitvoerbare Python-bestand. Het resultaat is een opdracht als deze: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

De SDK gebruikt drie Azure Monitor voor het verzenden van verschillende typen telemetrie naar Azure Monitor. Dit zijn traceergegevens, metrische gegevens en logboeken. Zie overzicht van het gegevensplatform voor meer informatie over [deze telemetrietypen.](../data-platform.md) Gebruik de volgende instructies om deze telemetrietypen via de drie producenten te verzenden.

## <a name="telemetry-type-mappings"></a>Toewijzingen van telemetrietype

Hier ziet u de exporten die OpenCensus biedt, die zijn toe te rekenen aan de typen telemetrie die u ziet in Azure Monitor.

| Pijler van waarneembaarheid | Telemetrietype in Azure Monitor    | Uitleg                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Logboeken                    | Traceringen, uitzonderingen, customEvents   | Telemetrie van logboeken, uitzonderings-telemetrie, telemetrie van gebeurtenissen |
| Metrische gegevens                 | customMetrics, performanceCounters | Prestatiemeters voor aangepaste metrische gegevens                |
| Tracering                 | Vraagt afhankelijkheden aan             | Binnenkomende aanvragen, uitgaande aanvragen                |

### <a name="logs"></a>Logboeken

1. Eerst gaan we enkele lokale logboekgegevens genereren.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. De code vraagt continu om een waarde die moet worden ingevoerd. Voor elke ingevoerde waarde wordt een logboekinvoer ingediend.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Hoewel het invoeren van waarden nuttig is voor demonstratiedoeleinden, willen we uiteindelijk de logboekgegevens naar de Azure Monitor. Geef uw connection string rechtstreeks door aan de export. U kunt deze ook opgeven in een omgevingsvariabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig uw code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. De export verzendt logboekgegevens naar Azure Monitor. U vindt de gegevens onder `traces` . 

    > [!NOTE]
    > In deze context `traces` is niet hetzelfde als `tracing` . Hier verwijst naar het type telemetrie dat u ziet in Azure Monitor `traces` u `AzureLogHandler` gebruikt. Maar `tracing` verwijst naar een concept in OpenCensus en heeft betrekking op [gedistribueerde tracering](./distributed-tracing.md).

    > [!NOTE]
    > De hoofdlogboekregistratie is geconfigureerd met het niveau WAARSCHUWING. Dit betekent dat logboeken die u verzendt met een minder ernst worden genegeerd en op hun beurt niet worden verzonden naar Azure Monitor. Zie de documentatie voor [meer informatie.](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)

1. U kunt ook aangepaste eigenschappen toevoegen aan uw logboekberichten in het *extra* trefwoordargument met behulp van custom_dimensions veld. Deze eigenschappen worden weergegeven als sleutel-waardeparen `customDimensions` in in Azure Monitor.
    > [!NOTE]
    > Deze functie werkt alleen als u een woordenlijst aan het custom_dimensions doorgeven. Als u argumenten van een ander type doorkeert, worden deze genegeerd door de logger.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Logboekregistratie configureren voor Django-toepassingen

U kunt logboekregistratie expliciet configureren in de toepassingscode zoals hierboven voor uw Django-toepassingen, of u kunt dit opgeven in de configuratie van de logboekregistratie van Django. Deze code kan worden gebruikt voor elk bestand dat u gebruikt voor de configuratie van Django-instellingen. Zie Django-instellingen voor het configureren van [Django-instellingen.](https://docs.djangoproject.com/en/3.0/topics/settings/) Zie Django-logboekregistratie voor meer informatie over het [configureren van logboekregistratie.](https://docs.djangoproject.com/en/3.0/topics/logging/)

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Zorg ervoor dat u de logboeken gebruikt met dezelfde naam als de naam die is opgegeven in uw configuratie.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Uitzonderingen verzenden

OpenCensus Python houdt telemetrie niet automatisch bij en verzendt `exception` deze niet automatisch. Ze worden verzonden met `AzureLogHandler` behulp van uitzonderingen via de Python-bibliotheek voor logboekregistratie. U kunt aangepaste eigenschappen toevoegen, net als bij normale logboekregistratie.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Omdat u uitzonderingen expliciet moet logboeken, is het aan de gebruiker hoe deze niet-afhandelde uitzonderingen wil aanmelden. OpenCensus stelt geen beperkingen op de manier waarop een gebruiker dit wil doen, zolang ze expliciet een uitzonderings-telemetrielogboek maken.

#### <a name="send-events"></a>Gebeurtenissen verzenden

U kunt `customEvent` telemetrie op exact dezelfde manier verzenden als u telemetrie verzendt, behalve door in plaats `trace` daarvan te `AzureEventHandler` gebruiken.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Steekproeven

Bekijk sampling in OpenCensus voor informatie over [steekproeven in OpenCensus.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="log-correlation"></a>Logboekcorrelatie

Zie Integratie van OpenCensus Python-logboeken voor meer informatie over het verrijken van [uw logboeken](./correlation.md#log-correlation)met traceercontextgegevens.

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus Python [telemetry processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)(Telemetrieprocessors voor OpenCensus Python) voor meer informatie over het wijzigen van bijgespoorde telemetrie voordat deze wordt verzonden naar Azure Monitor.


### <a name="metrics"></a>Metrische gegevens

OpenCensus.stats ondersteunt 4 aggregatiemethoden, maar biedt gedeeltelijke ondersteuning voor Azure Monitor:

- **Aantal:** Het aantal meetpunten. De waarde is cumulatief, kan alleen toenemen en wordt opnieuw ingesteld op 0 bij opnieuw opstarten. 
- **Som:** Een som van de meetpunten. De waarde is cumulatief, kan alleen toenemen en wordt opnieuw ingesteld op 0 bij opnieuw opstarten. 
- **LastValue:** Behoudt de laatst vastgelegde waarde en laat de rest vallen.
- **Distributie:** Histogramdistributie van de meetpunten. Deze methode wordt **NIET ondersteund door de Azure-export.**

### <a name="count-aggregation-example"></a>Voorbeeld van aggregatie aantal

1. Eerst gaan we enkele lokale metrische gegevens genereren. We maken een eenvoudige metriek om het aantal keren bij te houden dat de gebruiker de **Enter-toets selecteert.**

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Bij het uitvoeren van de code wordt u herhaaldelijk gevraagd om **Enter te selecteren.** Er wordt een metrische gegevens gemaakt om het aantal keren bij te houden **dat Enter** is geselecteerd. Bij elke vermelding wordt de waarde verhoogd en worden de metrische gegevens weergegeven in de console. De informatie bevat de huidige waarde en het huidige tijdstempel wanneer het metrische gegeven is bijgewerkt.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Hoewel het invoeren van waarden nuttig is voor demonstratiedoeleinden, willen we uiteindelijk de metrische gegevens naar de Azure Monitor. Geef uw connection string rechtstreeks door aan de exporteur. U kunt deze ook opgeven in een omgevingsvariabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig de code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. De export verzendt metrische gegevens naar Azure Monitor met een vast interval. De standaardwaarde is om de 15 seconden. We volgen één metrische waarde, dus deze metrische gegevens, met welke waarde en tijdstempel deze ook bevatten, worden elk interval verzonden. De waarde is cumulatief, kan alleen toenemen en wordt opnieuw ingesteld op 0 bij opnieuw opstarten. U vindt de gegevens onder , maar de eigenschappen `customMetrics` `customMetrics` valueCount, valueSum, valueMin, valueMax en valueStdDev worden niet effectief gebruikt.

### <a name="setting-custom-dimensions-in-metrics"></a>Aangepaste dimensies instellen in metrische gegevens

Met de Opencensus Python SDK kunt u aangepaste dimensies toevoegen aan uw telemetriegegevens met metrische gegevens via , wat in feite een woordenlijst van `tags` sleutel-waardeparen is. 

1. Voeg de tags die u wilt gebruiken in de tagkaart in. De tagkaart fungeert als een soort 'pool' van alle beschikbare tags die u kunt gebruiken.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. Geef voor een specifieke de tags op die u wilt gebruiken bij het vastleggen van metrische `View` gegevens met die weergave via de tagsleutel.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. Zorg ervoor dat u de tagkaart gebruikt bij het vastleggen in de metingskaart. De tagsleutels die zijn opgegeven in de `View` moeten worden gevonden in de tagmap die wordt gebruikt om vast te maken.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. Onder de `customMetrics` tabel hebben alle records met metrische gegevens die worden ingediend met behulp van de aangepaste `prompt_view` dimensies `{"url":"http://example.com"}` .

1. Als u tags met verschillende waarden wilt maken met dezelfde sleutels, maakt u er nieuwe tagkaarten voor.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Prestatiemeteritems

Standaard verzendt de export van metrische gegevens een set prestatiemeters naar Azure Monitor. U kunt dit uitschakelen door de vlag in `enable_standard_metrics` te stellen op in de `False` constructor van de export van metrische gegevens.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Deze prestatiemeters worden momenteel verzonden:

- Beschikbaar geheugen (bytes)
- CPU-processortijd (percentage)
- Aantal binnenkomende aanvragen (per seconde)
- Gemiddelde uitvoeringstijd van binnenkomende aanvraag (milliseconden)
- CPU-gebruik verwerken (percentage)
- Persoonlijke bytes (bytes) verwerken

U zou deze metrische gegevens moeten kunnen zien in `performanceCounters` . Zie prestatiemeters [voor meer informatie.](./performance-counters.md)

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus Python [telemetry processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)(Telemetrieprocessors voor OpenCensus Python) voor meer informatie over het wijzigen van bijgespoorde telemetrie voordat deze wordt verzonden naar Azure Monitor.

### <a name="tracing"></a>Tracering

> [!NOTE]
> In OpenCensus verwijst `tracing` naar [gedistribueerde tracering.](./distributed-tracing.md) De `AzureExporter` verzendt `requests` en `dependency` telemetrie naar Azure Monitor.

1. Eerst gaan we lokaal enkele traceergegevens genereren. Voer in Python IDLE of de editor van uw keuze de volgende code in:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Als u de code herhaaldelijk wilt uitvoeren, wordt u gevraagd een waarde in te voeren. Bij elke vermelding wordt de waarde in de shell afgedrukt. De OpenCensus Python-module genereert een bijbehorend onderdeel van `SpanData` . Het OpenCensus-project definieert een [traceer als een structuur van spans](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Hoewel het invoeren van waarden nuttig is voor demonstratiedoeleinden, willen we uiteindelijk naar de `SpanData` Azure Monitor. Geef uw connection string rechtstreeks door aan de exporteur. U kunt deze ook opgeven in een omgevingsvariabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig de code uit de vorige stap op basis van het volgende codevoorbeeld:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Wanneer u nu het Python-script gaat uitvoeren, moet u nog steeds worden gevraagd om waarden in te voeren, maar alleen de waarde wordt afgedrukt in de shell. Het gemaakte `SpanData` wordt verzonden naar Azure Monitor. U vindt de afgegeven spangegevens onder `dependencies` . Zie OpenCensus Python-afhankelijkheden voor meer informatie [over uitgaande aanvragen.](./opencensus-python-dependency.md)
Zie OpenCensus Python-aanvragen voor meer informatie over [binnenkomende aanvragen.](./opencensus-python-request.md)

#### <a name="sampling"></a>Steekproeven

Bekijk sampling in OpenCensus voor informatie over [steekproeven in OpenCensus.](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)

#### <a name="trace-correlation"></a>Correlatie traceren

Zie OpenCensus Python-telemetriecorrelatie voor meer informatie over [telemetriecorrelatie in uw traceergegevens.](./correlation.md#telemetry-correlation-in-opencensus-python)

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie OpenCensus Python telemetry processors (OpenCensus [Python-telemetrieprocessors)](./api-filtering-sampling.md#opencensus-python-telemetry-processors)voor meer informatie over het wijzigen van bij te houden telemetrie voordat deze wordt verzonden naar Azure Monitor.

## <a name="configure-azure-monitor-exporters"></a>Exporten Azure Monitor configureren

Zoals u kunt zien, zijn er drie verschillende Azure Monitor die ondersteuning bieden voor OpenCensus. Elk type verzendt verschillende typen telemetrie naar Azure Monitor. Zie de volgende lijst om te zien welke typen telemetrie elke exporteur verzendt.

Elke export accepteert dezelfde argumenten voor configuratie, die worden doorgegeven via de constructors. U kunt hier de details van elk van deze informatie bekijken:

- `connection_string`: de connection string gebruikt om verbinding te maken met uw Azure Monitor resource. Heeft prioriteit boven `instrumentation_key` .
- `enable_standard_metrics`: wordt gebruikt voor `AzureMetricsExporter` . Signaleert de export om metrische gegevens [van prestatiemeters](../essentials/app-insights-metrics.md#performance-counters) automatisch naar de Azure Monitor. De standaardwaarde is `True` .
- `export_interval`: wordt gebruikt om de frequentie op te geven in seconden na het exporteren.
- `instrumentation_key`: de instrumentatiesleutel die wordt gebruikt om verbinding te maken met Azure Monitor resource.
- `logging_sampling_rate`: wordt gebruikt voor `AzureLogHandler` . Biedt een steekproeffrequentie [0,1,0] voor het exporteren van logboeken. De standaardwaarde is 1.0.
- `max_batch_size`: Hiermee geeft u de maximale grootte van telemetrie op die in één keer wordt geëxporteerd.
- `proxies`: Hiermee geeft u een reeks -proxies op die moeten worden gebruikt voor het verzenden van gegevens naar Azure Monitor. Zie voor meer informatie [.](https://requests.readthedocs.io/en/master/user/advanced/#proxies)
- `storage_path`: Een pad naar waar de lokale opslagmap zich bevindt (niet-afgeziene telemetrie). Vanaf `opencensus-ext-azure` v1.0.3 is het standaardpad de tijdelijke map van het besturingssysteem + `opencensus-python`  +  `your-ikey` . Vóór v1.0.3 is het standaardpad $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Uw gegevens weergeven met query's

U kunt de telemetriegegevens die vanuit uw toepassing zijn verzonden, weergeven via het **tabblad Logboeken (Analytics).**

![Schermopname van het overzichtsvenster met 'Logboeken (Analytics)' in een rood vak geselecteerd](./media/opencensus-python/0010-logs-query.png)

In de lijst onder **Actief**:

- Voor telemetrie die wordt verzonden met Azure Monitor traceerlanding, worden binnenkomende aanvragen weergegeven onder `requests` . Uitgaande of in-process aanvragen worden weergegeven onder `dependencies` .
- Voor telemetrie die wordt verzonden met de Azure Monitor export, worden verzonden metrische gegevens weergegeven onder `customMetrics` .
- Voor telemetrie die wordt verzonden met de Azure Monitor export, worden logboeken weergegeven onder `traces` . Uitzonderingen worden weergegeven onder `exceptions` .

Zie Logboeken in Azure Monitor voor meer informatie over het gebruik [van query'Azure Monitor.](../logs/data-platform-logs.md)

## <a name="learn-more-about-opencensus-for-python"></a>Meer informatie over OpenCensus voor Python

* [OpenCensus Python op GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Aanpassing](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor op GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-integraties](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor-voorbeeldtoepassingen](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende aanvragen bijhouden](./opencensus-python-dependency.md)
* [Aanvragen die niet worden uitgevoerd bijhouden](./opencensus-python-request.md)
* [Toepassingskaart](./app-map.md)
* [End-to-end prestatiebewaking](../app/tutorial-performance.md)

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](./monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](./proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen:](../alerts/alerts-log.md)stel waarschuwingen in om u te waarschuwen als een metrische gegevens een drempelwaarde overschrijden. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.


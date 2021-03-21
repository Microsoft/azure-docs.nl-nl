---
title: Python-toepassingen bewaken met Azure Monitor | Microsoft Docs
description: Biedt instructies om opentellingen python te bekabelen met Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: d22174b269ba9cea3b2c9cb9de2b5521df2786fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704409"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Azure Monitor instellen voor uw python-toepassing

Azure Monitor ondersteunt gedistribueerde tracering, metrische verzameling en logboek registratie van python-toepassingen via integratie met [Opentellingen](https://opencensus.io). Dit artikel begeleidt u bij het instellen van opentellingen voor python en het verzenden van uw bewakings gegevens naar Azure Monitor.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Installatie van python. In dit artikel wordt [python 3.7.0](https://www.python.org/downloads/release/python-370/)gebruikt, hoewel andere versies waarschijnlijk zullen werken met kleine wijzigingen. De SDK biedt alleen ondersteuning voor python v 2.7 en v 3.4-v 3.7.
- Een Application Insights- [resource](./create-new-resource.md)maken. U krijgt uw eigen instrumentatie sleutel (Ikey) toegewezen voor uw resource.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument met opentellingen python SDK voor Azure Monitor

Installeer de Azure Monitor Exporters van opentellingen:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> De `python -m pip install opencensus-ext-azure` opdracht gaat ervan uit dat u een `PATH` omgevings variabele hebt ingesteld voor uw python-installatie. Als u deze variabele niet hebt geconfigureerd, moet u het volledige mappad opgeven waar uw python-uitvoerbaar bestand zich bevindt. Het resultaat is een opdracht die er als volgt uitziet: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

De SDK gebruikt drie Azure Monitor Exporters voor het verzenden van verschillende soorten telemetrie naar Azure Monitor. Ze zijn Trace, metrische gegevens en Logboeken. Zie [het overzicht van het gegevens platform](../data-platform.md)voor meer informatie over deze typen telemetrie. Gebruik de volgende instructies om deze typen telemetrie via de drie Exporters te verzenden.

## <a name="telemetry-type-mappings"></a>Toewijzingen van het type telemetrie

Dit zijn de Exporters die opentellingen bieden die zijn gekoppeld aan de typen telemetrie die u ziet in Azure Monitor.

| Pijler van de bewaarneembaar | Type telemetrie in Azure Monitor    | Uitleg                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Logboeken                    | Traceringen, uitzonde ringen, customEvents   | Telemetrie logboek registratie, telemetrie-uitzonde ring, telemetrie-gebeurtenis |
| Metrische gegevens                 | customMetrics, Performance Counters | Prestatie meter items voor aangepaste metrische gegevens                |
| Tracering                 | Aanvragen afhankelijkheden             | Binnenkomende aanvragen, uitgaande aanvragen                |

### <a name="logs"></a>Logboeken

1. Eerst gaan we een aantal lokale logboek gegevens genereren.

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

1. De code vraagt voortdurend om een waarde die moet worden ingevoerd. Voor elke ingevoerde waarde wordt een logboek vermelding verzonden.

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

1. Hoewel het invoeren van waarden nuttig is voor demonstratie doeleinden, willen we uiteindelijk de logboek gegevens naar Azure Monitor verzenden. Geef uw connection string rechtstreeks door aan de exporteur. Of u kunt deze opgeven in een omgevings variabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

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

1. De exporteur stuurt logboek gegevens naar Azure Monitor. U kunt de gegevens vinden onder `traces` . 

    > [!NOTE]
    > In deze context `traces` is niet hetzelfde als `tracing` . Hier wordt `traces` verwezen naar het type telemetrie dat u ziet in azure monitor wanneer u gebruikt `AzureLogHandler` . Maar `tracing` verwijst naar een concept in Opentelling en is gekoppeld aan [gedistribueerde tracering](./distributed-tracing.md).

    > [!NOTE]
    > De hoofd logboek registratie is geconfigureerd met het WAARSCHUWINGS niveau. Dit betekent dat de logboeken die u verzendt met een minder ernst worden genegeerd en op zijn beurt niet naar Azure Monitor worden verzonden. Zie de [documentatie](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)voor meer informatie.

1. U kunt ook aangepaste eigenschappen toevoegen aan uw logboek berichten in het *extra* trefwoord argument met behulp van het veld custom_dimensions. Deze eigenschappen worden weer gegeven als sleutel-waardeparen in `customDimensions` in azure monitor.
    > [!NOTE]
    > Als u deze functie wilt gebruiken, moet u een woorden lijst door geven aan het veld custom_dimensions. Als u argumenten van elk ander type doorgeeft, worden deze door de logboeken genegeerd.

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

#### <a name="configure-logging-for-django-applications"></a>Logboek registratie configureren voor Django-toepassingen

U kunt logboek registratie expliciet configureren in uw toepassings code zoals hierboven voor uw django-toepassingen, maar u kunt het ook opgeven in de registratie configuratie van Django. Met deze code kunt u het bestand dat u gebruikt voor de configuratie van de Django-instellingen. Zie [Django Settings](https://docs.djangoproject.com/en/3.0/topics/settings/)(Engelstalig) voor informatie over het configureren van Django-instellingen. Zie [Django logging](https://docs.djangoproject.com/en/3.0/topics/logging/)(Engelstalig) voor meer informatie over het configureren van logboek registratie.

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

Zorg ervoor dat u het logboek gebruikt met de naam die is opgegeven in uw configuratie.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Uitzonde ringen verzenden

Met opentellingen python worden telemetrie niet automatisch bijgehouden en verzonden `exception` . Ze worden via `AzureLogHandler` de logboek bibliotheek van python verzonden via uitzonde ringen. U kunt aangepaste eigenschappen toevoegen, net als bij normale logboek registratie.

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
Omdat u uitzonde ringen expliciet moet vastleggen, is het aan de gebruiker de wijze waarop ze onverwerkte uitzonde ringen willen vastleggen in een logboek. Bij opentellingen worden geen beperkingen ingesteld voor de manier waarop een gebruiker dit wil doen, zolang een telemetrie van een uitzonde ring expliciet wordt vastgelegd.

#### <a name="send-events"></a>Gebeurtenissen verzenden

U kunt `customEvent` telemetrie op dezelfde manier verzenden als telemetrie `trace` , met uitzonde ring van `AzureEventHandler` in plaats daarvan.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Steekproeven

Voor informatie over de bemonstering van opentellingen bekijkt u de [bemonstering in Opentellingen](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Logboekcorrelatie

Zie voor meer informatie over het verrijken van uw logboeken met tracerings context gegevens integratie met opentellingen python [Logboeken](./correlation.md#log-correlation).

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden, de python- [telemetrie-processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)van opentellingen.


### <a name="metrics"></a>Metrische gegevens

Opentelling. statistieken ondersteunt 4 aggregatie methoden, maar biedt gedeeltelijke ondersteuning voor Azure Monitor:

- **Aantal:** Het aantal meet punten. De waarde is cumulatief, kan alleen verhogen en opnieuw instellen op 0 bij opnieuw opstarten. 
- **Som:** Een som van de meet punten. De waarde is cumulatief, kan alleen verhogen en opnieuw instellen op 0 bij opnieuw opstarten. 
- **LastValue:** Houdt de laatst vastgelegde waarde bij, alle andere waarden.
- **Distributie:** De verdeling van de histogrammen van de meet punten. Deze methode wordt **niet ondersteund door de Azure-export functie**.

### <a name="count-aggregation-example"></a>Voor beeld van aantal aggregatie

1. Eerst gaan we een aantal lokale metrische gegevens genereren. We maken een eenvoudige metriek om het aantal keren dat de gebruiker de **Enter** -toets selecteert, te volgen.

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
1. Door de code herhaaldelijk uit te voeren, wordt u gevraagd om **Enter** te selecteren. Er wordt een metriek gemaakt om het aantal keren dat de **invoer** is geselecteerd bij te houden. Bij elke vermelding wordt de waarde verhoogd en wordt de metrische informatie weer gegeven in de-console. De informatie bevat de huidige waarde en het huidige tijds tempel waarop de metriek is bijgewerkt.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Hoewel het invoeren van waarden nuttig is voor demonstratie doeleinden, willen we uiteindelijk de metrische gegevens naar Azure Monitor verzenden. Geef uw connection string rechtstreeks door aan de exporteur. Of u kunt deze opgeven in een omgevings variabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

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

1. De exporteur verzendt metrische gegevens naar Azure Monitor met een vast interval. De standaard waarde is elke 15 seconden. Er wordt één metrische waarde bijgehouden, dus deze metrische gegevens, met wille keurige waarden en tijds tempels, worden elk interval verzonden. De waarde is cumulatief, kan alleen verhogen en opnieuw instellen op 0 bij opnieuw opstarten. U vindt de gegevens onder `customMetrics` , maar de `customMetrics` Eigenschappen ValueCount, ValueSum, ValueMin, ValueMax en valueStdDev worden niet daad werkelijk gebruikt.

#### <a name="performance-counters"></a>Prestatiemeteritems

Standaard verzendt de metrische gegevens export een set prestatie meter items naar Azure Monitor. U kunt dit uitschakelen door de `enable_standard_metrics` vlag in te stellen op `False` de constructor van de metrische gegevens Exporter.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Deze prestatie meter items worden momenteel verzonden:

- Beschikbaar geheugen (bytes)
- Processor tijd CPU (percentage)
- Binnenkomende aanvraag frequentie (per seconde)
- Gemiddelde uitvoerings tijd inkomende aanvragen (milliseconden)
- CPU-gebruik verwerken (percentage)
- Privé bytes verwerken (bytes)

U kunt deze metrische gegevens weer geven in `performanceCounters` . Zie [prestatie meter items](./performance-counters.md)voor meer informatie.

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden, de python- [telemetrie-processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)van opentellingen.

### <a name="tracing"></a>Tracering

> [!NOTE]
> In opentellingen `tracing` verwijst naar [gedistribueerde tracering](./distributed-tracing.md). De `AzureExporter` verzonden `requests` en `dependency` telemetrie naar Azure monitor.

1. Eerst gaan we een aantal tracerings gegevens lokaal genereren. Voer in python niet-actief of uw editor van keuze de volgende code in:

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

1. Door de code herhaaldelijk uit te voeren, wordt u gevraagd een waarde in te voeren. Bij elke vermelding wordt de waarde afgedrukt op de shell. In de module opentellingen python wordt een bijbehorend onderdeel van gegenereerd `SpanData` . Het project opentelling definieert een [tracering als een boom structuur van reeksen](https://opencensus.io/core-concepts/tracing/).
    
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

1. Hoewel het invoeren van waarden nuttig is voor demonstratie doeleinden, willen we uiteindelijk `SpanData` naar Azure monitor verzenden. Geef uw connection string rechtstreeks door aan de exporteur. Of u kunt deze opgeven in een omgevings variabele, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Wijzig uw code uit de vorige stap op basis van het volgende code voorbeeld:

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

1. Wanneer u nu het python-script uitvoert, moet u nog steeds worden gevraagd om waarden in te voeren, maar alleen de waarde wordt afgedrukt in de shell. De gemaakt `SpanData` wordt verzonden naar Azure monitor. U vindt de verzonden gegevens over het bereik onder `dependencies` . Zie voor meer informatie over uitgaande aanvragen opentellingen python- [afhankelijkheden](./opencensus-python-dependency.md).
Zie voor meer informatie over inkomende aanvragen opentellingen python- [aanvragen](./opencensus-python-request.md).

#### <a name="sampling"></a>Steekproeven

Voor informatie over de bemonstering van opentellingen bekijkt u de [bemonstering in Opentellingen](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlatie traceren

Bekijk voor meer informatie over de correlatie van de telemetrie in uw tracerings gegevens, de python-correlatie van opentellingen van de [telemetrie](./correlation.md#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Telemetrie wijzigen

Zie voor meer informatie over het wijzigen van bijgehouden telemetrie voordat deze naar Azure Monitor wordt verzonden, de python- [telemetrie-processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)van opentellingen.

## <a name="configure-azure-monitor-exporters"></a>Azure Monitor-Exporters configureren

Zoals u ziet, zijn er drie verschillende Azure Monitor Exporters die ondersteuning bieden voor opentellingen. Elk hiervan verzendt verschillende typen telemetrie naar Azure Monitor. Raadpleeg de volgende lijst om te zien welke typen telemetrie elke exporteur verzendt.

Elke exporteur accepteert dezelfde argumenten voor de configuratie, door gegeven via de constructors. U kunt hier de details van deze informatie bekijken:

- `connection_string`: De connection string die wordt gebruikt om verbinding te maken met uw Azure Monitor resource. Krijgt voor rang op `instrumentation_key` .
- `enable_standard_metrics`: Gebruikt voor `AzureMetricsExporter` . Geeft de export functie de metrische gegevens van het [prestatie meter item](../essentials/app-insights-metrics.md#performance-counters) automatisch naar Azure monitor verzenden. Wordt standaard ingesteld op `True` .
- `export_interval`: Wordt gebruikt om de frequentie in seconden van het exporteren op te geven.
- `instrumentation_key`: De instrumentatie sleutel die wordt gebruikt om verbinding te maken met uw Azure Monitor-resource.
- `logging_sampling_rate`: Gebruikt voor `AzureLogHandler` . Geeft een sampling frequentie [0, 1.0] voor het exporteren van Logboeken. De standaard waarde is 1,0.
- `max_batch_size`: Hiermee geeft u de maximale grootte op van de telemetrie die tegelijk wordt geëxporteerd.
- `proxies`: Hiermee geeft u een reeks proxy's op die moet worden gebruikt voor het verzenden van gegevens naar Azure Monitor. Zie [proxy's](https://requests.readthedocs.io/en/master/user/advanced/#proxies)voor meer informatie.
- `storage_path`: Een pad naar de locatie van de lokale opslagmap (niet-verzonden telemetrie). Vanaf `opencensus-ext-azure` v 1.0.3 is het standaardpad de tijdelijke map van het besturings systeem `opencensus-python`  +  `your-ikey` . Vóór v 1.0.3 is het standaardpad $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Uw gegevens weer geven met query's

U kunt de telemetriegegevens weer geven die vanuit uw toepassing zijn verzonden via het tabblad **Logboeken (analyse)** .

![Scherm opname van het deel venster overzicht met de melding ' logboeken (analyse) ' geselecteerd in een rood vak](./media/opencensus-python/0010-logs-query.png)

In de lijst onder **actief**:

- Voor telemetrie die wordt verzonden met de Azure Monitor Trace-export functie worden binnenkomende aanvragen weer gegeven onder `requests` . Uitgaand of in behandeling zijnde aanvragen worden weer gegeven onder `dependencies` .
- Voor telemetrie die met de export functie voor metrische gegevens van Azure Monitor worden verzonden, worden verzonden metrische gegevens weer gegeven onder `customMetrics` .
- Voor telemetrie die wordt verzonden met de export functie van de Azure Monitor logboeken, worden logboeken weer gegeven onder `traces` . Uitzonde ringen worden weer gegeven onder `exceptions` .

Zie [Logboeken in azure monitor](../logs/data-platform-logs.md)voor meer gedetailleerde informatie over het gebruik van query's en Logboeken.

## <a name="learn-more-about-opencensus-for-python"></a>Meer informatie over opentellingen voor python

* [Opentellingen python op GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Aanpassing](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor-exporteurs op GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integratie van opentellingen](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Voorbeeld toepassingen Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende aanvragen bijhouden](./opencensus-python-dependency.md)
* [Lopende aanvragen bijhouden](./opencensus-python-request.md)
* [Toepassings overzicht](./app-map.md)
* [End-to-end prestatie bewaking](../app/tutorial-performance.md)

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](./monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](./proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen](../alerts/alerts-log.md): Stel waarschuwingen in om u te waarschuwen als een metriek een drempel waarde overschrijdt. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.


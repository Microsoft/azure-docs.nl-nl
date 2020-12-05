---
title: Inkomende aanvraag tracering in Azure-toepassing Insights met opentellingen python | Microsoft Docs
description: Bewaak aanvraag aanroepen voor uw python-apps via opentellingen python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 4b88550ad489607bb66eb737067190d45a466a43
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607072"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Inkomende aanvragen bijhouden met opentellingen python

Gegevens van binnenkomende aanvragen worden verzameld met opentellingen python en de verschillende integraties. Spoor gegevens op die worden verzonden naar uw webtoepassingen die zijn gemaakt boven op de populaire web Frameworks `django` `flask` en `pyramid` . De gegevens worden vervolgens naar Application Insights onder Azure Monitor als `requests` telemetrie verzonden.

Eerst moet u uw python-toepassing instrumenteren met de nieuwste [Opentellingen PYTHON SDK](./opencensus-python.md).

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *Black*, een term die micro soft niet meer gebruikt. Wanneer de periode van de software wordt verwijderd, worden deze uit dit artikel verwijderd.

## <a name="tracking-django-applications"></a>Django-toepassingen bijhouden

1. Down load en Installeer `opencensus-ext-django` van [PyPI](https://pypi.org/project/opencensus-ext-django/) en instrumenteer uw toepassing met de `django` middleware. Binnenkomende aanvragen die naar uw `django` toepassing worden verzonden, worden bijgehouden.

2. Vermeld `opencensus.ext.django.middleware.OpencensusMiddleware` in uw `settings.py` bestand onder `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Zorg ervoor dat AzureExporter op de juiste wijze is geconfigureerd `settings.py` `OPENCENSUS` . Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Fles toepassingen bijhouden

1. Down load en Installeer `opencensus-ext-flask` van [PyPI](https://pypi.org/project/opencensus-ext-flask/) en instrumenteer uw toepassing met de `flask` middleware. Binnenkomende aanvragen die naar uw `flask` toepassing worden verzonden, worden bijgehouden.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. U kunt uw toepassing ook configureren `flask` via `app.config` . Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramide toepassingen bijhouden

1. Down load en Installeer `opencensus-ext-django` van [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) en instrumenteer uw toepassing met de `pyramid` tween. Binnenkomende aanvragen die naar uw `pyramid` toepassing worden verzonden, worden bijgehouden.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. U kunt uw `pyramid` tween rechtstreeks in de code configureren. Voor aanvragen van url's die u niet wilt bijhouden, voegt u deze toe aan `BLACKLIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>FastAPI-toepassingen bijhouden

Opentelling heeft geen uitbrei ding voor FastAPI. Voer de volgende stappen uit om uw eigen FastAPI-middleware te schrijven:

1. De volgende afhankelijkheden zijn vereist: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Voeg [FastAPI-middleware](https://fastapi.tiangolo.com/tutorial/middleware/)toe. Zorg ervoor dat u de server voor het soort span instelt: `span.span_kind = SpanKind.SERVER` .

3. Voer de toepassing uit. Aanroepen van uw FastAPI-toepassing moeten automatisch worden bijgehouden en telemetrie moet rechtstreeks worden aangemeld bij Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Volgende stappen

* [Toepassingskaart](./app-map.md)
* [Beschikbaarheid](./monitor-web-app-availability.md)
* [Zoeken](./diagnostic-search.md)
* [Logboek query (Analytics)](../log-query/log-query-overview.md)
* [Diagnostische gegevens voor transacties](./transaction-diagnostics.md)


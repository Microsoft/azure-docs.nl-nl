---
title: Correlatie van de telemetrie van Azure-toepassing Insights | Microsoft Docs
description: Application Insights telemetrie-correlatie
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 7a206d61124b2524feae0cf155083c1939696e18
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853319"
---
# <a name="telemetry-correlation-in-application-insights"></a>Intermetrie-correlatie in Application Insights

In de wereld van micro Services moet voor elke logische bewerking werk worden uitgevoerd in verschillende onderdelen van de service. U kunt elk van deze onderdelen afzonderlijk bewaken met behulp van [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights ondersteunt gedistribueerde telemetrie-correlatie, die u gebruikt om te detecteren welk onderdeel verantwoordelijk is voor storingen of verminderde prestaties.

In dit artikel wordt het gegevens model uitgelegd dat door Application Insights wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Hierin worden technieken en protocollen voor context doorgifte beschreven. Het behandelt ook de implementatie van correlatie tactieken op verschillende talen en platforms.

## <a name="data-model-for-telemetry-correlation"></a>Gegevens model voor de correlatie van de telemetrie

Application Insights definieert een [gegevens model](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetrie-correlatie. Om telemetrie te koppelen aan een logische bewerking, heeft elk telemetrie-item een context veld met de naam `operation_Id` . Deze id wordt gedeeld door elk telemetrie-item in de gedistribueerde tracering. Zelfs als u telemetrie van één laag kwijtraakt, kunt u nog steeds telemetrie koppelen die door andere onderdelen worden gerapporteerd.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen die aanvragen verwerken door een van de onderdelen. Deze bewerkingen worden gedefinieerd op basis van [aanvraag-telemetrie](../../azure-monitor/app/data-model-request-telemetry.md). Elk aanvraag-telemetrie-item heeft een eigen `id` id waarmee het uniek en wereld wijd wordt geïdentificeerd. En alle telemetrie-items (zoals traceringen en uitzonde ringen) die zijn gekoppeld aan de aanvraag, moeten de `operation_parentId` waarde van de aanvraag instellen `id` .

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een ander onderdeel, wordt weer gegeven door middel van [afhankelijkheids-telemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md). Met de telemetrie van een afhankelijkheid wordt ook een eigen, `id` unieke id gedefinieerd. Telemetrie aanvragen, geïnitieerd door deze afhankelijkheids aanroep, gebruikt deze `id` als `operation_parentId` .

U kunt een weer gave van de gedistribueerde logische bewerking maken met behulp van `operation_Id` , `operation_parentId` en `request.id` met `dependency.id` . Deze velden definiëren ook de causality volgorde van telemetrie-aanroepen.

In een micro Services-omgeving kunnen traceringen van onderdelen naar verschillende opslag items gaan. Elk onderdeel kan een eigen instrumentatie sleutel hebben in Application Insights. Als u telemetrie voor de logische bewerking wilt ophalen, Application Insights query's gegevens uit elk opslag item op. Wanneer het aantal opslag items groot is, hebt u een hint nodig over waar u de volgende moet zien. Het Application Insights gegevens model definieert twee velden om dit probleem op te lossen: `request.source` en `dependency.target` . Het eerste veld identificeert het onderdeel dat de afhankelijkheids aanvraag heeft gestart. Het tweede veld identificeert welk onderdeel het antwoord van de afhankelijkheids aanroep retourneert.

## <a name="example"></a>Voorbeeld

We kijken naar een voorbeeld. Een toepassing met de naam aandelen prijzen toont de huidige markt prijs van een aandeel met behulp van een externe API met de naam Stock. De voorraad prijzen toepassing heeft een pagina met de naam Stock pagina die wordt geopend door de webbrowser van de client `GET /Home/Stock` . De toepassing voert een query uit op de voorraad-API met behulp van de HTTP-aanroep `GET /api/stock/value` .

U kunt de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er rekening mee dat alle telemetrie-items de hoofdmap delen `operation_Id` . Wanneer er een Ajax-aanroep van de pagina wordt gemaakt, wordt er een nieuwe unieke ID ( `qJSXU` ) toegewezen aan de telemetrie van de afhankelijkheid en wordt de id van de pagina weergave gebruikt als `operation_ParentId` . De server aanvraag gebruikt vervolgens de Ajax-ID als `operation_ParentId` .

| Item type   | naam                      | Id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Pagina weergave   | Voorraad pagina                |              | STYz               | STYz         |
| einde | /Home/Stock ophalen           | qJSXU        | STYz               | STYz         |
| schot    | Home/Stock ophalen            | KqKwlrSt9PA = | qJSXU              | STYz         |
| einde | /API/Stock/value ophalen      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Wanneer de aanroep `GET /api/stock/value` naar een externe service wordt gedaan, moet u de identiteit van die server weten zodat u het veld op de `dependency.target` juiste wijze kunt instellen. Wanneer de externe service bewaking niet ondersteunt, `target` wordt ingesteld op de hostnaam van de service (bijvoorbeeld `stock-prices-api.com` ). Maar als de service zichzelf identificeert door een vooraf gedefinieerde HTTP-header te retour neren, `target` bevat de service-identiteit waarmee Application Insights een gedistribueerde tracering kunt bouwen door het uitvoeren van een query op telemetrie van die service.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Correlatie headers met behulp van W3C tracering voor

Application Insights wordt overgezet naar [W3C-tracering-context](https://w3c.github.io/trace-context/), die het volgende definieert:

- `traceparent`: Hiermee wordt de wereld wijde unieke bewerkings-ID en de unieke id van de aanroep uitgevoerd.
- `tracestate`: Bevat systeemspecifieke tracerings context.

De nieuwste versie van de Application Insights SDK ondersteunt het Trace-Context-protocol, maar u moet er mogelijk voor kiezen. (Achterwaartse compatibiliteit met het vorige correlatie protocol dat wordt ondersteund door de Application Insights SDK wordt behouden.)

Het [correlatie-HTTP-protocol, ook wel de aanvraag-id genoemd](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), wordt afgeschaft. Dit protocol definieert twee headers:

- `Request-Id`: Hiermee wordt de wereld wijde unieke ID van de aanroep uitgevoerd.
- `Correlation-Context`: Hiermee wordt de verzameling naam/waarde-paren van de gedistribueerde tracerings eigenschappen.

Application Insights definieert ook de [uitbrei ding](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het correlatie-http-protocol. Het maakt gebruik `Request-Context` van naam/waarde-paren voor het door geven van de verzameling eigenschappen die wordt gebruikt door de directe aanroeper of de aanroepee. De Application Insights-SDK gebruikt deze header om de `dependency.target` velden en in te stellen `request.source` .

De map [W3C Trace-context](https://w3c.github.io/trace-context/) en Application Insights data models op de volgende manier:

| Application Insights                   | W3C-tracering voor                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` van `Request` en `Dependency`     | [bovenliggend element-id](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [Trace-id](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [bovenliggende-id](https://w3c.github.io/trace-context/#parent-id) van de bovenliggende duur van dit bereik. Als dit een basis periode is, moet dit veld leeg zijn.     |


Zie [Application Insights telemetrie-gegevens model](../../azure-monitor/app/data-model.md)voor meer informatie.

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Ondersteuning voor gedistribueerde tracering van W3C inschakelen voor .NET-Apps

Op W3C tracering voor gebaseerde gedistribueerde tracering is standaard ingeschakeld in alle recente Sdk's van .NET Framework/. NET core, samen met achterwaartse compatibiliteit met verouderd Request-Id protocol.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor java-apps

#### <a name="java-30-agent"></a>Java 3,0-agent

  Java 3,0-agent ondersteunt W3C en er is geen aanvullende configuratie nodig. 

#### <a name="java-sdk"></a>Java-SDK
- **Binnenkomende configuratie**

  - Voor Java EE-apps voegt u het volgende toe aan de `<TelemetryModules>` tag in ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Voor Spring-boot-apps voegt u de volgende eigenschappen toe:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Uitgaande configuratie**

  Voeg het volgende toe aan AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > De modus voor achterwaartse compatibiliteit is standaard ingeschakeld en de `enableW3CBackCompat` para meter is optioneel. Gebruik deze optie alleen als u achterwaartse compatibiliteit wilt uitschakelen.
  >
  > In het ideale geval kunt u deze uitschakelen wanneer al uw services zijn bijgewerkt naar nieuwere versies van Sdk's die ondersteuning bieden voor het W3C-protocol. Wij raden u ten zeerste aan deze nieuwere Sdk's zo snel mogelijk te verplaatsen.

> [!IMPORTANT]
> Zorg ervoor dat de configuraties voor binnenkomende en uitgaande verbindingen exact hetzelfde zijn.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor web-apps

Deze functie is in `Microsoft.ApplicationInsights.JavaScript` . Het is standaard uitgeschakeld. Als u deze wilt inschakelen, gebruikt u `distributedTracingMode` configuratie. AI_AND_W3C is voor achterwaartse compatibiliteit met verouderde services die worden uitgevoerd door Application Insights.

- **[installatie op basis van NPM](./javascript.md#npm-based-setup)**

Voeg de volgende configuratie toe:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```
  
- **[Installatie op basis van een fragment](./javascript.md#snippet-based-setup)**

Voeg de volgende configuratie toe:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Zie de documentatie van de [Java script-correlatie](./javascript.md#enable-correlation)voor een overzicht van alle configuraties die vereist zijn om correlatie in te scha kelen.

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetrie-correlatie in opentellingen python

Opentellingen python ondersteunt [W3C-tracering-context](https://w3c.github.io/trace-context/) zonder extra configuratie.

Als referentie kunt u het gegevens model opentelling [hier](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)vinden.

### <a name="incoming-request-correlation"></a>Correlatie van binnenkomende aanvragen

Met opentellingen python worden de W3C-Trace-Context headers van binnenkomende aanvragen gecorreleerd aan de-reeksen die worden gegenereerd op basis van de aanvragen zelf. Met opentellingen wordt dit automatisch gedaan met integraties voor deze populaire Web Application Frameworks: kolf, Django en piramide. U hoeft alleen de W3C-Trace-Context headers in te vullen met de [juiste indeling](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) en deze te verzenden met de aanvraag. Hier volgt een voor beeld van een kolf toepassing waarin dit wordt gedemonstreerd:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Deze code voert een voorbeeld toepassing voor een fles op uw lokale computer uit en luistert naar poort `8080` . Als u de tracerings context wilt correleren, verzendt u een aanvraag naar het eind punt. In dit voor beeld kunt u een `curl` opdracht gebruiken:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Als u de indeling van de header van de [tracerings context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)bekijkt, kunt u de volgende informatie afleiden:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Als u de aanvraag vermelding bekijkt die naar Azure Monitor is verzonden, kunt u de velden weer geven die zijn ingevuld met de traceer header-informatie. U kunt deze gegevens vinden onder Logboeken (Analytics) in de Azure Monitor Application Insights resource.

![Telemetrie aanvragen in Logboeken (analyse)](./media/opencensus-python/0011-correlation.png)

Het `id` veld heeft de indeling `<trace-id>.<span-id>` , waarbij de `trace-id` wordt opgehaald uit de trace-header die in de aanvraag is door gegeven en het `span-id` een gegenereerde 8-bytes matrix is voor deze periode.

Het `operation_ParentId` veld heeft de indeling `<trace-id>.<parent-id>` , waarbij zowel de `trace-id` als de `parent-id` worden opgehaald uit de trace-header die in de aanvraag is door gegeven.

### <a name="log-correlation"></a>Logboekcorrelatie

Met opentellingen python kunt u Logboeken correleren door een tracerings-ID, een span-ID en een steekproef vlag toe te voegen om records te registreren. U voegt deze kenmerken toe door integratie van de [logboek registratie](https://pypi.org/project/opencensus-ext-logging/)van opentellingen te installeren. De volgende kenmerken worden toegevoegd aan python- `LogRecord` objecten: `traceId` , `spanId` en `traceSampled` . Houd er rekening mee dat dit alleen van toepassing is op Logboeken die zijn gemaakt na de integratie.

Hier volgt een voor beeld van een toepassing waarin dit wordt gedemonstreerd:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Wanneer deze code wordt uitgevoerd, worden de volgende afdrukken in de-console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
U ziet dat er een `spanId` bericht is dat zich binnen de reeks bevindt. Dit is hetzelfde `spanId` als bij de reeks met de naam `hello` .

U kunt de logboek gegevens exporteren met behulp van `AzureLogHandler` . Zie [dit artikel](./opencensus-python.md#logs)voor meer informatie.

## <a name="telemetry-correlation-in-net"></a>Telemetrie-correlatie in .NET

.NET runtime ondersteunt gedistribueerd met behulp van [activiteit](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) en [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

De Application Insights .NET SDK gebruikt `DiagnosticSource` en `Activity` voor het verzamelen en correleren van telemetrie.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Telemetrie-correlatie in Java

[Java-Agent](./java-in-process-agent.md) en [Java SDK](../../azure-monitor/app/java-get-started.md) versie 2.0.0 of hoger ondersteunt automatische correlatie van telemetrie. De gegevens worden automatisch gevuld `operation_id` voor alle telemetrie (zoals traceringen, uitzonde ringen en aangepaste gebeurtenissen) die binnen het bereik van een aanvraag worden uitgegeven. Ook worden de correlatie headers (eerder beschreven) door gegeven voor service-naar-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Application Insights de Java-agent automatisch aanvragen en afhankelijkheden voor JMS, Kafka, Netty/webstroom en meer. Voor Java SDK worden alleen aanroepen via Apache httpclient maakt ondersteund voor de correlatie functie. Automatische context doorgifte via berichten technologieën (zoals Kafka, RabbitMQ en Azure Service Bus) wordt niet ondersteund in de SDK. 

> [!NOTE]
> Als u een aangepaste telemetrie wilt verzamelen, moet u de toepassing instrumenteren met de Java 2,6-SDK. 

### <a name="role-names"></a>Rolnaams

Mogelijk wilt u de manier aanpassen waarop onderdeel namen worden weer gegeven in de [toepassings toewijzing](../../azure-monitor/app/app-map.md). U kunt dit doen `cloud_RoleName` door een van de volgende acties uit te voeren:

- Stel voor Application Insights Java-Agent 3,0 de naam van de Cloud functie als volgt in:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    U kunt ook de naam van de Cloud functie instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_ROLE_NAME` .

- Met Application Insights Java SDK 2.5.0 en hoger kunt u de `cloud_RoleName` toevoegen `<RoleName>` aan het ApplicationInsights.xml-bestand:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Als u de nood opstartdiskette met de Application Insights Spring boot Starter gebruikt, hoeft u alleen maar uw aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen:

  `spring.application.name=<name-of-app>`

  De Spring boot-Starter wordt automatisch toegewezen `cloudRoleName` aan de waarde die u invoert voor de `spring.application.name` eigenschap.

## <a name="next-steps"></a>Volgende stappen

- Schrijf [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Zie [aangepaste bewerkingen bijhouden](custom-operations-tracking.md)voor geavanceerde correlatie scenario's in ASP.NET Core en ASP.net.
- Meer informatie over het [instellen van cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) voor andere sdk's.
- Alle onderdelen van uw micro service op Application Insights onboarden. Bekijk de [ondersteunde platforms](./platforms.md).
- Zie het [gegevens model](./data-model.md) voor Application Insights typen.
- Meer informatie over hoe u [telemetrie kunt uitbreiden en filteren](./api-filtering-sampling.md).
- Raadpleeg de [Application Insights-configuratie referentie](configuration-with-applicationinsights-config.md).
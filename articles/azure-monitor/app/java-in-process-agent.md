---
title: Azure Monitor Application Insights Java
description: Bewaking van toepassingsprestaties voor Java-toepassingen die in elke omgeving worden uitgevoerd zonder dat code moet worden gewijzigd. Gedistribueerde tracering en toepassingskaart.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812044"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java-toepassingsbewaking zonder code Azure Monitor Application Insights

Toepassingsbewaking zonder Java-code gaat om eenvoud: er zijn geen codewijzigingen. De Java-agent kan worden ingeschakeld via een paar configuratiewijzigingen.

 De Java-agent werkt in elke omgeving en stelt u in staat om al uw Java-toepassingen te bewaken. Met andere woorden, of u nu uw Java-apps op VM's, on-premises, in AKS, in Windows, Linux, gebruikt, de Java 3.0-agent bewaakt uw app.

Het toevoegen Application Insights Java SDK aan uw toepassing is niet langer vereist, omdat de 3.0-agent automatisch aanvragen, afhankelijkheden en logboeken zelf verzamelt.

U kunt nog steeds aangepaste telemetrie verzenden vanuit uw toepassing. De 3.0-agent houdt deze bij en correleert deze samen met alle automatisch verzamelde telemetrie.

De 3.0-agent ondersteunt Java 8 en hoger.

## <a name="quickstart"></a>Snelstart

**1. De agent downloaden**

> [!WARNING]
> **Als u een upgrade van 3.0 Preview wilt uitvoeren**
>
> Controleer alle [configuratieopties](./java-standalone-config.md) zorgvuldig, omdat de JSON-structuur volledig is gewijzigd, naast de bestandsnaam zelf, die in kleine letters is gegaan.

Download [applicationinsights-agent-3.0.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. De JVM naar de agent laten wijzen**

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` aan de JVM-args van uw toepassing

Typische JVM-args zijn `-Xmx512m` en `-XX:+UseG1GC` . Dus als u weet waar u deze toevoegt, weet u al waar u dit moet toevoegen.

Zie Tips voor het bijwerken van uw JVM-args voor meer hulp bij het configureren van de [JVM-args van uw toepassing.](./java-standalone-arguments.md)

**3. De agent naar uw Application Insights wijzen**

Als u nog geen resource Application Insights, kunt u een nieuwe maken door de stappen in de handleiding voor het maken [van resources te volgen.](./create-new-resource.md)

Wijs de agent naar Application Insights resource, door een omgevingsvariabele in te stellen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Of door een configuratiebestand met de naam te `applicationinsights.json` maken en dit in dezelfde map te plaatsen als , met de volgende `applicationinsights-agent-3.0.3.jar` inhoud:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

U vindt uw connection string in uw Application Insights resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights verbindingsreeks":::

**4. Dat is alles.**

Start nu uw toepassing en ga naar uw Application Insights resource in de Azure Portal bewakingsgegevens te bekijken.

> [!NOTE]
> Het kan enkele minuten duren voordat uw bewakingsgegevens worden weer geven in de portal.


## <a name="configuration-options"></a>Configuratie-opties

In het `applicationinsights.json` bestand kunt u bovendien het volgende configureren:

* Naam van cloudrol
* Cloudrol-exemplaar
* Steekproeven
* Metrische JMX-gegevens
* Aangepaste dimensies
* Telemetrieprocessors (preview)
* Automatisch verzamelde logboekregistratie
* Automatisch verzamelde metrische gegevens van Micrometer (inclusief Spring Boot Actuator)
* Hartslag
* HTTP-proxy
* Zelfdiagnose

Zie [Configuratieopties voor](./java-standalone-config.md) meer informatie.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatisch verzamelde aanvragen, afhankelijkheden, logboeken en metrische gegevens

### <a name="requests"></a>Aanvragen

* JMS-consumenten
* Kafka-consumenten
* Netty/WebFhost
* Servlets
* Spring-planning

### <a name="dependencies-with-distributed-trace-propagation"></a>Afhankelijkheden met gedistribueerde traceer doorgeven

* Apache HttpClient en HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* Jms
* Kafka
* Netty-client
* OkHttp

### <a name="other-dependencies"></a>Andere afhankelijkheden

* Cassandra
* JDBC
* MongoDB (async en sync)
* Redis (Sla en Jedis)

### <a name="logs"></a>Logboeken

* java.util.logging
* Log4j (inclusief MDC-eigenschappen)
* SLF4J/Logback (inclusief MDC-eigenschappen)

### <a name="metrics"></a>Metrische gegevens

* Micrometer (inclusief metrische Spring Boot Actuator)
* Metrische JMX-gegevens

### <a name="azure-sdks-preview"></a>Azure SDK's (preview)

Zie de [configuratieopties voor het](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) inschakelen van deze preview-functie en het vastleggen van de telemetrie die door deze Azure SDK's wordt uitgezonden:

* [App Configuration](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Communicatie sms](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Event Grid](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs - Azure Blob Storage Checkpoint Store](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identiteit](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault certificaten](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault - Sleutels](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault - Geheimen](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Text Analytics](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "de bovenstaande namen en koppelingen die zijn afgeschroot https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "en versiesynchronisatie worden handmatig uitgevoerd op basis van de oudste versie in maven central die is gebouwd op azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    Blijven"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + naam + '](' + koppeling + ') ' + versie"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Aangepaste telemetrie verzenden vanuit uw toepassing

Ons doel in 3.0+ is om u in staat te stellen uw aangepaste telemetrie te verzenden met behulp van standaard-API's.

We ondersteunen Micrometer, populaire frameworks voor logboekregistratie en de Application Insights Java 2.x SDK tot nu toe.
Application Insights Java 3.0 legt automatisch de telemetrie vast die via deze API's wordt verzonden en correleert deze met automatisch verzamelde telemetrie.

### <a name="supported-custom-telemetry"></a>Ondersteunde aangepaste telemetrie

De onderstaande tabel vertegenwoordigt momenteel ondersteunde aangepaste telemetrietypen die u kunt inschakelen als aanvulling op de Java 3.0-agent. Samengevat: aangepaste metrische gegevens worden ondersteund via micrometer, aangepaste uitzonderingen en traceringen kunnen worden ingeschakeld via frameworks voor logboekregistratie en elk type aangepaste telemetrie wordt ondersteund via [de Application Insights Java 2.x SDK.](#send-custom-telemetry-using-the-2x-sdk)

|                     | Micrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Aangepaste gebeurtenissen**   |            |                     |  Yes    |
| **Aangepaste metrische gegevens**  |  Ja       |                     |  Ja    |
| **Afhankelijkheden**    |            |                     |  Yes    |
| **Uitzonderingen**      |            |  Ja                |  Ja    |
| **Paginaweergaven**      |            |                     |  Yes    |
| **Aanvragen**        |            |                     |  Yes    |
| **Traceringen**          |            |  Ja                |  Ja    |

We zijn op dit moment niet van plan om een SDK met Application Insights 3.0 uit te brengen.

Application Insights Java 3.0 luistert al naar telemetrie die is verzonden naar Application Insights Java 2.x SDK. Deze functionaliteit is een belangrijk onderdeel van de upgrade voor bestaande 2.x-gebruikers en vult een belangrijke hiaat in onze ondersteuning voor aangepaste telemetrie totdat de OpenTelemetry-API beschikbaar is.

### <a name="send-custom-metrics-using-micrometer"></a>Aangepaste metrische gegevens verzenden met Behulp van Micrometer

Voeg Micrometer toe aan uw toepassing:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Gebruik het globale [micrometerregister om](https://micrometer.io/docs/concepts#_global_registry) een meter te maken:

```java
static final Counter counter = Metrics.counter("test_counter");
```

en gebruiken deze om metrische gegevens vast te registreren:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Aangepaste traceringen en uitzonderingen verzenden met behulp van uw favoriete framework voor logboekregistratie

Log4j, Logback en java.util.logging worden automatisch geïns instrumenteerd en logboekregistratie die via deze frameworks voor logboekregistratie wordt uitgevoerd, wordt automatisch verzameld als traceer- en uitzonderings-telemetrie.

Standaard wordt logboekregistratie alleen verzameld wanneer die logboekregistratie wordt uitgevoerd op infoniveau of hoger.
Zie de [configuratieopties](./java-standalone-config.md#auto-collected-logging) voor het wijzigen van dit niveau.

Als u aangepaste dimensies aan uw logboeken wilt koppelen, kunt u [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)of [Logback MDC](http://logback.qos.ch/manual/mdc.html)gebruiken. Met Application Insights Java 3.0 worden deze MDC-eigenschappen automatisch opgenomen als aangepaste dimensies voor uw traceer- en uitzonderings-telemetrie.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Aangepaste telemetrie verzenden met de 2.x SDK

Voeg toe aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste versie te gebruiken als u een `applicationinsights-core-2.6.2.jar` keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Een TelemetryClient maken:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

en deze gebruiken om aangepaste telemetrie te verzenden:

##### <a name="events"></a>Gebeurtenissen

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metrische gegevens

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Afhankelijkheden

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Logboeken

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Uitzonderingen

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Aangepaste dimensies voor aanvragen toevoegen met behulp van de 2.x SDK

> [!NOTE]
> Deze functie is alleen beschikbaar in 3.0.2 en hoger

Voeg toe aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste versie te gebruiken als u een `applicationinsights-web-2.6.2.jar` keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en voeg aangepaste dimensies toe aan uw code:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Stel de telemetrie van de user_Id met behulp van de 2.x SDK

> [!NOTE]
> Deze functie is alleen beschikbaar in 3.0.2 en hoger

Voeg toe aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste versie te gebruiken als u een `applicationinsights-web-2.6.2.jar` keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en stel de `user_Id` in uw code in:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Overschrijven van de telemetrienaam van de aanvraag met behulp van de 2.x SDK

> [!NOTE]
> Deze functie is alleen beschikbaar in 3.0.2 en hoger

Voeg toe aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste versie te gebruiken als u een `applicationinsights-web-2.6.2.jar` keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en stel de naam in uw code in:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Haal de telemetrie-id van de aanvraag en de bewerkings-id op met behulp van de 2.x SDK

> [!NOTE]
> Deze functie is alleen beschikbaar in 3.0.3 en hoger

Voeg toe aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste versie te gebruiken als u een `applicationinsights-web-2.6.2.jar` keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en haal de telemetrie-id van de aanvraag en de bewerkings-id op in uw code:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```

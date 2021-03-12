---
title: Azure Monitor Application Insights java
description: Bewaking van toepassings prestaties voor Java-toepassingen die worden uitgevoerd in een omgeving zonder dat code hoeft te worden gewijzigd. Gedistribueerde tracering en toepassings toewijzing.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b695df29b7a4704ee9e4e25e402fa0de8f2b7685
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008209"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Azure Monitor Application Insights voor de bewaking van Java-toepassingen

Bewaking van Java-toepassingen is heel eenvoudig: er zijn geen code wijzigingen, de Java-Agent kan worden ingeschakeld via slechts een paar configuratie wijzigingen.

 De Java-agent werkt in elke omgeving en biedt u de mogelijkheid om al uw Java-toepassingen te bewaken. Met andere woorden, of u nu uw java-apps uitvoert op Vm's, on-premises, in AKS, op Windows, Linux-u de naam geeft, de Java 3,0-agent bewaakt uw app.

Het is niet meer nodig om de Application Insights Java-SDK toe te voegen aan uw toepassing, omdat de 3,0-agent automatisch aanvragen verzamelt, afhankelijkheden en logboeken zelf.

U kunt nog steeds aangepaste telemetrie verzenden vanuit uw toepassing. De 3,0-agent houdt deze samen met alle automatisch verzamelde telemetrie en correleert deze.

De 3,0-agent ondersteunt Java 8 en hoger.

## <a name="quickstart"></a>Snelstart

**1. de agent downloaden**

> [!WARNING]
> **Als u een upgrade uitvoert van 3,0 Preview**
>
> Controleer alle [configuratie opties](./java-standalone-config.md) zorgvuldig, omdat de JSON-structuur volledig is gewijzigd, naast de naam van het bestand dat alle kleine letters bevat.

[Applicationinsights-agent-3.0.2. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar) downloaden

**2. Wijs de JVM naar de agent**

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` aan de JVM-argumenten van uw toepassing

Typische argumenten voor JVM zijn onder andere `-Xmx512m` en `-XX:+UseG1GC` . Als u weet waar u deze toevoegt, weet u dus al waar u dit kunt toevoegen.

Raadpleeg [Tips voor het bijwerken van uw JVM-argumenten](./java-standalone-arguments.md)voor meer informatie over het configureren van de JVM-argumenten van uw toepassing.

**3. Wijs de agent naar uw Application Insights-resource**

Als u nog geen Application Insights resource hebt, kunt u een nieuw item maken door de stappen in de [hand leiding](./create-new-resource.md)voor het maken van resources te volgen.

Wijs de agent naar uw Application Insights-bron, door een omgevings variabele in te stellen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

U kunt ook een configuratie bestand maken met de naam `applicationinsights.json` en dit in dezelfde map plaatsen als `applicationinsights-agent-3.0.2.jar` met de volgende inhoud:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

U kunt uw connection string vinden in uw Application Insights-resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindings reeks Application Insights":::

**4. dat is alles!**

Start nu uw toepassing en ga naar uw Application Insights-resource in de Azure Portal om uw bewakings gegevens weer te geven.

> [!NOTE]
> Het kan enkele minuten duren voordat uw bewakings gegevens in de portal worden weer gegeven.


## <a name="configuration-options"></a>Configuratie-opties

In het `applicationinsights.json` bestand kunt u verder configureren:

* Rolnaam van Cloud
* Cloud rolinstantie
* Steekproeven
* Metrische gegevens van JMX
* Aangepaste dimensies
* Telemetrie-processors (preview-versie)
* Automatisch verzamelde logboek registratie
* Automatisch verzamelde metrische gegevens over micrometer (inclusief lente-metrische gegevens over het starten van de klep)
* Hartslag
* HTTP-proxy
* Zelf diagnostische gegevens

Zie [configuratie opties](./java-standalone-config.md) voor volledige informatie.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatisch verzamelde aanvragen, afhankelijkheden, logboeken en metrische gegevens

### <a name="requests"></a>Aanvragen

* JMS consumenten
* Kafka consumenten
* Netty/webstroom
* Servlets
* Lente planning

### <a name="dependencies-with-distributed-trace-propagation"></a>Afhankelijkheden met gedistribueerde traceer doorgifte

* Apache httpclient maakt en HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Netty-client
* OkHttp

### <a name="other-dependencies"></a>Andere afhankelijkheden

* Cassandra
* JDBC
* MongoDB (async en sync)
* Redis (sla en jedis)

### <a name="logs"></a>Logboeken

* Java. util. Logging
* Log4j (inclusief MDC-eigenschappen)
* SLF4J/logback (inclusief MDC-eigenschappen)

### <a name="metrics"></a>Metrische gegevens

* Micrometer (met inbegrip van gegevens over de veer boot-klep)
* Metrische gegevens van JMX

## <a name="send-custom-telemetry-from-your-application"></a>Aangepaste telemetrie verzenden vanuit uw toepassing

Met ons doel in 3.0 + kunt u uw aangepaste telemetrie verzenden met behulp van standaard-Api's.

We ondersteunen micrometer, populaire logging frameworks en de Application Insights Java 2. x SDK tot nu toe.
Application Insights Java 3,0 wordt de telemetrie die via deze Api's wordt verzonden automatisch vastgelegd en correleert deze met automatisch verzamelde telemetrie.

### <a name="supported-custom-telemetry"></a>Ondersteunde aangepaste telemetrie

De volgende tabel bevat momenteel ondersteunde aangepaste typen telemetrie die u kunt inschakelen om de Java 3,0-agent aan te vullen. Om samen te vatten worden aangepaste metrische gegevens ondersteund via micrometer, aangepaste uitzonde ringen en traceringen kunnen worden ingeschakeld via logging frameworks, en elk type van de aangepaste telemetrie wordt ondersteund via de [Application Insights Java 2. x SDK](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrometer | Log4j, logback, JUL | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Aangepaste gebeurtenissen**   |            |                     |  Ja    |
| **Aangepaste metrische gegevens**  |  Ja       |                     |  Ja    |
| **Afhankelijkheden**    |            |                     |  Ja    |
| **Uitzonderingen**      |            |  Ja                |  Ja    |
| **Paginaweergaven**      |            |                     |  Ja    |
| **Aanvragen**        |            |                     |  Ja    |
| **Traceringen**          |            |  Ja                |  Ja    |

Er is op dit moment geen planning voor het vrijgeven van een SDK met Application Insights 3,0.

Application Insights Java 3,0 wordt al geluisterd naar telemetrie die wordt verzonden naar de Application Insights Java 2. x SDK. Deze functionaliteit is een belang rijk onderdeel van het upgrade verhaal voor bestaande 2. x-gebruikers, en het vult een belang rijke tussen ruimte in onze aangepaste telemetrie-ondersteuning totdat de OpenTelemetry-API GA is.

### <a name="send-custom-metrics-using-micrometer"></a>Aangepaste metrische gegevens verzenden met micrometer

Voeg micrometer toe aan uw toepassing:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Gebruik het [globale REGI ster](https://micrometer.io/docs/concepts#_global_registry) van micrometer om een meter te maken:

```java
static final Counter counter = Metrics.counter("test_counter");
```

en gebruiken voor het vastleggen van metrische gegevens:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Aangepaste traceringen en uitzonde ringen verzenden met uw favoriete Framework voor logboek registratie

Log4j, logback en Java. util. logging zijn automatisch instrumenteel en logboek registratie die via deze logboek registratie raamwerken wordt uitgevoerd, wordt automatisch verzameld als traceer-en uitzonderings-telemetrie.

Logboek registratie wordt standaard alleen verzameld wanneer de logboek registratie wordt uitgevoerd op het niveau van de INFO of hierboven.
Zie de [configuratie opties](./java-standalone-config.md#auto-collected-logging) voor het wijzigen van dit niveau.

Als u aangepaste dimensies aan uw logboeken wilt koppelen, kunt u [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)of [Logback MDC](http://logback.qos.ch/manual/mdc.html)gebruiken, en Application Insights Java 3,0 worden deze MDC-eigenschappen automatisch vastgelegd als aangepaste dimensies op uw traceer-en uitzonderings-telemetrie.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Aangepaste telemetrie verzenden met behulp van de 2. x SDK

Toevoegen `applicationinsights-core-2.6.2.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

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

en gebruiken om aangepaste telemetrie te verzenden:

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

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Aangepaste dimensies voor aanvragen toevoegen met behulp van de 2. x SDK

> [!NOTE]
> Deze functie is alleen in 3.0.2 en hoger

Toevoegen `applicationinsights-web-2.6.2.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en voeg aangepaste dimensies toe in uw code:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>De user_Id van de telemetrie aanvragen instellen met behulp van de 2. x SDK

> [!NOTE]
> Deze functie is alleen in 3.0.2 en hoger

Toevoegen `applicationinsights-web-2.6.2.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

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

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>De naam van de telemetrie van de aanvraag overschrijven met behulp van de 2. x SDK

> [!NOTE]
> Deze functie is alleen in 3.0.2 en hoger

Toevoegen `applicationinsights-web-2.6.2.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

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

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>De telemetrie-aanvraag-id en de bewerkings-id ophalen met behulp van de 2. x SDK

> [!NOTE]
> Deze functie is alleen in 3.0.3-BETA en hoger

Toevoegen `applicationinsights-web-2.6.2.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

en ontvang de telemetrie-aanvraag-id en de bewerkings-id in uw code:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```

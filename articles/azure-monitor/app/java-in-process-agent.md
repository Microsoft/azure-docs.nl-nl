---
title: Azure Monitor Application Insights java
description: Bewaking van toepassings prestaties voor Java-toepassingen die worden uitgevoerd in een omgeving zonder dat code hoeft te worden gewijzigd. Gedistribueerde tracering en toepassings toewijzing.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 36e2b419da2bccdf2f5f13227457172cf644994c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351534"
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

[Applicationinsights-agent-3.0.0. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar) downloaden

**2. Wijs de JVM naar de agent**

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` aan de JVM-argumenten van uw toepassing

Typische argumenten voor JVM zijn onder andere `-Xmx512m` en `-XX:+UseG1GC` . Als u weet waar u deze toevoegt, weet u dus al waar u dit kunt toevoegen.

Raadpleeg [Tips voor het bijwerken van uw JVM-argumenten](./java-standalone-arguments.md)voor meer informatie over het configureren van de JVM-argumenten van uw toepassing.

**3. Wijs de agent naar uw Application Insights-resource**

Als u nog geen Application Insights resource hebt, kunt u een nieuw item maken door de stappen in de [hand leiding](./create-new-resource.md)voor het maken van resources te volgen.

Wijs de agent naar uw Application Insights-bron, door een omgevings variabele in te stellen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

U kunt ook een configuratie bestand maken met de naam `applicationinsights.json` en dit in dezelfde map plaatsen als `applicationinsights-agent-3.0.0.jar` met de volgende inhoud:

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

## <a name="sending-custom-telemetry-from-your-application"></a>Aangepaste telemetrie van uw toepassing verzenden

Met ons doel in 3.0 + kunt u uw aangepaste telemetrie verzenden met behulp van standaard-Api's.

We ondersteunen micrometer, OpenTelemetry-API en de populaire logboek registratie raamwerken. Application Insights Java 3,0 wordt de telemetrie automatisch vastgelegd en correleren samen met alle automatisch verzamelde telemetrie.

### <a name="supported-custom-telemetry"></a>Ondersteunde aangepaste telemetrie

De volgende tabel bevat momenteel ondersteunde aangepaste typen telemetrie die u kunt inschakelen om de Java 3,0-agent aan te vullen. Om samen te vatten worden aangepaste metrische gegevens ondersteund via micrometer, aangepaste uitzonde ringen en traceringen kunnen worden ingeschakeld via logging frameworks, en elk type van de aangepaste telemetrie wordt ondersteund via de [Application Insights Java 2. x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

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

Application Insights Java 3,0 wordt al geluisterd naar telemetrie die wordt verzonden naar de Application Insights Java SDK 2. x. Deze functionaliteit is een belang rijk onderdeel van het upgrade verhaal voor bestaande 2. x-gebruikers, en het vult een belang rijke tussen ruimte in onze aangepaste telemetrie-ondersteuning totdat de OpenTelemetry-API GA is.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Aangepaste telemetrie verzenden met Application Insights Java SDK 2. x

Toevoegen `applicationinsights-core-2.6.0.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Een TelemetryClient maken:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

en gebruiken voor het verzenden van aangepaste telemetrie.

### <a name="events"></a>Gebeurtenissen

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metrische gegevens

U kunt metrische telemetriegegevens verzenden via [micrometer](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Afhankelijkheden

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Logboeken
U kunt aangepaste logboek-telemetrie verzenden via uw favoriete Framework voor logboek registratie.

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Uitzonderingen
U kunt aangepaste telemetrie voor uitzonde ringen verzenden via uw favoriete Framework voor logboek registratie.

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

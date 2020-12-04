---
title: Java-traceer logboeken verkennen in Azure-toepassing Insights
description: Log4J-of logback-traceringen zoeken in Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: fb91662dfcdeb404f51f91c1fef893dc72dcac73
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601047"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Java-traceer logboeken verkennen in Application Insights

> [!IMPORTANT]
> De aanbevolen benadering voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Volg de richt lijnen voor [Application Insights Java 3,0-agent](./java-in-process-agent.md).

Als u logback of Log4J (v 1.2 of v 2.0) gebruikt voor tracering, kunt u uw traceer logboeken automatisch laten verzenden naar Application Insights waar u ze kunt verkennen en doorzoeken.

> [!TIP]
> U hoeft uw Application Insights instrumentatie sleutel slechts eenmaal in te stellen voor uw toepassing. Als u een framework zoals Java lente gebruikt, hebt u de sleutel mogelijk al elders in de configuratie van uw app geregistreerd.

## <a name="using-the-application-insights-java-agent"></a>De Application Insights Java-Agent gebruiken

Standaard legt de Application Insights Java-agent automatisch de logboek registratie vast die op `WARN` niveau en hoger wordt uitgevoerd.

U kunt de drempel waarde wijzigen van de logboek registratie die wordt vastgelegd met het `AI-Agent.xml` bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

U kunt de logboek registratie van de Java Agent uitschakelen met het `AI-Agent.xml` bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>U kunt ook de onderstaande instructies volgen (in plaats van de Java-Agent).

### <a name="install-the-java-sdk"></a>De Java-SDK installeren

Volg de instructies voor het installeren [van Application INSIGHTS SDK voor Java][java], als u dat nog niet hebt gedaan.

### <a name="add-logging-libraries-to-your-project"></a>Logboek registratie bibliotheken toevoegen aan uw project
*Kies de juiste methode voor uw project.*

#### <a name="if-youre-using-maven"></a>Als u Maven gebruikt...
Als uw project al is ingesteld voor het gebruik van Maven voor Build, kunt u een van de volgende code fragmenten in uw pom.xml-bestand samen voegen.

Vernieuw vervolgens de Project afhankelijkheden om de binaire bestanden te downloaden.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Als u Gradle gebruikt...
Als uw project al is ingesteld voor het gebruik van Gradle voor Build, voegt u een van de volgende regels toe aan de `dependencies` groep in uw build. Gradle-bestand:

Vernieuw vervolgens de Project afhankelijkheden om de binaire bestanden te downloaden.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Of...
Volg de richt lijnen om Application Insights Java SDK hand matig te installeren, down load het jar (nadat u op de Maven-centrale pagina hebt geklikt op de koppeling ' jar ' in de sectie downloaden) voor de juiste appender en voeg de gedownloade toevoegder jar toe aan het project.

| Logger | Downloaden | Bibliotheek |
| --- | --- | --- |
| Logback |[Logback-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v 2.0 |[Log4J v2-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v 1.2 |[Log4J v 1.2-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Voeg de toevoegder toe aan uw Framework voor logboek registratie
Als u wilt beginnen met het ophalen van traceringen, voegt u het relevante code fragment uit in het configuratie bestand Log4J of logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

De Application Insights-toevoegers kunnen worden verwezen door een geconfigureerde logger en niet noodzakelijkerwijs door de hoofd logboek registratie (zoals weer gegeven in de bovenstaande code voorbeelden).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Verken uw traceringen in de Application Insights Portal
Nu u het project zodanig hebt geconfigureerd dat traceringen naar Application Insights worden verzonden, kunt u deze traceringen bekijken en zoeken in de Application Insights Portal, op de Blade [zoeken][diagnostic] .

Uitzonde ringen die via Logboeken worden verzonden, worden in de portal weer gegeven als een telemetrie-uitzonde ring.

![Open in de Application Insights Portal zoek opdracht](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Volgende stappen
[Diagnostische gegevens zoeken][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md


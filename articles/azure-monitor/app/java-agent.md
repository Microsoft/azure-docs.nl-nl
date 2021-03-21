---
title: Prestatie bewaking Java Web apps-Azure-toepassing Insights
description: Uitgebreide prestaties en gebruiks bewaking van uw Java-website met Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573839"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Afhankelijkheden, onderschepte uitzonde ringen en methode-uitvoerings tijden in Java-Web-apps bewaken

> [!IMPORTANT]
> De benadering die in dit document wordt beschreven, wordt niet meer aanbevolen.
>
> De aanbevolen benadering voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Volg de richt lijnen voor [Application Insights Java 3,0-agent](./java-in-process-agent.md).

Als u [uw Java-Web-app hebt geinstrumenteerd met Application INSIGHTS SDK][java], kunt u de Java-Agent gebruiken om diepere inzichten te verkrijgen, zonder dat u code hoeft te wijzigen:

* **Afhankelijkheden:** Gegevens over de aanroepen die uw toepassing maakt met andere onderdelen, waaronder:
  * **Uitgaande HTTP-aanroepen** via Apache httpclient maakt, OkHttp en `java.net.HttpURLConnection` worden vastgelegd.
  * **Redis-aanroepen** via de jedis-client worden vastgelegd.
  * **JDBC-query's** : voor MySQL en PostgreSQL, als de aanroep langer dan 10 seconden duurt, rapporteert de agent het query plan.

* **Toepassings logboeken:** Uw toepassings logboeken vastleggen en correleren met HTTP-aanvragen en andere telemetrie
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Betere bewerkings naam:** (gebruikt voor aggregatie van aanvragen in de portal)
  * **Lente** op basis van `@RequestMapping` .
  * **Jax-RS** -gebaseerd op `@Path` . 

Als u de Java-Agent wilt gebruiken, installeert u deze op uw server. Uw web-apps moeten worden beinstrumented met de [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>De Application Insights-agent voor Java installeren
1. [Down load de agent van 2. x](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2)op de computer waarop de Java-Server wordt uitgevoerd. Zorg ervoor dat de versie van de 2. x Java-agent die u gebruikt overeenkomt met de versie van de 2. x Application Insights Java-SDK die u gebruikt.
2. Bewerk het opstart script van de toepassings server en voeg het volgende JVM-argument toe:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Bijvoorbeeld in Tomcat op een Linux-computer:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Start de toepassings server opnieuw op.

## <a name="configure-the-agent"></a>De agent configureren
Maak een bestand `AI-Agent.xml` met de naam en plaats het in dezelfde map als het jar-bestand van de agent.

Stel de inhoud van het XML-bestand in. Bewerk het volgende voor beeld om de gewenste functies toe te voegen of te weglaten.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Aanvullende configuratie (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Ga als volgt te werk voor Azure-app Services:

* Selecteer Instellingen > Toepassingsinstellingen
* Voeg een nieuw sleutelwaardepaar toe bij App-instellingen:

Sleutel: `JAVA_OPTS` waarde: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

De agent moet worden verpakt als een resource in uw project, zodat deze wordt beëindigd op de D:/Home/site/wwwroot/map. U kunt controleren of uw agent zich in de juiste app service Directory bevindt door te gaan naar **ontwikkel hulpprogramma's**  >  **Geavanceerde hulpprogram ma's**  >  **console voor fout opsporing** en de inhoud van de sitemap te controleren.    

* Sla de instellingen op met Opslaan en start de app opnieuw met Opnieuw opstarten. (Deze stappen zijn alleen van toepassing op App Services die worden uitgevoerd op Windows.)

> [!NOTE]
> AI-Agent.xml en het jar-bestand van de agent moeten zich in dezelfde map bevindt. Ze worden vaak samen in de `/resources` map van het project geplaatst.  

#### <a name="enable-w3c-distributed-tracing"></a>In W3C gedistribueerde tracering inschakelen

Voeg het volgende toe aan AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Achterwaartse compatibiliteits modus is standaard ingeschakeld en de para meter enableW3CBackCompat is optioneel en moet alleen worden gebruikt als u deze wilt uitschakelen. 

In het ideale geval is dit de situatie waarin al uw services zijn bijgewerkt naar een nieuwere versie van Sdk's die W3C-protocol ondersteunen. Het wordt sterk aanbevolen om zo snel mogelijk over te stappen op een nieuwere versie van Sdk's met W3C-ondersteuning.

Zorg ervoor dat de **configuraties voor [Inkomend](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) en uitgaand verkeer (agent)** precies hetzelfde zijn.

## <a name="view-the-data"></a>De gegevens weergeven
In de Application Insights resource worden de geaggregeerde externe afhankelijkheden en de uitvoerings tijden van de methode weer gegeven [onder de tegel prestaties][metrics].

Als u wilt zoeken naar afzonderlijke exemplaren van de rapporten afhankelijkheid, uitzonde ring en methode, opent u [zoeken][diagnostic].

[Afhankelijkheids problemen vaststellen-meer informatie](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Vragen? Problemen?
* Zijn er geen gegevens? [Firewall-uitzonde ringen instellen](./ip-addresses.md)
* [Problemen met Java oplossen](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md


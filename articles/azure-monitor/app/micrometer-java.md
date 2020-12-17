---
title: Micrometer gebruiken met Azure-toepassing Insights-Java-SDK
description: Een stapsgewijze hand leiding voor het gebruik van micrometer met uw Application Insights Spring-en non-veer boot-toepassingen.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: bb5caafea944d21547a904b99f9043aef63a6ffa
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656463"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Micrometer gebruiken met Azure-toepassing Insights Java SDK (niet aanbevolen)

> [!IMPORTANT]
> De aanbevolen benadering voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Micrometer-telemetrie wordt automatisch verzameld met de Application Insights Java 3,0-agent-Volg de richt lijnen voor [Application Insights Java 3,0-agent](./java-in-process-agent.md).

> [!NOTE]
> Application Insights Java SDK biedt geen ondersteuning voor de Spring-webstroom: gebruik in plaats daarvan [Application Insights Java 3,0-agent](./java-in-process-agent.md) . 
>
> Zowel webstroom als micrometer worden ondersteund in [Application Insights Java 3,0-agent](./java-on-premises.md) waarvoor geen instrumentatie is vereist. 

Met de micrometer-toepassings bewaking worden metrische gegevens gemeten voor op JVM gebaseerde toepassings code en kunt u deze exporteren naar uw favoriete bewakings systemen. In dit artikel leert u hoe u micrometer kunt gebruiken met Application Insights voor zowel veer boot-als niet-veer boot-toepassingen.

## <a name="using-spring-boot-15x"></a>Lente boot gebruiken 1,5 x
Voeg de volgende afhankelijkheden toe aan uw pom.xml-of build. gradle-bestand: 
* [Application Insights lente-boot-starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 of hoger
* Micrometer Azure Registry 1.1.0 of hoger
* [Micrometer lente verouderde](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 of hoger (dit backports de AutoConfig-code in het lente-Framework).
* [ApplicationInsights-resource](./create-new-resource.md)

Stappen

1. Werk het pom.xml-bestand van de Spring boot-toepassing bij en voeg de volgende afhankelijkheden toe:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Werk het bestand Application. Properties of yml bij met de Application Insights instrumentatie sleutel met behulp van de volgende eigenschap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Uw toepassing bouwen en uitvoeren
2. Hierboven kunt u aan de slag met vooraf geaggregeerde metrische gegevens die automatisch worden verzameld voor Azure Monitor. Raadpleeg het [Leesmij-bestand op github](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md)voor meer informatie over het afstemmen van Application Insights Spring boot starter.

## <a name="using-spring-2x"></a>Met lente 2. x

Voeg de volgende afhankelijkheden toe aan uw pom.xml-of build. gradle-bestand:

* Application Insights veer boot-starter 2.1.2 of hoger
* Azure-veer-boot-metrische gegevens-starters 2.0.7 of hoger
* [Application Insights resource](./create-new-resource.md)

Stappen:

1. Werk het pom.xml-bestand van de Spring boot-toepassing bij en voeg de volgende afhankelijkheden toe:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Werk het bestand Application. Properties of yml bij met de Application Insights instrumentatie sleutel met behulp van de volgende eigenschap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Uw toepassing bouwen en uitvoeren
4. In het bovenstaande wordt weer gegeven met vooraf geaggregeerde metrische gegevens die automatisch worden verzameld voor Azure Monitor. Raadpleeg het [Leesmij-bestand op github](https://github.com/Microsoft/azure-spring-boot/releases/latest)voor meer informatie over het afstemmen van Application Insights Spring boot starter.

Standaard metrische gegevens:

*    Automatisch geconfigureerde metrische gegevens voor Tomcat, JVM, logback meet waarden, Log4J Metrics, metrische gegevens over tijds duur, metrische gegevens over processors, FileDescriptorMetrics.
*    Als bijvoorbeeld Netflix Hystrix aanwezig is in het pad naar de klasse, worden deze metrische gegevens ook weer gegeven. 
*    De volgende metrische gegevens kunnen beschikbaar zijn door het toevoegen van respectieve bonen. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Metrische gegevens van OkHttp3 
        - Metrische gegevens van Kafka 



De automatische metrische gegevens verzameling uitschakelen: 

- JVM metrische gegevens: 
    - Management. Metrics. Binders. JVM. Enabled = False 
- Logback metrische gegevens: 
    - Management. Metrics. Binders. logback. Enabled = False
- Metrische gegevens over tijds duur: 
    - Management. Metrics. Binders. uptime. Enabled = False 
- Metrische gegevens van processor:
    -  Management. Metrics. Binders. processor. Enabled = False 
- FileDescriptorMetrics:
    - Management. Metrics. Binders. files. Enabled = False 
- Hystrix metrische gegevens als bibliotheek op klassenpad: 
    - Management. Metrics. Binders. Hystrix. Enabled = False 
- AspectJ metrische gegevens als bibliotheek op klassenpad: 
    - lente. AOP. Enabled = False 

> [!NOTE]
> Geef de bovenstaande eigenschappen op in het bestand Application. Properties of Application. yml van de Spring boot-toepassing

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Micrometer gebruiken met niet-veer boot-webtoepassingen

Voeg de volgende afhankelijkheden toe aan uw pom.xml-of build. gradle-bestand:

* Application Insights Web auto 2.5.0 of hoger
* Micrometer Azure Registry 1.1.0 of hoger
* [Application Insights resource](./create-new-resource.md)

Stappen:

1. Voeg de volgende afhankelijkheden toe aan uw pom.xml-of build. gradle-bestand:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. `ApplicationInsights.xml`Bestand in de map Resources plaatsen:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>

       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>

    </ApplicationInsights>
    ```

3. Voor beeld van een servlet-klasse (een metrische waarde voor een timer):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {

          private static final long serialVersionUID = -4751096228274971485L;

          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {

            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");

        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }

        }

    ```

4. Voorbeeld configuratie klasse:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {

           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {

         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}

             @Override
             public boolean enabled() {
                 return false;
             }
         };

      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);

             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);

           }

           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {

           }
         }
    ```

Raadpleeg de [micrometer-documentatie](https://micrometer.io/docs/)voor meer informatie over metrische gegevens.

Andere voorbeeld code voor het maken van verschillende soorten metrische gegevens vindt u in[de officiële micrometer github opslag plaats](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Een extra metrische verzameling maken

### <a name="springbootspring"></a>SpringBoot/lente

Maak een bonen van de betreffende meet categorie. Stel dat we guava-cache gegevens nodig hebben:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Er zijn diverse metrische gegevens die niet standaard zijn ingeschakeld, maar kunnen op de bovenstaande manier worden gebonden. Raadpleeg [de officiële micrometer github opslag plaats](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )voor een volledige lijst.

### <a name="non-spring-apps"></a>Niet-lente-apps
Voeg de volgende bindings code toe aan het configuratie bestand:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Volgende stappen

* Zie de officiële [micrometer-documentatie](https://micrometer.io/docs)voor meer informatie over micrometer.
* Zie de [documentatie van de officiële lente op Azure](/java/azure/spring-framework/?view=azure-java-stable)voor meer informatie over de lente op Azure.
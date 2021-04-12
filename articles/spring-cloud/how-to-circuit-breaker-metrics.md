---
title: Metrische gegevens van de Resilience4J-circuit onderbreker met micrometer verzamelen
description: Wat is het verzamelen van metrische gegevens over de Resilience4J-circuit onderbreker met micrometer in azure lente-Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0b24e8e07b4038d6def9945b7c347bb81ae5378b
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258177"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Metrische gegevens van het Resilience4J-circuit voor de lente van de Cloud verzamelen met micrometer (preview)

In dit document wordt uitgelegd hoe u metrische gegevens van de lente Cloud Resilience4j-circuits kunt verzamelen met Application Insights Java-agent in het proces. Met deze functie kunt u de metrische gegevens van de resilience4j-circuit onderbreker bewaken van Application Insights met micrometer.

We gebruiken de [demonstratie-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) ---circuit-en-instructie om te laten zien hoe het werkt.

## <a name="prerequisites"></a>Vereisten

* Schakel Java In-Process agent in via de [java In-Process-agent voor Application Insights Guide](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Schakel dimensie verzameling voor resilience4j-metrische gegevens in de [hand leiding Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Installeer Git, maven en Java, als deze nog niet worden gebruikt door de ontwikkel computer.

## <a name="build-and-deploy-apps"></a>Apps bouwen en implementeren

De volgende procedure bouwt en implementeert apps.

1. De demo opslagplaats klonen en bouwen.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Toepassingen maken met eind punten

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Toepassingen implementeren.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * De vereiste afhankelijkheid voor Resilience4j toevoegen:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * De code van de klant moet gebruikmaken van de API van `CircuitBreakerFactory` , die wordt geïmplementeerd als `bean` automatisch gemaakt wanneer u een lente-starter van een veer Cloud circuit opneemt. Zie voor meer informatie de [lente Cloud circuit onderbreker](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * De volgende twee afhankelijkheden hebben een conflict met bovenstaande resilient4j-pakketten.  Zorg ervoor dat de klant deze niet bevat.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Ga naar de URL die wordt verschaft door gateway toepassingen en open het eind punt via de volgende [demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) :
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Resilence4j metrische gegevens uit Portal zoeken

1. Selecteer de Blade **Application Insights** in azure lente Cloud Portal en klik op **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Selecteer **metrische gegevens** op de pagina **Application Insights** .  Selecteer **Azure. applicationinsights** uit de **naam ruimte Metrics**.  Selecteer ook **resilience4j_circuitbreaker_buffered_calls** metrische gegevens met **gemiddelde**.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Selecteer **resilience4j_circuitbreaker_calls** metrische gegevens en **gemiddeld**.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Selecteer **resilience4j_circuitbreaker_calls**  metrische gegevens en **gemiddeld**.  Klik op **filter toevoegen** en selecteer naam als **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Selecteer **resilience4j_circuitbreaker_calls**  metrische gegevens en **gemiddeld**.  Klik vervolgens op **splitsing Toep assen** en selecteer **type**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Selecteer **resilience4j_circuitbreaker_calls**,**resilience4j_circuitbreaker_buffered_calls** en **resilience4j_circuitbreaker_slow_calls** metrische gegevens met **gemiddelde**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Zie ook

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Gedistribueerde tracering](spring-cloud-howto-distributed-tracing.md)
* [Dash board voor circuit onderbreker](spring-cloud-tutorial-circuit-breaker.md)

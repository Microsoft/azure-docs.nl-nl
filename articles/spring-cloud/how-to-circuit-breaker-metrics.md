---
title: Metrische Spring Cloud resilience4J-circuit breaker verzamelen met Micrometer
description: Meer informatie over het Spring Cloud Resilience4J Circuit Breaker met Micrometer in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6e22966aab2d424dbe2944ddca24d3200f97bffa
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886408"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Metrische Spring Cloud resilience4J-circuit breaker verzamelen met Micrometer (preview)

In dit document wordt uitgelegd hoe u Spring Cloud Resilience4j Circuit Breaker Metrics verzamelt met Application Insights in-process-agent van Java. Met deze functie kunt u metrische gegevens van de tolerantie4j-circuit breaker bewaken vanuit Application Insights micrometer.

We gebruiken de [spring-cloud-circuit-breaker-demo om](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) te laten zien hoe het werkt.

## <a name="prerequisites"></a>Vereisten

* Schakel Java In-Process-agent in vanuit de [Java In-Process Agent for Application Insights-handleiding](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Schakel dimensieverzameling in voor metrische tolerantie4j-gegevens uit Application Insights [handleiding](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Installeer git, Maven en Java, als dit nog niet wordt gebruikt door de ontwikkelcomputer.

## <a name="build-and-deploy-apps"></a>Apps bouwen en implementeren

Met de volgende procedure worden apps gebouwd en geïmplementeerd.

1. Kloon en bouw de demo-opslagplaats.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Toepassingen met eindpunten maken

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
> * Neem de vereiste afhankelijkheid voor Resilience4j op:
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
> * De klantcode moet gebruikmaken van de API van , die automatisch wordt geïmplementeerd wanneer u een Spring Cloud `CircuitBreakerFactory` `bean` circuitonderbreker-starter opsluit. Zie circuit [breaker Spring Cloud voor meer informatie.](https://spring.io/projects/spring-cloud-circuitbreaker#overview)
>
> * De volgende twee afhankelijkheden hebben conflicten met resilient4j-pakketten hierboven.  Zorg ervoor dat de klant deze niet op neemt.
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
> Navigeer als volgt naar de URL van gatewaytoepassingen en open het eindpunt vanuit [spring-cloud-circuit-breaker-demo:](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo)
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Metrische gegevens van Resilence4j zoeken vanuit de portal

1. Selecteer de **Application Insights** blade in Azure Spring Cloud portal en klik op **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Selecteer **Metrische** gegevens op **Application Insights** pagina.  Selecteer **azure.applicationinsights in** **Metrics Namespace**.  Selecteer ook **resilience4j_circuitbreaker_buffered_calls** met **Gemiddelde.**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Selecteer **resilience4j_circuitbreaker_calls** en **Gemiddelde.**

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Selecteer **resilience4j_circuitbreaker_calls** en **Gemiddelde.**  Klik **op Filter toevoegen** en selecteer vervolgens de naam **createNewAccount.**

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Selecteer **resilience4j_circuitbreaker_calls** en **Gemiddelde.**  Klik vervolgens **op Splitsen toepassen** en selecteer **soort**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Selecteer **resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls** en **resilience4j_circuitbreaker_slow_calls** metrische gegevens met **Gemiddelde**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Zie ook

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Gedistribueerde tracering](spring-cloud-howto-distributed-tracing.md)
* [Dashboard circuit breaker](spring-cloud-tutorial-circuit-breaker.md)

---
title: Zelf studie-een circuit onderbreker-dash board gebruiken met Azure veer Cloud
description: Informatie over het gebruik van Circuit Breaker Dashboard met Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9fbd137f8fa36a7b0526b25d664fceac795ecd81
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879151"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Zelfstudie: Circuit Breaker Dashboard gebruiken met Azure Spring Cloud

**Dit artikel is van toepassing op:** ✔️ Java

Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) wordt veel gebruikt voor de aggregatie van meerdere metrische gegevensstromen van [Hystrix](https://github.com/Netflix/Hystrix) zodat meerdere stromen kunnen worden gemonitord in een enkele weergave met het Hystrix-dashboard. In deze zelfstudie leert u hoe u deze kunt gebruiken in Azure Spring Cloud.
> [!NOTE]
> Netflix Hystrix wordt gebruikt in vele Spring Cloud-apps maar wordt niet langer actief ontwikkeld. Als u een nieuw project ontwikkelt, gebruikt u in plaats ervan Spring Cloud Circuit Breaker-implementaties zoals [resilience4j](https://github.com/resilience4j/resilience4j). Het nieuwe Spring Cloud Circuit Breaker-framework werkt anders dan Turbine en voegt alle implementaties van de metrische gegevenspijplijn samen in Micrometer. We werken nog aan ondersteuning van Micrometer in Azure Spring Cloud, dus daarom wordt het nog niet behandeld in deze zelfstudie.

## <a name="prepare-your-sample-applications"></a>Uw voorbeeldtoepassingen voorbereiden
Het voorbeeld is gevorkt van deze [opslagplaats](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Kloon de voorbeeldopslagplaats naar uw ontwikkelomgeving:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Bouw de 3 toepassingen die in deze zelfstudie worden gebruikt:
* gebruikersservice: Een eenvoudige REST-service met één eindpunt van /personalized/{id}
* aanbevelingsservice: Een eenvoudige REST-service met één eindpunt van /recommendations, die wordt aangeroepen door de gebruikersservice.
* hystrix-turbine: Een Hystrix-dashboardservice voor het weergeven van Hystrix-stromen en een Turbine-service die metrische gegevensstromen van Hystrix samenvoegt vanuit andere services.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Richt uw exemplaar van Azure Spring Cloud in
Volg de procedure [Een service-exemplaar inrichten op de Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Implementeer uw toepassingen naar Azure Spring Cloud
Deze apps maken geen gebruik van **Config Server**, dus het is niet nodig om **Config Server** in te stellen voor Azure Spring Cloud.  Maak en implementeer als volgt:
```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Uw apps verifiëren
Nadat alle apps zijn uitgevoerd en detecteerbaar zijn, gaat u naar de `user-service` met het pad https://<username>-user-service.azuremicroservices.io/personalized/1 in uw browser. Als de gebruikersservice toegang heeft tot `recommendation-service`, moet u de volgende uitvoer zien. Vernieuw de webpagina een paar keer als het niet werkt.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Open uw Hystrix-dashboard en metrische gegevensstroom
Verifieer met openbare eindpunten of privé-testeindpunten.

### <a name="using-public-endpoints"></a>Openbare eindpunten gebruiken
Ga naar hystrix-turbine met het pad `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` in uw browser.  In de volgende afbeelding ziet u de Hystrix-dashboard die wordt uitgevoerd in deze app.

![Hystrix-dashboard](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Kopieer de URL van de Turbine-gegevensstroom `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` naar het tekstvak en klik op **Gegevensstroom monitoren**.  Hiermee wordt het dashboard weergegeven. Als er niets wordt weergegeven in de viewer, gebruikt u de `user-service`-eindpunten om gegevensstromen te genereren.

![Hystrix-gegevensstroom](media/spring-cloud-circuit-breaker/hystrix-stream.png) U kunt nu experimenteren met het Circuit Breaker-dashboard.
> [!NOTE] 
> In productie mogen het dashboard en de gegevensstroom van Hystrix niet bereikbaar zijn via internet.

### <a name="using-private-test-endpoints"></a>Privé-testeindpunten gebruiken
Hystrix-gegevensstromen zijn ook toegankelijk vanaf `test-endpoint`. Omdat het een backend-service is, hebben we geen openbaar eindpunt toegewezen aan `recommendation-service`, maar we kunnen de metrische gegevens wel weergeven met een testeindpunt op `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Hystrix-testeindpuntstroom](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Als web-app moet Hystrix-dashboard werken op `test-endpoint`. Als deze niet goed werkt, kunnen er twee oorzaken zijn. Ten eerste kan het gebruik van `test-endpoint` de basis-URL van `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` hebben gewijzigd, en ten tweede kan de web-app het absolute pad van de statische resource gebruiken. Om het te laten werken op `test-endpoint` moet u mogelijk de <base> handmatig bewerken in de front-endbestanden.

## <a name="next-steps"></a>Volgende stappen
* [Een service-exemplaar inrichten op de Azure CLI](spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Een Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud](how-to-prepare-app-deployment.md)


---
title: 'Procedure: Uw Spring Cloud-toepassing starten vanuit broncode'
description: In deze quickstart leert u hoe u uw Azure Spring Cloud-toepassing rechtstreeks vanuit uw broncode kunt starten
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210322"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Uw Spring Cloud-toepassing starten vanuit broncode

**Dit artikel is van toepassing op:** ✔️ Java

Met Azure Spring Cloud kunnen op Spring Cloud gebaseerde microservicetoepassingen worden uitgevoerd in Azure.

U kunt toepassingen rechtstreeks starten vanuit Java-broncode of vanuit een vooraf gebouwde JAR. In dit artikel worden de implementatieprocedures beschreven.

In deze quickstart wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratieserver instellen voor een exemplaar
> * Lokaal een microservicetoepassing bouwen
> * Alle microservices implementeren
> * Een openbaar eindpunt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten
Voordat u begint, moet u ervoor zorgen dat uw Azure-abonnement over de vereiste afhankelijkheden beschikt:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [Azure CLI installeren](/cli/azure/install-azure-cli)
5. [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Deze bevat vooraf geïnstalleerde algemene Azure-hulpprogramma's, met inbegrip van de nieuwste versies van Git, JDK, Maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanuit shell.azure.com.  Meer informatie over Azure Cloud Shell vindt u door [onze documentatie te lezen](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure Spring Cloud-extensie voor de Azure CLI met de volgende opdracht

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Een service-exemplaar inrichten met behulp van de Azure CLI

Meld u aan bij de Azure CLI en kies uw actieve abonnement. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Maak een resourcegroep die uw Azure Spring Cloud-service bevat. Hier vindt u meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten. Bedenk een naam voor uw Azure Spring Cloud-service. De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten. Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

De implementatie van het service-exemplaar duurt ongeveer vijf minuten.

Stel de standaardnaam van de resourcegroep en van de Azure Spring Cloud-instantie in met behulp van de volgende opdrachten:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>De Azure Spring Cloud-toepassing maken

Met de volgende opdracht maakt u een Azure Spring Cloud-toepassing in uw abonnement.  Hiermee maakt u een lege service waarnaar we onze toepassing kunnen uploaden.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Uw Spring Cloud-toepassing implementeren

U kunt uw toepassing implementeren vanuit een vooraf gebouwde JAR of vanuit een Gradle- of Maven-opslagplaats.  Hieronder vindt u de instructies voor beide gevallen.

### <a name="deploy-a-built-jar"></a>Implementeren vanuit een ingebouwde JAR

Als u wilt implementeren vanuit een JAR die op uw lokale computer is gebouwd, moet u ervoor zorgen dat uw build een [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)produceert.

De fat-JAR implementeren in een actieve implementatie

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

De fat-JAR implementeren in een specifieke implementatie

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Implementeren vanuit broncode

Azure Spring Cloud maakt gebruik van [kpack](https://github.com/pivotal/kpack) om uw project te bouwen.  U kunt Azure CLI gebruiken om uw broncode te uploaden, uw project te bouwen met kpack en het te implementeren in de doeltoepassing.

> [!WARNING]
> Het project moet slechts één JAR-bestand produceren met een `main-class`-vermelding in de `MANIFEST.MF` in `target` (voor Maven-implementaties) of `build/libs` (voor Gradle-implementaties).  Meerdere JAR-bestanden met `main-class`-vermeldingen zullen ervoor zorgen dat de implementatie mislukt.

Voor Maven-/Gradle-projecten met één module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Voor Maven-/Gradle-projecten met meerdere modules herhaalt u voor elke module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Implementatielogboeken weergeven

Bekijk de kpack-buildlogboeken met behulp van de volgende opdracht:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> In de kpack-logboeken wordt alleen de meest recente implementatie weergegeven als die implementatie is gebouwd op basis van de bron met behulp van kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan de gateway

1. Open de pagina **Toepassingsdashboard**.
2. Selecteer de toepassing `gateway` om de pagina **Toepassingsdetails** weer te geven.
3. Selecteer **Eindpunt toewijzen** om een openbaar eindpunt toe te wijzen aan de gateway. Dit kan een paar minuten duren. 
4. Voer het toegewezen openbare IP in uw browser in om de actieve toepassing weer te geven.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratieserver instellen voor een exemplaar
> * Lokaal een microservicetoepassing bouwen
> * Alle microservices implementeren
> * Omgevingsvariabelen bewerken voor toepassingen
> * Een openbaar IP voor uw toepassingsgateway toewijzen

> [!div class="nextstepaction"]
> [Logboeken, metrische gegevens en tracing van Spring Cloud](spring-cloud-quickstart-logs-metrics-tracing.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
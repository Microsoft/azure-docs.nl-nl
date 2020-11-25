---
title: 'Quickstart: Azure Spring Cloud-configuratieserver instellen'
description: Hierin wordt het instellen van de Azure Spring Cloud-configuratieserver voor app-implementatie beschreven.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 0802f09cfb03f837fb7080620da776e79b37c9ed
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845081"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Quickstart: Azure Spring Cloud-configuratieserver instellen

Azure Spring Cloud-configuratieserver is een gecentraliseerde configuratieservice voor gedistribueerde systemen. Hierin wordt een pluggable opslagplaatslaag gebruikt die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion. In deze quickstart stelt u de configuratieserver in om gegevens op te halen uit een Git-opslagplaats.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Vereisten

* Voltooi de vorige quickstart in deze serie: [Azure Spring Cloud-service inrichten](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedures voor Azure Spring Cloud-configuratieserver

Stel uw configuratieserver in met de locatie van de Git-opslagplaats voor het project door de volgende opdracht uit te voeren. Vervang `<service instance name>` door de naam van de service die u eerder hebt gemaakt. De standaardwaarde voor de naam van het service-exemplaar die u in de voorgaande quickstart hebt ingesteld, werkt niet met deze opdracht.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Deze opdracht vertelt de configuratieserver dat deze de configuratiegegevens moet vinden in de map [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) in de opslagplaats van de voorbeeld-app. Omdat de naam van de app die de configuratiegegevens ontvangt `planet-weather-provider` is, wordt het bestand [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml) gebruikt.

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud-configuratieserver is een gecentraliseerde configuratieservice voor gedistribueerde systemen. Hierin wordt een pluggable opslagplaatslaag gebruikt die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion.  Stel de configuratieserver in om microservice-apps te implementeren in Azure Spring Cloud.

## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedures voor Azure Spring Cloud-configuratieserver

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Met de volgende procedure stelt u de configuratieserver in met behulp van de Azure-portal om het [Piggymetrics-voorbeeld](spring-cloud-quickstart-sample-app-introduction.md) te implementeren.

1. Ga naar de pagina **Overzicht** van de service en selecteer **Config Server**.

2. Stel in de sectie **Standaardopslagplaats** **URI** in op https://github.com/Azure-Samples/piggymetrics-config.

3. Klik op **Valideren**.

    ![Ga naar de configuratieserver](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Wanneer de validatie is voltooid, klikt u op **Toepassen** om de wijzigingen op te slaan.

    ![Configuratieserver valideren](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. Het bijwerken van de configuratie kan enkele minuten duren.
 
    ![Configuratieserver bijwerken](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. U ontvangt een melding wanneer de configuratie is voltooid.

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

In de volgende procedure wordt de Azure CLI gebruikt om de configuratieserver in te stellen om het [Piggymetrics-voorbeeld](spring-cloud-quickstart-sample-app-introduction.md) te implementeren.

Stel uw configuratieserver in met de locatie van de Git-opslagplaats voor het project:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Problemen oplossen met de Azure Spring Cloud-configuratieserver

In de volgende procedure wordt uitgelegd hoe u problemen met de instellingen van de configuratieserver oplost.

1. Ga in Azure Portal naar de servicepagina **Overzicht** en selecteer **Logboeken**. 
1. Selecteer **Query's** en **De toepassingslogboeken weergeven die de termen 'fout' of 'uitzondering' bevatten**. 
1. Klik op **Run**. 
1. Als u in logboeken de fout **java.lang.illegalStateException** vindt, geeft dit aan dat de Spring Cloud-service geen eigenschappen van de configuratieserver kan vinden.

    [ ![Query voor ASC-Portal uitvoeren](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Ga naar de servicepagina **Overzicht**.
1. Selecteer **Problemen vaststellen en oplossen**. 
1. Selecteer de **Config Server**-detector.

    [ ![Problemen vaststellen met ASC-Portal](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Klik op **Config Server-statuscontrole**.

    [ ![Genie ASC-portal](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Klik op **Config Server-status** om meer details van de detector te bekijken.

    [ ![Integriteitsstatus van ASC-Portal](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstarts hebt u Azure-resources gemaakt waarmee de kosten blijven toenemen als de resources in uw abonnement blijven. Zie [Resources opschonen](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources) als u niet wilt doorgaan met de volgende quickstart. Ga anders verder met de volgende quickstart:

> [!div class="nextstepaction"]
> [Apps bouwen en implementeren](spring-cloud-quickstart-deploy-apps.md)

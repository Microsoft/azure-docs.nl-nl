---
title: 'Quickstart: apps bouwen en implementeren in Azure Spring Cloud'
description: Beschrijft de implementatie van apps in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 727354593488cd5af77408eaa7c8d9e747a0b071
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879292"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Quickstart: Apps bouwen en implementeren in Azure Spring Cloud

::: zone pivot="programming-language-csharp"
In deze quickstart bouwt en implementeert u microservicetoepassingen in Azure Spring Cloud met behulp van de Azure CLI.

## <a name="prerequisites"></a>Vereisten

* Voltooi de vorige quickstarts in deze reeks:

  * [Azure Spring Cloud-service inrichten](spring-cloud-quickstart-provision-service-instance.md).
  * [Azure Spring Cloud-configuratieserver instellen](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>De voorbeeld-app downloaden

Als u tot nu toe Azure Cloud Shell hebt gebruikt, schakelt u over naar een lokale opdrachtprompt om de volgende stappen uit te voeren.

1. Maak een nieuwe map en kloon de opslagplaats van de voorbeeld-app.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Navigeer naar de map van de opslagplaats.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>PlanetWeatherProvider implementeren

1. Maak een app voor het PlanetWeatherProvider-project in uw Azure Spring Cloud-exemplaar.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Als u automatische serviceregistratie wilt inschakelen, hebt u de app dezelfde naam gegeven als de waarde van `spring.application.name` in het bestand *appsettings.json* van het project:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Het uitvoeren van deze opdracht kan enkele minuten duren.

1. Wijzig de map in de projectmap van `PlanetWeatherProvider`.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Maak de binaire bestanden en het *ZIP-bestand* dat moet worden geïmplementeerd.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > Het projectbestand bevat de volgende XML om de binaire bestanden in een *ZIP-bestand* te verpakken nadat ze naar de map *./publish* zijn geschreven:
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Implementeren in Azure.

   Zorg ervoor dat de opdrachtprompt zich in de projectmap bevindt voordat u de volgende opdracht uitvoert.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   Met de optie `--main-entry` geeft u het relatieve pad op vanaf de hoofdmap van het *. ZIP-bestand* naar het *DLL-bestand* met het beginpunt van de toepassing. Nadat het *ZIP-bestand* is geüpload, worden alle bestanden en mappen geëxtraheerd en wordt geprobeerd het beginpunt in het opgegeven *DLL-bestand* uit te voeren.

   Het uitvoeren van deze opdracht kan enkele minuten duren.

## <a name="deploy-solarsystemweather"></a>SolarSystemWeather implementeren

1. Maak nog een app in uw Azure Spring Cloud-exemplaar, ditmaal voor het project SolarSystemWeather:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` is de naam die is opgegeven in het bestand *appsettings.json* van het project `SolarSystemWeather`.

   Het uitvoeren van deze opdracht kan enkele minuten duren.

1. Wijzig de map in de map van het project `SolarSystemWeather`.

   ```console
   cd ../solar-system-weather
   ```

1. Maak de binaire bestanden en het *ZIP-bestand* dat u wilt implementeren.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Implementeren in Azure.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Het uitvoeren van deze opdracht kan enkele minuten duren.

## <a name="assign-public-endpoint"></a>Openbaar eindpunt toewijzen

Als u de toepassing wilt testen, verzendt u een HTTP GET-aanvraag naar de toepassing `solar-system-weather` vanuit een browser.  Hiervoor hebt u een openbaar eindpunt voor de aanvraag nodig.

1. Voer de volgende opdracht uit om het eindpunt toe te wijzen.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --assign-endpoint true
   ```

1. Voer de volgende opdracht uit om de URL van het eindpunt op te halen.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>De toepassing testen

Verzend een GET-aanvraag naar de app `solar-system-weather`. Ga in een browser naar de openbare URL waaraan `/weatherforecast` is toegevoegd. Bijvoorbeeld:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

De uitvoer is JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

In dit antwoord ziet u dat beide microservice-apps werken. De app `SolarSystemWeather` retourneert gegevens die zijn opgehaald uit de app `PlanetWeatherProvider`.
::: zone-end

::: zone pivot="programming-language-java"
In dit document wordt uitgelegd hoe u microservicetoepassingen bouwt en implementeert in Azure Spring Cloud met behulp van:
* Azure CLI
* Maven-invoegtoepassing
* Intellij

Voordat u implementeert met behulp van Azure CLI of Maven, voltooit u eerst de voorbeelden van [inrichting van Azure Spring Cloud-exemplaar](spring-cloud-quickstart-provision-service-instance.md) en [het instellen van de configuratieserver](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](/java/azure/jdk/)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](/cli/azure/install-azure-cli) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Implementatieprocedures

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Lokaal microservicetoepassingen bouwen

1. Kloon de opslagplaats van de voorbeeld-app in uw Azure Cloud-account.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Wijzig de map en bouw het project.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Het compileren van het project duurt ongeveer vijf minuten. Als u klaar bent, moet u voor elke service afzonderlijke JAR-bestanden in de corresponderende mappen hebben.

### <a name="create-and-deploy-the-apps"></a>De apps maken en implementeren

1. Stel de standaardnaam van de resourcegroep en van het cluster in met behulp van de volgende opdrachten:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Maak Azure Spring Cloud-microservices met behulp van de JAR-bestanden die in de vorige stap zijn gemaakt. U maakt drie apps: **gateway**, **auth-service** en **account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Toepassingen die in de vorige stap zijn gemaakt, moeten in Azure worden geïmplementeerd. Gebruik de volgende opdrachten om alle drie de toepassingen te implementeren:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan de gateway

We hebben een manier nodig om via een webbrowser toegang te krijgen tot de toepassing. Voor onze gatewaytoepassing is een openbaar eindpunt vereist.

1. Wijs het eindpunt toe met behulp van de volgende opdracht:

    ```azurecli
    az spring-cloud app update -n gateway --assign-endpoint true
    ```

2. Voer een query uit op de **gateway**-toepassing om het openbare IP-adres ervan op te halen, zodat u kunt controleren of de toepassing wordt uitgevoerd:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>De voorbeeldtoepassingsopslagplaats klonen en bouwen

1. Voer de volgende opdracht uit om de Git-opslagplaats te klonen:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Configuraties genereren en implementeren in de Azure Spring Cloud

1. Genereer configuraties door de volgende opdracht uit te voeren in de hoofdmap van Piggy Metrics die de bovenliggende POM bevat. Als u zich al hebt aangemeld bij Azure CLI, worden de referenties automatisch opgehaald met de opdracht. Als u dit niet doet, wordt u aangemeld via promptinstructies. Zie onze [wiki-pagina](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) voor meer informatie.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    U wordt gevraagd om het volgende te selecteren:
    * **Modules:** Selecteer `gateway`, `auth-service` en `account-service`.
    * **Abonnement:** Dit is het abonnement dat wordt gebruikt om een Azure Spring Cloud-exemplaar te maken.
    * **Service-exemplaar:** Dit is de naam van het Azure Spring Cloud-exemplaar.
    * **Openbaar eindpunt:** Voer in de lijst met opgegeven projecten het getal in dat overeenkomt met `gateway`.  Dit verleent openbare toegang.

1. De POM bevat nu de afhankelijkheden en configuraties van de invoegtoepassing. Implementeer de apps met behulp van de volgende opdracht. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Voorbeeldproject importeren in IntelliJ

1. Download de bronopslagplaats voor deze zelfstudie en pak deze uit, of kloon deze met Git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Open het dialoogvenster **Welkom** van IntelliJ en selecteer **Project importeren** om de wizard Importeren te openen.

1. Selecteer de map `piggymetric`.

    ![Project importeren](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Gateway-app implementeren in Azure Spring Cloud
Als u wilt implementeren in Azure, moet u zich met uw Azure-account aanmelden bij de Azure-toolkit voor IntelliJ, en uw abonnement kiezen. Zie [Installatie en aanmelding](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) voor meer informatie over aanmelden.

1. Klik met de rechtermuisknop op uw project in de projectverkenner van IntelliJ en selecteer **Azure** -> **Implementeren in Azure Spring Cloud**.

    ![Implementeren in Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. Voeg in het veld **Naam** het achtervoegsel *:gateway* toe aan de bestaande **Naam**.
1. Selecteer in het tekstvak **Artefact** de optie *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Controleer uw abonnement in het tekstvak **Abonnement**.
1. Selecteer in het tekstvak **Spring Cloud** het exemplaar van Azure Spring Cloud dat u hebt gemaakt in [Azure Spring Cloud-exemplaar inrichten](./spring-cloud-quickstart-provision-service-instance.md).
1. Stel **Openbaar eindpunt** in op *Inschakelen*.
1. Selecteer in het tekstvak **App:** de optie **App maken...** .
1. Voer *gateway* in en klik vervolgens op **OK**.

    ![Implementeren naar Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Dubbelklik in de sectie **Vóór het starten** van het dialoogvenster op *Maven-doel uitvoeren*.
1. Ga in het tekstvak **Werkmap** naar de map *piggymetrics/gateway*.
1. Voer *package-DskipTests* in het tekstvak **Opdrachtregel** in. Klik op **OK**.
1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. De invoegtoepassing voert de opdracht `mvn package` uit in de app `gateway` en implementeert het JAR-bestand dat is gegenereerd met de opdracht `package`.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Apps voor auth-service en account-service implementeren in Azure Spring Cloud
U kunt de bovenstaande stappen herhalen om de apps `auth-service` en `account-service` te implementeren in Azure Spring Cloud:

1. Wijzig **Naam** en **Artefact** om de app `auth-service` te identificeren.
1. Selecteer in het tekstvak **App:** de optie **App maken...** om de apps `auth-service` te maken.
1. Controleer of de optie **Openbaar eindpunt** is ingesteld op *Uitgeschakeld*.
1. Schakel in de sectie **Vóór het starten** van het dialoogvenster de **Werkmap** naar de map *piggymetrics/auth-service*.
1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. 
1. Herhaal deze procedures om `account-service` te configureren en te implementeren.
---

Ga naar de URL die in de uitvoer van de vorige stappen is opgegeven, voor toegang tot de Piggy Metrics-toepassing. Bijvoorbeeld: `https://<service instance name>-gateway.azuremicroservices.io`

![Toegang tot Piggy Metrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

U kunt ook door Azure Portal bladeren om de URL te vinden. 
1. Navigeer naar de service
2. Selecteer **Apps**
3. Selecteer **gateway**

    ![Navigeren in de app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Zoek de URL op de pagina **Gateway | Overzicht**

    ![Navigeren in de app, twee](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Volgende stappen

In deze quickstarts hebt u Azure-resources gemaakt waarmee de kosten blijven toenemen als de resources in uw abonnement blijven. Zie [Resources opschonen](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources) als u niet wilt doorgaan met de volgende quickstart. Ga anders verder met de volgende quickstart:

> [!div class="nextstepaction"]
> [Logboeken, metrische gegevens en tracering](spring-cloud-quickstart-logs-metrics-tracing.md)

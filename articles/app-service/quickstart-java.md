---
title: 'Quickstart: Een Java web-app maken in Azure App Service'
description: Implementeer in enkele minuten uw eerste Java Hallo wereld in Azure App Service. De Maven-invoegtoepassing voor Azure Web Apps maakt het eenvoudig om Java-apps te implementeren.
keywords: azure, app service, web app, windows, linux, java, maven, quickstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701719"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Quickstart: Een Java web-app maken in Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.  In deze quickstart ziet u hoe u [Azure CLI](/cli/azure/get-started-with-azure-cli) met de [Maven-invoegtoepassing voor Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) wordt gebruikt om een JAR-bestand of WAR-bestand te implementeren. Gebruik de tabbladen om te schakelen tussen de instructies voor Java SE en Tomcat.


> [!NOTE]
> Dit kan ook worden gedaan met populaire IDE's zoals IntelliJ en Eclipse. Bekijk onze vergelijkbare documenten via [Quickstart: Azure-toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) of [Quickstart: Azure-toolkit voor Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![Voorbeeld-app uitgevoerd in Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Een Java-app maken

# <a name="java-se"></a>[Java SE](#tab/javase)

Kloon het voorbeeldproject [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Ga naar de map met het voltooide project.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Voer de volgende Maven opdracht uit in de Cloud Shell-prompt om een ​​nieuwe app met de naam `helloworld` te maken:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Wijzig vervolgens uw werkmap in de projectmap:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>De Maven-invoegtoepassing configureren

Bij het implementatieproces naar Azure App Service worden uw Azure-referenties automatisch opgehaald uit Azure CLI. Als Azure CLI niet lokaal is geïnstalleerd, wordt de verificatie met de Maven-invoegtoepassing uitgevoerd via OAuth of apparaataanmelding. Zie [verificatie met Maven-invoegtoepassingen](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) voor meer informatie.

Voer de onderstaande Maven-opdracht uit om de implementatie te configureren. Met deze opdracht kunt u het App Service-besturingssysteem, de Java-versie en de Tomcat-versie instellen.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Wanneer u hierom wordt gevraagd bij de optie **Abonnement**, selecteert u het juiste `Subscription` door het getal in te voeren dat aan het begin van de regel wordt weergegeven.
1. Wanneer u hierom wordt gevraagd bij de optie **Web-app**, accepteert u de standaardoptie `<create>` door op Enter te drukken, of selecteert u een bestaande app.
1. Wanneer u hierom wordt gevraagd bij de optie **Besturingssysteem**, selecteert u **Windows** door `3` in te voeren.
1. Wanneer u hierom wordt gevraagd bij de optie **Prijscategorie**, selecteert u **B2** door `2` in te voeren.
1. Gebruik de Java-standaardversie, **Java 8**, door op Enter te drukken.
1. Druk tot slot op de ENTER-toets bij de laatste prompt om uw selecties te bevestigen.

    Het samenvattingsoverzicht ziet er ongeveer uit zoals het fragment hieronder.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wanneer u hierom wordt gevraagd bij de optie **Abonnement**, selecteert u het juiste `Subscription` door het getal in te voeren dat aan het begin van de regel wordt weergegeven.
1. Wanneer u hierom wordt gevraagd bij de optie **Web-app**, accepteert u de standaardoptie `<create>` door op Enter te drukken, of selecteert u een bestaande app.
1. Wanneer u hierom wordt gevraagd bij de optie **Besturingssysteem**, selecteert u **Windows** door `3` in te voeren.
1. Wanneer u hierom wordt gevraagd bij de optie **Prijscategorie**, selecteert u **B2** door `2` in te voeren.
1. Gebruik de Java-standaardversie, **Java 8**, door op Enter te drukken.
1. Gebruik de standaardwebcontainer, **Tomcat 8.5**, door op Enter te drukken.
1. Druk tot slot op de ENTER-toets bij de laatste prompt om uw selecties te bevestigen.

    Het samenvattingsoverzicht ziet er ongeveer uit zoals het fragment hieronder.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Wanneer u hierom wordt gevraagd bij de optie **Abonnement**, selecteert u het juiste `Subscription` door het getal in te voeren dat aan het begin van de regel wordt weergegeven.
1. Wanneer u hierom wordt gevraagd bij de optie **Web-app**, accepteert u de standaardoptie `<create>` door op Enter te drukken, of selecteert u een bestaande app.
1. Wanneer u hierom wordt gevraagd bij de optie **Besturingssysteem**, selecteert u **Linux** door op Enter te drukken.
1. Wanneer u hierom wordt gevraagd bij de optie **Prijscategorie**, selecteert u **B2** door `2` in te voeren.
1. Gebruik de Java-standaardversie, **Java 8**, door op Enter te drukken.
1. Druk tot slot op de ENTER-toets bij de laatste prompt om uw selecties te bevestigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wanneer u hierom wordt gevraagd bij de optie **Abonnement**, selecteert u het juiste `Subscription` door het getal in te voeren dat aan het begin van de regel wordt weergegeven.
1. Wanneer u hierom wordt gevraagd bij de optie **Web-app**, accepteert u de standaardoptie `<create>` door op Enter te drukken, of selecteert u een bestaande app.
1. Wanneer u hierom wordt gevraagd bij de optie **Besturingssysteem**, selecteert u **Linux** door op Enter te drukken.
1. Wanneer u hierom wordt gevraagd bij de optie **Prijscategorie**, selecteert u **B2** door `2` in te voeren.
1. Gebruik de Java-standaardversie, **Java 8**, door op Enter te drukken.
1. Gebruik de standaardwebcontainer, **Tomcat 8.5**, door op Enter te drukken.
1. Druk tot slot op de ENTER-toets bij de laatste prompt om uw selecties te bevestigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

U kunt de configuraties voor App Service indien nodig rechtstreeks in uw `pom.xml` wijzigen. Dit zijn een aantal algemene instellingen:

Eigenschap | Vereist | Beschrijving | Versie
---|---|---|---
`<schemaVersion>` | false | Geef de versie van het configuratieschema op. Ondersteunde waarden zijn: `v1` of `v2`. | 1.5.2
`<subscriptionId>` | false | Geef de abonnements-id op. | 0.1.0+
`<resourceGroup>` | true | Azure-resourcegroep voor uw web-app. | 0.1.0+
`<appName>` | true | De naam is van uw web-app. | 0.1.0+
`<region>` | true | Hiermee geeft u de regio op waar uw web-app wordt gehost. De standaard waarde is **westeurope**. Alle geldige regio's staan in de sectie [Ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | De prijscategorie voor uw web-app. De standaardwaarde is **P1V2** voor productieworkloads. Voor Java dev/test-workloads wordt minimaal **B2** aanbevolen. [Meer informatie](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | De configuratie van de runtime-omgeving. U kunt u de details [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) bekijken. | 0.1.0+
`<deployment>` | true | De implementatieconfiguratie. U kunt de details [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) bekijken. | 0.1.0+

Let op de waarden voor `<appName>` en `<resourceGroup>`(`helloworld-1590394316693` en `helloworld-1590394316693-rg` in de demo) aangezien deze later worden gebruikt.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>De app implementeren

Bij het implementeren naar App Services met de Maven-invoegtoepassing worden de accountreferenties uit Azure CLI gebruikt. [Meld u aan met Azure CLI](/cli/azure/authenticate-azure-cli) voordat u doorgaat.

```azurecli
az login
```

Implementeer vervolgens uw Java-app in Azure met de volgende opdracht.

```bash
mvn package azure-webapp:deploy
```

Zodra de implementatie is voltooid, is uw toepassing beschikbaar op `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` in de demo). Open de URL met uw lokale webbrowser. U ziet dan het volgende als het goed is:

![Voorbeeld-app uitgevoerd in Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gefeliciteerd!** U hebt uw eerste Java-app geïmplementeerd in App Service.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met Azure DB voor PostgreSQL met Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [CI/CD instellen](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Prijsinformatie](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Logboeken en metrische gegevens aggregeren](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Omhoog schalen](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Resources voor Azure voor Java-ontwikkelaars](/java/azure/)

> [!div class="nextstepaction"]
> [De Java-app configureren](configure-language-java.md)

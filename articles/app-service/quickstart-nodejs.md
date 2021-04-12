---
title: 'Quickstart: Een Node.js-web-app maken'
description: Implementeer in enkele minuten uw eerste Node.js Hallo wereld naar Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 3b1ff02d6a9b3e8c7b7abaf1b185a9becd2be12b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105830"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure

::: zone pivot="platform-windows"  

Ga aan de slag met Azure App Service door een lokale Node.js/Express-app te maken met Visual Studio Code en de app vervolgens te implementeren in de cloud. Omdat u een gratis servicelaag van App Service gebruikt, zijn er geen kosten verbonden aan het voltooien van deze quickstart.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- <a href="https://git-scm.com/" target="_blank">Git installeren</a>
- [Node.js en NPM](https://nodejs.org). Voer de opdracht `node --version` uit om te controleren of Node.js is geïnstalleerd.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Azure App Service-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) voor Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Een lokale Node.js-toepassing klonen en uitvoeren

1. Open een terminal op uw lokale computer en kloon de voorbeeldopslagplaats:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigeer naar de map voor de nieuwe app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Start de app om deze lokaal te testen:

    ```bash
    npm start
    ```
    
1. Open uw browser en ga naar `http://localhost:1337`. In de browser moet ‘Hallo wereld!’ worden weergegeven.

1. Druk op **Ctrl**+**C** in de terminal om de server te stoppen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

In deze sectie implementeert u de Node.js-app in Azure met behulp van VS Code en de Azure App Service-extensie.

1. Controleer in de terminal of u zich in de map *nodejs-docs-hello-world* bevindt en start Visual Studio Code met de volgende opdracht:

    ```bash
    code .
    ```

1. Selecteer op de activiteitenbalk van VS Code het Azure-logo om de verkenner van **AZURE APP SERVICE** weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies. (Zie [Probleem oplossen met aanmelden bij Azure](#troubleshooting-azure-sign-in) hieronder als er fouten optreden.) Als u bent aangemeld, moet de verkenner de naam van uw Azure-abonnement weergeven.

    ![Aanmelden bij Azure](media/quickstart-nodejs/sign-in.png)

1. Selecteer in de verkenner van **AZURE APP SERVICE** binnen VS Code het pictogram met de blauwe pijl omhoog om uw app te implementeren in Azure. (U kunt dezelfde opdracht ook aanroepen vanuit het **Opdrachtpalet** (**Ctrl**+**Shift**+**P**) door 'implementeren naar web-app' te typen en **Azure App Service: Implementeren naar web-app** te kiezen).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Schermopname van de Azure App Service in VS Code, waarbij het pictogram met de blauwe pijl is geselecteerd.":::
        
1. Kies de map *nodejs-docs-hello-world*.

1. Kies een optie voor het maken van een web-app op basis van het besturingssysteem waarnaar u wilt implementeren:

    - Linux: Kies **Nieuwe web-app maken**
    - Windows: Kies **Nieuwe web-app maken... Geavanceerd**

1. Typ een unieke naam voor uw web-app en druk op **Enter**. De naam moet uniek zijn binnen Azure en mag alleen alfanumerieke tekens (‘A-Z’, ‘a-z’ en ‘0-9’) en afbreekstreepjes (‘-’) bevatten.

1. Als u Linux gebruikt, selecteert u een node.js-versie wanneer hierom wordt gevraagd. Een **LTS**-versie wordt aanbevolen.

1. Als u Windows gebruikt, volgt u de extra prompts:
    1. Selecteer **Een nieuwe resourcegroep maken** en voer een naam in voor de resourcegroep, zoals `AppServiceQS-rg`.
    1. Selecteer **Windows** als uw besturingssysteem.
    1. Selecteer **Nieuw App Service-plan maken**, voer een naam in voor het plan (bijvoorbeeld `AppServiceQS-plan`) en selecteer vervolgens **F1 Gratis** als prijscategorie.
    1. Kies **Voorlopig overslaan** wanneer u een vraag krijgt over Application Insights.
    1. Kies een regio bij u in de buurt of in de buurt van de resources die u wilt gebruiken.

1. Nadat u alle prompts hebt doorlopen, worden de Azure-resources die met VS Code worden gemaakt voor uw app weergegeven in het pop-upvenster.

    Wanneer u implementeert in Linux, selecteert u **Ja** wanneer u wordt gevraagd uw configuratie bij te werken om `npm install` uit te voeren op de Linux-doelserver.

    ![Prompt om de configuratie van de Linux-doelserver bij te werken](media/quickstart-nodejs/server-build.png)

1. Selecteer **Ja** wanneer u de melding **De werkruimte ‘nodejs-docs-hello-world’ altijd implementeren naar (app-naam)** krijgt. Als u **Ja** selecteert, gebruikt VS Code automatisch dezelfde App Service-web-app bij volgende implementaties.

1. Als u implementeert in Linux, selecteert u **Bladeren door website** in de prompt om uw zojuist geïmplementeerde web-app weer te geven nadat de implementatie is voltooid. In de browser moet ‘Hallo wereld!’ worden weergegeven.

1. Als u implementeert in Windows, moet u eerst het Node.js-versienummer van de web-app instellen.

    1. Vouw in VS Code het knooppunt voor de nieuwe app-service uit, klik met de rechtermuisknop op **Toepassingsinstellingen** en selecteer **Nieuwe instelling toevoegen...** :

        ![Opdracht App-instelling toevoegen](media/quickstart-nodejs/add-setting.png)

    1. Voer `WEBSITE_NODE_DEFAULT_VERSION` in bij de instellingssleutel.
    1. Voer `10.15.2` in bij de instellingswaarde.
    1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Opnieuw opstarten**

        ![Opdracht App-service opnieuw opstarten](media/quickstart-nodejs/restart.png)

    1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Bladeren door website**

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Probleemoplossing voor inloggen bij Azure

Als de fout **‘Kan geen abonnement vinden met de naam [abonnements-id]’** wordt weergegeven als u zich aanmeldt bij Azure, kan dit zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer de omgevingsvariabelen `HTTP_PROXY` en `HTTPS_PROXY` met uw proxygegevens in uw terminal met behulp van `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het probleem niet wordt opgelost door de omgevingsvariabelen in te stellen, kunt u contact met ons opnemen door de knop **Er is een fout opgetreden** hierboven te selecteren.

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren door wijzigingen in VS Code aan te brengen, uw bestanden op te slaan en vervolgens hetzelfde proces te gebruiken als hierboven, alleen selecteert u nu de bestaande app en maakt u geen nieuwe.

## <a name="viewing-logs"></a>Logboeken weergeven

U kunt de uitvoer van logboeken (aanroepen naar `console.log`) van de app rechtstreeks bekijken in het uitvoervenster van VS Code.

1. Klik in de verkenner van **AZURE APP SERVICE** met de rechtermuisknop op het app-knooppunt en kies **Streaminglogboeken starten**.

    ![Streaminglogboeken starten](media/quickstart-nodejs/view-logs.png)

1. Wanneer u hierom wordt gevraagd, kiest u ervoor om logboekregistratie in te schakelen en start u de toepassing opnieuw. Zodra de app opnieuw is opgestart, wordt het uitvoervenster van VS Code geopend met een verbinding met de logboekstroom. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Schermopname van de Visual Studio Code-prompt om logboekregistratie in te schakelen en de toepassing opnieuw te starten, waarbij de knop Ja is geselecteerd.":::

1. Na enkele seconden wordt in het uitvoervenster een bericht weergegeven dat aangeeft dat u bent verbonden met de streamservice voor logboekregistratie. U kunt meer uitvoeractiviteiten genereren door de pagina in de browser te vernieuwen.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze quickstart voltooid.

> [!div class="nextstepaction"]
> [Zelfstudie: Node.js-app met MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js-app configureren](configure-language-nodejs.md)

Bekijk de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-hulpprogramma’s](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook allemaal downloaden door het extensiepakket [Node-pakket voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Vereisten

Als u geen Azure-account hebt, kunt u zich [vandaag registreren](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) voor een gratis account met $ 200 aan Azure-tegoed waarmee u elke combinatie services kunt uitproberen.

[Visual Studio Code](https://code.visualstudio.com/) moet zijn geïnstalleerd, samen met [Node.js en npm](https://nodejs.org/en/download), het Node.js-pakketbeheer.

U moet ook de [Azure App Service-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) installeren. Deze kunt u gebruiken om Linux-web-apps te maken, beheren en implementeren in Azure PaaS (Platform as a Service).

### <a name="sign-in"></a>Aanmelden

Zodra de extensie is geïnstalleerd, meldt u zich aan bij uw Azure-account. Selecteer op de activiteitenbalk het Azure-logo om de verkenner van **AZURE APP SERVICE** weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies.

![aanmelden bij Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als de fout **Kan geen abonnement vinden met de naam [abonnements-id]** wordt weergegeven, kan dit zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer de omgevingsvariabelen `HTTP_PROXY` en `HTTPS_PROXY` met uw proxygegevens in uw terminal met behulp van `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het probleem niet wordt opgelost door de omgevingsvariabelen in te stellen, kunt u contact met ons opnemen door hieronder de knop **Er is een fout opgetreden** te selecteren.

### <a name="prerequisite-check"></a>Controle van vereisten

Voordat u doorgaat, moet u controleren of alle vereiste onderdelen zijn geïnstalleerd en geconfigureerd.

In VS Code ziet u uw Azure-e-mailadres in de statusbalk, en uw abonnement in de verkenner van **AZURE APP SERVICE**.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>De Node.js-toepassing maken

Maak vervolgens een Node.js-toepassing die kan worden geïmplementeerd in de cloud. In deze quickstart wordt een toepassingsgenerator gebruikt om de toepassing snel te maken vanuit een terminal.

> [!TIP]
> Als u de [Node.js-zelfstudie](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) als hebt voltooid, kunt u verdergaan met [Implementeren in Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Een nieuwe toepassing maken met de Express-generator

[Express](https://www.expressjs.com) is een populair framework voor het bouwen en uitvoeren van Node.js-toepassingen. U kunt een nieuwe Express-toepassing maken met behulp van het hulpprogramma [Express-generator](https://expressjs.com/en/starter/generator.html). De Express-generator wordt verzonden als een NPM-module en kan rechtstreeks (zonder installatie) worden uitgevoerd met behulp van het NPM-opdrachtregelprogramma `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

De parameters `--view pug --git` geven de generator de opdracht om de engine [pug](https://pugjs.org/api/getting-started.html)-sjabloon (voorheen bekend als `jade`) te gebruiken en een `.gitignore`-bestand te maken.

Als u alle afhankelijkheden van de toepassing wilt installeren, gaat u naar de nieuwe map en voert u `npm install`uit.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Controleer vervolgens of de toepassing actief is. Start vanuit de terminal de toepassing met behulp van de opdracht `npm start` om de server te starten.

```bash
npm start
```

Open nu de browser en ga naar `http://localhost:3000`, waar u ongeveer het volgende ziet:

![Express-toepassing uitvoeren](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze sectie implementeert u de Node.js-app met behulp van VS Code en de Azure App Service-extensie. Deze quickstart gebruikt het meest eenvoudige implementatiemodel waarbij uw app wordt ingepakt en geïmplementeerd naar een Azure-web-app in Linux.

### <a name="deploy-using-azure-app-service"></a>Implementeren met behulp van Azure App Service

Open eerst de toepassingsmap in VS code.

```bash
code .
```

Selecteer in de verkenner van **AZURE APP SERVICE** het pictogram met de blauwe pijl omhoog om uw app te implementeren in Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Schermopname van het Azure App Service-menu in Visual Studio Code, waarbij de blauwe implementatiepijl is geselecteerd.":::

> [!TIP]
> U kunt ook implementeren vanuit het **Opdrachtpalet** (Ctrl + Shift + P) door 'implementeren naar web-app' te typen en de opdracht **Azure App Service: Implementeren naar web-app** uit te voeren.

1. Kies de map die momenteel is geopend, `myExpressApp`.

1. Kies **Nieuwe web-app maken**. Hiermee wordt standaard geïmplementeerd in App Service op Linux.

1. Typ een globaal unieke naam voor de web-app en druk op Enter. Geldige tekens voor een app-naam zijn: a-z, 0-9, en -.

1. Kies de **Node.js-versie**. (LTS wordt aanbevolen.)

    Het meldingskanaal toont de Azure-resources die worden gemaakt voor uw app.

1. Selecteer **Ja** wanneer u wordt gevraagd de configuratie bij te werken om `npm install` uit te voeren op de Linux-doelserver. De app wordt vervolgens geïmplementeerd.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Schermopname van de prompt om uw configuratie op de doelserver bij te werken, waarbij de knop Ja is geselecteerd.":::

1. Wanneer de implementatie start, wordt u gevraagd om uw werkruimte bij te werken zodat latere implementaties automatisch gericht zijn op dezelfde Azure Service-web-app. Kies **Ja** om ervoor te zorgen dat uw wijzigingen worden geïmplementeerd in de juiste app.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Schermopname van de prompt om uw werkruimte bij te werken, waarbij de knop Ja is geselecteerd.":::

> [!TIP]
> Zorg ervoor dat de toepassing luistert op de poort die is opgegeven met de PORT-omgevingsvariabele: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Door de app bladeren in Azure

Zodra de implementatie is voltooid, selecteert u **Bladeren door website** in de prompt, om de zojuist geïmplementeerde web-app weer te geven.

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout **U bent niet gemachtigd om deze map of pagina weer te geven.** ziet, is de toepassing waarschijnlijk niet juist gestart. Ga naar de volgende sectie en bekijk de logboekuitvoer om de fout te vinden en te herstellen. Als dit niet lukt, neemt u contact met ons op via de knop **Ik ondervind een probleem** hieronder. We helpen u graag!

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren met behulp van hetzelfde proces. Alleen gebruikt u de bestaande app in plaats van een nieuwe te maken.

## <a name="viewing-logs"></a>Logboeken weergeven

In deze sectie leert u hoe u de logboeken kunt weergeven vanuit de actieve App Service-app. Alle aanroepen naar `console.log` in de app worden weergegeven in het uitvoervenster van Visual Studio Code.

Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de app en kies **streaming-Logboeken starten**.

Het uitvoervenster van VS Code wordt geopend met een verbinding naar de logboekstroom.

![Streaminglogboeken starten](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Schermopname van de VS Code-prompt om logboekregistratie voor bestanden in te schakelen en de web-app opnieuw te starten, waarbij de knop Ja is geselecteerd.":::

Na enkele seconden ziet u een bericht waarin staat dat u bent verbonden met de streamingservice voor logboeken. Vernieuw de pagina enkele keren om meer activiteiten te zien.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze quickstart voltooid.

Bekijk hierna de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-hulpprogramma’s](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook allemaal downloaden door het extensiepakket [Node-pakket voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.


::: zone-end

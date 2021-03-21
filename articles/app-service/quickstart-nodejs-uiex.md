---
title: 'Quickstart: Een Node.js-web-app maken'
description: Implementeer in enkele minuten uw eerste Node.js Hallo wereld naar Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748448"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure

Aan de slag met <abbr title="Een HTTP-gebaseerde service voor het hosten van webtoepassingen, REST-Api's en mobiele back-end-toepassingen.">Azure App Service</abbr> door een Node.js/Express-app lokaal te maken met Visual Studio code en vervolgens de app te implementeren in de Azure-Cloud. Omdat u een <abbr title="In Azure App Service is een basislaag waarin uw app wordt uitgevoerd op dezelfde Vm's als andere apps, inclusief de apps van andere klanten. Deze laag is bedoeld voor ontwikkelen en testen.">gratis laag</abbr>, worden er geen kosten in rekening gebracht om deze Snelstartgids te volt ooien.

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

- Een Azure-account met een actief <abbr title="Een Azure-abonnement is een logische container die wordt gebruikt voor het inrichten van resources in Azure. Het abonnement bevat informatie over al uw resources, zoals virtuele machines (VM's), databases, enzovoort.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- <a href="https://git-scm.com/" target="_blank">Git installeren</a>
- [Node.js en NPM](https://nodejs.org). Voer de opdracht `node --version` uit om te controleren of Node.js is geïnstalleerd.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Azure App Service-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) voor Visual Studio Code.

[Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. een lokale Node.js-toepassing klonen en uitvoeren

1. Open een terminal op uw lokale computer en kloon de voorbeeldopslagplaats:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigeer naar de map voor de nieuwe app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. De afhankelijkheden installeren:

    ```bash
    npm install
    ```

1. Start de app om deze lokaal te testen:

    ```bash
    npm start
    ```
    
1. Open uw browser en ga naar `http://localhost:1337`. In de browser moet ‘Hallo wereld!’ worden weergegeven.

1. Druk op <kbd>Ctrl</kbd> + <kbd>C</kbd> in de terminal om de server te stoppen.

[Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. logboeken van Visual Studio code weer geven

Bekijk de <abbr title="Alle aanroepen naar `console.log` in de app worden weergegeven in het uitvoervenster van Visual Studio Code.">logboek</abbr> van de actieve App Service-app.

1. Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de naam van de app en kies **streaming-Logboeken starten**.

1. Het venster Visual Studio code-uitvoer wordt geopend.

    ![Streaminglogboeken weergeven](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Schermopname van de VS Code-prompt om logboekregistratie voor bestanden in te schakelen en de web-app opnieuw te starten, waarbij de knop Ja is geselecteerd.":::

1. Na enkele seconden ziet u een bericht waarin staat dat u bent verbonden met de streamingservice voor logboeken. 
1. Vernieuw de pagina enkele keren om meer activiteiten te zien.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. opschonen van resources

Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de naam van de app en kies **verwijderen**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Zoek de app in de Verkenner ' * * ' AZURE APP SERVICE ' * * ', klik met de rechter muisknop op de naam van de app en kies verwijderen.":::

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze quickstart voltooid. U kunt wijzigingen in deze app implementeren met behulp van hetzelfde proces. Alleen gebruikt u de bestaande app in plaats van een nieuwe te maken.

Bekijk hierna de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-hulpprogramma’s](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook allemaal downloaden door het extensiepakket [Node-pakket voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.
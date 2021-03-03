---
title: 'Snelstartgids: een Node.js-web-app maken-Windows'
description: Implementeer uw eerste Node.js Hallo wereld tot Azure App Service in enkele minuten voor het Windows-platform.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 302e0dc79d13eedebf810df042dc31f78b173fb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748446"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. implementeren naar Azure App Service vanuit Visual Studio code

1. Open de toepassingsmap in Visual Studio code.

    ```bash
    code .
    ```

1. In de **Azure app service** Explorer selecteert **u aanmelden bij Azure...** en volgt u de instructies. Als u bent aangemeld, moet de Explorer de naam van uw Azure-abonnement weer geven.

    ![Aanmelden bij Azure](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Probleemoplossing voor inloggen bij Azure</summary>
    
    Als de fout **‘Kan geen abonnement vinden met de naam [abonnements-id]’** wordt weergegeven als u zich aanmeldt bij Azure, kan dit zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer de omgevingsvariabelen `HTTP_PROXY` en `HTTPS_PROXY` met uw proxygegevens in uw terminal met behulp van `export`.
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. Selecteer in de verkenner van **AZURE APP SERVICE** het pictogram met de blauwe pijl omhoog om uw app te implementeren in Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Schermopname van de Azure App Service in VS Code, waarbij het pictogram met de blauwe pijl is geselecteerd.":::

1. Kies de map die momenteel is geopend, `nodejs-docs-hello-world`.

1. Kies **nieuwe web-app maken... Geavanceerd**, om te implementeren in app service in Windows.

1. Typ een wereld wijd uniek <abbr title="Geldige tekens voor een app-naam zijn: a-z, 0-9, en -.">naam</abbr> voor uw web-app en druk op **Enter**. 
1. Selecteer **Een nieuwe resourcegroep maken** en voer een naam in voor de resourcegroep, zoals `AppServiceQS-rg`.
1. Kies de **Node.js-versie**. (LTS wordt aanbevolen.)

    Het meldingskanaal toont de Azure-resources die worden gemaakt voor uw app.
1. Selecteer **Windows** als uw besturingssysteem.
1. Selecteer **Nieuw App Service-plan maken**, voer een naam in voor het plan (bijvoorbeeld `AppServiceQS-plan`) en selecteer vervolgens **F1 Gratis** als prijscategorie.
1. Kies **Voorlopig overslaan** wanneer u een vraag krijgt over Application Insights.
1. Kies een regio bij u in de buurt of in de buurt van de resources die u wilt gebruiken.

1. Kies **Ja** wanneer u wordt gevraagd om uw werk ruimte bij te werken zodat latere implementaties automatisch worden gericht op dezelfde app service Web-app. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Schermopname van de prompt om uw werkruimte bij te werken, waarbij de knop Ja is geselecteerd.":::

1. Klik nog een keer met de rechter muisknop op het knoop punt voor de app-service en selecteer **implementeren naar web-app**.

1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Bladeren door website**

    [Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Zodra de implementatie is voltooid, selecteert u **Bladeren door website** in de prompt, om de zojuist geïmplementeerde web-app weer te geven.

<br>
<details>
<summary>Problemen oplossen</summary>
* Zorg ervoor dat de toepassing luistert op de poort die is opgegeven met de PORT-omgevingsvariabele: `process.env.PORT`.
* Als u de fout **U bent niet gemachtigd om deze map of pagina weer te geven.** ziet, is de toepassing waarschijnlijk niet juist gestart. Controleer de logboek uitvoer om de fout te vinden en op te lossen. 

</details>

<br>

[Een probleem melden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>
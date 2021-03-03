---
title: 'Quick Start: een Node.js web-app maken Linux'
description: Implementeer in enkele minuten uw eerste Node.js Hallo wereld naar Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6c580f7d6bc03f298621b1a33fcb9f3f461e802
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748449"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. implementeren naar Azure App Service vanuit Visual Studio code

1. Open de toepassingsmap in Visual Studio code.

    ```bash
    code .
    ```

1. In de **Azure app service** Explorer selecteert **u aanmelden bij Azure...** en volgt u de instructies. Als u bent aangemeld, moet de Explorer de naam van uw Azure-abonnement weer geven.

    ![Aanmelden bij Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
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

1. Kies **Nieuwe web-app maken**. Hiermee wordt standaard geïmplementeerd in App Service op Linux.

1. Typ een wereld wijd uniek <abbr title="Geldige tekens voor een app-naam zijn: a-z, 0-9, en -.">naam</abbr> voor uw web-app en druk op **Enter**. 

1. Kies de **Node.js-versie**. (LTS wordt aanbevolen.)

    Het meldingskanaal toont de Azure-resources die worden gemaakt voor uw app.

1. Selecteer **Ja** wanneer u wordt gevraagd de configuratie bij te werken om `npm install` uit te voeren op de Linux-doelserver. De app wordt vervolgens geïmplementeerd.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Schermopname van de prompt om uw configuratie op de doelserver bij te werken, waarbij de knop Ja is geselecteerd.":::

1. Kies **Ja** wanneer u wordt gevraagd om uw werk ruimte bij te werken zodat latere implementaties automatisch worden gericht op dezelfde app service Web-app. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Schermopname van de prompt om uw werkruimte bij te werken, waarbij de knop Ja is geselecteerd.":::




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



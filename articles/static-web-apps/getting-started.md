---
title: 'Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483837"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps

Azure Static Web Apps publiceert een website door apps te bouwen vanuit een codeopslagplaats. In deze quickstart implementeert u een toepassing in Azure Static Web Apps met behulp van de Visual Studio Code-extensie.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git installeren](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Open vervolgens Visual Studio Code en ga naar **Bestand > Map** openen om de opslagplaats te openen die u in de editor naar uw computer hebt gekloond.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Selecteer in Visual Studio Code het Azure-logo in de activiteitenbalk om het venster met Azure-extensies te openen.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logo":::

    > [!NOTE]
    > Aanmelding bij Azure en GitHub zijn vereist. Als u zich nog niet hebt aangemeld bij Azure en GitHub via Visual Studio Code, wordt u tijdens het aanmaakproces gevraagd om u bij beide aan te melden.

1. Selecteer onder _Static Web Apps_ label het **plusteken**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Toepassingsnaam":::

1. Het opdrachtenpalet wordt boven aan de editor geopend en u wordt gevraagd uw toepassing een naam te gegeven.

    Typ **my-first-static-web-app** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statische web-app maken":::

1. Selecteer de voorinstellingen die overeenkomen met uw toepassingstype.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Toepassingsvoorinstellingen: Geen framework":::

    Voer **./ in** als de locatie voor de toepassingsbestanden.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Locatie van toepassingsbestanden":::

    Selecteer **Nu overslaan als** de locatie voor de Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-locatie":::

    Voer **./ in** als de uitvoerlocatie van de build.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Uitvoerlocatie van toepassingsbouw":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Hoewel er een Angular-voorinstelling is, selecteert u de optie Aangepast zodat u een geschikte uitvoerlocatie voor deze toepassing kunt bieden. 

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Toepassingsvoorinstellingen: Angular":::

    Voer **./ in** als de locatie voor de toepassingsbestanden.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Locatie van toepassingsbestanden: Angular":::

    Selecteer **Nu overslaan als** de locatie voor de Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-locatie: Angular":::

    Voer **dist/angular-basic in** als de uitvoerlocatie van de build.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Uitvoerlocatie van toepassings build: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Toepassingsvoorinstellingen: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Toepassingsvoorinstellingen: Vue":::

    ---

1. Selecteer de locatie die het dichtst bij u in de buurt is en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resourcelocatie":::

1. Zodra de app is gemaakt, wordt er een bevestigingsbericht weergegeven in Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Bevestiging dat de app is gemaakt":::

    Klik vervolgens op de knop **Acties openen in GitHub.** Op deze pagina ziet u de buildstatus van de toepassing.

    Zodra de GitHub-actie is voltooid, kunt u naar de gepubliceerde website bladeren.

1. Als u de website in de browser wilt weergeven, klikt u met de rechtermuisknop op het project in Static Web Apps extensie en selecteert u **Bladeren door site.**

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Bladeren door site":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen via de extensie.

Ga in het Visual Studio Code Explorer-venster terug naar het gedeelte _Static Web Apps_ en klik met de rechtermuisknop op **my-first-static-web-app** en selecteer **Verwijderen**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="App verwijderen":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)

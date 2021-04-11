---
title: 'Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449139"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps

Azure static Web Apps een website publiceert door apps te bouwen vanuit een code opslagplaats. In deze Quick Start implementeert u een toepassing in azure static web apps met behulp van de Visual Studio code extension.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git installeren](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Open vervolgens Visual Studio code en ga naar de **map bestand > openen** om de opslag plaats te openen die u hebt gekloond naar uw computer in de editor.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Selecteer in Visual Studio Code het Azure-logo in de activiteitenbalk om het venster met Azure-extensies te openen.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logo":::

    > [!NOTE]
    > Aanmelding bij Azure en GitHub zijn vereist. Als u zich nog niet hebt aangemeld bij Azure en GitHub via Visual Studio Code, wordt u tijdens het aanmaakproces gevraagd om u bij beide aan te melden.

1. Selecteer het **plus teken** onder het label _statische web apps_ .

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Toepassingsnaam":::

1. Het opdrachtenpalet wordt boven aan de editor geopend en u wordt gevraagd uw toepassing een naam te gegeven.

    Typ **my-first-static-web-app** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statische web-app maken":::

1. Selecteer de voor instellingen die overeenkomen met uw toepassings type.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Voor instellingen van toepassing: geen Framework":::

    Voer **./** als de locatie voor de toepassings bestanden

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Locatie van toepassings bestanden":::

    Selecteer **nu overs Laan** als locatie voor de Azure functions-API

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-locatie":::

    Enter **./** als de uitvoer locatie van de build

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Uitvoer locatie van de toepassings build":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Voor instellingen van toepassing: hoek":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Voor instellingen van toepassing: reageren":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Voor instellingen van toepassing: Vue":::

    ---

1. Selecteer de locatie die het dichtst bij u in de buurt is en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resourcelocatie":::

1. Zodra de app is gemaakt, wordt er een bevestigingsbericht weergegeven in Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Bevestiging dat de app is gemaakt":::

    Klik vervolgens op de knop **acties openen in github**. Op deze pagina wordt de bouw status van de toepassing weer gegeven.

    Zodra de GitHub-actie is voltooid, kunt u naar de gepubliceerde website bladeren.

1. Als u de website in de browser wilt weer geven, klikt u met de rechter muisknop op het project in de extensie statische Web Apps en selecteert u **Bladeren naar site**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Door site bladeren":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen via de extensie.

Ga in het Visual Studio Code Explorer-venster terug naar het gedeelte _Static Web Apps_ en klik met de rechtermuisknop op **my-first-static-web-app** en selecteer **Verwijderen**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="App verwijderen":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)

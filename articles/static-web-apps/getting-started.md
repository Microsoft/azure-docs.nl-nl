---
title: 'Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 2d7a2dcbbd0b6e9a10ca8af93798bfddbee94ee3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182652"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps

Met Azure Static Web Apps wordt een website gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze quickstart implementeert u een webtoepassing in Azure Static Web Apps met behulp van de Visual Studio Code-extensie.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git installeren](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Open vervolgens Visual Studio Code en ga naar **File > Open Folder** om de opslagplaats te openen die u zojuist in de editor naar uw computer hebt gekloond.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Selecteer in Visual Studio Code het Azure-logo in de activiteitenbalk om het venster met Azure-extensies te openen.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logo":::

    > [!NOTE]
    > Aanmelding bij Azure en GitHub zijn vereist. Als u zich nog niet hebt aangemeld bij Azure en GitHub via Visual Studio Code, wordt u tijdens het aanmaakproces gevraagd om u bij beide aan te melden.

1. Plaats de muisaanwijzer op het label van _Static Web Apps_ en selecteer het **plusteken**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Toepassingsnaam":::

1. Het opdrachtenpalet wordt boven aan de editor geopend en u wordt gevraagd uw toepassing een naam te gegeven.

    Typ **my-first-static-web-app** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statische web-app maken":::

1. Selecteer de **hoofdvertakking** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Naam van vertakking":::

1. Selecteer **/** als locatie voor de toepassingscode en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Locatie van de toepassingscode":::

1. De extensie zoekt naar de locatie van de API in uw toepassing. In dit artikel wordt geen API geïmplementeerd.

    Selecteer **Skip for now** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-locatie":::

1. Selecteer de locatie waar de bestanden worden gebouwd voor productie in uw app.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

    Schakel het selectievakje uit en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Pad naar app-bestanden":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Typ **dist/angular-basic** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Pad naar Angular-app-bestanden":::

    # <a name="react"></a>[React](#tab/react)

    Typ **build** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Pad naar React-app-bestanden":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Typ **dist** en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Pad naar Vue-app-bestanden":::

    ---

1. Selecteer de locatie die het dichtst bij u in de buurt is en druk op **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resourcelocatie":::

1. Zodra de app is gemaakt, wordt er een bevestigingsbericht weergegeven in Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Bevestiging dat de app is gemaakt":::

1. Navigeer in het venster Visual Studio Code Explorer naar het knooppunt met de naam van uw abonnement en vouw dit uit. Houd er rekening mee dat het enkele minuten kan duren voordat de implementatie is voltooid. Ga vervolgens terug naar het gedeelte Static Web Apps, selecteer de naam van uw app en klik daarna met de rechtermuisknop op Mijn eerste statische web-app, en selecteer In portal openen om de app in Azure Portal weer te geven.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Portal openen":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen via de extensie.

Ga in het Visual Studio Code Explorer-venster terug naar het gedeelte _Static Web Apps_ en klik met de rechtermuisknop op **my-first-static-web-app** en selecteer **Verwijderen**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="App verwijderen":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)

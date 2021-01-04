---
title: 'Quickstart: Uw eerste statische web-app bouwen met Azure Static Web Apps met behulp van de Azure Portal'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps met behulp van de Azure-portal.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: cshoe
ms.openlocfilehash: aab99721b61ababe218e509ab1078198b5f4152b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030362"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Quickstart: Uw eerste statische site bouwen in de Azure-portal

Met Azure Static Web Apps wordt een website gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze quickstart implementeert u een webtoepassing in Azure Static Web Apps met behulp van de Azure-portal.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Statische web-app maken

Nu de opslagplaats is gemaakt, kunt u een statische web-app maken in de Azure-portal.

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Selecteer **Een resource maken**
1. Zoek naar **Static Web Apps**
1. Selecteer **Static Web Apps (preview)**
1. Selecteer **Maken**

Op het tabblad _Basisbeginselen_ kunt u de nieuwe app configureren en aan een GitHub-opslagplaats koppelen.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Basissectie":::

1. Selecteer uw _Azure-abonnement_
1. Een nieuwe _Resourcegroep_ selecteren of maken
1. Geef de app de naam **mijn-eerste-statische-web-app**.
      1. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.
1. Selecteer de _Regio_ het dichtst bij u in de buurt
1. Selecteer de **gratis** _SKU_
1. Selecteer de knop **Aanmelden bij GitHub** en verifieer u bij GitHub

Nadat u zich hebt aangemeld bij GitHub, voert u de gegevens van de opslagplaats in.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Gegevens van opslagplaats":::

1. Kies de gewenste _organisatie_
1. Selecteer **mijn-eerste-statische-web-app** in de vervolgkeuzelijst _Opslagplaats_
1. Selecteer **hoofd** in de vervolgkeuzelijst _Vertakking_

> [!NOTE]
> Als u geen opslagplaatsen ziet, moet u mogelijk Azure Static Web Apps autoriseren in GitHub. Ga naar de GitHub-opslagplaats en ga naar **Instellingen > Toepassingen > Geautoriseerde OAuth-apps**, selecteer **Azure Static Web Apps** en selecteer **Verlenen**. Voor organisatie-opslagplaatsen moet u een eigenaar van de organisatie zijn om de machtigingen te verlenen.

1. Voeg in de sectie _Build-details_ configuratiegegevens toe die specifiek zijn voor het front-end-framework van uw keuze.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

    1. Selecteer **Aangepast** in het vervolgkeuzemenu _Build-voorinstellingen_
    1. Laat de standaardwaarde in het vak _API-locatie_ staan
    1. Wis de standaardwaarde in het vak _API-locatie_
    1. Laat het vak _Locatie van app-artefact_ leeg

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Selecteer **Hoek** in het vervolgkeuzemenu _Build-voorinstellingen_
    1. Laat de standaardwaarde in het vak _API-locatie_ staan
    1. Wis de standaardwaarde in het vak _API-locatie_
    1. Typ **dist/angular-basic** in het vak _Locatie van app-artefact_

    # <a name="react"></a>[React](#tab/react)

    1. Selecteer **Reageren** in het vervolgkeuzemenu _Build-voorinstellingen_
    1. Laat de standaardwaarde in het vak _API-locatie_ staan
    1. Wis de standaardwaarde in het vak _API-locatie_
    1. Typ **build** in het vak _Locatie van app-artefact_

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Selecteer **Vue.js** in het vervolgkeuzemenu _Build-voorinstellingen_
    1. Laat de standaardwaarde in het vak _API-locatie_ staan
    1. Wis de standaardwaarde in het vak _API-locatie_
    1. Laat de standaardwaarde in het vak _Locatie van app-artefact_ staan

    ---

1. Selecteer **Controleren + maken**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Knop Beoordelen en maken":::

    > [!NOTE]
    > U kunt het [werkstroombestand](github-actions-workflow.md) bewerken als u deze waarden wilt wijzigen nadat u de app hebt gemaakt.

1. Selecteer **Maken**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="De knop Maken":::

1. Selecteer **Ga naar resource**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="De knop Naar de resource gaan":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen door de volgende stappen te volgen:

1. Open de [Azure-portal](https://portal.azure.com)
1. Ga in de bovenste zoekbalk naar **mijn-eerste-statische-web-app**
1. Selecteer de naam van de app
1. Selecteer de knop **Verwijderen**
1. Selecteer **Ja** om de verwijdering te bevestigen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)

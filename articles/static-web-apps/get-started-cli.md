---
title: 'Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps met behulp van CLI.'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps met behulp van Azure CLI.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: ca05194c0d8e63290a9cab8d9f7903daa192069b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030345"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Quickstart: Uw eerste statische site bouwen met behulp van Azure CLI

Met Azure Static Web Apps wordt een website gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze quickstart implementeert u een webtoepassing in Azure Static Web Apps met behulp van de Azure CLI.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Persoonlijk GitHub-toegangstoken](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com)-account
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) geïnstalleerd (versie 2.8.0 en hoger)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ga vervolgens naar de nieuwe map met behulp van de volgende opdracht.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Statische web-app maken

Nu de opslagplaats is gemaakt, kunt u een statische web-app maken vanuit de Azure CLI.

> [!IMPORTANT]
> Zorg dat u zich in de map _mijn-eerste-statische-web-app_ in uw terminal bevindt.

1. Meld u aan bij de Azure CLI met behulp van de volgende opdracht.

    ```bash
    az login
    ```

1. Maak een nieuwe statische web-app vanuit uw opslagplaats.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Vervang deze waarde door de naam van een bestaande Azure-resourcegroep.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Vervang deze waarde door uw GitHub-gebruikersnaam.

    - `<LOCATION>`: Vervang deze waarde door de dichtstbijzijnde locatie. Een aantal opties: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ of _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Vervang deze waarde door het [persoonlijke toegangstoken van GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) dat u eerder hebt gegenereerd.

    Nu kunt u de gemaakte app weergeven in Azure.

1. Open [Azure Portal](https://portal.azure.com).

1. Zoek in de bovenste zoekbalk naar **mijn-eerste-statische-web-app**.

1. Selecteer **mijn-eerste-statische-web-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-instantie verwijderen door de volgende opdracht uit te voeren:

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)

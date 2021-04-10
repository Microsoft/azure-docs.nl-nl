---
title: 'Quickstart: Uw eerste statische site bouwen met Azure Static Web Apps met behulp van CLI.'
description: Leer hoe u een statische site kunt implementeren in Azure Static Web Apps met behulp van Azure CLI.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a95e1658c3633f4ae8d09b71e9d3b0c82446754a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727583"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Quickstart: Uw eerste statische site bouwen met behulp van Azure CLI

Met Azure Static Web Apps wordt een website gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze quickstart implementeert u een webtoepassing in Azure Static Web Apps met behulp van de Azure CLI.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Persoonlijk GitHub-toegangstoken](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com)-account
- [Azure CLI](/cli/azure/install-azure-cli) geïnstalleerd (versie 2.8.0 en hoger)

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

    ```azurecli
    az login
    ```

1. Maak een nieuwe statische web-app vanuit uw opslagplaats.

    # <a name="no-framework"></a>[Geen framework](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > De URL die wordt door gegeven aan de `s` para meter mag niet het `.git` achtervoegsel bevatten.

    - `<RESOURCE_GROUP_NAME>`: Vervang deze waarde door een bestaande [naam van een Azure-resource groep](../azure-resource-manager/management/manage-resources-cli.md).

      - Zie de documentatie van [AZ Group](/cli/azure/group#az_group_list) voor meer informatie over het weer geven van resource groepen.

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

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)
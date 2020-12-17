---
title: 'CLI: Continue implementatie vanuit GitHub'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u een app maakt met CI/CD vanuit GitHub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2147976ae73f93e6f451dbd871ead865e2331455
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006316"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Een App Service-app maken met continue implementatie vanuit GitHub met behulp van CLI

In dit voorbeeldscript wordt een app gemaakt in App Service met de bijbehorende resources en wordt er vervolgens continue implementatie vanuit een GitHub-opslagplaats ingesteld. Zie [Een app maken en code implementeren vanuit GitHub](cli-deploy-github.md) voor de implementatie van GitHub zonder continue implementatie. Voor dit voorbeeld hebt u het volgende nodig:

* Een GitHub-opslagplaats met toepassingscode, waarvoor u beheerdersbevoegdheden hebt. Als u automatische builds wilt krijgen, structureert u uw opslagplaats volgens de tabel [Uw opslagplaats voorbereiden](../deploy-continuous-deployment.md#prepare-your-repository).
* Een [Personal Access Token (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) voor uw GitHub-account.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Hiermee maakt u een App Service-app. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config) | Hiermee koppelt u een App Service-app aan een Git- of Mercurial-opslagplaats. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).

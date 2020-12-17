---
title: 'CLI: Implementeren vanuit lokale Git-opslagplaats'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u code implementeert vanuit een lokale Git-opslagplaats.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 226eda2c4a1e58cda8119f1fb4e76866216e890b
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006250"
---
# <a name="create-an-app-service-app-and-deploy-code-from-a-local-git-repository-using-azure-cli"></a>Een App Service-app maken en code implementeren vanuit een lokale Git-opslagplaats met behulp van Azure CLI

Met dit voorbeeldscript wordt een app gemaakt in App Service, inclusief de bijbehorende resources, en wordt vervolgens de code van de app geïmplementeerd in een lokale Git-opslagplaats.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Create an app and deploy code from a local Git repository")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Hiermee maakt u een App Service-app. |
| [`az webapp deployment user set`](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Hiermee stelt u de implementatiereferenties voor App Service in op accountniveau. |
| [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Hiermee maakt u een configuratie voor broncodebeheer voor een lokale Git-opslagplaats. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).

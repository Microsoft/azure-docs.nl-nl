---
title: 'CLI: ASP.NET Core-app maken met Docker'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u een ASP.NET Core-app maakt vanuit Docker Hub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 7e2400d42798dddf33a0431938e7f13ff29b64f9
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005848"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-from-docker-hub-using-azure-cli"></a>Een ASP.NET Core-app vanuit Docker Hub in een Docker-container maken met Azure CLI

Met dit voorbeeldscript maakt u een resourcegroep, een Linux App Service-plan en een app. Vervolgens wordt er een ASP.NET Core-toepassing geïmplementeerd met behulp van een Docker-Container.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-docker/deploy-linux-docker.sh?highlight=6 "Linux Docker")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een App Service-app en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Hiermee maakt u een App Service-app. |
| [`az webapp config container set`](/cli/azure/webapp/config/container#az-webapp-config-container-set) | Hiermee stelt u de Docker-container voor de App Service-app in. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).

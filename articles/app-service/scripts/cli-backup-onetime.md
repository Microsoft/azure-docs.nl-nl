---
title: 'CLI: Een back-up maken van een app'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u een back-up maakt van een app.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: a5771882e85c69ae54212b2ea8eb1e9482f27821
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782635"
---
# <a name="back-up-an-app-using-cli"></a>Een back-up maken van een app met behulp van CLI

Met dit voorbeeldscript wordt er een app inclusief de bijbehorende resources gemaakt in App Service, en wordt er vervolgens een eenmalige back-up van de app gemaakt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Hiermee maakt u een opslagaccount. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Hiermee wordt een Azure-opslagcontainer gemaakt. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Hiermee wordt een SAS-token gegenereerd voor een Azure-opslagcontainer.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Hiermee maakt u een App Service-app. |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup#az_webapp_config_backup_create) | Hiermee maakt u een back-up voor een App Service-app. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Hiermee haalt u een lijst op van back-ups voor een App Service-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).

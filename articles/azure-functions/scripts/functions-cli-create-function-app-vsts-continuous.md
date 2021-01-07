---
title: Een functie-app maken met DevOps-implementatie - Azure CLI
description: Een functie-app maken en functiecode implementeren vanuit Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f89da9fc146d753442f2a8c8aa38861e66c9a3d9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934370"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Een functie in Azure maken die wordt geïmplementeerd vanuit Azure DevOps

In dit onderwerp leest u hoe u Azure Functions kunt gebruiken om een functie-app [zonder server](https://azure.microsoft.com/solutions/serverless/) te maken met behulp van het [verbruiksabonnement](../consumption-plan.md). De functie-app, wat een container is voor uw functies, wordt continu vanuit een Azure DevOps-opslagplaats geïmplementeerd. 

Voor het voltooien van dit onderwerp hebt u het volgende nodig:

* Een Azure DevOps-opslagplaats die uw functie-app-project bevat en waarvoor u beheerdersmachtigingen hebt.
* Een [persoonlijk toegangstoken (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) voor toegang tot uw Azure DevOps-opslagplaats.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="sample-script"></a>Voorbeeldscript

In dit voorbeeld maakt u een Azure-functie-app en implementeert u functiecode vanuit Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten voor het maken van een resourcegroep, opslagaccount, functie-app en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u het opslagaccount dat vereist is voor de functie-app. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app in het serverloze [verbruiksabonnement](../consumption-plan.md). |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Hiermee koppelt u een functie-app aan een Git- of Mercurial-opslagplaats. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).

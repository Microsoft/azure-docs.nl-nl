---
title: Een serverloze Python-functie-app maken - Azure CLI
description: Een serverloze Python-functie-app maken met behulp van de Azure CLI
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 9bd80d5644587c6dcc074a91bf91382d0692bc18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786149"
---
# <a name="create-a-serverless-python-function-app-using-azure-cli"></a>Een serverloze Python-functie-app maken met behulp van Azure CLI

Met dit voorbeeldscript voor Azure Functions maakt u een functie-app die een container vormt voor uw functies. 

>[!NOTE]
>De functie-app die wordt gemaakt, wordt uitgevoerd op Python versie 3.6. Python-versie 3.7 wordt ook ondersteund door Azure Functions.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een Azure Function-app gemaakt met behulp van het [Verbruiksabonnement](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption-python/create-function-app-consumption-python.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Hiermee wordt een Azure Storage-account gemaakt. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Hiermee maakt u een functie-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).

---
title: Een functie-app in een Premium-plan maken - Azure CLI
description: Een functie-app in een schaalbaar Premium-plan in Azure maken met Azure CLI
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 714dd8d886f567bcdf07c93803662ef4b2c635f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565157"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Een functie-app in een Premium-plan maken - Azure CLI

Met dit voorbeeldscript voor Azure Functions maakt u een functie-app die een container vormt voor uw functies. De functie-app die gaat maken, maakt gebruik van een [schaalbaar Premium-plan](../functions-premium-plan.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een functie-app met behulp van een [Premium-plan](../functions-premium-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u een Azure-opslagaccount. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Hiermee maakt u een Premium-plan in een [specifieke SKU](../functions-premium-plan.md#available-instance-skus). |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app in het App Service-abonnement. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).

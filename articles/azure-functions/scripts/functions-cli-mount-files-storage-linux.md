---
title: Een bestandsshare koppelen aan een Python-functie-app - Azure CLI
description: Lees hoe u een serverloze Python-functie-app maakt en een bestaande bestandsshare koppelt met behulp van de Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762277"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Een bestandsshare koppelen aan een Python-functie-app met behulp van Azure CLI

Met dit voorbeeldscript voor Azure Functions maakt u een functie-app en een share in Azure Files. Vervolgens wordt de share gekoppeld zodat de gegevens toegankelijk zijn voor uw functies.  

>[!NOTE]
>De functie-app die wordt gemaakt, wordt uitgevoerd met Python versie 3.7. Azure Functions [ondersteunt ook Python-versies 3.6 en 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een functie-app in Azure Functions gemaakt met behulp van het [Verbruiksabonnement](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Hiermee wordt een Azure Storage-account gemaakt. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Hiermee maakt u een functie-app. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Hiermee maakt u een Azure Files-share in het opslagaccount. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | Hiermee maakt u een map in de share. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Hiermee koppelt u de share aan de functie-app. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Hiermee worden de bestandsshares weergegeven die zijn gekoppeld aan de functie-app. | 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).

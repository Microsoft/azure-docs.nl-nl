---
title: Azure CLI-voorbeeldscript - Batch-account maken - Batch-service
description: Dit script maakt een Azure Batch-account in de Batch-servicemodus en laat zien hoe u verschillende eigenschappen van het account kunt opvragen of bijwerken.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3d978cf56e92abf9824c116e51f94d53bbe14bbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768319"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-voorbeeld: Een Batch-account maken in Batch-servicemodus

Dit script maakt een Azure Batch-account in de Batch-servicemodus en laat zien hoe u verschillende eigenschappen van het account kunt opvragen of bijwerken. Wanneer u een Batch-account maakt in de standaard Batch-servicemodus, worden de rekenknooppunten ervan intern toegewezen door de Batch-service. Toegewezen rekenknooppunten hebben een afzonderlijk vCPU (kern) quotum en het account kan worden geauthenticeerd via gedeelde sleutelgegevens of een Azure Active Directory-token.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Hiermee wordt de Batch-account gemaakt. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Hiermee maakt u een opslagaccount. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Hiermee worden eigenschappen van het Batch-account bijgewerkt.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Hiermee worden details van het opgegeven Batch-account opgehaald.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Hiermee worden de toegangssleutels van het opgegeven Batch-account opgehaald.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

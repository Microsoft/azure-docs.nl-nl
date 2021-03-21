---
title: Azure CLI-voorbeeldscript - Linux-pool in Batch
description: Dit script toont enkele van de opdrachten die in de Azure CLI beschikbaar zijn voor het maken en beheren van een groep Linux-rekenknooppunten in Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b2e0fbf44be5718cf5577f6bc9aea436968e2fc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073538"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-voorbeeld: Een Linux-groep maken en beheren in Azure Batch

Dit script toont enkele van de opdrachten die in de Azure CLI beschikbaar zijn voor het maken en beheren van een groep Linux-rekenknooppunten in Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Hiermee wordt de Batch-account gemaakt. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Hiermee maakt u een lijst met de beschikbare knooppuntagent-SKU's en Installatiekopiegegevens.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Hiermee wordt een groep rekenknooppunten gemaakt.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Hiermee wordt het aantal actieve VM's in de opgegeven groep aangepast.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Hiermee worden de eigenschappen van een groep opgehaald.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Hiermee wordt een lijst gemaakt van alle rekenknooppunten in de opgegeven groep.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Hiermee wordt het opgegeven rekenknooppunt opnieuw opgestart.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Hiermee worden de vermelde knooppunten uit de opgegeven groep verwijderd.  |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

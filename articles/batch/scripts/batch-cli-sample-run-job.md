---
title: Azure CLI-voorbeeldscript - Een Batch-job uitvoeren
description: Met dit script maakt u een Batch-job en voegt u een reeks taken toe aan de job. U ziet ook hoe u een job en de taken daarvan kunt bewaken.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768157"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-voorbeeld: Een job en taken uitvoeren met Azure Batch

Met dit script maakt u een Batch-job en voegt u een reeks taken toe aan de job. U ziet ook hoe u een job en de taken daarvan kunt bewaken. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Hiermee wordt een groep rekenknooppunten gemaakt.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Hiermee wordt een Batch-taak gemaakt.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Hiermee voegt u een taak toe aan de opgegeven Batch-taak.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Hiermee worden eigenschappen van een Batch-taak bijgewerkt.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Hiermee worden details van een opgegeven Batch-taak opgehaald.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Haalt de details van een taak op uit de opgegeven Batch-job.  |
| [az group delete](/cli/azure/group#az_group_delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

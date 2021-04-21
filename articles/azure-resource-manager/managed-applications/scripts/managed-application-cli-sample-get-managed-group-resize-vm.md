---
title: Beheerde resourcegroep ophalen en grootte van VM's wijzigen - Azure CLI
description: Biedt een Azure CLI-voorbeeldscript voor het ophalen van een beheerde resourcegroep in een door Azure beheerde toepassing. Met het script wordt de grootte van VM's aangepast.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 179b1b64656d3f97778e183d57797e4b3660fece
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775435"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Resources in een beheerde resourcegroep opvragen en de grootte van virtuele machines wijzigen met Azure CLI

Met dit script worden resources opgehaald uit een beheerde resourcegroep en wordt vervolgens de grootte van de virtuele machines in die resourcegroep aangepast.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az_managedapp_list) | Hiermee vraagt u een lijst met de beheerde toepassingen op. Geef querywaarden op om gerichte resultaten te krijgen. |
| [az resource list](/cli/azure/resource#az_resource_list) | Hiermee vraagt u een lijst met resources op. Geef een resourcegroep op en querywaarden om gerichte resultaten te krijgen. |
| [az vm resize](/cli/azure/vm#az_vm_resize) | Hiermee werkt u de grootte van een virtuele machine bij. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

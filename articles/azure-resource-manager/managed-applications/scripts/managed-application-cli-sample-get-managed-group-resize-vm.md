---
title: Beheerde resourcegroep ophalen en grootte van VM's wijzigen - Azure CLI
description: Biedt een Azure CLI-voorbeeldscript voor het ophalen van een beheerde resourcegroep in een door Azure beheerde toepassing. Met het script wordt de grootte van VM's aangepast.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497814"
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
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Hiermee vraagt u een lijst met de beheerde toepassingen op. Geef querywaarden op om gerichte resultaten te krijgen. |
| [az resource list](/cli/azure/resource#az-resource-list) | Hiermee vraagt u een lijst met resources op. Geef een resourcegroep op en querywaarden om gerichte resultaten te krijgen. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Hiermee werkt u de grootte van een virtuele machine bij. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

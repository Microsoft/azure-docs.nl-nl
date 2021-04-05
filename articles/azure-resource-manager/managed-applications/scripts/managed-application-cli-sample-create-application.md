---
title: Voorbeeld van Azure CLI-script - Een beheerde toepassing implementeren
description: Geeft een voorbeeld van een Azure CLI-script voor het implementeren van een beheerde Azure-toepassing op het abonnement.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: a9912f5134c3596740ef53f23531d57fe9467682
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497865"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Een beheerde toepassing implementeren voor de servicecatalogus met Azure CLI

Met dit script implementeert u de definitie van een beheerde toepassing uit de servicecatalogus. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az-managedapp-create) | Hiermee maakt u een beheerde toepassing. Geef de definitie-id en de parameters op voor de sjabloon. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

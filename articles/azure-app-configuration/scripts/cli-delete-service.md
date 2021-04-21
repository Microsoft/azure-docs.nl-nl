---
title: 'Azure CLI-voorbeeldscript: een Azure App Configuration-archief verwijderen'
titleSuffix: Azure App Configuration
description: Een Azure App Configuration-archief verwijderen met behulp van een Azure CLI-voorbeeldscript. Zie koppelingen naar referentieartikelen voor opdrachten die worden gebruikt in het script.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07c4ac3234dc67978f16f6c0c7e0702924bf2bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768881"
---
# <a name="delete-an-azure-app-configuration-store"></a>Een Azure App Configuration-archief verwijderen

Met dit voorbeeldscript verwijdert u een exemplaar van Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten om een App Configuration-archief te verwijderen. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az appconfig kv delete](/cli/azure/appconfig#az_appconfig_delete) | Hiermee verwijdert u een resource voor een App Configuration-archief. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van CLI-scripts voor een App Configuration-archief vindt u in de [Azure App Configuration CLI-voorbeelden](../cli-samples.md).

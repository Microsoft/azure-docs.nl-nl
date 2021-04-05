---
title: Een Premium-exemplaar van Azure Cache voor Redis maken met clustering - Azure CLI
description: Dit voorbeeld met Azure CLI-code laat zien hoe u een exemplaar van Azure Cache voor Redis van 6 GB maakt in de Premium-categorie met clustering ingeschakeld en twee shards.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb5a6ac082ebaf978023321f15341ec7f35779a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184213"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Een Premium-exemplaar van Azure Cache voor Redis maken met clustering

In dit scenario leert u hoe u een exemplaar van Azure Cache voor Redis van 6 GB maakt in de Premium-categorie met clustering ingeschakeld en twee shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten voor het maken van een resourcegroep en een exemplaar van Azure Cache voor Redis in de Premium-categorie met clustering ingeschakeld. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az redis create](/cli/azure/redis) | Een exemplaar van Azure Cache voor Redis maken. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van CLI-scripts voor Azure Cache voor Redis vindt u in de [documentatie van Azure Cache voor Redis](../cli-samples.md).
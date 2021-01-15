---
title: Voor beeld van Azure CLI-script-peering van twee virtuele netwerken | Microsoft Docs
description: Gebruik een Azure CLI-voorbeeld script voor het maken en verbinden van twee virtuele netwerken in dezelfde regio via het Azure-netwerk.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 527a2e0dd5a807552aed8d6a833d513fa40c72e3
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234372"
---
# <a name="use-an-azure-cli-sample-script-to-connect-two-virtual-networks"></a>Een Azure CLI-voorbeeld script gebruiken om twee virtuele netwerken te verbinden

Met dit script maakt en verbindt u twee virtuele netwerken in dezelfde regio via het Azure-netwerk. Nadat het script is uitgevoerd, maakt u een peering tussen twee virtuele netwerken.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Hiermee maakt u een peering tussen twee virtuele netwerken.  |
| [az group delete](/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor netwerken vindt u in de [Documentatie met een overzicht van Azure-netwerken](../cli-samples.md).
---
title: VM-netwerkverkeer filteren - Azure CLI-voorbeeldscript
description: Binnenkomend en uitgaand VM-netwerkverkeer filteren met een Azure CLI-voorbeeldscript.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 86d33ba411c4a0294454a8e1421c35b694feb65d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87492970"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-using-an-azure-cli-script-sample"></a>Binnenkomend en uitgaand VM-netwerkverkeer filteren met een Azure CLI-voorbeeldscript

Met dit voorbeeldscript wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerkverkeer naar het front-end-subnet is beperkt tot HTTP, HTTPS en SSH, terwijl uitgaand verkeer naar internet vanuit het back-end-subnet niet is toegestaan. Nadat het script is uitgevoerd, hebt u een virtuele machine met twee NIC's. Elke NIC is verbonden met een ander subnet.

U kunt het script uitvoeren vanuit de Azure [Cloud Shell](https://shell.azure.com/bash) of vanuit een lokale installatie van de Azure CLI. Als u de CLI lokaal gebruikt, hebt u versie 2.0.28 of hoger nodig om dit script uit te voeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de CLI lokaal uitvoert, moet u ook `az login` uitvoeren om verbinding te maken met Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Voer de volgende opdracht uit om de resourcegroep, VM en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtueel netwerk en netwerkbeveiligingsgroepen. Elke opdracht in de volgende tabel is een koppeling naar opdrachtspecifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en front-end-subnet. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Hiermee maakt u een back-end-subnet. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Hiermee koppelt u NSG's aan subnetten. |
| [az network public-ip create](/cli/azure/network/public-ip) | Hiermee maakt u een openbaar IP-adres voor toegang tot de VM via internet. |
| [az network nic create](/cli/azure/network/nic) | Hiermee maakt u virtuele netwerkinterfaces en koppelt u ze aan de front-end- en back-end-subnetten van het virtuele netwerk. |
| [az network nsg create](/cli/azure/network/nsg) | Hiermee maakt u netwerkbeveiligingsgroepen (NSG's) die zijn gekoppeld aan de front-end- en back-end-subnetten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Hiermee maakt u NSG-regels die bepaalde poorten tot specifieke subnetten blokkeren of toestaan. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u virtuele machines en koppelt u een NIC aan elke virtuele machine. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, evenals de beheerdersreferenties. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep en de bijhorende resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer CLI-voorbeeldscripts voor virtuele netwerken kunt u vinden in [CLI-voorbeelden voor virtuele netwerken](../cli-samples.md).

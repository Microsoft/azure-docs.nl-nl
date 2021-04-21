---
title: Azure CLI-voorbeeldscript - Webverkeer beheren | Microsoft Docs
description: Azure CLI-voorbeeldscript - Webverkeer beheren met een toepassingsgateway en een schaalset voor virtuele machines.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 17cac91ae62f5faa80fa130364bf67f81ad20a81
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789407"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Webverkeer beheren met de Azure CLI

Met dit script maakt u een toepassingsgateway die gebruikmaakt van een virtuele-machineschaalset die is ingesteld voor back-endservers. De toepassingsgateway kan vervolgens worden geconfigureerd om webverkeer te beheren. Nadat het script is uitgevoerd, kunt u de toepassingsgateway testen met behulp van het openbare IP-adres.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, de toepassingsgateway en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel netwerk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Hiermee maakt u een subnet in een virtueel netwerk. |
| [az network public-ip create](/cli/azure/network/public-ip) | Hiermee maakt u het openbare IP-adres voor de toepassingsgateway. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Maak een toepassingsgateway. |
| [az vmss create](/cli/azure/vmss) | Hiermee maakt u een schaalset voor virtuele machines. |
| [az network public-ip show](/cli/azure/network/public-ip) | Hiermee haalt u het openbare IP-adres van de toepassingsgateway op. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure/overview) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor de toepassingsgateway vindt u in de [Documentatie voor Azure Windows-VM's](../cli-samples.md).

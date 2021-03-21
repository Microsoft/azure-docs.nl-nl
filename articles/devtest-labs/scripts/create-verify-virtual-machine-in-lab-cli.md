---
title: Azure CLI - Een virtuele machine maken en controleren in een lab
description: Met dit Azure CLI-script wordt een virtuele machine in een lab gemaakt en gecontroleerd of deze beschikbaar is.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: c7625f62d7897d61903f864b216ccf9aa13648ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198418"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI gebruiken om een virtuele machine in een lab te maken en de beschikbaarheid te controleren in Azure DevTest Labs

Met dit Azure CLI-script wordt een virtuele machine (VM) in een lab gemaakt. De virtuele machine is gemaakt op basis van een Marketplace-installatiekopie met SSH-verificatie. Via dit script wordt gecontroleerd of de virtuele machine beschikbaar is voor gebruik. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az lab vm create ](/cli/azure/lab/vm#az-lab-vm-create) | Hiermee wordt een virtuele machine (VM) in een lab gemaakt. |
| [az lab vm show](/cli/azure/lab/vm#az-lab-vm-show) | Hiermee wordt de status van de virtuele machine in een lab weergegeven. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Er zijn extra voorbeelden van Azure Lab Services CLI-scripts te vinden in de [voorbeelden van Azure Lab Services CLI](../samples-cli.md).

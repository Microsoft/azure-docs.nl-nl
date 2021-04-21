---
title: 'Azure CLI: een virtuele machine in een lab stoppen en verwijderen'
description: Dit artikel bevat een Azure CLI-script waarmee u een virtuele machine in een lab in Azure DevTest Labs kunt stoppen en verwijderen.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: e30ee47c4f34fe6a71e8c934a4f36cb7edbbe20e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777310"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI gebruiken om een virtuele machine in een lab te stoppen en te verwijderen in Azure DevTest Labs

Met dit Azure CLI-script wordt een virtuele machine (VM) in een lab gestopt en verwijderd. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az_lab_vm_stop) | Hiermee wordt een virtuele machine (VM) in een lab gestopt. Deze bewerking kan enige tijd duren. |
| [az lab vm delete](/cli/azure/lab/vm#az_lab_vm_delete) | Hiermee wordt een virtuele machine (VM) in een lab verwijderd. Deze bewerking kan enige tijd duren. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Er zijn extra voorbeelden van Azure Lab Services CLI-scripts te vinden in de [voorbeelden van Azure Lab Services CLI](../samples-cli.md).

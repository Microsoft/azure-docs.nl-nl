---
title: 'PowerShell-script: Toegestane VM-grootten instellen in Azure Lab Services | Microsoft Docs'
description: Dit artikel bevat een voorbeeld van een PowerShell-script waarmee toegestane VM-grootten (virtuele machine) in Azure Lab Services worden ingesteld.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998126"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell gebruiken om toegestane VM-grootten in Azure Lab Services in te stellen

Met dit voorbeeld van een PowerShell-script worden toegestane VM-grootten (virtuele machine) in Azure Lab Services ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab**. Het script vereist dat u een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| Find-AzResource | Zoekt naar resources op basis van opgegeven parameters. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt resources op. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Wijzigt een resource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Een resource maken. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Er zijn extra voorbeelden van Azure Lab Services PowerShell-scripts te vinden in de [PowerShell-voorbeelden van Azure Lab Services CLI](../samples-powershell.md).

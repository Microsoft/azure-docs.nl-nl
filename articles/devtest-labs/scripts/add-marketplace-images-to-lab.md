---
title: 'PowerShell: een marketplace-installatiekopie toevoegen aan een lab in Azure DevTest Labs'
description: Met dit PowerShell-script wordt een marketplace-installatiekopie toegevoegd aan een lab in Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022270"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een marketplace-installatiekopie aan een lab in Azure DevTest Labs toe te voegen

Met dit PowerShell-voorbeeldscript wordt een marketplace-installatiekopie toegevoegd aan een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab**. Het script vereist dat u een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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

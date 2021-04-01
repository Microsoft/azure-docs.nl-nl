---
title: 'PowerShell: een aangepaste rol maken in een lab in Azure DevTest Labs'
description: Dit artikel bevat een Azure PowerShell-script waarmee een aangepaste rol wordt gemaakt in een lab in Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021194"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een aangepaste rol te maken in een lab in Azure DevTest Labs

Met dit voorbeeld van een PowerShell-script maakt u een aangepaste rol in een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab**. Het script vereist dat u een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hiermee worden de bewerkingen opgehaald voor een Azure-resourceprovider die kunnen worden beveiligd met behulp van op rollen gebaseerd toegangsbeheer van Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Hiermee vraagt u een lijst op met alle Azure-functies die beschikbaar zijn voor toewijzing. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Hiermee maakt u een aangepaste rol. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Er zijn extra voorbeelden van Azure Lab Services PowerShell-scripts te vinden in de [PowerShell-voorbeelden van Azure Lab Services CLI](../samples-powershell.md).

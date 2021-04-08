---
title: 'PowerShell: een externe gebruiker toevoegen aan een lab in Azure DevTest Labs'
description: Dit artikel bevat een Azure PowerShell-script waarmee een externe gebruiker aan een lab in Azure DevTest Labs wordt toegevoegd.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022391"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een externe gebruiker aan een lab in Azure DevTest Labs toe te voegen

Met dit PowerShell-script wordt een externe gebruiker aan een lab in Azure DevTest Labs toegevoegd. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab**. Het script vereist dat u een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Het gebruikersobject wordt opnieuw geprobeerd vanuit Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Wijst de opgegeven rol voor het opgegeven bereik aan de opgegeven principal toe. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Er zijn extra voorbeelden van Azure Lab Services PowerShell-scripts te vinden in de [PowerShell-voorbeelden van Azure Lab Services CLI](../samples-powershell.md).

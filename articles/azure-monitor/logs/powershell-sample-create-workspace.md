---
title: Log Analytics-werkruimte maken - Azure PowerShell
description: Voorbeeld van Azure PowerShell-script - Een Log Analytics-werkruimte maken
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: c04eb177eb8c52464be2ab7702a365d7a7b54e07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040937"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Een Log Analytics-werkruimte maken met PowerShell

Met dit script kunt u snel aan de slag met een Azure Log Analytics-werkruimte. Deze is vereist als u gegevens wilt verzamelen, analyseren of er actie op wilt ondernemen.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe Log Analytics-werkruimte voor uw abonnement te maken. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Hiermee wordt informatie opgehaald over een bestaande werkruimte. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Hiermee wordt een werkruimte gemaakt in de opgegeven resourcegroep en locatie. |


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).


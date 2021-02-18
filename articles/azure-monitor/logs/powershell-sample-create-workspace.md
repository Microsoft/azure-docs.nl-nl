---
title: Log Analytics-werkruimte maken - Azure PowerShell
description: Voorbeeld van Azure PowerShell-script - Een Log Analytics-werkruimte maken
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: f6bfb3a244874f6160d34c174b6d10b9a03ca437
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627372"
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


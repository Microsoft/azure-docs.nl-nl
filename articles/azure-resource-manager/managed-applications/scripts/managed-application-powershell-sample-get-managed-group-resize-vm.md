---
title: Beheerde resourcegroep ophalen en grootte van VM's wijzigen - Azure PowerShell
description: Biedt een Azure PowerShell-voorbeeldscript voor het ophalen van een beheerde resourcegroep voor een door Azure beheerde toepassing. Met het script wordt de grootte van VM's aangepast.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86055984"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Resources in een beheerde resourcegroep opvragen en de grootte van virtuele machines wijzigen met Azure PowerShell

Met dit script worden resources opgehaald uit een beheerde resourcegroep en wordt vervolgens de grootte van de virtuele machines in die resourcegroep aangepast.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | Hiermee vraagt u een lijst met de beheerde toepassingen op. Geef de naam op van de resourcegroep waarop u de resultaten wilt focussen. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hiermee vraagt u een lijst met resources op. Geef de resourcegroep en het resourcetype op waarop u het resultaat wilt focussen. |
| [Update-AzVM](/powershell/module/az.compute/update-azvm) | Hiermee werkt u de grootte van een virtuele machine bij. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.

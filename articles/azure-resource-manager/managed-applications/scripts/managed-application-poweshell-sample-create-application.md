---
title: 'Voorbeeld van Azure PowerShell-script: een beheerde toepassing implementeren'
description: Geeft een voorbeeld van een Azure PowerShell-script voor het implementeren van een definitie van een beheerde toepassing op het abonnement.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86055882"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Een beheerde toepassing implementeren voor een servicecatalogus met Azure PowerShell

Met dit script implementeert u de definitie van een beheerde toepassing uit de servicecatalogus.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | Hiermee maakt u een beheerde toepassing. Geef de definitie-id en de parameters op voor de sjabloon. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.

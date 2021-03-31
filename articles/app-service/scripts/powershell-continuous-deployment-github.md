---
title: 'PowerShell: Continu implementeren vanuit GitHub'
description: Ontdek hoe u Azure PowerShell kunt gebruiken om implementatie en beheer van App Service kunt automatiseren. In dit voorbeeld ziet u hoe u een app maakt met CI/CD vanuit GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0ccb108b55844f822a42a3f1bccbb182a5658289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073859"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Een web-app maken met continue implementatie vanuit GitHub

In dit voorbeeldscript wordt een web-app gemaakt in App Service met de bijbehorende resources en wordt er vervolgens [continue implementatie](../deploy-continuous-deployment.md) vanuit een GitHub-opslagplaats ingesteld. Zie [Een web-app maken en code implementeren vanuit GitHub](powershell-deploy-github.md) voor de implementatie van GitHub zonder continue implementatie.

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure. Controleer ook of:

- De code van de toepassing zich bevindt in een openbare of particuliere GitHub-opslagplaats waarvan u eigenaar bent. Als u automatische builds wilt krijgen, structureert u uw opslagplaats volgens de tabel [Uw opslagplaats voorbereiden](../deploy-continuous-deployment.md#prepare-your-repository).
- U hebt [een persoonlijk toegangstoken gemaakt in uw GitHub-account](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een webtoepassing. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Hiermee wijzigt u een resource in een resourcegroep. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).

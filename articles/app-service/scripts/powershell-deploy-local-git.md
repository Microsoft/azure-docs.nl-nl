---
title: 'PowerShell: Implementeren vanuit lokale Git-opslagplaats'
description: Ontdek hoe u Azure PowerShell kunt gebruiken om implementatie en beheer van App Service kunt automatiseren. In dit voorbeeld ziet u hoe u code implementeert vanuit een lokale Git-opslagplaats.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070289"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Een web-app maken en code implementeren vanuit een lokale Git-opslagplaats

Met dit voorbeeldscript wordt er in App Service een web-app met bijbehorende resources gemaakt. Daarna wordt de code van de web-app geïmplementeerd vanuit een lokale Git-opslagplaats.

Gebruik zo nodig de instructie in de [Azure PowerShell-handleiding](/powershell/azure/) om bij te werken naar de nieuwste Azure PowerShell, en voer `Connect-AzAccount` uit om een verbinding te maken met Azure. Daarnaast moet de toepassingscode worden vastgelegd in een lokale Git-opslagplaats.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een web-app met de nodig resourcegroep en App Service-groep. Wanneer de huidige map een Git-opslagplaats bevat, voeg dan ook een externe `azure` toe. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).

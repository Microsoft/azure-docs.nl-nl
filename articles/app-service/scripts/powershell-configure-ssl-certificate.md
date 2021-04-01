---
title: 'PowerShell: TLS/SSL uploaden en binden'
description: Ontdek hoe u Azure PowerShell kunt gebruiken om implementatie en beheer van App Service kunt automatiseren. In dit voorbeeld ziet u hoe u een aangepast TLS/SSL-certificaat aan een app bindt.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 74bec2e8793331019a6eca986446880c10565aeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079945"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Een aangepast TLS/SSL-certificaat koppelen aan een web-app met PowerShell

Met dit voorbeeldscript wordt een web-app in App Service gemaakt met de bijbehorende resources, waarna het TLS/SSL-certificaat van een aangepaste domeinnaam daaraan wordt gekoppeld. 

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure. Controleer ook of:

- Een verbinding met Azure is gemaakt met behulp van de opdracht `az login`.
- U hebt toegang tot de pagina voor DNS-configuratie van uw domeinregistrar.
- U hebt een geldig .PFX-bestand en het bijbehorende wachtwoord voor het TLS/SSL-certificaat dat u wilt uploaden en koppelen.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Hiermee bewerkt u een App Service-plan om de prijscategorie te wijzigen. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Hiermee wijzigt u de configuratie van een web-app. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Hiermee maakt u een TLS/SSL-certificaatbinding voor een web-app. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).

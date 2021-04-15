---
title: 'PowerShell-voorbeeld: apps exporteren met verlopende geheimen en certificaten in Azure Active Directory tenant.'
description: PowerShell-voorbeeld waarin alle apps met verlopende geheimen en certificaten voor de opgegeven apps in uw tenant Azure Active Directory exporteert.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375403"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Apps exporteren met verlopende geheimen en certificaten

Met dit PowerShell-voorbeeldscript exporteert u alle app-registraties met verlopende geheimen, certificaten en hun eigenaren voor de opgegeven apps uit uw map in een CSV-bestand.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

Het script kan rechtstreeks zonder wijzigingen worden gebruikt. De beheerder wordt gevraagd naar de vervaldatum en of deze al verlopen geheimen of certificaten wil zien.

De opdracht Add-Member is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
Met de opdracht New-Object maakt u een -object dat moet worden gebruikt voor de kolommen in de CSV-bestandsexport.
U kunt de variabele '$Path' rechtstreeks in PowerShell wijzigen met een CSV-bestandspad, voor het geval u liever niet-interactief exporteert.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u een toepassing op uit uw directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u de eigenaren van een toepassing op uit uw directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie Azure AD PowerShell-voorbeelden voor toepassingsbeheer voor andere [PowerShell-voorbeelden voor toepassingsbeheer.](../app-management-powershell-samples.md)

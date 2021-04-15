---
title: 'PowerShell-voorbeeld: geheimen en certificaten exporteren voor zakelijke apps in Azure Active Directory tenant.'
description: PowerShell-voorbeeld waarin alle geheimen en certificaten voor de opgegeven bedrijfsapps in uw tenant Azure Active Directory exporteert.
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
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378598"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Geheimen en certificaten exporteren voor bedrijfs-apps
In dit PowerShell-voorbeeldscript worden alle geheimen, certificaten en eigenaren voor de opgegeven bedrijfsapps vanuit uw directory naar een CSV-bestand geexporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

De opdracht Add-Member is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
U kunt de variabele $Path rechtstreeks in PowerShell wijzigen met een CSV-bestandspad, voor het geval u liever niet-interactief exporteert.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u een bedrijfstoepassing op uit uw directory. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u de eigenaren van een bedrijfstoepassing op uit uw directory. |


## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie Azure AD PowerShell-voorbeelden voor toepassingsbeheer voor andere PowerShell-voorbeelden [voor toepassingsbeheer.](../app-management-powershell-samples.md)

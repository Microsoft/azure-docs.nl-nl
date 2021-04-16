---
title: 'PowerShell-voorbeeld: exporteert geheimen en certificaten voor app-registraties in Azure Active Directory tenant.'
description: PowerShell-voorbeeld waarin alle geheimen en certificaten voor de opgegeven app-registraties in uw tenant Azure Active Directory exporteert.
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
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377021"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Geheimen en certificaten voor app-registraties exporteren

In dit PowerShell-voorbeeldscript worden alle geheimen en certificaten voor de opgegeven app-registraties vanuit uw map naar een CSV-bestand geexporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

De opdracht Add-Member is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
U kunt de variabele $Path rechtstreeks in PowerShell wijzigen met een CSV-bestandspad, voor het geval u liever niet-interactief exporteert.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee haalt u een toepassing op uit uw directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Hiermee haalt u de eigenaren van een toepassing op uit uw directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie Azure AD PowerShell-voorbeelden voor toepassingsbeheer voor andere PowerShell-voorbeelden [voor toepassingsbeheer.](../app-management-powershell-samples.md)

---
title: 'Power shell-voor beeld: Exporteer apps met geheimen en certificaten die na de vereiste datum in Azure Active Directory Tenant verlopen.'
description: Power shell-voor beeld van het exporteren van alle apps met geheimen en certificaten die na de vereiste datum voor de opgegeven apps in uw Azure Active Directory-Tenant verlopen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 3572f481cc2cbcb1df73b33eb2543e32256ad9fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584293"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Apps exporteren met geheimen en certificaten die na de vereiste datum verlopen

Met dit Power shell-voorbeeld script worden alle app-geheimen en certificaten die na de vereiste datum voor de opgegeven apps uit uw map in een CSV-bestand verlopen, geëxporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

De opdracht ' lid toevoegen ' is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
U kunt de variabele ' $Path ' rechtstreeks in Power shell wijzigen, met een pad naar een CSV-bestand, als u de voor keur geeft aan het exporteren naar niet-interactief.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u een toepassing op uit uw Directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u de eigen aren van een toepassing uit uw Directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.

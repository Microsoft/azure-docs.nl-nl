---
title: 'Power shell-voor beeld: apps exporteren met verlopen geheimen en certificaten in Azure Active Directory Tenant.'
description: Power shell-voor beeld dat alle apps met verlopen geheimen en certificaten voor de opgegeven apps in uw Azure Active Directory Tenant exporteert.
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
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149695"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Apps met verlopen geheimen en certificaten exporteren

Met dit Power shell-voorbeeld script worden alle app-registraties geëxporteerd met verlopen geheimen, certificaten en hun eigen aren voor de opgegeven apps uit uw map in een CSV-bestand.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

Het script kan rechtstreeks zonder wijzigingen worden gebruikt. De beheerder wordt gevraagd om de verval datum en of ze al verlopen geheimen of certificaten willen zien.

De opdracht ' lid toevoegen ' is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
De opdracht ' nieuw-object ' maakt een object dat moet worden gebruikt voor de kolommen in het CSV-bestand dat wordt geëxporteerd.
U kunt de variabele ' $Path ' rechtstreeks in Power shell wijzigen, met een pad naar een CSV-bestand, als u de voor keur geeft aan het exporteren naar niet-interactief.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u een toepassing op uit uw Directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u de eigen aren van een toepassing uit uw Directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.

---
title: 'Power shell-voor beeld: geheimen exporteren en certificaten voor app-registraties in Azure Active Directory Tenant.'
description: Power shell-voor beeld dat alle geheimen en certificaten voor de opgegeven app-registraties in uw Azure Active Directory Tenant exporteert.
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
ms.openlocfilehash: d0de96d0d8a5edc6fbacc25dcbcb868073e57183
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556550"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Geheimen en certificaten voor app-registraties exporteren

Met dit Power shell-voorbeeld script worden alle geheimen en certificaten voor de opgegeven app-registraties vanuit uw directory geëxporteerd naar een CSV-bestand.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

De opdracht ' lid toevoegen ' is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
U kunt de variabele ' $Path ' rechtstreeks in Power shell wijzigen, met een pad naar een CSV-bestand, als u de voor keur geeft aan het exporteren naar niet-interactief.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee haalt u een toepassing op uit uw Directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Hiermee haalt u de eigen aren van een toepassing uit uw Directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.

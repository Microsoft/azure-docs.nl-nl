---
title: 'Power shell-voor beeld: geheimen exporteren en certificaten voor zakelijke apps in Azure Active Directory Tenant.'
description: Power shell-voor beeld van het exporteren van alle geheimen en certificaten voor de opgegeven zakelijke apps in uw Azure Active Directory-Tenant.
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
ms.openlocfilehash: 20caefe74a7c047fb8690bb1d9e6f4eb9da7e9b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635192"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Geheimen en certificaten voor zakelijke apps exporteren
Met dit Power shell-voorbeeld script worden alle geheimen, certificaten en eigen aren van de opgegeven zakelijke apps vanuit uw directory geëxporteerd naar een CSV-bestand.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

De opdracht ' lid toevoegen ' is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.
U kunt de variabele ' $Path ' rechtstreeks in Power shell wijzigen, met een pad naar een CSV-bestand, als u de voor keur geeft aan het exporteren naar niet-interactief.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u een bedrijfs toepassing op uit uw Directory. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Hiermee haalt u de eigen aren van een bedrijfs toepassing uit uw Directory. |


## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.

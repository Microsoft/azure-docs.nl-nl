---
title: 'Power shell-voor beeld: app-registraties, geheimen en certificaten exporteren in Azure Active Directory Tenant.'
description: Power shell-voor beeld van het exporteren van alle app-registraties, geheimen en certificaten voor de opgegeven apps in uw Azure Active Directory-Tenant.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 768f2f3241144085acb7a218b60034cdfa9e45b9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102185373"
---
# <a name="export-app-registrations-secrets-and-certificates"></a>App-registraties, geheimen en certificaten exporteren

Met dit Power shell-voorbeeld script worden alle app-registraties, geheimen en certificaten voor de opgegeven apps in uw directory geëxporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all app registrations, secrets, and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Exporteert alle app-registraties, geheimen en certificaten voor de opgegeven apps in uw Directory. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.

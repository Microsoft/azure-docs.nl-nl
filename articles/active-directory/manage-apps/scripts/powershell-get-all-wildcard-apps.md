---
title: 'PowerShell-voorbeeld: toepassingsproxy-apps die gebruikmaken van jokertekens vermelden'
description: PowerShell-voorbeeld waarin alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen worden vermeld die gebruikmaken van jokertekens.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8968f97d64fe66ac203796654a8e243b1b2e31be
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858838"
---
# <a name="get-all-application-proxy-apps-using-wildcard-publishing"></a>Alle toepassingsproxy-apps ophalen die gebruikmaken van jokertekens

In dit PowerShell-voorbeeldscript worden alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen vermeld die voor publicatie gebruikmaken van jokertekens.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-wildcard-apps.ps1 "Get all Application Proxy apps using wildcards")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hiermee wordt een service-principal opgehaald. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee wordt een Azure AD-toepassing opgehaald. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hiermee wordt een toepassing opgehaald die is geconfigureerd voor een toepassingsproxy in Azure AD. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

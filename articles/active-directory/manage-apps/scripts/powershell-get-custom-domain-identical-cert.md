---
title: 'PowerShell-voorbeeld: toepassingsproxy-apps met identieke certificaten'
description: PowerShell-voorbeeld waarin alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen worden vermeld die met een identiek certificaat worden gepubliceerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a45333b9df2e75e443ed40568943c03c703799f3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044104"
---
# <a name="get-all-application-proxy-apps-that-are-published-with-the-identical-certificate"></a>Alle toepassings proxy-apps ophalen die met hetzelfde certificaat zijn gepubliceerd

In dit PowerShell-voorbeeld worden alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen vermeld die met een identiek certificaat worden gepubliceerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hiermee wordt een service-principal opgehaald. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee wordt een Azure AD-toepassing opgehaald. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hiermee wordt een toepassing opgehaald die is geconfigureerd voor een toepassingsproxy in Azure AD. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

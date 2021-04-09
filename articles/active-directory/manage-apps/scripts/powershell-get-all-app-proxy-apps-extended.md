---
title: 'PowerShell-voorbeeld: uitgebreide informatie voor toepassingsproxy-apps vermelden'
description: PowerShell-voorbeeld waarin alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen worden vermeld, samen met de toepassings-id (AppId), de naam (DisplayName), de externe URL (ExternalUrl), de interne URL (InternalUrl) en het verificatietype (ExternalAuthenticationType).
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
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258673"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Alle toepassingsproxy-apps ophalen en uitgebreide informatie vermelden

In dit voor beeld van een Power shell-script wordt informatie weer gegeven over alle Azure Active Directory-toepassings proxy toepassingen (Azure AD), waaronder de toepassings-ID (AppId), de naam (DisplayName), de externe URL (ExternalUrl), de interne URL (InternalUrl), het verificatie type (ExternalAuthenticationType), de SSO-modus en verdere instellingen.

Als u de waarde van de $ssoMode variabele wijzigt, wordt een gefilterde uitvoer per SSO-modus ingeschakeld. Meer informatie vindt u in het script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voor beeld is de [AzureAD v2 Power shell voor Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hiermee wordt een service-principal opgehaald. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee wordt een Azure AD-toepassing opgehaald. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hiermee wordt een toepassing opgehaald die is geconfigureerd voor een toepassingsproxy in Azure AD. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

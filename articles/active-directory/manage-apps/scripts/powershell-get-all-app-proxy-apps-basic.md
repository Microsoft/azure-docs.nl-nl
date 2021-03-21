---
title: 'PowerShell-voorbeeld: basisinformatie voor toepassingsproxy-apps vermelden'
description: PowerShell-voorbeeld waarin Azure Active Directory (Azure AD)-toepassingsproxytoepassingen worden vermeld, samen met de toepassings-id (AppId), de naam (DisplayName) en de object-id (ObjId).
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
ms.openlocfilehash: 0a5ee23a16c10a579c9b39ce3589028603ea7a3c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550617"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Alle toepassingsproxy-apps ophalen en basisinformatie vermelden

In dit PowerShell-voorbeeld wordt informatie over alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen vermeld, inclusief de toepassings-id (AppId), de naam (DisplayName) en de object-id (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.
## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hiermee wordt een service-principal opgehaald. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

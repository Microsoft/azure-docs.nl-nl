---
title: 'PowerShell-voorbeeld: toepassingsproxy-apps naar een andere groep verplaatsen'
description: PowerShell-voorbeeld van Azure Active Directory (Azure AD) dat wordt gebruikt om alle toepassingen die momenteel aan een connectorgroep zijn toegewezen, naar een andere connectorgroep te verplaatsen.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: eb421a1a8040a406c8f3e4be04f9a99d881237d9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376916"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Alle apps die aan een connectorgroep zijn toegewezen, naar een andere connectorgroep verplaatsen

In dit PowerShell-voorbeeld worden alle Azure Active Directory (Azure AD)-toepassingsproxytoepassingen die momenteel aan een connectorgroep zijn toegewezen, naar een andere connectorgroep verplaatst.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hiermee wordt een service-principal opgehaald. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hiermee wordt een Azure AD-toepassing opgehaald. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Hiermee wordt een lijst opgehaald met alle connectorgroepen of, indien opgegeven, de details van de opgegeven connectorgroep. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Hiermee wordt de opgegeven connectorgroep aan een bepaalde toepassing toegewezen.|

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

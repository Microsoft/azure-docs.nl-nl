---
title: 'PowerShell-voorbeeld: alle connectorgroepen van de toepassingsproxy vermelden'
description: PowerShell-voorbeeld waarin alle connectorgroepen en connectors van de Azure Active Directory (Azure AD)-toepassingsproxy in uw map worden vermeld.
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
ms.openlocfilehash: 032d4930e29126ec9636e710f016d9612dc2cda0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377561"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Alle connectorgroepen en connectors in de map ophalen

In dit PowerShell-voorbeeldscript worden alle connectorgroepen en connectors van de Azure Active Directory (Azure AD)-toepassingsproxy in uw map vermeld.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) (AzureAD) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) vereist.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Uitleg van het script

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Hiermee wordt een lijst opgehaald met alle connectorgroepen of, indien opgegeven, de details van de opgegeven connectorgroep. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Hiermee worden alle connectors van de toepassingsproxy opgehaald die aan alle connectorgroepen zijn gekoppeld.|

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD PowerShell-voorbeelden voor de Azure AD-toepassingsproxy](../application-proxy-powershell-samples.md) voor andere PowerShell-voorbeelden voor de toepassingsproxy.

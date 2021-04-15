---
title: PowerShell-voorbeelden voor Azure Active Directory Application Management
description: Deze PowerShell-voorbeelden worden gebruikt voor apps die u beheert in uw Azure Active Directory tenant. U kunt deze voorbeeldscripts gebruiken om verloopinformatie over geheimen en certificaten te vinden.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378819"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-voorbeelden voor toepassingsbeheer

De volgende tabel bevat koppelingen naar PowerShell-scriptvoorbeelden voor Azure AD Application Management. Voor deze voorbeelden is het volgende vereist:
- De [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2) of,
- De [preview-versie van de AzureAD V2 PowerShell voor Graph-module,](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)tenzij anders vermeld.

Zie Toepassingen voor meer informatie over de cmdlets die in deze voorbeelden [worden gebruikt.](/powershell/module/azuread/#applications)

| Koppeling | Beschrijving |
|---|---|
|**Scripts voor toepassingsbeheer**||
| [Geheimen en certificaten exporteren (app-registraties)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exporteert geheimen en certificaten voor app-registraties in Azure Active Directory tenant. |
| [Geheimen en certificaten exporteren (bedrijfsapps)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exporteert geheimen en certificaten voor bedrijfsapps in Azure Active Directory tenant. |
| [Verlopende geheimen en certificaten exporteren](scripts/powershell-export-apps-with-expriring-secrets.md) | Exporteert app-registraties met verlopende geheimen en certificaten en hun eigenaren in Azure Active Directory tenant. |
| [Geheimen en certificaten exporteren die na de vereiste datum verlopen](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exporteert app-registraties met geheimen en certificaten die na de vereiste datum verlopen in Azure Active Directory tenant. Hierbij wordt de niet-interactieve Client_Credentials Oauth-stroom gebruikt. |

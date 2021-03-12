---
title: Power shell-voor beelden voor Azure Active Directory toepassings beheer
description: Deze Power shell-voor beelden worden gebruikt voor apps die u beheert in uw Azure Active Directory-Tenant. U kunt deze voorbeeld scripts gebruiken om informatie over de verval datum van geheimen en certificaten te vinden.
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
ms.openlocfilehash: f5f7ec8245a43440a400b9ca6b55bf1093eb62cc
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636182"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory Power shell-voor beelden voor toepassings beheer

De volgende tabel bevat koppelingen naar Power shell-script voorbeelden voor Azure AD-toepassings beheer. De volgende voor beelden zijn vereist:
- De [AzureAD v2 Power shell voor Graph-module](/powershell/azure/active-directory/install-adv2) of,
- De [Preview-versie van de AzureAD v2 Power shell voor Graph-module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), tenzij anders vermeld.

Zie [toepassingen](/powershell/module/azuread/#applications)voor meer informatie over de cmdlets die in deze voor beelden worden gebruikt.

| Koppeling | Beschrijving |
|---|---|
|**Scripts voor toepassings beheer**||
| [Geheimen en certificaten exporteren (app-registraties)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exporteer geheimen en certificaten voor app-registraties in Azure Active Directory Tenant. |
| [Geheimen en certificaten exporteren (Enter prise-apps)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exporteer geheimen en certificaten voor zakelijke apps in Azure Active Directory Tenant. |
| [Verlopen geheimen en certificaten exporteren](scripts/powershell-export-apps-with-expriring-secrets.md) | App-registraties exporteren met verlopen geheimen en certificaten en hun eigen aren in Azure Active Directory Tenant. |
| [Exporteren van geheimen en certificaten die na de vereiste datum verlopen](scripts/powershell-export-apps-with-secrets-beyond-required.md) | App-registraties exporteren met geheimen en certificaten die na de vereiste datum in Azure Active Directory Tenant verlopen. Hierbij wordt gebruikgemaakt van de niet-interactieve Client_Credentials OAuth-stroom. |

---
title: Voorbeelden van PowerShell voor Azure AD-toepassingsproxy
description: Gebruik deze PowerShell-voorbeelden voor Azure AD-toepassingsproxy om informatie te krijgen over toepassingsproxy-apps en -connectors in uw map, gebruikers en groepen toe te wijzen aan apps en certificaatgegevens op te halen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f10e2b0aa5c7e178ae6b58805703fcb1b0ff3e2b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352487"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell-voorbeelden voor Azure AD-toepassingsproxy

De volgende tabel bevat koppelingen naar PowerShell-scriptvoorbeelden voor Azure AD-toepassingsproxy. Voor deze voorbeelden is de [AzureAD V2 PowerShell voor Graph-module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) of de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) vereist, tenzij anders vermeld.


Voor meer informatie over de cmdlets die in deze voorbeelden worden gebruikt, raadpleegt u [Toepassingsbeheer voor toepassingsproxy](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) en [Connector-beheer voor toepassingsproxy](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| Koppeling | Beschrijving |
|---|---|
|**Toepassingsproxy-apps**||
| [Basisinformatie vermelden voor alle toepassingsproxy-apps](scripts/powershell-get-all-app-proxy-apps-basic.md) | Bevat basisinformatie (AppId, DisplayName, ObjId) over alle toepassingsproxy-apps in uw map. |
| [Uitgebreide informatie vermelden voor alle toepassingsproxy-apps](scripts/powershell-get-all-app-proxy-apps-extended.md) | Bevat uitgebreide informatie (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) over alle toepassingsproxy-apps in uw map.  |
| [Alle toepassingsproxy-apps per connectorgroep vermelden](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Bevat informatie over alle toepassingsproxy-apps in uw map en aan welke connectorgroepen de apps worden toegewezen. |
| [Alle toepassingsproxy-apps ophalen met een beleid voor de levensduur van het token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Bevat alle toepassingsproxy-apps in uw map met een levensduurbeleid voor tokens en de bijbehorende details. Voor dit voorbeeld is de [AzureAD V2 PowerShell voor Graph-module (preview)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) vereist. |
|**Connectorgroepen**||
| [Alle connectorgroepen en connectors in de map ophalen](scripts/powershell-get-all-connectors.md) | Bevat alle connectorgroepen en connectors in uw map. |
| [Alle apps die aan een connectorgroep zijn toegewezen, naar een andere connectorgroep verplaatsen](scripts/powershell-move-all-apps-to-connector-group.md) | Hiermee verplaatst u alle toepassingen die momenteel aan een connectorgroep zijn toegewezen naar een andere connectorgroep. |
|**Toegewezen gebruikers en groep**||
| [Gebruikers en groepen weergeven die zijn toegewezen aan een toepassingsproxytoepassing](scripts/powershell-display-users-group-of-app.md) | Bevat de gebruikers en groepen die zijn toegewezen aan een specifieke toepassingsproxytoepassing. |
| [Een gebruiker toewijzen aan een toepassing](scripts/powershell-assign-user-to-app.md) | Hiermee wordt een specifieke gebruiker toegewezen aan een toepassing. |
| [Een groep toewijzen aan een toepassing](scripts/powershell-assign-group-to-app.md) | Hiermee wordt een specifieke groep toegewezen aan een toepassing. |
|**Configuratie van externe URL**||
| [Alle toepassingsproxy-apps ophalen die gebruikmaken van standaarddomeinen (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Bevat alle toepassingsproxy-apps die gebruikmaken van standaarddomeinen (.msappproxy.net). |
| [Alle toepassingsproxy-apps ophalen die gebruikmaken van jokertekens](scripts/powershell-get-all-wildcard-apps.md) | Bevat alle toepassingsproxy-apps die gebruikmaken van jokertekens. |
|**Custom Domain-configuratie**||
| [Alle toepassingsproxy-apps ophalen die gebruikmaken van aangepaste domeinen en certificaatgegevens](scripts/powershell-get-all-custom-domains-and-certs.md) | Bevat alle toepassingsproxy-apps die gebruikmaken van aangepaste domeinen en de certificaatinformatie die is gekoppeld aan de aangepaste domeinen. |
| [Alle Azure AD-toepassingsproxy-apps publiceren waarvoor geen certificaat is geüpload](scripts/powershell-get-all-custom-domain-no-cert.md) | Bevat alle toepassingsproxy-apps die gebruikmaken van aangepaste domeinen maar waarvoor geen geldig TLS/SSL-certificaat is geüpload. |
| [Alle Azure AD-toepassingsproxytoepassingen ophalen die met een identiek certificaat worden gepubliceerd](scripts/powershell-get-custom-domain-identical-cert.md) | Bevat alle Azure AD-toepassingsproxytoepassingen die met een identiek certificaat worden gepubliceerd. |
| [Alle Azure AD-toepassingsproxytoepassingen ophalen die met een identiek certificaat worden gepubliceerd en dit vervangen](scripts/powershell-get-custom-domain-replace-cert.md) | Voor Azure AD-toepassingsproxy-apps die met een identiek certificaat worden gepubliceerd, kunt u het certificaat in bulk vervangen. |
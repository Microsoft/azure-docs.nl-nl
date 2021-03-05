---
title: Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
description: Azure RBAC inschakelen om toegang te verlenen tot uw Azure-app-configuratie-exemplaar
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: f29be1807dfcc314c89d30301107670a970263ce
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172872"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
Naast het gebruik van op hash gebaseerde Message Authentication Code (HMAC), ondersteunt Azure-app configuratie het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor app-configuratie-exemplaren.  Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een beveiligings-principal.  Een beveiligingsprincipal kan een gebruiker, een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) of een [Application Service-Principal](../active-directory/develop/app-objects-and-service-principals.md)zijn.  Zie voor meer informatie over rollen en roltoewijzingen [wat verschillende rollen zijn](../role-based-access-control/overview.md).

## <a name="overview"></a>Overzicht
Aanvragen van een beveiligingsprincipal om toegang te krijgen tot een app-configuratie bron moeten worden geautoriseerd. Met Azure AD is toegang tot een resource een proces in twee stappen:
1. De identiteit van de beveiligingsprincipal wordt geverifieerd en er wordt een OAuth 2,0-token geretourneerd.  De naam van de resource om een token aan te vragen, `https://login.microsoftonline.com/{tenantID}` `{tenantID}` komt overeen met de Azure Active Directory Tenant-ID waartoe de service-principal behoort.
2. Het token wordt door gegeven als onderdeel van een aanvraag bij de app Configuration-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime.  Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure Functions-app, een Azure-web-app of een Azure-VM, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources.  Zie voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden gemaakt naar Azure-app configuratie [verifiëren van toegang tot Azure-app configuratie resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](howto-integrate-azure-managed-service-identity.md).

De autorisatie stap vereist dat er een of meer Azure-rollen aan de beveiligingsprincipal worden toegewezen. Azure-app-configuratie biedt Azure-rollen die sets machtigingen voor app-configuratie resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die aan de principal worden gegeven. Zie voor meer informatie over Azure-rollen [ingebouwde rollen van Azure voor Azure-app configuratie](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Azure-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/overview.md).

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang tot de configuratie bron van de app wordt beperkt. Een beveiligings-principal van Azure AD kan een gebruiker of een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Ingebouwde rollen van Azure voor Azure-app configuratie
Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- **Eigenaar van app-configuratie gegevens**: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- **Gegevens lezer app-configuratie**: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- **Inzender**: gebruik deze rol om de configuratie bron van de app te beheren. Terwijl de app-configuratie gegevens kunnen worden geopend met behulp van toegangs sleutels, verleent deze rol geen directe toegang tot de gegevens met behulp van Azure AD.
- **Lezer**: gebruik deze rol om Lees toegang te verlenen aan de configuratie bron van de app. Hiermee wordt geen toegang verleend tot de toegangs sleutels van de resource en evenmin op de gegevens die zijn opgeslagen in de app-configuratie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van [beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor het beheren van uw app-configuratie service.

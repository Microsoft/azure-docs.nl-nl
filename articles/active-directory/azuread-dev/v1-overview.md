---
title: Overzicht van Azure Active Directory voor ontwikkelaars (v1.0)
description: Dit artikel geeft een overzicht van het aanmelden bij werk- en schoolaccounts van Microsoft met het Azure Active Directory v1.0-eindpunt en -platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117665"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Overzicht van Azure Active Directory voor ontwikkelaars (v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) is een identiteitsservice in de cloud waarmee ontwikkelaars apps kunnen maken die ervoor zorgen dat elke gebruiker met een Microsoft-werkaccount of -schoolaccount zich veilig kan aanmelden. Azure AD biedt ondersteuning voor ontwikkelaars die Line-Of-Business-apps met één tenant maken en voor ontwikkelaars die apps met meerdere tenants willen ontwikkelen. Naast eenvoudige aanmelding maakt Azure AD ook mogelijk dat apps Microsoft-API's zoals [Microsoft Graph](/graph/overview) en aangepaste API's die zijn gemaakt op het Azure AD-platform aanroepen. In deze documentatie wordt beschreven hoe u Azure AD-ondersteuning aan uw app kunt toevoegen met standaardprotocollen zoals OAuth 2.0 en OpenID Connect.

> [!NOTE]
> De meeste inhoud op deze pagina is gericht op het v1.0-eindpunt en -platform, dat alleen ondersteuning biedt voor Microsoft-werkaccounts en -schoolaccounts. Raadpleeg de informatie over het [v2.0-eindpunt en -platform](../develop/v2-overview.md) als u persoonlijke Microsoft-accounts of Microsoft-accounts van klanten wilt aanmelden. Het v2.0-eindpunt biedt een geïntegreerde ontwikkelaarservaring voor apps die alle Microsoft-identiteiten willen aanmelden.

- [Basisbeginselen van verificatie](v1-authentication-scenarios.md) Een inleiding tot verificatie met Azure AD.
- [Soorten toepassingen](app-types.md) Een overzicht van de verificatiescenario's die worden ondersteund door Azure AD.

## <a name="get-started"></a>Aan de slag

De v1.0-quickstarts en -zelfstudies begeleiden u bij het maken van een app op het platform van uw voorkeur met behulp van de Azure AD Authentication Library (ADAL) SDK. Raadpleeg de **v1.0-quickstarts** en **v1.0-zelfstudies** in [Microsoft Identity Platform (Azure Active Directory voor ontwikkelaars)](index.yml) om aan de slag te gaan.

## <a name="how-to-guides"></a>Instructiegidsen

Raadpleeg de **v1.0-instructiegidsen** voor gedetailleerde informatie en overzichten van de meest algemene taken in Azure AD.

## <a name="reference-topics"></a>Onderwerpen met naslaginformatie

De volgende artikelen bieden gedetailleerde informatie over API's, protocolberichten en termen die worden gebruikt in Azure AD.

- [Authentication Libraries (ADAL)](active-directory-authentication-libraries.md) Een overzicht van de bibliotheken en SDK's die worden geleverd door Azure AD.
- [Codevoorbeelden](sample-v1-code.md) Een lijst met alle Azure AD-codevoorbeelden.
- [Woordenlijst](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Termen en definities van woorden die in deze documentatie worden gebruikt.

## <a name="videos"></a>Video's

Raadpleeg [video’s over het Azure Active Directory-ontwikkelaarsplatform](videos.md) voor hulp bij het migreren naar het nieuwe Microsoft Identity Platform.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
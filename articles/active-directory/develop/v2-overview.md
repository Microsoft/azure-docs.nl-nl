---
title: Overzicht van het Microsoft-identiteitsplatform - Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de onderdelen van het Microsoft-identiteitsplatform en hoe u deze kunt gebruiken om ondersteuning voor identiteits- en toegangsbeheer (IAM) in uw toepassingen te bouwen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: e5a9292097933ff35eb8b77445f07f10def6e5c3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029614"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Wat is het Microsoft Identity Platform?

Met het Microsoft-identiteitsplatform bouwt u toepassingen waarbij uw gebruikers en klanten zich kunnen aanmelden met hun Microsoft-identiteiten of sociale accounts en toegang verkrijgen tot uw eigen API's of Microsoft-API's, zoals Microsoft Graph.

Het Microsoft-identiteitsplatform bestaat uit verschillende onderdelen:

- De **OAuth 2.0- en OpenID Connect-verificatieservice die compliant zijn** stellen ontwikkelaars in staat om verschillende identiteitstypes te verifiëren, waaronder:
  - Werk- of schoolaccounts, ingericht met Azure AD
  - Persoonlijke Microsoft-accounts, zoals Skype, Xbox en Outlook.com
  - Sociale of lokale accounts, met behulp van Azure AD B2C
- **Opensource-bibliotheken**: Microsoft Authentication Libraries (MSAL) en ondersteuning voor bibliotheken die niet compliant zijn
- **Beleid voor toepassingsbeheer**: Een registratie- en configuratie-ervaring in het Azure-portal, samen met de andere beheersmogelijkheden voor Azure.
- **Toepassingsconfiguratie-API en PowerShell**: Programmatische configuratie van uw toepassingen via de Microsoft Graph-API en PowerShell, zodat u uw DevOps-taken kunt automatiseren.
- **Inhoud voor ontwikkelaars**: Technische documentatie, waaronder quickstarts, zelfstudies, handleidingen en codevoorbeelden.

Voor ontwikkelaars biedt het Microsoft-identiteitsplatform integratie van moderne innovaties op het gebied van identiteits- en beveiligingsruimte, zoals verificatie zonder wachtwoord, aanvullende verificatie en voorwaardelijke toegang. U hoeft deze functionaliteit niet zelf te implementeren: het Microsoft-identiteitsplatform bevat standaard toepassingen die dergelijke innovaties gebruiken.

Met het Microsoft-identiteitsplatform kunt u één keer code schrijven en elke gebruiker bereiken. U kunt een app één keer bouwen voor verschillende platforms, of een app bouwen die als client en als resourcetoepassing (API) werkt.

## <a name="getting-started"></a>Aan de slag

Kies het [toepassingsscenario](authentication-flows-app-scenarios.md) dat u wilt bouwen. Elk van deze scenariopaden begint met een overzicht en koppelingen naar een quickstart waarmee u aan de slag kunt:

- [App met één pagina (SPA)](scenario-spa-overview.md)
- [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)
- [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md)
- [Beveiligde web-API](scenario-protected-web-api-overview.md)
- [Web-API die web-API's aanroept](scenario-web-api-call-api-overview.md)
- [Desktop-app](scenario-desktop-overview.md)
- [Daemon-apps](scenario-daemon-overview.md)
- [Mobiele app](scenario-mobile-overview.md)

Wanneer u het Microsoft-identiteitsplatform gebruikt om verificatie en autorisatie te integreren in uw apps, kunt u deze afbeelding raadplegen waarop de meest gebruikelijke toepassingsscenario's en hun identiteitscomponenten worden weergegeven. Selecteer de afbeelding om deze in volledig formaat weer te geven.

[![Metrokaart met verschillende toepassingsscenario's in het Microsoft-identiteitsplatform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Meer informatie over concepten voor verificatie

Meer informatie over de basisverificatie- en Azure AD-concepten die van toepassing zijn op het Microsoft-identiteitsplatform in deze aanbevolen artikelen:

- [De basisbeginselen van verificatie](./authentication-vs-authorization.md)
- [Toepassing en service-principals](app-objects-and-service-principals.md)
- [Doelgroepen](v2-supported-account-types.md)
- [Machtigingen en toestemming](v2-permissions-and-consent.md)
- [Id-tokens](id-tokens.md)
- [Toegangstokens](access-tokens.md)
- [Verificatiestromen en app-scenario's](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Meer opties voor identiteits- en toegangsbeheer

[Azure AD B2C](../../active-directory-b2c/overview.md) - Bouw klantgerichte toepassingen waarmee gebruikers zich kunnen aanmelden met hun sociale accounts zoals Facebook of Google, of met een e-mail adres en een wachtwoord.

[Azure AD B2B](../external-identities/what-is-b2b.md) - Nodig externe gebruikers in uw Azure AD-tenant uit in als 'gastgebruikers' en wijs machtigingen toe voor autorisatie, terwijl ze hun bestaande referenties gebruiken voor verificatie.

[Azure Active Directory voor ontwikkelaars (v1.0)](../azuread-dev/v1-overview.md): hier weergegeven voor ontwikkelaars met bestaande apps die gebruikmaken van het oudere v1.0-eindpunt. Gebruik v1.0 **niet** voor nieuwe projecten.

## <a name="next-steps"></a>Volgende stappen

Als u een Azure-account hebt, hebt u al toegang tot een Azure Active Directory-tenant, maar de meeste ontwikkelaars van het Microsoft-identiteitsplatform hebben hun eigen Azure AD-tenant nodig om te gebruiken terwijl ze toepassing ontwikkelen, een 'dev-tenant'.

Meer informatie over het maken van uw eigen tenant om te gebruiken terwijl u uw toepassingen bouwt:

[Snelstart: Een Azure Active Directory-tenant instellen](quickstart-create-new-tenant.md)

---
title: Evolutie van het Microsoft Identity Platform - Azure
description: Informatie over het Microsoft Identity Platform, dat voortbouwt op de identiteitsservice en het ontwikkelaarsplatform van Azure Active Directory (Azure AD).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96608211"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolutie van het Microsoft Identity Platform

Het [Microsoft Identity Platform](../develop/index.yml) bouwt voort op het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Met het Microsoft-identiteitsplatform kunnen ontwikkelaars toepassingen maken die gebruikers aanmelden en waarmee tokens worden opgehaald voor het aanroepen van API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt. Het bevat een verificatieservice, open source-bibliotheken, mogelijkheden voor toepassingsregistratie en -configuratie (via een ontwikkelaarsportal en toepassings-API), volledige ontwikkelaarsdocumentatie, quickstart-voorbeelden, zelfstudies, handleidingen en andere inhoud voor ontwikkelaars. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect.

Veel ontwikkelaars hebben hiervoor gewerkt met het Azure AD v1.0 voor het verifiëren van werk- en schoolaccounts (ingericht door Azure AD), door tokens aan te vragen van het Azure AD v1.0-eindpunt en gebruik te maken van Azure AD Authentication Library (ADAL), Azure Portal voor de registratie en configuratie van toepassingen, en de Microsoft Graph API voor het programmatisch configureren van toepassingen.

Met het geïntegreerde Microsoft-identiteitsplatform (v2.0) schrijft u één keer code en verifieert u elke Microsoft-identiteit in uw toepassing. Voor verschillende platforms wordt de volledig ondersteunde opensource Microsoft Authentication Library (MSAL) aanbevolen voor gebruik met de eindpunten van het identiteitsplatform. MSAL is eenvoudig te gebruiken, biedt uw gebruikers een fantastische SSO-ervaring (eenmalige aanmelding), helpt u om betrouwbaarheid en prestaties te bereiken en in ontwikkeld met behulp van Microsoft Security Development Lifecycle (SDL). Wanneer u API's aanroept, kunt u uw toepassing zo configureren dat deze gebruikmaakt van incrementele toestemming, zodat u de aanvraag voor toestemming voor meer vergaande bereiken kunt uitstellen tot het gebruik van de toepassing dit in runtime garandeert.  MSAL biedt ook ondersteuning voor Azure Active Directory B2C, zodat uw klanten hun eigen sociale, zakelijke of lokale accountidentiteiten kunnen gebruiken om via eenmalige aanmelding toegang te krijgen tot uw toepassingen en API's.

Vergroot met het Microsoft-identiteitsplatform uw bereik voor dit soort gebruikers:

- Werk- en schoolaccounts (door Azure AD ingerichte accounts)
- Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
- Uw klanten die hun eigen e-mailadres of sociale identiteit (zoals LinkedIn, Facebook, Google) meebrengen via MSAL en Azure AD B2C

U kunt het Azure-portal gebruiken om uw toepassing te registreren en te configureren, en de Microsoft Graph API gebruiken voor de programmatische toepassingsconfiguratie.

Werk uw toepassing bij op uw eigen tempo. Toepassingen die zijn gebouwd met ADAL-bibliotheken, worden nog steeds ondersteund. Gemengde toepassingsportfolio's, die bestaan uit toepassingen die zijn gebouwd met ADAL-bibliotheken en toepassingen die zijn gebouwd met MSAL-bibliotheken, worden ook ondersteund. Dit betekent dat toepassingen die gebruikmaken van de nieuwste ADAL en de nieuwste MSAL, het hele portfolio eenmalige aanmelding kunnen leveren, die voorzien wordt door de gedeelde tokencache tussen deze bibliotheken. Toepassingen die zijn bijgewerkt van ADAL naar MSAL behouden de aanmeldingsstatus voor gebruikers bij de upgrade.

## <a name="microsoft-identity-platform-experience"></a>Gebruikerservaring met het Microsoft Identity Platform

In het volgende schema wordt de gebruikerservaring met Microsoft Identity weergegeven op hoog niveau, inclusief de registratie van apps, SDK’s, eindpunten en ondersteunde identiteiten.

![Het Microsoft Identity Platform vandaag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Ervaring voor app-registratie

De ervaring voor **[App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908)** van het Azure-portal is de enige portal-ervaring om alle toepassingen die u hebt geïntegreerd met het Microsoft-identiteitsplatform te beheren. Als u voorheen het portal voor toepassingsregistratie gebruikte, kunt u in plaats daarvan de ervaring voor toepassingsregistratie van het Azure-portal gebruiken.

Voor integratie met Azure AD B2C (bij het verifiëren van sociale of lokale identiteiten) moet u uw toepassing registreren in een Azure AD B2C-tenant. Deze ervaring maakt ook deel uit van het Azure-portal.

Gebruik de [Toepassings-API](/graph/api/resources/application) om uw toepassingen die zijn geïntegreerd met het Microsoft-identiteitsplatform te configureren om alle Microsoft-identiteiten te verifiëren.

### <a name="msal-libraries"></a>MSAL-bibliotheken

U kunt de MSAL-bibliotheek gebruiken om toepassingen te bouwen die alle Microsoft-identiteiten verifiëren. De MSAL-bibliotheken in .NET en Javascript zijn algemeen beschikbaar. MSAL-bibliotheken voor iOS en Android zijn beschikbaar als preview-versie en zijn geschikt voor gebruik in een productieomgeving. We bieden dezelfde ondersteuning op productieniveau voor MSAL-bibliotheken in preview als voor versies van MSAL en ADAL die algemeen beschikbaar zijn.

U kunt ook de MSAL-bibliotheken gebruiken om uw toepassing te integreren met Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>Eindpunt van het Microsoft Identity Platform

Het eindpunt van het Microsoft Identity Platform (v2.0) is OIDC-gecertificeerd. Het werkt met de Microsoft Authentication Libraries (MSAL) of met andere bibliotheken die voldoen aan de normen. Het implementeert door mensen leesbare bereiken, conform de sectornormen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in de [documentatie over het Microsoft Identity Platform](../develop/index.yml).

---
title: Externe identiteiten vergelijken - Azure Active Directory | Microsoft Docs
description: Met externe Azure AD-identiteiten kunnen mensen buiten uw organisatie toegang krijgen tot uw apps en resources met hun eigen identiteit. Vergelijk oplossingen voor externe identiteiten, waaronder B2B-samenwerking Azure Active Directory en Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644062"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Wat zijn externe identiteiten in Azure Active Directory?

Met externe identiteiten in Azure AD kunt u personen buiten uw organisatie toegang verlenen tot uw apps en resources, terwijl ze zich aanmelden met de identiteit van hun voorkeur. Uw partners, distributeurs, leveranciers en andere gastgebruikers kunnen 'hun eigen identiteiten meenemen'. Of ze nu een zakelijke of door de overheid verstrekte digitale identiteit hebben, of een niet-beheerde sociale identiteit als Google of Facebook, ze kunnen hun eigen referenties gebruiken om zich aan te melden. De id-provider van de externe gebruiker beheert hun identiteit en u beheert de toegang tot uw apps met Azure Active Directory om uw resources te beschermen.

## <a name="external-identities-scenarios"></a>Scenario's voor externe identiteiten

Externe identiteiten van Azure Active Directory focussen minder op de relatie van een gebruiker met uw organisatie en meer op de manier waarop de gebruiker zich wil aanmelden bij uw apps en resources. Binnen dit kader ondersteunt Azure Active Directory diverse scenario's van business-to-business (B2B)-samenwerking tot toegangsbeheer voor consumenten-/klantentoepassingen of burgergerichte toepassingen (business-to-customer of B2C).

- **Deel uw apps en resources met externe gebruikers (B2B-samenwerking)** . Nodig externe gebruikers in uw eigen tenant uit in als 'gastgebruikers' aan wie u machtigingen kunt toewijzen (voor autorisatie), terwijl ze hun bestaande referenties kunnen gebruiken (voor verificatie). Gebruikers melden zich aan bij de gedeelde resources met behulp van een eenvoudige uitnodiging en een inwisselproces met hun werk- of school-account of een andere e-mailaccount. U kunt [Azure AD-rechtenbeheer](../governance/entitlement-management-overview.md) ook gebruiken om beleidsregels te configureren die [de toegang voor externe gebruikers beheren](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). En nu de beschik baarheid van [gebruikers stromen voor selfservice registratie](self-service-sign-up-overview.md), kunt u externe gebruikers toestaan zich aan te melden voor toepassingen. De ervaring kan worden aangepast om het registreren met een werk-, school- of sociale identiteit (zoals Google of Facebook) toe te staan. U kunt ook informatie over de gebruiker verzamelen tijdens het registratieproces. Zie de [Azure AD-B2B-documentatie](index.yml) voor meer informatie.

- **Bouw gebruikersbelevingen met een white-label-oplossing voor identiteitsbeheer voor consument- en klantgerichte apps (Azure Active Directory B2C)** . Als u een bedrijf of ontwikkelaar bent die klantgerichte apps maakt, kunt u schalen naar miljoenen consumenten, klanten of burgers door gebruik te maken van Azure Active Directory B2C. Ontwikkel aars kunnen Azure Active Directory gebruiken als het volledige CIAM-systeem (Customer Identity and Access Management) voor hun toepassingen. Klanten kunnen zich aanmelden met een identiteit die zij al hebben ingesteld (zoals Facebook of Gmail). Met Azure AD B2C kunt u volledig aanpassen en beheren hoe klanten zich registreren en aanmelden en hoe zij hun profielen beheren wanneer ze uw toepassingen gebruiken. Zie de [Azure AD-B2C-documentatie](../../active-directory-b2c/index.yml) voor meer informatie.

## <a name="compare-external-identities-solutions"></a>Oplossingen voor externe identiteiten vergelijken

De volgende tabel bevat een gedetailleerde vergelijking van de scenario's die mogelijk zijn met externe Azure Active Directory-identiteiten.

|   | Samenwerking van externe gebruikers (B2B) | Toegang tot consument-/klantgerichte apps (B2C)  |
| ---- | --- | --- |
| **Primair scenario** | Samenwerking met Microsoft-toepassingen (Microsoft 365, Teams, enzovoort) of uw eigen toepassingen (SaaS-apps, op maat gemaakte apps, enz.).  | Identiteits- en toegangsbeheer voor moderne SaaS- of op maat gemaakte toepassingen (Micro soft-apps van derden).   |
| **Bedoeld voor**    | Samenwerken met zakelijke partners van externe organisaties, zoals leveranciers en partners. Gebruikers worden weergegeven als gastgebruikers in uw directory. Deze gebruikers kunnen al dan niet beheerde IT hebben.  | Klanten van uw product. Deze gebruikers worden beheerd in een afzonderlijke directory van Azure Active Directory.  |
| **Ondersteunde id-providers** | Externe gebruikers kunnen samenwerken met behulp van werkaccounts, schoolaccounts, elk e-mailadres, SAML en op WS-Fed gebaseerde id-providers, Gmail en Facebook.  | Consumentgebruikers met lokale toepassingsaccounts (een e-mailadres of gebruikersnaam), verschillende ondersteunde sociale identiteiten en gebruikers met door een bedrijf of de overheid verleende identiteiten via direct federation.       |
| **Beheer van externe gebruikers**   | Externe gebruikers worden beheerd in dezelfde directory als werknemers, maar met een speciale aantekening als gastgebruiker. Gastgebruikers kunnen op dezelfde manier als werknemers worden beheerd, worden toegevoegd aan dezelfde groepen, enzovoort.    | Externe gebruikers worden beheerd in de Azure Active Directory B2C-directory. Ze worden afzonderlijk beheerd vanaf de werknemers- en partnermap van de organisatie (indien van toepassing).  |
| **Eenmalige aanmelding (SSO)**      | SSO voor alle Azure AD verbonden apps wordt ondersteund. U kunt bijvoorbeeld toegang bieden voor Microsoft 365 of on-premises apps en andere SaaS-apps, zoals Salesforce of Workday.    | SSO voor apps van klanten in de Azure AD B2C-tenants wordt ondersteund. Eenmalige aanmelding bij Microsoft 365 of andere SaaS-apps van Microsoft wordt niet ondersteund.    |
| **Beveiligingsbeleid en naleving**        | Beheerd door de host/uitnodigende organisatie (bijvoorbeeld met [Beleid voor voorwaardelijke toegang](conditional-access.md)). | Beheerd door de organisatie via voorwaardelijke toegang en identiteitsbeveiliging.        |
| **Huisstijl**  | Merk van host/uitnodigende organisatie wordt gebruikt.    | Volledig aanpasbare huisstijl per toepassing of organisatie.   |
| **Factureringsmodel** | [Prijzen voor externe identiteiten](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) op basis van maandelijks actieve gebruikers (MAU). <br>(Zie ook: [Details van B2B-installatie](external-identities-pricing.md)) | [Prijzen voor externe identiteiten](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) op basis van maandelijks actieve gebruikers (MAU). <br>(Zie ook: [Details van B2C-installatie](../../active-directory-b2c/billing.md)) |
| **Meer informatie** | [Blogpost](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentatie](what-is-b2b.md)                   | [Productpagina](https://azure.microsoft.com/services/active-directory-b2c/), [Documentatie](../../active-directory-b2c/index.yml)       |

Beveilig en beheer klanten en partners buiten de grenzen van uw organisatie met externe Azure AD-identiteiten.

## <a name="about-multitenant-applications"></a>Over multitenant-toepassingen

Als u een app als een service levert en u de gebruikersaccounts van uw klanten niet wilt beheren, is een app voor meerdere tenants waarschijnlijk de juiste keuze voor u. Wanneer u toepassingen ontwikkelt die bedoeld zijn voor andere Azure AD-tenants, kunt u gebruikers van één organisatie (één tenant) of gebruikers van elke organisatie die al een Azure AD-tenant heeft (multitenant-toepassingen) als doel instellen. App-registraties in Azure Active Directory zijn standaard voor één tenant, maar u kunt uw registratie multitenant maken. Deze multitenant-toepassing wordt door uzelf eenmaal geregistreerd in uw eigen Azure Active Directory. Maar vervolgens kan elke gebruiker van Azure Active Directory van elke organisatie de toepassing gebruiken zonder dat u er iets extra voor hoeft te doen. Zie [Identiteitsbeheer in multitenant-toepassingen](/azure/architecture/multitenant-identity/), [Instructiegids](../develop/howto-convert-app-to-be-multi-tenant.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Over Azure AD B2C](../../active-directory-b2c/overview.md)
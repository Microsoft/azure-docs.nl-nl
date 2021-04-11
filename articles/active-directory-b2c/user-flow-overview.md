---
title: Gebruikers stromen en aangepaste beleids regels in Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Meer informatie over ingebouwde gebruikers stromen en het flexibele beleids raamwerk voor aangepaste beleid van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225997"
---
# <a name="user-flows-and-custom-policies-overview"></a>Overzicht van gebruikers stromen en aangepaste beleids regels

In Azure AD B2C kunt u de bedrijfs logica definiëren die gebruikers volgen om toegang te krijgen tot uw toepassing. U kunt bijvoorbeeld de volg orde bepalen van de stappen die gebruikers volgen wanneer ze zich aanmelden, registreren, een profiel bewerken of een wacht woord opnieuw instellen. Na het volt ooien van de reeks, verkrijgt de gebruiker een token en krijgt hij toegang tot uw toepassing. 

In Azure AD B2C zijn er twee manieren om gebruikers ervaring voor identiteiten te bieden:

* **Gebruikersstromen** zijn vooraf gedefinieerde, ingebouwde, configureerbare beleidsregels die we aanbieden, zodat u binnen een paar minuten ervaringen kunt maken voor registratie, aanmelden en profielbewerking.

* Via **aangepaste beleidsregels** kunt u uw eigen gebruikersbelevingen samenstellen voor scenario's met complexe identiteitservaringen.

In de volgende scherm afbeelding ziet u de gebruikers interface-instellingen in plaats van de aangepaste beleids configuratie bestanden.

![Scherm afbeelding toont de gebruikers interface-instellingen-UI, versus aangepaste beleids configuratie bestanden.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Dit artikel bevat een kort overzicht van de gebruikers stromen en aangepaste beleids regels en helpt u te bepalen welke methode het beste werkt voor uw bedrijfs behoeften.

## <a name="user-flows"></a>Gebruikersstromen

Voor het instellen van de meest voorkomende identiteits taken bevat de Azure Portal verschillende vooraf gedefinieerde en configureer bare beleids regels die *gebruikers stromen* worden genoemd.

U kunt instellingen voor gebruikersstromen zoals deze configureren om het gedrag van identiteitservaringen in uw toepassingen te beheren:

* Accounttypen die worden gebruikt voor aanmelding, zoals accounts voor sociale netwerken zoals een Facebook-account of lokale accounts die gebruikmaken van een e-mail adres en wachtwoord voor aanmelding
* Kenmerken die moeten worden verzameld voor de consument, zoals voornaam, postcode of het land of de regio van vestiging
* Azure AD MFA (Multi-Factor Authentication)
* Aanpassing van de gebruikersinterface
* Een set claims in een token die door de toepassing wordt ontvangen nadat de gebruiker de gebruikersstroom heeft voltooid
* Sessiebeheer
* ... en meer

De meeste algemene identiteits scenario's voor apps kunnen effectief worden gedefinieerd en geïmplementeerd met gebruikers stromen. U wordt aangeraden de ingebouwde gebruikers stromen te gebruiken, tenzij u complexe gebruikers traject scenario's hebt waarvoor de volledige flexibiliteit van aangepast beleid nodig is.

## <a name="custom-policies"></a>Aangepast beleid

Aangepaste beleids regels zijn configuratie bestanden waarmee het gedrag van uw Azure AD B2C Tenant gebruikers ervaring wordt gedefinieerd. Terwijl gebruikers stromen vooraf zijn gedefinieerd in de Azure AD B2C portal voor de meest voorkomende identiteits taken, kunnen aangepaste beleids regels volledig worden bewerkt door een identiteits ontwikkelaar om veel verschillende taken uit te voeren.

Een aangepast beleid is volledig configureerbaar en op basis van beleid. Het vertrouwt de vertrouwens relatie tussen entiteiten in standaard protocollen. Bijvoorbeeld, OpenID Connect Connect, OAuth, SAML en enkele niet-standaards, bijvoorbeeld op REST API gebaseerde systeem-naar-systeem claim uitwisselingen. Het Framework maakt gebruikers vriendelijke, met wit gelabeld ervaring.

Het aangepaste beleid biedt u de mogelijkheid om gebruikers ritten te maken met elke combi natie van stappen. Bijvoorbeeld:

* Federatie met andere id-providers
* Oplossingen voor meervoudige verificatie door eerste en externe partijen
* Verzamelen van gebruikersinvoer
* Integratie met externe systemen via communicatie met behulp van een REST-API

Elke gebruikers traject wordt gedefinieerd door een beleid. U kunt zoveel of zoveel beleids regels maken als u nodig hebt om de beste gebruikers ervaring voor uw organisatie in te scha kelen.

![Diagram van een voorbeeld van een complexe gebruikersbeleving dat mogelijk is gemaakt door het gebruik van IEF](media/user-flow-overview/custom-policy-diagram.png)

Een aangepast beleid bestaat uit een of meer XML-bestanden die in een hiërarchische keten naar elkaar verwijzen. De XML-elementen definiëren het claimschema, claimtransformaties, inhoudsdefinities, claimproviders, technische profielen, orkestratiestappen voor gebruikerstrajecten en andere aspecten van de identiteitservaring.

De krachtige flexibiliteit van aangepast beleidsregels komt van pas wanneer u complexe identiteitsscenario's moet maken. Ontwikkelaars die aangepaste beleidsregels configureren, moeten de vertrouwde relaties zeer zorgvuldig definiëren en rekening houden met zaken zoals eindpunten van metagegevens, exacte definities voor claimuitwisseling, en het configureren van geheimen, sleutels en certificaten die nodig zijn voor elke id-provider.

Meer informatie over aangepaste beleidsregels vindt u in [Aangepaste beleidsregels in Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Gebruikers stromen en aangepaste beleids regels vergelijken

De volgende tabel bevat een gedetailleerde vergelijking van de scenario's die u kunt met Azure AD B2C gebruikers stromen en aangepaste beleids regels.

| Context | Gebruikersstromen | Aangepast beleid |
|-|-------------------|-----------------|
| Doel gebruikers | Alle toepassings ontwikkelaars met of zonder identiteits expertise. | Identiteits-professionals, systeem integrators, consultants en interne identiteits teams. Ze zijn vertrouwd met OpenID Connect Connect flows en begrijpen identiteits providers en verificatie op basis van claims. |
| Configuratie methode | Azure Portal met een gebruikers vriendelijke gebruikers interface (UI). | Rechtstreeks bewerken van XML-bestanden en vervolgens uploaden naar de Azure Portal. |
| UI-aanpassing | [Volledige UI-aanpassing](customize-ui-with-html.md) , inclusief HTML, CSS en [Java script](javascript-and-page-layout.md).<br><br>[Meertalige ondersteuning](language-customization.md) met aangepaste teken reeksen. | Hetzelfde |
| Kenmerk aanpassing | Standaard-en aangepaste kenmerken. | Hetzelfde |
| Token-en sessie beheer | Het gedrag van [tokens](configure-tokens.md) en [sessies](session-behavior.md)aanpassen. | Hetzelfde |
| Id-providers | [Vooraf gedefinieerde lokale](identity-provider-local.md) of [sociale provider](add-identity-provider.md), zoals Federatie met Azure Active Directory tenants. | Op standaarden gebaseerde OIDC, OAUTH en SAML.  Verificatie is ook mogelijk met behulp van integratie met REST Api's. |
| Identiteits taken | [Meld u aan of Meld u](add-sign-up-and-sign-in-policy.md) aan met lokale of veel sociale accounts.<br><br>[Self-service voor wacht woord opnieuw instellen](add-password-reset-policy.md).<br><br>[Profiel bewerken](add-profile-editing-policy.md).<br><br>Multi-Factor Authentication.<br><br>Toegangs token stromen. | Voer dezelfde taken uit als voor gebruikers stromen met aangepaste ID-providers of gebruik aangepaste bereiken.<br><br>Een gebruikers account inrichten in een ander systeem op het moment van registratie.<br><br>Stuur een welkomst-e-mail met uw eigen e-mailservice provider.<br><br>Gebruik een gebruikers archief buiten Azure AD B2C.<br><br>Door de gebruiker verstrekte informatie met een vertrouwd systeem valideren met behulp van een API. |

## <a name="application-integration"></a>Integratie van toepassingen

U kunt veel gebruikers stromen of aangepaste beleids regels maken voor verschillende typen in uw Tenant en deze naar behoefte gebruiken in uw toepassingen. Gebruikers stromen en aangepaste beleids regels kunnen opnieuw worden gebruikt voor alle toepassingen. Met deze flexibiliteit kunt u identiteits ervaringen definiëren en wijzigen met minimale of geen wijzigingen in uw code. 

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, initieert de toepassing een autorisatie aanvraag naar een eind punt van een gebruikers stroom of een aangepast beleid. De gebruikersstroom of het aangepaste beleid definieert en bepaalt de gebruikerservaring. Wanneer een gebruikers stroom is voltooid, wordt door Azure AD B2C een token gegenereerd. vervolgens wordt de gebruiker omgeleid naar uw toepassing.

![Mobiele app met pijlen die de stroom tussen de app en de aanmeldingspagina van Azure AD B2C aangeven](media/user-flow-overview/app-integration.png)

Meerdere toepassingen kunnen dezelfde gebruikersstroom of hetzelfde aangepaste beleid gebruiken. Meerdere toepassingen kunnen dezelfde gebruikersstroom of hetzelfde aangepaste beleid gebruiken.

Als u zich bijvoorbeeld wilt aanmelden bij een toepassing, gebruikt de toepassing de gebruikersstroom *registreren of aanmelden*. Nadat de gebruiker zich heeft aangemeld, kan het zijn dat ze hun profiel willen bewerken. Als u het profiel wilt bewerken, initieert de toepassing een andere autorisatie aanvraag, met behulp van de gebruikers stroom voor het bewerken van het *profiel* .

Uw toepassing activeert een gebruikers stroom met behulp van een standaard-HTTP-verificatie aanvraag die de gebruikers stroom of het aangepaste beleids naam bevat. Er wordt een aangepast [token](tokens-overview.md) ontvangen als antwoord.


## <a name="next-steps"></a>Volgende stappen

- Als u de aanbevolen gebruikers stromen wilt maken, volgt u de instructies in de [zelf studie: een gebruikers stroom maken](tutorial-create-user-flows.md).
- Meer informatie over de [versies van de gebruikers stroom in azure AD B2C](user-flow-versions.md).
- Meer informatie over [Azure AD B2C aangepast beleid](custom-policy-overview.md).

---
title: Verificatie versus autorisatie | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de basis principes van verificatie en autorisatie in het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b81b34010736bce33085cb1ebf0faa3da6a41bd6
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755392"
---
# <a name="authentication-vs-authorization"></a>Verificatie vs. autorisatie

In dit artikel worden verificatie en autorisatie gedefinieerd. Ook wordt beschreven hoe u het micro soft-identiteits platform kunt gebruiken om gebruikers te verifiëren en te autoriseren in uw web-apps, Web-Api's of apps die beveiligde web-Api's aanroepen. Als u een term ziet die u niet kent, probeert u onze [woorden lijst](developer-glossary.md) of onze [Video's over micro soft Identity platform](identity-videos.md), die basis concepten beslaan.

## <a name="authentication"></a>Verificatie

*Verificatie* is het bewijs dat u bent wie u zegt te zijn. Het wordt soms Inge kort tot *authn*. Het micro soft-identiteits platform maakt gebruik van het [OpenID Connect Connect](https://openid.net/connect/) -protocol voor de verwerking van authenticatie.

## <a name="authorization"></a>Autorisatie

*Autorisatie* is het verlenen van een geverifieerde partij toestemming om iets te doen. Hiermee geeft u op welke gegevens u toegang hebt tot en wat u met die gegevens kunt doen. De autorisatie wordt soms Inge kort tot *AuthZ*. Het micro soft-identiteits platform gebruikt het [OAuth 2,0](https://oauth.net/2/) -protocol voor de verwerking van autorisatie.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Verificatie en autorisatie met het micro soft-identiteits platform

Het maken van apps die elk eigen gebruikers naam-en wachtwoord gegevens onderhouden, wordt een hoge administratieve belasting door het toevoegen of verwijderen van gebruikers aan meerdere apps. In plaats daarvan kunnen uw apps deze verantwoordelijkheid delegeren aan een gecentraliseerde ID-provider.

Azure Active Directory (Azure AD) is een gecentraliseerde ID-provider in de Cloud. Als u verificatie en autorisatie voor het delegeren uitschakelt, kunnen scenario's zoals:

- Beleid voor voorwaardelijke toegang waarvoor een gebruiker op een specifieke locatie moet zijn.
- Het gebruik van [multi-factor Authentication](../authentication/concept-mfa-howitworks.md), ook wel twee ledige verificatie of twee ledige genoemd.
- Wanneer een gebruiker zich eenmaal kan aanmelden en vervolgens automatisch wordt aangemeld bij alle web-apps die dezelfde gecentraliseerde map delen. Deze mogelijkheid wordt *eenmalige aanmelding (SSO)* genoemd.

Het micro soft Identity-platform vereenvoudigt autorisatie en verificatie voor toepassings ontwikkelaars door identiteit als een service te bieden. Het ondersteunt gestandaardiseerde protocollen en open source-bibliotheken voor verschillende platformen om u te helpen bij het snel starten van code ring. Ontwikkel aars kunnen toepassingen bouwen die zich aanmelden bij alle micro soft-identiteiten, tokens verkrijgen om [Microsoft Graph](https://developer.microsoft.com/graph/)aan te roepen, toegang te krijgen tot micro soft-api's of andere api's te gebruiken die ontwikkel aars hebben gebouwd.

In deze video worden het micro soft-identiteits platform en de basis beginselen van moderne verificatie uitgelegd: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Hier volgt een vergelijking van de protocollen die door het micro soft-identiteits platform worden gebruikt:

* **OAuth versus OpenID Connect Connect**: het platform gebruikt OAuth voor autorisatie en OpenID Connect Connect (OIDC) voor verificatie. OpenID Connect Connect is gebaseerd op OAuth 2,0, dus de terminologie en de stroom zijn vergelijkbaar tussen de twee. U kunt zelfs een gebruiker verifiëren (via OpenID Connect Connect) en toestemming krijgen om toegang te krijgen tot een beveiligde resource die de gebruiker in een aanvraag bezit (via OAuth 2,0). Zie [OAuth 2,0 en OpenID Connect Connect protocollen](active-directory-v2-protocols.md) en [OpenID Connect Connect protocol](v2-protocols-oidc.md)(Engelstalig) voor meer informatie.
* **OAuth versus SAML**: voor het platform wordt OAuth 2,0 gebruikt voor autorisatie en SAML voor authenticatie. Zie voor meer informatie over het gebruik van deze protocollen in combi natie met verificatie van een gebruiker en het verkrijgen van autorisatie voor toegang tot een beveiligde bron, [micro soft Identity platform en OAuth 2,0-bevestiging stroom voor SAML Bearer](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect Connect versus SAML**: voor het platform wordt zowel OpenID Connect Connect als SAML gebruikt voor het verifiëren van een gebruiker en het inschakelen van eenmalige aanmelding. SAML-verificatie wordt meestal gebruikt met id-providers, zoals Active Directory Federation Services (AD FS), die worden gebruikt in combi natie met Azure AD. OpenID Connect Connect wordt meestal gebruikt voor apps die zich uitsluitend in de cloud bevinden, zoals mobiele apps, websites en Web-Api's.

## <a name="next-steps"></a>Volgende stappen

Voor andere onderwerpen met betrekking tot de basis verificatie en autorisatie:

* Zie [beveiligings tokens](security-tokens.md)voor meer informatie over de toegangs tokens, het vernieuwen van tokens en id-tokens die worden gebruikt in autorisatie en verificatie.
* Zie [toepassings model](application-model.md)voor meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft-identiteits platform.
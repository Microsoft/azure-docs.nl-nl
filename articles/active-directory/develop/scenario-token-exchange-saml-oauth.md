---
title: Micro soft Identity platform token Exchange scenario met SAML en OIDC/OAuth in Azure Active Directory
description: Meer informatie over algemene scenario's voor het uitwisselen van tokens bij het werken met SAML en OIDC/OAuth in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 92d0dad86b3f048eb96dd7b17ed09f6e20d7cde2
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063294"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Scenario's voor uitwisseling van micro soft Identity platform-tokens met SAML en OIDC/OAuth

SAML en OpenID Connect Connect (OIDC)/OAuth zijn populaire protocollen die worden gebruikt voor het implementeren van single Sign-On (SSO). Sommige apps implementeren mogelijk alleen SAML en andere implementeren mogelijk alleen OIDC/OAuth. Beide protocollen gebruiken tokens om geheimen te communiceren. Zie voor meer informatie over SAML [Single Sign-On SAML-protocol](single-sign-on-saml-protocol.md). Zie voor meer informatie over OIDC/OAuth [oauth 2,0 en OpenID Connect Connect protocols in micro soft Identity platform](active-directory-v2-protocols.md).

Dit artikel bevat een overzicht van een veelvoorkomend scenario waarbij een app SAML implementeert, maar u moet aanroepen in de Graph API, die gebruikmaakt van OIDC/OAuth. Basis richtlijnen worden gegeven voor personen die met dit scenario werken.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scenario: u hebt een SAML-token en wilt de Graph API aanroepen
Veel apps worden geïmplementeerd met SAML. De Graph API maakt echter gebruik van de OIDC/OAuth-protocollen. Het is mogelijk, maar niet trivial, om OIDC/OAuth-functionaliteit aan een SAML-app toe te voegen. Zodra OAuth-functionaliteit beschikbaar is in een app, kan de Graph API worden gebruikt.

De algemene strategie bestaat uit het toevoegen van de OIDC/OAuth-stack aan uw app. Met uw app die beide standaarden implementeert, kunt u een sessie cookie gebruiken. U hoeft geen token expliciet uit te wisselen. U meldt zich aan bij een gebruiker met SAML, waarmee een sessie cookie wordt gegenereerd. Wanneer de Graph API een OAuth-stroom aanroept, gebruikt u de sessie cookie om te verifiëren. Deze strategie gaat ervan uit dat de controles voor voorwaardelijke toegang worden goedgekeurd en dat de gebruiker gemachtigd is.

> [!NOTE]
> De aanbevolen bibliotheek voor het toevoegen van het gedrag OIDC/OAuth is de micro soft Authentication Library (MSAL). Zie [overzicht van de micro soft Authentication Library (MSAL)](msal-overview.md)voor meer informatie over MSAL. De vorige bibliotheek heeft Active Directory Authentication Library (ADAL) genoemd, maar dit wordt niet aanbevolen omdat MSAL deze vervangt.

## <a name="next-steps"></a>Volgende stappen
- [Verificatiestromen en app-scenario's](authentication-flows-app-scenarios.md)

---
title: Een web-API bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API die downstream Web-Api's (overzicht) aanroept.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a66f0a2de1d8239baffbe53dfb5d6f2dd275d448
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756336"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: een web-API die web-Api's aanroept

Meer informatie over wat u moet weten om een web-API te maken die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

Dit scenario, waarin een beveiligde web-API andere web-Api's aanroept, bouwt voort op [scenario: beveiligde web-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Overzicht

- Een web-, Desktop-, mobiele of toepassing met één pagina (niet weer gegeven in het bijbehorende diagram) roept een beveiligde web-API aan en biedt een JSON Web Token (JWT) Bearer-token in de HTTP-header autorisatie.
- De beveiligde web-API valideert het token en maakt gebruik van de methode micro soft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` om een andere token van Azure Active Directory (Azure AD) aan te vragen, zodat de beveiligde web-API een tweede Web-API of downstream Web-API kan aanroepen namens de gebruiker.
- De beveiligde web-API kan later ook aanroepen `AcquireTokenSilent` om tokens voor andere downstream-api's namens dezelfde gebruiker aan te vragen. `AcquireTokenSilent` Hiermee wordt het token vernieuwd wanneer dit nodig is.

![Diagram van een web-API die een web-API aanroept](media/scenarios/web-api.svg)

## <a name="specifics"></a>Opsporingsgegevens

Het app-registratie onderdeel dat is gerelateerd aan API-machtigingen is klassiek. De configuratie van de app omvat het gebruik van de OAuth 2,0-stroom om het JWT Bearer-token uit te wisselen op basis van een token voor een downstream API. Dit token wordt toegevoegd aan de token cache, waar deze beschikbaar is in de controller van de Web-API, en kan vervolgens een token op de achtergrond verkrijgen om downstream-Api's aan te roepen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](scenario-web-api-call-api-app-registration.md).

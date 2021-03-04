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
ms.date: 03/03/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 376c61f6a5ba94492cac26950465c61e3d8fe4ed
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038557"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: een web-API die web-Api's aanroept

Meer informatie over wat u moet weten om een web-API te maken die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

Dit scenario, waarin een beveiligde web-API andere web-Api's aanroept, bouwt voort op [scenario: beveiligde web-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Overzicht

- Een web-, Desktop-, mobiele of toepassing met één pagina (niet weer gegeven in het bijbehorende diagram) roept een beveiligde web-API aan en biedt een JSON Web Token (JWT) Bearer-token in de HTTP-header autorisatie.
- De beveiligde web-API valideert het token en maakt gebruik van de methode micro soft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` om een andere token van Azure Active Directory (Azure AD) aan te vragen, zodat de beveiligde web-API een tweede Web-API of downstream Web-API kan aanroepen namens de gebruiker. `AcquireTokenOnBehalfOf` Hiermee wordt het token vernieuwd wanneer dit nodig is.
![Diagram van een web-API die een web-API aanroept](media/scenarios/web-api.svg)

## <a name="specifics"></a>Opsporingsgegevens

Het app-registratie onderdeel dat is gerelateerd aan API-machtigingen is klassiek. De configuratie van de app omvat het gebruik van de OAuth 2,0-stroom om het JWT Bearer-token uit te wisselen op basis van een token voor een downstream API. Dit token wordt toegevoegd aan de token cache, waar deze beschikbaar is in de controller van de Web-API, en kan vervolgens een token op de achtergrond verkrijgen om downstream-Api's aan te roepen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](scenario-web-api-call-api-app-registration.md).

---
title: Beveiligings tokens | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de basis principes van beveiligings tokens in het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795646"
---
# <a name="security-tokens"></a>Beveiligingstokens

Een gecentraliseerde ID-provider is vooral nuttig voor apps met gebruikers die zich op de hele wereld bevinden en zich niet hoeven aan te melden bij het bedrijfs netwerk. Het micro soft Identity-platform verifieert gebruikers en biedt beveiligings tokens, zoals [toegangs tokens](developer-glossary.md#access-token), [vernieuwings tokens](developer-glossary.md#refresh-token)en [id-tokens](developer-glossary.md#id-token). Met beveiligings tokens kan een [client toepassing](developer-glossary.md#client-application) toegang krijgen tot beveiligde bronnen op een [bron server](developer-glossary.md#resource-server).

**Toegangs token**: een toegangs token is een beveiligings token dat is uitgegeven door een [autorisatie server](developer-glossary.md#authorization-server) als onderdeel van een [OAuth 2,0](active-directory-v2-protocols.md) -stroom. Het bevat informatie over de gebruiker en de resource waarvoor het token is bedoeld. De informatie kan worden gebruikt voor toegang tot Web-Api's en andere beveiligde bronnen. Toegangs tokens worden gevalideerd door resources om toegang te verlenen tot een client-app. Zie [toegangs tokens](access-tokens.md)voor meer informatie over hoe het micro soft Identity-platform toegangs tokens uitgeeft.

**Vernieuwings token**: omdat toegangs tokens slechts voor een korte periode geldig zijn, geven autorisatie servers soms een vernieuwings token af op het moment dat het toegangs token wordt uitgegeven. De client toepassing kan dit vernieuwings token vervolgens voor een nieuw toegangs token uitwisselen wanneer dat nodig is. Zie voor meer informatie over de manier waarop het micro soft Identity-platform vernieuwings tokens gebruikt voor het intrekken van machtigingen, het [intrekken van tokens](access-tokens.md#token-revocation).

**Id-token**: id-tokens worden verzonden naar de client toepassing als onderdeel van een [OpenID Connect Connect](v2-protocols-oidc.md) -stroom. Ze kunnen naast of in plaats van een toegangs token worden verzonden. ID-tokens worden door de client gebruikt om de gebruiker te verifiëren. Zie [id-tokens](id-tokens.md)voor meer informatie over hoe de tokens van het micro soft Identity-platform worden uitgegeven.

> [!NOTE]
> In dit artikel worden beveiligings tokens beschreven die worden gebruikt door de Connect-protocollen OAuth2 en OpenID Connect. Veel zakelijke toepassingen gebruiken SAML om gebruikers te verifiëren. Zie [Azure Active Directory SAML-token referentie](reference-saml-tokens.md)voor informatie over SAML-bevestigingen.

## <a name="validate-security-tokens"></a>Beveiligings tokens valideren

Het is tot de app waarvoor het token is gegenereerd, de web-app die de gebruiker heeft ondertekend of de Web-API die wordt aangeroepen om het token te valideren. Het token is ondertekend door de autorisatie server met een persoonlijke sleutel. De autorisatie server publiceert de bijbehorende open bare sleutel. Om een token te valideren, controleert de app de hand tekening met behulp van de open bare sleutel van de autorisatie server om te valideren dat de hand tekening is gemaakt met behulp van de persoonlijke sleutel.

Tokens zijn alleen geldig voor een beperkte periode. De autorisatie server biedt meestal een paar tokens, zoals:

* Een toegangs token waarmee de toepassing of beveiligde resource wordt geopend.
* Een vernieuwings token dat wordt gebruikt voor het vernieuwen van het toegangs token wanneer het toegangs token bijna is verlopen.

Toegangs tokens worden door gegeven aan een web-API als het Bearer-token in de `Authorization` header. Een app kan een vernieuwings token leveren aan de autorisatie server. Als de gebruiker toegang tot de app niet heeft ingetrokken, wordt een nieuw toegangs token en een nieuw vernieuwings token teruggestuurd. Zo wordt het scenario van iemand die de onderneming verlaat, afgehandeld. Wanneer de autorisatie server het vernieuwings token ontvangt, wordt er geen ander geldig toegangs token uitgegeven als de gebruiker niet meer is gemachtigd.

## <a name="json-web-tokens-and-claims"></a>JSON-webtokens en claims

Het micro soft Identity-platform implementeert beveiligings tokens als JSON-webtokens (JWTs) die *claims* bevatten. Aangezien JWTs als beveiligings tokens worden gebruikt, wordt deze vorm van verificatie ook wel *JWT-verificatie* genoemd.

Een [claim](developer-glossary.md#claim) biedt bevestigingen over één entiteit, zoals een client toepassing of [resource-eigenaar](developer-glossary.md#resource-owner), naar een andere entiteit, zoals een resource server. Een claim kan ook worden aangeduid als een JWT-claim of een JSON Web Token claim.

Claims zijn naam-of waardeparen die feiten over het onderwerp van de token door sturen. Een claim kan bijvoorbeeld feiten bevatten over de beveiligingsprincipal die is geverifieerd door de autorisatie server. De claims die aanwezig zijn in een specifiek token zijn afhankelijk van veel dingen, zoals het type token, het type referentie dat wordt gebruikt voor het verifiëren van het onderwerp en de toepassings configuratie.

Toepassingen kunnen claims gebruiken voor verschillende taken, zoals:

* Valideer het token.
* Identificeer de [Tenant](developer-glossary.md#tenant)van het token onderwerp.
* Gebruikers gegevens weer geven.
* Bepaal de autorisatie van het onderwerp.

Een claim bestaat uit sleutel-waardeparen die informatie geven, zoals:

* Beveiligings token server die het token heeft gegenereerd.
* De datum waarop het token is gegenereerd.
* Onderwerp (zoals de gebruiker, met uitzonde ring van daemons).
* Doel groep: de app waarvoor het token is gegenereerd.
* De app (de client) die voor het token is aangevraagd. In het geval van web-apps kan deze app hetzelfde zijn als de doel groep.

Zie [toegangs tokens](access-tokens.md) en [id-tokens](id-tokens.md)voor meer informatie over de wijze waarop het micro soft Identity-platform tokens en claim informatie implementeert.

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt

Afhankelijk van hoe uw client is gebouwd, kunnen er één (of meerdere) verificatie stromen worden gebruikt die worden ondersteund door het micro soft Identity-platform. Deze stromen kunnen diverse tokens (ID-tokens, vernieuwings tokens, toegangs tokens) en autorisatie codes produceren. Ze vereisen verschillende tokens om ze te laten werken. Deze tabel bevat een overzicht.

|Stroom | Nodig | ID-token | Toegangs token | Token vernieuwen | Autorisatiecode |
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisatie code stroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Aflossingen van token vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | Toegangs token| x| x| x| |
|[Client referenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens die zijn uitgegeven via de impliciete modus, hebben een beperkte lengte omdat ze via de URL worden teruggestuurd naar de browser, waarbij `response_mode` `query` of `fragment` . Voor sommige browsers geldt een limiet voor de grootte van de URL die in de browser balk kan worden geplaatst en die te lang is. Als gevolg hiervan zijn deze tokens niet `groups` of `wids` claims.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over verificatie en autorisatie in het micro soft-identiteits platform:

* Zie [Verificatie versus autorisatie](authentication-vs-authorization.md)voor meer informatie over de basis beginselen van verificatie en autorisatie.
* Zie [toepassings model](application-model.md)voor meer informatie over het registreren van uw toepassing voor integratie.
* Zie [app-aanmeldings flow](app-sign-in-flow.md)voor meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps.

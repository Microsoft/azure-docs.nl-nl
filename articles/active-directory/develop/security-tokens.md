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
ms.openlocfilehash: 9668d3b0b57e36fb95421f8b502b9b743be8eb31
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756221"
---
# <a name="security-tokens"></a>Beveiligingstokens

Een gecentraliseerde ID-provider is vooral nuttig voor apps die gebruikers op de hele wereld hebben die zich niet per se aanmelden vanuit het netwerk van de onderneming. Het micro soft Identity-platform verifieert gebruikers en biedt beveiligings tokens, zoals [toegangs token](developer-glossary.md#access-token), [vernieuwings token](developer-glossary.md#refresh-token)en [id-token](developer-glossary.md#id-token), waarmee een [client toepassing](developer-glossary.md#client-application) toegang kan krijgen tot beveiligde bronnen op een [bron server](developer-glossary.md#resource-server).

Een **toegangs token** is een beveiligings token dat is uitgegeven door een [autorisatie server](developer-glossary.md#authorization-server) als onderdeel van een [OAuth 2,0](active-directory-v2-protocols.md) -stroom. Het bevat informatie over de gebruiker en de app waarvoor het token is bedoeld; die kunnen worden gebruikt voor toegang tot Web-Api's en andere beveiligde bronnen. Zie [toegangs tokens](access-tokens.md)voor meer informatie over hoe het micro soft Identity-platform toegangs tokens uitgeeft.

Toegangs tokens zijn alleen geldig gedurende korte tijd, waardoor autorisatie servers soms een **vernieuwings token** uitgeven op hetzelfde moment dat het toegangs token wordt uitgegeven. De client toepassing kan dit vernieuwings token vervolgens voor een nieuw toegangs token uitwisselen wanneer dat nodig is. Zie voor meer informatie over de manier waarop het micro soft Identity-platform vernieuwings tokens gebruikt voor het intrekken van machtigingen, het [intrekken van tokens](access-tokens.md#token-revocation).

**Id-tokens** worden verzonden naar de client toepassing als onderdeel van een [OpenID Connect Connect](v2-protocols-oidc.md) -stroom. Ze kunnen worden verzonden aan de zijkant of in plaats van een toegangs token en worden door de client gebruikt om de gebruiker te verifiëren. Zie [id-tokens](id-tokens.md)voor meer informatie over hoe de tokens van het micro soft Identity-platform worden uitgegeven.

> [!NOTE]
> In dit artikel worden beveiligings tokens beschreven die worden gebruikt door de Connect-protocollen OAuth2 en OpenID Connect. Veel zakelijke toepassingen gebruiken SAML om gebruikers te verifiëren. Zie overzicht van [Azure AD SAML-tokens](reference-saml-tokens.md) voor informatie over SAML-bevestigingen.

## <a name="validating-security-tokens"></a>Beveiligings tokens valideren

Het is de app waarvoor het token is gegenereerd, de web-app die is aangemeld bij de gebruiker of de Web-API die wordt aangeroepen om het token te valideren. Het token is ondertekend door de **Security Token server (STS)** met een persoonlijke sleutel. De STS publiceert de bijbehorende open bare sleutel. Om een token te valideren, controleert de app de hand tekening met behulp van de open bare sleutel STS om te valideren dat de hand tekening is gemaakt met behulp van de persoonlijke sleutel.

Tokens zijn alleen geldig voor een beperkte periode. Doorgaans biedt de STS een paar tokens:

* Een toegangs token voor toegang tot de toepassing of beveiligde bron en
* Een vernieuwings token dat wordt gebruikt voor het vernieuwen van het toegangs token wanneer het toegangs token bijna is verlopen.

Toegangs tokens worden door gegeven aan een web-API als het Bearer-token in de `Authorization` header. Een app kan een vernieuwings token bieden voor de STS en als de gebruiker de toegang tot de app niet heeft ingetrokken, wordt een nieuw toegangs token en een nieuw vernieuwings token teruggestuurd. Zo wordt het scenario van iemand die de onderneming verlaat, afgehandeld. Wanneer de STS het vernieuwings token ontvangt, wordt er geen ander geldig toegangs token uitgegeven als de gebruiker niet meer is gemachtigd.

## <a name="json-web-tokens-jwts-and-claims"></a>JSON-webtokens (JWTs) en claims

Het micro soft Identity-platform implementeert beveiligings tokens als **JSON-Webtokens (JWTs)** die **claims** bevatten. Aangezien JWTs als beveiligings tokens worden gebruikt, wordt deze vorm van verificatie ook wel **JWT-verificatie** genoemd.

Een [claim](developer-glossary.md#claim) biedt bevestigingen over één entiteit, zoals een client toepassing of [resource-eigenaar](developer-glossary.md#resource-owner), naar een andere entiteit, zoals een resource server. Een claim kan ook worden aangeduid als een JWT-claim of JSON Web Token claim.

Claims zijn naam/waarde-paren die feiten over het onderwerp van de token door sturen. Een claim kan bijvoorbeeld feiten bevatten over de beveiligingsprincipal die door de autorisatie server is geverifieerd. De claims in een bepaald token zijn afhankelijk van veel dingen, waaronder het type token, het type referentie dat wordt gebruikt voor het verifiëren van het onderwerp, de configuratie van de toepassing, enzovoort.

Toepassingen kunnen claims gebruiken voor verschillende taken, zoals:

* Het token valideren
* De [Tenant](developer-glossary.md#tenant) van het token onderwerp identificeren
* Gebruikers gegevens weer geven
* De autorisatie van het onderwerp bepalen

Een claim bestaat uit sleutel-waardeparen die informatie geven, zoals:

* Beveiligings token server die het token heeft gegenereerd
* Datum waarop het token is gegenereerd
* Onderwerp (zoals de gebruiker, met uitzonde ring van daemons)
* Doel groep: de app waarvoor het token is gegenereerd
* De app (de client) die voor het token is aangevraagd. In het geval van web-apps kan dit hetzelfde zijn als de doel groep

Zie [toegangs tokens](access-tokens.md) en [id-tokens](id-tokens.md)voor meer informatie over de wijze waarop het micro soft Identity-platform tokens en claim informatie implementeert.

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt

Afhankelijk van hoe uw client is gebouwd, kunnen er één (of meerdere) verificatie stromen worden gebruikt die worden ondersteund door het micro soft Identity-platform. Deze stromen kunnen een aantal tokens (ID-tokens, vernieuwings tokens, toegangs tokens) en autorisatie codes produceren, en vereisen verschillende tokens om ze te laten werken. Dit diagram bevat een overzicht:

|Stroom | Nodig | ID-token | toegangs token | token vernieuwen | autorisatie code |
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisatie code stroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Aflossingen van token vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | toegangs token| x| x| x| |
|[Client referenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens die zijn uitgegeven via de impliciete modus, hebben een beperkte lengte omdat ze via de URL worden teruggestuurd naar de browser (waarbij `response_mode` `query` of `fragment` ).  Voor sommige browsers geldt een limiet voor de grootte van de URL die in de browser balk kan worden geplaatst en die kan worden uitgevoerd als deze te lang is.  Daarom hebben deze tokens geen `groups` `wids` claims.

## <a name="next-steps"></a>Volgende stappen

Voor andere onderwerpen met betrekking tot de basis van verificatie en autorisatie:

* Zie [Verificatie versus autorisatie](authentication-vs-authorization.md) voor meer informatie over de basis concepten van verificatie en autorisatie in het micro soft Identity-platform.
* Zie [toepassings model](application-model.md) voor meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform.
* Zie [app-aanmeldings flow](app-sign-in-flow.md) voor meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps in het micro soft Identity-platform.

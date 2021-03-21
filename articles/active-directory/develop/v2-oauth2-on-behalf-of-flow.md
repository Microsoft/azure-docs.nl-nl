---
title: Micro soft Identity platform en OAuth 2.0 namens-of-flow | Azure
titleSuffix: Microsoft identity platform
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om service te implementeren voor service verificatie met behulp van de OAuth 2.0 namens-of-stroom.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ff8e03b813e2cb890192667e3466d920eaabc72c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756086"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Micro soft Identity platform en OAuth 2,0-of-flow


De OAuth 2,0-of-flow (OBO) wordt gebruikt als een toepassing een service/Web-API aanroept, die op zijn beurt een andere service/Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. Voor de middelste service om geverifieerde aanvragen voor de downstream-service te maken, moet het een toegangs token van het micro soft Identity-platform worden beveiligd namens de gebruiker.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).


Vanaf mei 2018 kan enige impliciete stroom `id_token` die is afgeleid niet worden gebruikt voor OBO-stroom. Apps met één pagina (SPAs) moeten een **toegangs** token door geven aan een vertrouwelijke client voor de middelste laag om in plaats daarvan OBO stromen uit te voeren. Zie [beperkingen](#client-limitations)voor meer informatie over welke clients OBO-aanroepen kunnen uitvoeren.

## <a name="protocol-diagram"></a>Protocol diagram

Stel dat de gebruiker is geverifieerd op een toepassing met behulp van de [OAuth 2,0-autorisatie code subsidie stroom](v2-oauth2-auth-code-flow.md) of een andere aanmeldings stroom. Op dit moment heeft de toepassing een toegangs token *voor API a* (token a) met de claims van de gebruiker en toestemming om toegang te krijgen tot de Web-API (API a) van de middelste laag. Nu moet API A een geverifieerde aanvraag indienen bij de downstream Web API (API B).

De volgende stappen vormen de OBO-stroom en worden uitgelegd in de Help van het onderstaande diagram.

![Hiermee worden de OAuth 2.0 namens-stroom weer gegeven](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. De client toepassing maakt een aanvraag voor API A met token A (met een `aud` claim van API a).
1. API A wordt geverifieerd bij het micro soft Identity platform token uitgifte-eind punt en vraagt een token aan voor toegang tot API B.
1. Met het micro soft Identity platform uitgifte-eind punt worden de referenties van de API A gevalideerd samen met token A en wordt het toegangs token voor API B (token B) naar API A uitgegeven.
1. Token B wordt ingesteld door API A in de autorisatie-header van de aanvraag voor API B.
1. Gegevens van de beveiligde bron worden geretourneerd door API B naar API A en vervolgens naar de client.

In dit scenario heeft de middelste laag service geen interactie van de gebruiker om de toestemming van de gebruiker voor toegang tot de downstream API te krijgen. Daarom wordt de optie voor het verlenen van toegang tot de downstream API vooraf weer gegeven als onderdeel van de stap voor toestemming tijdens de verificatie. Zie [toestemming geven voor de middelste toepassing](#gaining-consent-for-the-middle-tier-application)voor meer informatie over het instellen van dit voor uw app.

## <a name="middle-tier-access-token-request"></a>Aanvraag voor toegangs token op middelste laag

Als u een toegangs token wilt aanvragen, maakt u een HTTP POST naar het Tenant-specifieke micro soft Identity platform token-eind punt met de volgende para meters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Er zijn twee gevallen, afhankelijk van het feit of de client toepassing wordt beveiligd door een gedeeld geheim of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste case: toegangs token aanvraag met een gedeeld geheim

Bij gebruik van een gedeeld geheim bevat een aanvraag voor service-naar-service-toegangs token de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type van de token aanvraag. Voor een aanvraag met behulp van een JWT moet de waarde zijn `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Vereist | De ID van de toepassing (client) waaraan [de Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina is toegewezen aan uw app. |
| `client_secret` | Vereist | Het client geheim dat u hebt gegenereerd voor uw app op de pagina Azure Portal-App-registraties. |
| `assertion` | Vereist | Het toegangs token dat is verzonden naar de middelste laag-API.  Dit token moet een claim van een doel groep hebben `aud` van de app die deze OBO-aanvraag maakt (de app wordt aangeduid met het `client-id` veld). Toepassingen kunnen geen token inwisselen voor een andere app (dus als een client een API verzendt die bedoeld is voor MS Graph, kan de API deze niet inwisselen met OBO.  Het token moet in plaats daarvan worden afgewezen.  |
| `scope` | Vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie. |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of` . |

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt een toegangs token en vernieuwings token met `user.read` het bereik voor de https://graph.microsoft.com Web-API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: toegangs token aanvraag met een certificaat

Een aanvraag voor service-naar-service-toegangs token met een certificaat bevat de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type van de token aanvraag. Voor een aanvraag met behulp van een JWT moet de waarde zijn `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Vereist |  De ID van de toepassing (client) waaraan [de Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina is toegewezen aan uw app. |
| `client_assertion_type` | Vereist | De waarde moet zijn `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Vereist | Een verklaring (een JSON-webtoken) die u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie [certificaat referenties](active-directory-certificate-credentials.md)voor meer informatie over het registreren van uw certificaat en de indeling van de bevestiging. |
| `assertion` | Vereist |  Het toegangs token dat is verzonden naar de middelste laag-API.  Dit token moet een claim van een doel groep hebben `aud` van de app die deze OBO-aanvraag maakt (de app wordt aangeduid met het `client-id` veld). Toepassingen kunnen geen token inwisselen voor een andere app (dus als een client een API verzendt die bedoeld is voor MS Graph, kan de API deze niet inwisselen met OBO.  Het token moet in plaats daarvan worden afgewezen.  |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of` . |
| `scope` | Vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Zie [scopes](v2-permissions-and-consent.md)voor meer informatie.|

U ziet dat de para meters bijna hetzelfde zijn als in het geval van de aanvraag van het gedeelde geheim, behalve dat de `client_secret` para meter wordt vervangen door twee para meters: a `client_assertion_type` en `client_assertion` .

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt een toegangs token met `user.read` het bereik voor de https://graph.microsoft.com Web-API met een certificaat.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Reactie van toegangs token in middelste laag

Een reactie op geslaagde pogingen is een JSON OAuth 2,0-antwoord met de volgende para meters.

| Parameter | Beschrijving |
| --- | --- |
| `token_type` | Geeft de waarde van het token type aan. Het enige type dat het micro soft Identity-platform ondersteunt is `Bearer` . Zie voor meer informatie over Bearer-tokens het [OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Het bereik van toegang dat in het token wordt verleend. |
| `expires_in` | De tijds duur in seconden dat het toegangs token geldig is. |
| `access_token` | Het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om te verifiëren bij de ontvangende service. |
| `refresh_token` | Het vernieuwings token voor het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om een ander toegangs token aan te vragen nadat het huidige toegangs token is verlopen. Het vernieuwings token wordt alleen opgegeven als de `offline_access` Scope is aangevraagd. |

### <a name="success-response-example"></a>Voor beeld van een geslaagd antwoord

Het volgende voor beeld toont een geslaagde reactie op een aanvraag voor een toegangs token voor de https://graph.microsoft.com Web-API.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

Het bovenstaande toegangs token is een token met v 1.0-indeling voor Microsoft Graph. Dit komt doordat de token indeling is gebaseerd op de **bron** waartoe toegang wordt verkregen en die niet gerelateerd is aan de eind punten die worden gebruikt om deze te vragen. De Microsoft Graph is ingesteld om v 1.0-tokens te accepteren. het micro soft Identity-platform produceert nu toegangs tokens van v 1.0 wanneer een client tokens voor Microsoft Graph aanvraagt. Andere apps kunnen aangeven dat ze v 2.0-indelings tokens, v 1.0-notatie tokens of zelfs eigen of versleutelde token-indelingen willen hebben.  Zowel de eind punten van de v 1.0 als van de v 2.0 kunnen een wille keurige indeling van het token geven: de resource kan altijd de juiste indeling van token verkrijgen, ongeacht hoe of waar het token door de client is aangevraagd. 

Alleen toepassingen moeten de toegangs tokens bekijken. Clients **mogen deze niet** controleren. Als de toegangs tokens voor andere apps in uw code worden geïnspecteerd, wordt de app onverwacht verbroken wanneer de app de indeling van de tokens wijzigt of versleutelt. 

### <a name="error-response-example"></a>Voor beeld van fout antwoorden

Er wordt een fout bericht geretourneerd door het token-eind punt bij het ophalen van een toegangs token voor de downstream API als voor de downstream API een beleid voor voorwaardelijke toegang is ingesteld (zoals [multi-factor Authentication](../authentication/concept-mfa-howitworks.md)). De middelste laag service moet deze fout op de client toepassing belicht, zodat de client toepassing de gebruikers interactie kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangs token gebruiken om toegang te krijgen tot de beveiligde resource

De middelste laag service kan nu het aangeschafte token gebruiken om geverifieerde aanvragen naar de downstream Web-API te maken, door het token in de header in te stellen `Authorization` .

### <a name="example"></a>Voorbeeld

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-bevestigingen verkregen met een OAuth 2.0 OBO-stroom

Sommige op OAuth gebaseerde webservices moeten toegang hebben tot andere web service-Api's die SAML-bevestigingen in niet-interactieve stromen accepteren. Azure Active Directory kunt een SAML-verklaring geven in reactie op een naam van een stroom die gebruikmaakt van een SAML-gebaseerde webservice als doel bron.

Dit is een niet-standaard extensie voor de OAuth 2,0-stroom, waarmee een OAuth2 toepassing toegang kan krijgen tot Web Service API-eind punten die gebruikmaken van SAML-tokens.

> [!TIP]
> Wanneer u een met SAML beveiligde webservice aanroept vanuit een front-end-webtoepassing, kunt u gewoon de API aanroepen en een normale interactieve verificatie stroom initiëren met de bestaande sessie van de gebruiker. U hoeft alleen een OBO-stroom te gebruiken wanneer een service-naar-service-oproep een SAML-token vereist om gebruikers context te bieden.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Toestemming geven voor de toepassing in de middelste laag

Afhankelijk van de architectuur of het gebruik van uw toepassing, kunt u verschillende strategieën overwegen om ervoor te zorgen dat de OBO-stroom slaagt. In alle gevallen is het ultieme doel om ervoor te zorgen dat de client-app de middelste laag kan aanroepen, en de app voor de middelste laag is gemachtigd om de back-end-bron aan te roepen.

> [!NOTE]
> Voorheen heeft het Microsoft-account systeem (persoonlijke accounts) het veld ' bekende client toepassing ' niet ondersteund, noch kon er geen gecombineerde toestemming worden weer gegeven.  Dit is toegevoegd en alle apps in het micro soft Identity-platform kunnen gebruikmaken van de benadering van een bekende client toepassing om toestemming te krijgen voor OBO-aanroepen.

### <a name="default-and-combined-consent"></a>/.default en gecombineerde toestemming

De middelste laag toepassing voegt de client toe aan de lijst met bekende client toepassingen in het manifest en vervolgens kan de client een gecombineerde toestemmings stroom voor zowel zichzelf als de middelste laag toepassing activeren. Op het micro soft-identiteits platform wordt dit gedaan met de [ `/.default` Scope](v2-permissions-and-consent.md#the-default-scope). Wanneer er een goedkeurings scherm wordt geactiveerd met behulp van bekende client toepassingen en `/.default` , wordt in  het venster toestemming de machtigingen voor de client voor de middelste laag weer gegeven en wordt ook elke machtiging gevraagd die nodig is voor de API van het tweede niveau. De gebruiker geeft toestemming voor beide toepassingen en vervolgens werkt de OBO-stroom.

### <a name="pre-authorized-applications"></a>Vooraf geautoriseerde toepassingen

Resources kunnen aangeven dat een bepaalde toepassing altijd gemachtigd is om bepaalde bereiken te ontvangen. Dit is vooral handig om verbindingen te maken tussen een front-end-client en een back-end-bron naadloos. Een resource kan meerdere vooraf geautoriseerde toepassingen declareren: elke toepassing kan deze machtigingen aanvragen in een OBO-stroom en ze ontvangen zonder toestemming te geven aan de gebruiker.

### <a name="admin-consent"></a>toestemming van de beheerder

Een Tenant beheerder kan garanderen dat toepassingen gemachtigd zijn om de vereiste Api's aan te roepen door toestemming van de beheerder voor de middelste laag toepassing op te geven. Om dit te doen, kan de beheerder de toepassing voor het middelste niveau vinden in hun Tenant, de pagina vereiste machtigingen openen en ervoor kiezen om toestemming te geven voor de app. Zie de [documentatie voor toestemming en machtigingen](v2-permissions-and-consent.md)voor meer informatie over de toestemming van de beheerder.

### <a name="use-of-a-single-application"></a>Gebruik van één toepassing

In sommige scenario's is het mogelijk dat u slechts één paar van de middelste laag en front-end-client hoeft te koppelen. In dit scenario is het mogelijk dat u deze app gemakkelijker kunt maken, waardoor het niet meer nodig is om een toepassing met een middelste laag te verbinden. Als u wilt verifiëren tussen de front-end en de Web-API, kunt u cookies, een id_token of een toegangs token gebruiken dat is aangevraagd voor de toepassing zelf. Vraag vervolgens toestemming van deze enkele toepassing aan de back-end-bron.

## <a name="client-limitations"></a>Client beperkingen

Als een client de impliciete stroom gebruikt om een id_token op te halen en die client ook joker tekens heeft in een antwoord-URL, kan de id_token niet worden gebruikt voor een OBO-stroom.  Toegangs tokens die zijn verkregen via de impliciete toekennings stroom kunnen echter nog steeds worden ingewisseld door een vertrouwelijke client, zelfs als de initiërende client een antwoord-URL voor joker tekens heeft geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2,0-protocol en een andere manier om service-to-service-verificatie uit te voeren met behulp van client referenties.

* [OAuth 2,0-client referenties verlenen in het micro soft Identity-platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0-code stroom in micro soft Identity platform](v2-oauth2-auth-code-flow.md)
* [Het `/.default` bereik gebruiken](v2-permissions-and-consent.md#the-default-scope)

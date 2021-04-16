---
title: Microsoft Identity Platform en OAuth2.0 On-Behalf-Of | Azure
titleSuffix: Microsoft identity platform
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om service-naar-serviceverificatie te implementeren met behulp van de OAuth2.0 On-Behalf-Of-stroom.
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
ms.openlocfilehash: 1c8ea1580047910cb2d6634aad885d61e99113f3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529974"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform en OAuth 2.0 On-Behalf-Of-flow


OAuth 2.0 On-Behalf-Of Flow (OBO) dient als use-case waarbij een toepassing een service/web-API aanroept, die op zijn beurt een andere service/web-API moet aanroepen. Het idee is om de gedelegeerde gebruikersidentiteit en -machtigingen door te geven via de aanvraagketen. De service in de middelste laag kan alleen geverifieerde aanvragen indienen bij de downstreamservice als deze namens de gebruiker een toegang token van het Microsoft Identity Platform moet beveiligen.

In dit artikel wordt beschreven hoe u rechtstreeks met het protocol in uw toepassing kunt programmeren.  Indien mogelijk raden we u aan om in plaats daarvan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te verkrijgen en beveiligde [web-API's aan te roepen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Bekijk ook de [voorbeeld-apps die gebruikmaken van MSAL](sample-v2-code.md).


Vanaf mei 2018 kan een impliciete afgeleide stroom niet meer `id_token` worden gebruikt voor de OBO-stroom. Apps met één pagina (SPA's) moeten een toegangs **token** doorgeven aan een vertrouwelijke client in de middelste laag om in plaats daarvan OBO-stromen uit te voeren. Zie beperkingen voor meer informatie over welke clients OBO-aanroepen kunnen [uitvoeren.](#client-limitations)

## <a name="protocol-diagram"></a>Protocoldiagram

Stel dat de gebruiker is geverifieerd voor een toepassing met behulp van de goedkeuringsstroom voor [OAuth 2.0-autorisatiecode](v2-oauth2-auth-code-flow.md) of een andere aanmeldingsstroom. Op dit moment heeft de toepassing een toegang token voor *API A* (token A) met de claims van de gebruiker en toestemming voor toegang tot de web-API in de middelste laag (API A). Nu moet API A een geverifieerde aanvraag indienen bij de downstream web-API (API B).

De volgende stappen vormen de OBO-stroom en worden uitgelegd met behulp van het volgende diagram.

![Toont de OAuth2.0 On-Behalf-Of-stroom](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. De clienttoepassing doet een aanvraag naar API A met token A (met een `aud` claim van API A).
1. API A wordt geverifieerd bij het uitgifte-eindpunt van het Microsoft Identity Platform-token en vraagt een token aan voor toegang tot API B.
1. Het uitgifte-eindpunt van het Microsoft Identity Platform-token valideert de api A-referenties samen met token A en geeft het toegangsken voor API B (token B) uit aan API A.
1. Token B wordt ingesteld door API A in de autorisatie-header van de aanvraag voor API B.
1. Gegevens van de beveiligde resource worden geretourneerd door API B naar API A en vervolgens naar de client.

In dit scenario heeft de service in de middelste laag geen gebruikersinteractie om toestemming van de gebruiker te krijgen voor toegang tot de downstream-API. Daarom wordt de optie om toegang te verlenen tot de downstream-API vooraf weergegeven als onderdeel van de toestemmingsstap tijdens de verificatie. Zie Toestemming verkrijgen voor de toepassing in de middelste laag voor meer informatie over het instellen van [deze toepassing voor uw app.](#gaining-consent-for-the-middle-tier-application)

## <a name="middle-tier-access-token-request"></a>Toegangs tokenaanvraag voor middelste laag

Als u een toegangs token wilt aanvragen, maakt u een HTTP POST naar het tenantspecifieke Microsoft Identity Platform-token-eindpunt met de volgende parameters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Er zijn twee gevallen afhankelijk van of de clienttoepassing ervoor kiest om te worden beveiligd door een gedeeld geheim of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegangsaanvraag voor token met een gedeeld geheim

Wanneer u een gedeeld geheim gebruikt, bevat een service-to-service-toegangsaanvraag de volgende parameters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type tokenaanvraag. Voor een aanvraag met behulp van een JWT moet de waarde `urn:ietf:params:oauth:grant-type:jwt-bearer` zijn. |
| `client_id` | Vereist | De toepassings-id [(client)-id Azure Portal - App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) is toegewezen aan uw app. |
| `client_secret` | Vereist | Het clientgeheim dat u hebt gegenereerd voor uw app op de pagina Azure Portal - App-registraties app. |
| `assertion` | Vereist | Het toegangs token dat is verzonden naar de API voor de middelste laag.  Dit token moet een doelgroepclaim () hebben van de app die deze `aud` OBO-aanvraag indient (de app die wordt aangeduid in het `client-id` veld ). Toepassingen kunnen geen token inwisselen voor een andere app (als een client bijvoorbeeld een API een token verzendt dat is bedoeld voor MS Graph, kan de API deze niet inwisselen met OBO.  In plaats daarvan moet het token worden afgewezen).  |
| `scope` | Vereist | Een door spatie gescheiden lijst met scopes voor de tokenaanvraag. Zie [scopes voor meer informatie.](v2-permissions-and-consent.md) |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of` . |

#### <a name="example"></a>Voorbeeld

Met de volgende HTTP POST wordt een toegangs token en vernieuwings-token met `user.read` bereik voor de https://graph.microsoft.com web-API gevraagd.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: Toegangsaanvraag voor een token met een certificaat

Een service-to-service-toegangs tokenaanvraag met een certificaat bevat de volgende parameters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| `grant_type` | Vereist | Het type tokenaanvraag. Voor een aanvraag met behulp van een JWT moet de waarde `urn:ietf:params:oauth:grant-type:jwt-bearer` zijn. |
| `client_id` | Vereist |  De toepassings-id (client-id) [die Azure Portal - App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) is toegewezen aan uw app. |
| `client_assertion_type` | Vereist | De waarde moet `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` zijn. |
| `client_assertion` | Vereist | Een verklaring (een JSON-web-token) die u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie certificaatreferenties voor meer informatie over het registreren van uw certificaat en de indeling [van de verklaring.](active-directory-certificate-credentials.md) |
| `assertion` | Vereist |  Het toegangs token dat is verzonden naar de API in de middelste laag.  Dit token moet een doelgroepclaim () hebben van de app die deze `aud` OBO-aanvraag maakt (de app die wordt aangeduid door het `client-id` veld ). Toepassingen kunnen een token niet inwisselen voor een andere app (als een client bijvoorbeeld een API een token verzendt dat is bedoeld voor MS Graph, kan de API dit niet inwisselen met behulp van OBO.  In plaats daarvan moet het token worden afgewezen).  |
| `requested_token_use` | Vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de OBO-stroom moet de waarde worden ingesteld op `on_behalf_of` . |
| `scope` | Vereist | Een door spatie gescheiden lijst met scopes voor de tokenaanvraag. Zie [scopes voor meer informatie.](v2-permissions-and-consent.md)|

U ziet dat de parameters bijna hetzelfde zijn als in het geval van de aanvraag door een gedeeld geheim, behalve dat de parameter wordt vervangen door twee `client_secret` parameters: a `client_assertion_type` en `client_assertion` .

#### <a name="example"></a>Voorbeeld

Met de volgende HTTP POST wordt een toegangs token met `user.read` bereik voor de https://graph.microsoft.com web-API met een certificaat gevraagd.

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

## <a name="middle-tier-access-token-response"></a>Antwoord van toegangs token voor middelste laag

Een geslaagd antwoord is een JSON OAuth 2.0-antwoord met de volgende parameters.

| Parameter | Beschrijving |
| --- | --- |
| `token_type` | Geeft de waarde van het tokentype aan. Het enige type dat door het Microsoft Identity Platform wordt ondersteund, is `Bearer` . Zie het [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over Bearer-tokens. |
| `scope` | Het toegangsbereik dat in het token wordt verleend. |
| `expires_in` | De tijdsduur, in seconden, dat het toegangsken geldig is. |
| `access_token` | Het aangevraagde toegangs token. De aanroepingsservice kan dit token gebruiken om te verifiëren bij de ontvangende service. |
| `refresh_token` | Het vernieuwings token voor het aangevraagde toegangs token. De aanroepingsservice kan dit token gebruiken om een ander toegangsteken aan te vragen nadat het huidige toegangsteken is verlopen. Het vernieuwings token wordt alleen opgegeven als het `offline_access` bereik is aangevraagd. |

### <a name="success-response-example"></a>Voorbeeld van een geslaagd antwoord

In het volgende voorbeeld ziet u een geslaagd antwoord op een aanvraag voor een toegangs token voor de https://graph.microsoft.com web-API.

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

Het bovenstaande toegangsteken is een token met v1.0-indeling voor Microsoft Graph. Dit komt doordat de tokenindeling is gebaseerd op de **resource** die wordt gebruikt en niet gerelateerd is aan de eindpunten die worden gebruikt om deze aan te vragen. De Microsoft Graph is ingesteld om v1.0-tokens te accepteren, zodat het Microsoft Identity Platform v1.0-toegangstokens produceert wanneer een client tokens voor Microsoft Graph. Andere apps kunnen aangeven dat ze tokens met v2.0-indeling, v1.0-indelingstokens of zelfs eigen of versleutelde tokenindelingen willen.  Zowel de v1.0- als v2.0-eindpunten kunnen beide tokenindelingen hebben. Op deze manier kan de resource altijd de juiste indeling van het token krijgen, ongeacht hoe of waar het token is aangevraagd door de client. 

Alleen toepassingen moeten toegangstokens bekijken. Clients **mogen ze** niet inspecteren. Het inspecteren van toegangstokens voor andere apps in uw code leidt ertoe dat uw app onverwacht wordt gebroken wanneer die app de indeling van de tokens wijzigt of ze begint te versleutelen. 

### <a name="error-response-example"></a>Voorbeeld van een foutreactie

Er wordt een foutbericht geretourneerd door het token-eindpunt bij het verkrijgen van een toegangs token voor de downstream-API, als voor de [downstream-API](../authentication/concept-mfa-howitworks.md)beleid voor voorwaardelijke toegang (zoals meervoudige verificatie) is ingesteld. De service in de middelste laag moet deze fout aan de clienttoepassing geven, zodat de clienttoepassing de interactie van de gebruiker kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangs token gebruiken voor toegang tot de beveiligde resource

De service in de middelste laag kan nu het hierboven verkregen token gebruiken om geverifieerde aanvragen te maken voor de downstream web-API, door het token in de header in te `Authorization` stellen.

### <a name="example"></a>Voorbeeld

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-asserten verkregen met een OAuth2.0 OBO-stroom

Sommige op OAuth gebaseerde webservices moeten toegang hebben tot andere webservice-API's die SAML-assertingen accepteren in niet-interactieve stromen. Azure Active Directory kunt een SAML-verklaring verstrekken als reactie op een on-behalf-of-stroom die gebruikmaakt van een op SAML gebaseerde webservice als doelresource.

Dit is een niet-standaarduitbreiding voor de OAuth 2.0 On-Behalf-Of-stroom waarmee een op OAuth2 gebaseerde toepassing toegang kan krijgen tot API-eindpunten van de webservice die SAML-tokens gebruiken.

> [!TIP]
> Wanneer u een met SAML beveiligde webservice aanroept vanuit een front-endwebtoepassing, kunt u gewoon de API aanroepen en een normale interactieve verificatiestroom initiëren met de bestaande sessie van de gebruiker. U hoeft alleen een OBO-stroom te gebruiken wanneer voor een service-naar-service-aanroep een SAML-token is vereist om gebruikerscontext te bieden.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Een SAML-token verkrijgen met behulp van een OBO-aanvraag met een gedeeld geheim

Een service-naar-service-aanvraag voor een SAML-asserty bevat de volgende parameters:

| Parameter | Type | Description |
| --- | --- | --- |
| grant_type |vereist | Het type tokenaanvraag. Voor een aanvraag die gebruikmaakt van een JWT, moet de waarde **urn:ietf:params:oauth:grant-type:jwt-bearer zijn.** |
| Bewering |vereist | De waarde van het toegangsken dat in de aanvraag wordt gebruikt.|
| client_id |vereist | De app-id die tijdens de registratie bij Azure AD is toegewezen aan de aanroepende service. Als u de app-id in de Azure Portal selecteert u **Active Directory,** kiest u de map en selecteert u vervolgens de naam van de toepassing. |
| client_secret |vereist | De sleutel die is geregistreerd voor de aanroepende service in Azure AD. Deze waarde moet zijn genoteerd op het moment van registratie. |
| resource |vereist | De URI van de app-id van de ontvangende service (beveiligde resource). Dit is de resource die de doelgroep van het SAML-token wordt. Als u de URI van de app-id in Azure Portal, selecteert u **Active Directory** en kiest u de map. Selecteer de naam van de toepassing, **kies Alle instellingen** en selecteer vervolgens **Eigenschappen.** |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de stroom On-Behalf-Of moet de waarde **on_behalf_of.** |
| requested_token_type | vereist | Hiermee geeft u het type aangevraagde token. De waarde kan **urn:ietf:params:oauth:token-type:saml2** of **urn:ietf:params:oauth:token-type:saml1** zijn, afhankelijk van de vereisten van de resource die wordt gebruikt. |

Het antwoord bevat een SAML-token dat is gecodeerd in UTF8 en Base64url.

- **SubjectConfirmationData** voor een SAML-assertie afkomstig van een OBO-aanroep: als voor de doeltoepassing een ontvangerwaarde in **SubjectConfirmationData** is vereist, moet de waarde een antwoord-URL zonder jokertekens zijn in de configuratie van de resourcetoepassing.
- **Het knooppunt SubjectConfirmationData:** het knooppunt kan geen **InResponseTo-kenmerk** bevatten omdat het geen deel uitmaakt van een SAML-antwoord. De toepassing die het SAML-token ontvangt, moet de SAML-asserty kunnen accepteren zonder een **InResponseTo-kenmerk.**

- **Toestemming:** er moet toestemming zijn gegeven voor het ontvangen van een SAML-token met gebruikersgegevens in een OAuth-stroom. Zie Machtigingen en toestemming in het Azure Active Directory [v1.0-eindpunt](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent)voor meer informatie over machtigingen en het verkrijgen van beheerders toestemming.

### <a name="response-with-saml-assertion"></a>Antwoord met SAML-bewering

| Parameter | Beschrijving |
| --- | --- |
| token_type |Geeft de waarde van het tokentype aan. Het enige type dat door Azure AD wordt ondersteund, is **Bearer**. Zie [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)voor meer informatie over Bearer-tokens. |
| scope |Het toegangsbereik dat in het token wordt verleend. |
| expires_in |De tijdsduur dat het toegangs token geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangs token verloopt. De datum wordt weergegeven als het aantal seconden tussen 1970-01-01T0:0:0Z UTC tot de vervaldatum. Deze waarde wordt gebruikt om de levensduur van tokens in de cache te bepalen. |
| resource |De URI van de app-id van de ontvangende service (beveiligde resource). |
| access_token |De parameter die de SAML-asserty retourneert. |
| refresh_token |Het vernieuwings token. De aanroepingsservice kan dit token gebruiken om een ander toegangsteken aan te vragen nadat de huidige SAML-asserty is verlopen. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Resource: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Toestemming verkrijgen voor de toepassing in de middelste laag

Afhankelijk van de architectuur of het gebruik van uw toepassing, kunt u verschillende strategieën overwegen om ervoor te zorgen dat de OBO-stroom slaagt. In alle gevallen is het uiteindelijke doel ervoor te zorgen dat de juiste toestemming wordt gegeven, zodat de client-app de middelste laag-app kan aanroepen en de middelste laag-app toestemming heeft om de back-endresource aan te roepen.

> [!NOTE]
> Voorheen Microsoft-account het systeem (persoonlijke accounts) het veld 'Bekende clienttoepassing' niet ondersteund en kon er geen gecombineerde toestemming worden gegeven.  Dit is toegevoegd en alle apps in het Microsoft Identity Platform kunnen gebruikmaken van de bekende clienttoepassingsbenadering voor het verkrijgen van toestemming voor OBO-aanroepen.

### <a name="default-and-combined-consent"></a>/.default en gecombineerde toestemming

De toepassing voor de middelste laag voegt de client toe aan de lijst met bekende clienttoepassingen in het manifest, waarna de client een gecombineerde toestemmingsstroom kan activeren voor zowel zichzelf als de toepassing in de middelste laag. Op het Microsoft Identity Platform wordt dit gedaan met behulp van het [ `/.default` bereik](v2-permissions-and-consent.md#the-default-scope). Wanneer u een toestemmingsscherm activeert met behulp van bekende clienttoepassingen en , toont het toestemmingsscherm machtigingen voor zowel de client voor de API van de middelste laag als de machtigingen die vereist zijn voor de API in de middelste `/.default` laag.  De gebruiker geeft toestemming voor beide toepassingen en vervolgens werkt de OBO-stroom.

### <a name="pre-authorized-applications"></a>Vooraf geautoriseerde toepassingen

Resources kunnen aangeven dat een bepaalde toepassing altijd toestemming heeft om bepaalde scopes te ontvangen. Dit is voornamelijk handig om verbindingen tussen een front-endclient en een back-endresource naadlooser te maken. Een resource kan meerdere vooraf geautoriseerde toepassingen declareeren. Een dergelijke toepassing kan deze machtigingen aanvragen in een OBO-stroom en deze ontvangen zonder dat de gebruiker toestemming geeft.

### <a name="admin-consent"></a>toestemming van de beheerder

Een tenantbeheerder kan garanderen dat toepassingen toestemming hebben om hun vereiste API's aan te roepen door beheerders toestemming te geven voor de middelste laag-toepassing. Hiervoor kan de beheerder de middelste laag-app vinden in de tenant, de pagina vereiste machtigingen openen en ervoor kiezen om toestemming te geven voor de app. Zie de documentatie over toestemming en machtigingen voor meer informatie over [beheerdersmachtigingen.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Gebruik van één toepassing

In sommige scenario's hebt u mogelijk slechts één koppeling van de middelste laag en de front-endclient. In dit scenario is het wellicht eenvoudiger om van deze toepassing één toepassing te maken, waarbij u de noodzaak van een toepassing in de middelste laag helemaal niet hoeft te vermijden. Voor verificatie tussen de front-end en de web-API kunt u cookies, een id_token of een toegangsteken gebruiken dat is aangevraagd voor de toepassing zelf. Vraag vervolgens toestemming van deze ene toepassing aan de back-endresource.

## <a name="client-limitations"></a>Clientbeperkingen

Als een client de impliciete stroom gebruikt om een id_token op te halen en die client ook jokertekens in een antwoord-URL heeft, kan de id_token niet worden gebruikt voor een OBO-stroom.  Toegangstokens die zijn verkregen via de impliciete toekenningsstroom, kunnen echter nog steeds worden ingewisseld door een vertrouwelijke client, zelfs als de initiëren client een antwoord-URL met jokertekens heeft geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2.0-protocol en een andere manier om service-naar-service-auth uit te voeren met clientreferenties.

* [Referenties voor OAuth 2.0-client verlenen in Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-codestroom in Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Het bereik `/.default` gebruiken](v2-permissions-and-consent.md#the-default-scope)

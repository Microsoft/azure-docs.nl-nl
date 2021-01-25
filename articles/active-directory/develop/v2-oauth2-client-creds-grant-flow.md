---
title: OAuth 2,0-client referenties stroom op het micro soft Identity-platform | Azure
description: Bouw webtoepassingen met behulp van de micro soft Identity platform-implementatie van het OAuth 2,0-verificatie protocol.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/2/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 96f7d7c94ce908d953a6941bfa237fe8da1dc482
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752666"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Micro soft Identity platform en de OAuth 2,0-client referenties stroom

U kunt de [OAuth 2,0-client referenties toekenning](https://tools.ietf.org/html/rfc6749#section-4.4) die is opgegeven in RFC 6749, ook wel *twee legged OAuth* genoemd, gebruiken om toegang te krijgen tot webbronnen met behulp van de identiteit van een toepassing. Dit type toekenning wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. Deze typen toepassingen worden vaak *daemons* of *service accounts* genoemd.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing. Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

Met de OAuth 2,0-client referenties toewijzen stroom kan een webservice (vertrouwelijke client) eigen referenties gebruiken, in plaats van een gebruiker te imiteren, om te verifiëren bij het aanroepen van een andere webservice. In dit scenario is de client doorgaans een middelste laag, een daemon-service of een website. Voor een hoger garantie niveau kan het micro soft Identity-platform ook een certificaat (in plaats van een gedeeld geheim) als referentie gebruiken.

In de client referenties stroom worden machtigingen rechtstreeks verleend aan de toepassing zelf door een beheerder. Wanneer de app een token aan een resource presenteert, dwingt de resource af dat de app zelf een actie heeft geautoriseerd, omdat er geen gebruiker is betrokken bij de verificatie.  In dit artikel worden de stappen besproken die nodig zijn om een [toepassing te machtigen voor het aanroepen van een API](#application-permissions), en het [verkrijgen van de tokens die nodig zijn om die API aan te roepen](#get-a-token).

## <a name="protocol-diagram"></a>Protocol diagram

De gehele stroom van de client referenties ziet er ongeveer uit als in het volgende diagram. Verderop in dit artikel worden de stappen beschreven.

![Diagram van de stroom van de client referenties](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Directe autorisatie ophalen

Een app ontvangt doorgaans directe autorisatie voor toegang tot een bron op een van de volgende twee manieren:

* [Via een toegangs beheer lijst (ACL) bij de resource](#access-control-lists)
* [Via toewijzing van toepassings machtigingen in azure AD](#application-permissions)

Deze twee methoden worden het meest gebruikt in azure AD en wij raden ze aan voor clients en resources die de client referenties stroom uitvoeren. Een resource kan er ook voor kiezen om de clients op andere manieren te autoriseren. Elke resource server kan kiezen welke methode het meest zinvol is voor de toepassing.

### <a name="access-control-lists"></a>Toegangsbeheerlijsten

Een resource provider kan een autorisatie controle afdwingen op basis van een lijst met toepassings-Id's die het kent en verleent een specifiek toegangs niveau voor. Wanneer de resource een token van het micro soft Identity-platform ontvangt, kan het het token decoderen en de toepassings-ID van de client uit de `appid` en `iss` claims ophalen. Vervolgens wordt de toepassing vergeleken met een toegangs beheer lijst (ACL) die het onderhoudt. De granulatie en methode van de toegangs beheer lijst kan aanzienlijk verschillen tussen resources.

Een veelvoorkomende use-case is het gebruik van een ACL voor het uitvoeren van tests voor een webtoepassing of voor een web-API. De Web-API kan slechts een subset van volledige machtigingen verlenen aan een specifieke client. Als u end-to-end-tests wilt uitvoeren op de API, maakt u een test-client die tokens ophaalt uit het micro soft-identiteits platform en verzendt u deze vervolgens naar de API. De API controleert vervolgens de ACL voor de toepassings-ID van de test client voor volledige toegang tot de volledige functionaliteit van de API. Als u dit type toegangs beheer lijst gebruikt, moet u ervoor zorgen dat u niet alleen de waarde van de beller valideert, `appid` maar ook te controleren of de `iss` waarde van het token vertrouwd is.

Dit type autorisatie is gebruikelijk voor daemons en service accounts die toegang nodig hebben tot gegevens die eigendom zijn van consumenten gebruikers met persoonlijke micro soft-accounts. Voor gegevens die eigendom zijn van organisaties, raden we u aan de vereiste autorisatie te verkrijgen via toepassings machtigingen.

#### <a name="controlling-tokens-without-the-roles-claim"></a>Tokens beheren zonder de `roles` claim

Om dit autorisatie patroon op basis van een toegangs beheer lijst in te scha kelen, is voor Azure AD niet vereist dat toepassingen worden gemachtigd om tokens voor een andere toepassing op te halen. Daarom kunnen alleen app-tokens worden uitgegeven zonder een `roles` claim. Toepassingen die Api's beschikbaar stellen, moeten controle van machtigingen implementeren om tokens te accepteren.

Als u wilt voor komen dat toepassingen alleen voor de app verstuurde toegangs tokens voor uw toepassing worden ontvangen, moet u [ervoor zorgen dat de vereisten voor de gebruikers toewijzing zijn ingeschakeld voor uw app](../manage-apps/assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Hiermee kunnen gebruikers en toepassingen zonder toegewezen rollen een token voor deze toepassing ophalen. 

### <a name="application-permissions"></a>Toepassings machtigingen

In plaats van Acl's te gebruiken, kunt u Api's gebruiken om een set **toepassings machtigingen** beschikbaar te maken. Een toepassings machtiging wordt verleend aan een toepassing door de beheerder van een organisatie en kan alleen worden gebruikt om toegang te krijgen tot gegevens die eigendom zijn van die organisatie en haar mede werkers. Microsoft Graph biedt bijvoorbeeld verschillende toepassings machtigingen om het volgende te doen:

* E-mail in alle post vakken lezen
* E-mail in alle post vakken lezen en schrijven
* E-mail verzenden als gebruiker
* Mapgegevens lezen

Als u toepassings machtigingen met uw eigen API (in plaats van Microsoft Graph) wilt gebruiken, moet u [de API eerst zichtbaar](quickstart-configure-app-expose-web-apis.md) maken door scopes in de app-registratie van de API in de Azure portal te definiëren. Vervolgens kunt u [de toegang tot de API configureren](quickstart-configure-app-access-web-apis.md) door die machtigingen te selecteren in de app-registratie van uw client toepassing. Als u geen scopes hebt weer gegeven in de app-registratie van uw API, kunt u geen toepassings machtigingen voor die API opgeven in de app-registratie van uw client toepassing in de Azure Portal.

Bij verificatie als een toepassing (in plaats van een gebruiker) kunt u geen *gedelegeerde machtigingen* bereiken die door een gebruiker worden verleend. U moet toepassings machtigingen, ook wel bekend als rollen, gebruiken die door een beheerder voor de toepassing worden verleend of via vooraf-autorisatie door de Web-API.

Zie [machtigingen en toestemming](v2-permissions-and-consent.md#permission-types)voor meer informatie over toepassings machtigingen.

#### <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: Onderteken de gebruiker in uw app

Wanneer u een toepassing bouwt die gebruikmaakt van toepassings machtigingen, moet de app doorgaans een pagina of weer gave hebben waarop de beheerder de machtigingen van de app goedkeurt. Deze pagina kan deel uitmaken van de aanmeldings stroom van de app, een deel van de instellingen van de app of een specifieke stroom ' Connect ' zijn. In veel gevallen is het zinvol voor de app om deze weer gave ' verbinding maken ' alleen weer te geven nadat een gebruiker zich heeft aangemeld met een werk-of school Microsoft-account.

Als u de gebruiker aan uw app ondertekent, kunt u de organisatie waartoe de gebruiker behoort, identificeren voordat u de gebruiker vraagt de machtigingen voor de toepassing goed te keuren. Hoewel het niet strikt nood zakelijk is, kan het u helpen een intuïtieve ervaring te creëren voor uw gebruikers. Als u de gebruiker wilt ondertekenen in, volgt u de [zelf studies voor het micro soft Identity platform-protocol](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen van een adreslijst beheerder aanvragen

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van de organisatie, kunt u de gebruiker omleiden naar het micro soft Identity platform *Administrator toestemmings eindpunt*.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Gebruik uw eigen App-ID voor de beste resultaten: de zelfstudie toepassing vraagt geen nuttige machtigingen aan.) [ ![ Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Pro-Tip: Probeer de volgende aanvraag in een browser te plakken.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De Directory-Tenant waarvan u toestemming wilt aanvragen. Dit kan een GUID of beschrijvende naam zijn. Als u niet weet op welke Tenant de gebruiker zich bevindt en u zich wilt aanmelden met een Tenant, gebruikt u `common` . |
| `client_id` | Vereist | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `redirect_uri` | Vereist | De omleidings-URI waar u het antwoord voor uw app wilt laten afhandelen. De waarde moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd, behalve dat deze URL moet worden gecodeerd en dat er extra padsegmenten kunnen zijn. |
| `state` | Aanbevolen | Een waarde die is opgenomen in de aanvraag die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van elke gewenste inhoud. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |

Op dit moment dwingt Azure AD af dat alleen een Tenant beheerder zich kan aanmelden bij het volt ooien van de aanvraag. De beheerder wordt gevraagd om alle directe toepassings machtigingen die u hebt aangevraagd voor uw app, goed te keuren in de app-registratie Portal.

##### <a name="successful-response"></a>Geslaagde reactie

Als de beheerder de machtigingen voor uw toepassing goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beschrijving |
| --- | --- |
| `tenant` | De Directory-Tenant die de toepassing heeft toegewezen aan de opgegeven machtigingen, in GUID-indeling. |
| `state` | Een waarde die is opgenomen in de aanvraag die ook wordt geretourneerd in het token antwoord. Dit kan een teken reeks zijn van elke gewenste inhoud. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `admin_consent` | Stel deze **waarde** in op True. |

##### <a name="error-response"></a>Fout bericht

Als de beheerder de machtigingen voor uw toepassing niet goed keuren, ziet de mislukte reactie er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor fout codes die u kunt gebruiken om typen fouten te classificeren en die u kunt gebruiken om te reageren op fouten. |
| `error_description` | Een specifieke fout melding die u kan helpen bij het identificeren van de hoofd oorzaak van een fout. |

Nadat u een geslaagde reactie van het app-inrichtings eindpunt hebt ontvangen, heeft uw app de door u aangevraagde directe toepassings machtigingen verkregen. U kunt nu een token aanvragen voor de gewenste resource.

## <a name="get-a-token"></a>Een Token ophalen

Nadat u de benodigde autorisatie voor uw toepassing hebt verkregen, gaat u verder met het verkrijgen van toegangs tokens voor Api's. Als u een token wilt ophalen met behulp van de client referenties toekenning, verzendt u een POST-aanvraag naar het `/token` micro soft Identity-platform:

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Gebruik uw eigen App-ID voor de beste resultaten: de zelfstudie toepassing vraagt geen nuttige machtigingen aan.) [ ![ Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste case: toegangs token aanvraag met een gedeeld geheim

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De Directory-Tenant waarmee de toepassing moet worden uitgevoerd, in GUID of domeinnaam indeling. |
| `client_id` | Vereist | De toepassings-ID die is toegewezen aan uw app. U kunt deze informatie vinden in de portal waar u uw app hebt geregistreerd. |
| `scope` | Vereist | De waarde die is door gegeven voor de `scope` para meter in deze aanvraag moet de resource-id (id van de toepassings-URI) zijn van de resource die u wilt, met het `.default` achtervoegsel. Voor het Microsoft Graph-voor beeld is de waarde `https://graph.microsoft.com/.default` . <br/>Deze waarde vertelt het micro soft-identiteits platform dat van alle directe toepassings machtigingen die u hebt geconfigureerd voor uw app, het eind punt moet een token uitgeven voor de resources die u wilt gebruiken. `/.default`Zie de documentatie van de [toestemming](v2-permissions-and-consent.md#the-default-scope)voor meer informatie over het bereik. |
| `client_secret` | Vereist | Het client geheim dat u hebt gegenereerd voor uw app in de app-registratie Portal. Het client geheim moet URL-gecodeerd zijn voordat het wordt verzonden. |
| `grant_type` | Vereist | Moet worden ingesteld op `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: toegangs token aanvraag met een certificaat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De Directory-Tenant waarmee de toepassing moet worden uitgevoerd, in GUID of domeinnaam indeling. |
| `client_id` | Vereist |De client-ID van de toepassing die is toegewezen aan uw app. |
| `scope` | Vereist | De waarde die is door gegeven voor de `scope` para meter in deze aanvraag moet de resource-id (id van de toepassings-URI) zijn van de resource die u wilt, met het `.default` achtervoegsel. Voor het Microsoft Graph-voor beeld is de waarde `https://graph.microsoft.com/.default` . <br/>Met deze waarde wordt het micro soft Identity-platform geïnformeerd over alle directe toepassings machtigingen die u hebt geconfigureerd voor uw app. het moet een token uitgeven voor de resources die zijn gekoppeld aan de resource die u wilt gebruiken. `/.default`Zie de documentatie van de [toestemming](v2-permissions-and-consent.md#the-default-scope)voor meer informatie over het bereik. |
| `client_assertion_type` | Vereist | De waarde moet worden ingesteld op `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Vereist | Een verklaring (een JSON-webtoken) die u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Lees de informatie over [certificaat referenties](active-directory-certificate-credentials.md) voor meer informatie over het registreren van uw certificaat en de indeling van de verklaring.|
| `grant_type` | Vereist | Moet worden ingesteld op `client_credentials`. |

U ziet dat de para meters bijna hetzelfde zijn als in het geval van de aanvraag van het gedeelde geheim, behalve dat de para meter client_secret wordt vervangen door twee para meters: een client_assertion_type en client_assertion.

### <a name="successful-response"></a>Geslaagde reactie

Een geslaagd antwoord ziet er zo uit:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` | Het aangevraagde toegangs token. De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals bij een web-API. |
| `token_type` | Geeft de waarde van het token type aan. Het enige type dat het micro soft Identity-platform ondersteunt is `bearer` . |
| `expires_in` | De hoeveelheid tijd die een toegangs token geldig is (in seconden). |

### <a name="error-response"></a>Fout bericht

Een fout bericht ziet er als volgt uit:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor de fout code die u kunt gebruiken om typen fouten te classificeren die optreden en om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| `error_codes` | Een lijst met STS-specifieke fout codes die mogelijk kunnen helpen met diagnostische gegevens. |
| `timestamp` | Het tijdstip waarop de fout is opgetreden. |
| `trace_id` | Een unieke id voor de aanvraag om te helpen met diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag om te helpen met diagnostische gegevens over onderdelen. |

## <a name="use-a-token"></a>Een token gebruiken

Nu u een token hebt verkregen, gebruikt u het token om aanvragen voor de resource te maken. Wanneer het token verloopt, herhaalt u de aanvraag voor het `/token` eind punt om een nieuw toegangs token op te halen.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Code voorbeelden en andere documentatie

Raadpleeg de [documentatie over client referenties overzicht](https://aka.ms/msal-net-client-credentials) van de micro soft-verificatie bibliotheek

| Voorbeeld | Platform |Beschrijving |
|--------|----------|------------|
|[Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2,1-console | Een eenvoudige .NET core-toepassing waarin de gebruikers van een Microsoft Graph Tenant worden weer gegeven met de identiteit van de toepassing, in plaats van namens een gebruiker. Het voor beeld illustreert ook de variatie met behulp van certificaten voor verificatie. |
|[Active-Directory-DotNet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Een webtoepassing die gegevens van de Microsoft Graph synchroniseert met behulp van de identiteit van de toepassing, in plaats van namens een gebruiker. |

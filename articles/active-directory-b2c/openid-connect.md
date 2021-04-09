---
title: Webaanmelding met OpenID Connect Connect-Azure Active Directory B2C
description: Bouw webtoepassingen met behulp van het OpenID Connect Connect-verificatie protocol in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 87415fc98bbcc9331ae4ff6282a65c85b570042d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579770"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webaanmelding met OpenID Connect Connect in Azure Active Directory B2C

OpenID Connect Connect is een verificatie protocol dat is gebouwd op OAuth 2,0 en dat kan worden gebruikt om gebruikers veilig te ondertekenen bij webtoepassingen. Door gebruik te maken van de implementatie van de Azure Active Directory B2C (Azure AD B2C) van OpenID Connect Connect, kunt u zich aanmelden, aanmelden en andere ervaring voor identiteits beheer in uw webtoepassingen tot Azure Active Directory (Azure AD). In deze hand leiding wordt uitgelegd hoe u dit kunt doen op een taal onafhankelijke manier. Hierin wordt beschreven hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van de open source-bibliotheken.

> [!NOTE]
> De meeste open source-verificatie bibliotheken verkrijgen en valideren de JWT-tokens voor uw toepassing. We raden u aan deze opties te verkennen, in plaats van uw eigen code te implementeren. Zie [overzicht van de micro soft Authentication Library (MSAL) en de](../active-directory/develop/msal-overview.md) [micro soft Identity Web-verificatie bibliotheek](../active-directory/develop/microsoft-identity-web.md)voor meer informatie.

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) breidt het OAuth 2,0- *autorisatie* protocol uit voor gebruik als *verificatie* protocol. Met dit verificatie protocol kunt u eenmalige aanmelding uitvoeren. Hierin wordt het concept van een *id-token* geïntroduceerd, waarmee de client de identiteit van de gebruiker kan verifiëren en basis profiel informatie over de gebruiker kan verkrijgen.

Omdat het OAuth 2,0 uitbreidt, kunnen toepassingen ook veilig *toegangs tokens* verkrijgen. U kunt toegangs tokens gebruiken om toegang te krijgen tot bronnen die worden beveiligd door een [autorisatie server](protocols-overview.md). OpenID Connect Connect wordt aanbevolen als u een webtoepassing bouwt die wordt gehost op een server en toegankelijk is via een browser. Zie het [overzicht van tokens in azure Active Directory B2C](tokens-overview.md) voor meer informatie over tokens.

Azure AD B2C breidt het Standard OpenID Connect Connect-protocol uit voor meer dan eenvoudige verificatie en autorisatie. Hiermee wordt de [para meter User flow](user-flow-overview.md)geïntroduceerd, waarmee u OpenID Connect Connect kunt gebruiken om gebruikers ervaringen toe te voegen aan uw toepassing, zoals aanmelden, aanmelden en Profiel beheer.

## <a name="send-authentication-requests"></a>Verificatie aanvragen verzenden

Wanneer uw webtoepassing de gebruiker moet verifiëren en een gebruikers stroom kan uitvoeren, kan deze de gebruiker naar het `/authorize` eind punt sturen. De gebruiker actie onderneemt afhankelijk van de gebruikers stroom.

In deze aanvraag geeft de client aan welke machtigingen moeten worden verkregen van de gebruiker in de `scope` para meter en geeft u de gebruikers stroom op die moet worden uitgevoerd. Als u wilt weten hoe de aanvraag werkt, kunt u de aanvraag in een browser plakken en deze uitvoeren. Vervang `{tenant}` door de naam van uw tenant. Vervang door `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` de app-id van de toepassing die u eerder hebt geregistreerd in uw Tenant. Wijzig ook de naam van het beleid ( `{policy}` ) in de naam van het beleid dat u in uw Tenant hebt, bijvoorbeeld `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| bouw | Yes | De naam van uw Azure AD B2C-Tenant |
| verslaggev | Yes | De gebruikers stroom die moet worden uitgevoerd. Geef de naam op van een gebruikers stroom die u hebt gemaakt in uw Azure AD B2C-Tenant. Bijvoorbeeld: `b2c_1_sign_in` , `b2c_1_sign_up` of `b2c_1_edit_profile` . |
| client_id | Yes | De toepassings-ID die de [Azure Portal](https://portal.azure.com/) toegewezen aan uw toepassing. |
| nonce | Yes | Een waarde die is opgenomen in de aanvraag (gegenereerd door de toepassing) die is opgenomen in de resulterende ID-token als claim. De toepassing kan vervolgens deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. |
| response_type | Yes | Moet een ID-token voor OpenID Connect Connect bevatten. Als uw webtoepassing ook tokens nodig heeft voor het aanroepen van een web-API, kunt u gebruiken `code+id_token` . |
| scope | Yes | Een lijst met door spaties gescheiden bereiken. Het `openid` bereik geeft een machtiging aan voor het aanmelden van de gebruiker en het ophalen van gegevens over de gebruiker in de vorm van ID-tokens. Het `offline_access` bereik is optioneel voor webtoepassingen. Dit geeft aan dat uw toepassing een *vernieuwings token* nodig heeft voor uitgebreide toegang tot resources. |
| verschijnt | No | Het type gebruikers interactie dat is vereist. De enige geldige waarde is op dit moment `login` , waardoor de gebruiker de referenties voor die aanvraag invoert. |
| redirect_uri | No | De `redirect_uri` para meter van uw toepassing, waar verificatie reacties kunnen worden verzonden en ontvangen door uw toepassing. De waarde moet exact overeenkomen met een van de `redirect_uri` para meters die u hebt geregistreerd in de Azure Portal, behalve dat deze URL moet worden gecodeerd. |
| response_mode | No | De methode die wordt gebruikt om de resulterende autorisatie code terug te sturen naar uw toepassing. Dit kan ofwel `query` , `form_post` , of `fragment` .  De `form_post` antwoord modus wordt aanbevolen voor de beste beveiliging. |
| staat | No | Een waarde die is opgenomen in de aanvraag die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van elke gewenste inhoud. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om vervalsing van aanvragen op meerdere sites te voor komen. De status wordt ook gebruikt voor het coderen van informatie over de status van de gebruiker in de toepassing voordat de verificatie aanvraag heeft plaatsgevonden, zoals de pagina waarop deze zich bevonden. |
| login_hint | No| Kan worden gebruikt om het veld voor de aanmeldings naam van de aanmeldings pagina vooraf in te vullen. Zie [vooraf invullen van de aanmeldings naam](direct-signin.md#prepopulate-the-sign-in-name)voor meer informatie.  |
| domain_hint | No| Biedt een hint voor het Azure AD B2C van de ID-provider voor sociale netwerken die moet worden gebruikt voor aanmelding. Als een geldige waarde is opgenomen, gaat de gebruiker rechtstreeks naar de aanmeldings pagina van de identiteits provider.  Zie voor meer informatie [aanmelden door sturen naar een sociale provider](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Aangepaste parameters | No| Aangepaste para meters die kunnen worden gebruikt met [aangepaste beleids regels](custom-policy-overview.md). Bijvoorbeeld een [dynamische URL voor aangepaste pagina-inhoud](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)of [claim conflicten voor sleutel waarden](claim-resolver-overview.md#oauth2-key-value-parameters). |

Op dit moment wordt de gebruiker gevraagd om de werk stroom te volt ooien. De gebruiker moet mogelijk hun gebruikers naam en wacht woord invoeren, zich aanmelden met een sociale identiteit of zich aanmelden voor de Directory. Er kunnen verschillende stappen worden uitgevoerd, afhankelijk van de manier waarop de gebruikers stroom is gedefinieerd.

Nadat de gebruiker de gebruikers stroom heeft voltooid, wordt er met de aangegeven para meter een reactie naar uw toepassing geretourneerd met `redirect_uri` behulp van de methode die is opgegeven in de `response_mode` para meter. Het antwoord is hetzelfde voor elk van de voor gaande gevallen, onafhankelijk van de gebruikers stroom.

Er is een succes volle reactie met het `response_mode=fragment` volgende:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --------- | ----------- |
| id_token | Het ID-token dat de toepassing heeft aangevraagd. U kunt het ID-token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te starten. |
| code | De autorisatie code die de toepassing heeft aangevraagd als u deze hebt gebruikt `response_type=code+id_token` . De toepassing kan de autorisatie code gebruiken om een toegangs token voor een doel bron aan te vragen. Autorisatie codes verlopen doorgaans na ongeveer 10 minuten. |
| staat | Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De toepassing moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` para meter, zodat de toepassing deze op de juiste wijze kan afhandelen:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --------- | ----------- |
| fout | Een code die kan worden gebruikt voor het classificeren van de typen fouten die optreden. |
| error_description | Een specifiek fout bericht dat kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| staat | Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De toepassing moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="validate-the-id-token"></a>Het ID-token valideren

Alleen het ontvangen van een ID-token is voldoende om de gebruiker te verifiëren. Valideer de hand tekening van het ID-token en controleer de claims in het token volgens de vereisten van uw toepassing. Azure AD B2C maakt gebruik van [JSON Web tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) en open bare-sleutel cryptografie om tokens te ondertekenen en te controleren of ze geldig zijn. 

> [!NOTE]
> De meeste open source-verificatie bibliotheken valideren de JWT-tokens voor uw toepassing. We raden u aan deze opties te verkennen, in plaats van uw eigen validatie logica te implementeren. Zie [overzicht van de micro soft Authentication Library (MSAL) en de](../active-directory/develop/msal-overview.md) [micro soft Identity Web-verificatie bibliotheek](../active-directory/develop/microsoft-identity-web.md)voor meer informatie.

Azure AD B2C heeft een OpenID Connect voor het verbinden van meta gegevens, waarmee een toepassing informatie over Azure AD B2C tijdens runtime kan ophalen. Deze informatie omvat eind punten, token inhoud en sleutels voor token-ondertekening. Er is een JSON-meta gegevens document voor elke gebruikers stroom in uw B2C-Tenant. Het meta gegevens document voor de `b2c_1_sign_in` gebruikers stroom in `fabrikamb2c.onmicrosoft.com` bevindt zich bijvoorbeeld in:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Een van de eigenschappen van dit configuratie document is `jwks_uri` , waarvan de waarde voor dezelfde gebruikers stroom zou zijn:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Als u wilt bepalen welke gebruikers stroom is gebruikt voor het ondertekenen van een ID-token (en van waaruit de meta gegevens worden opgehaald), hebt u twee opties. Eerst wordt de naam van de gebruikers stroom opgenomen in de `acr` claim in het ID-token. De andere optie is het coderen van de gebruikers stroom in de waarde van de `state` para meter wanneer u de aanvraag afgeeft en deze vervolgens decodeert om te bepalen welke gebruikers stroom is gebruikt. Beide methoden zijn geldig.

Nadat u het meta gegevens document van het OpenID Connect Connect-meta gegevens-eind punt hebt opgehaald, kunt u de open bare sleutels van RSA 256 gebruiken om de hand tekening van het ID-token te valideren. Er kunnen meerdere sleutels worden vermeld op dit eind punt, elk geïdentificeerd door een `kid` claim. De header van het ID-token bevat ook een `kid` claim, waarmee wordt aangegeven welke van deze sleutels is gebruikt voor het ondertekenen van het ID-token.

Als u de tokens van Azure AD B2C wilt verifiëren, moet u de open bare sleutel genereren met de exponent (e) en modulus (n). U moet bepalen hoe u dit in uw respectieve programmeer taal kunt doen. De officiële documentatie over het genereren van open bare sleutels met het RSA-protocol vindt u hier: https://tools.ietf.org/html/rfc3447#section-3.1

Nadat u de hand tekening van het ID-token hebt gevalideerd, zijn er verschillende claims die u moet verifiëren. Bijvoorbeeld:

- Valideer de `nonce` claim om token replay-aanvallen te voor komen. De waarde moet zijn wat u hebt opgegeven in het aanmeldings verzoek.
- Valideer de `aud` claim om ervoor te zorgen dat het ID-token is uitgegeven voor uw toepassing. De waarde moet de toepassings-ID van uw toepassing zijn.
- Valideer de `iat` en `exp` claims om ervoor te zorgen dat het id-token niet is verlopen.

Er zijn ook meerdere validaties die u moet uitvoeren. De validaties worden gedetailleerd beschreven in de [OpenID Connect Connect core-specificatie](https://openid.net/specs/openid-connect-core-1_0.html). Het is ook mogelijk dat u aanvullende claims wilt valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

- Controleren of de gebruiker/organisatie zich heeft geregistreerd voor de toepassing.
- Controleren of de gebruiker de juiste autorisatie/bevoegdheden heeft.
- Ervoor zorgen dat er een zekere mate van verificatie is opgetreden, zoals Azure AD-Multi-Factor Authentication.

Nadat het ID-token is gevalideerd, kunt u met de gebruiker beginnen met een sessie. U kunt de claims in het ID-token gebruiken om informatie over de gebruiker in uw toepassing op te halen. Gebruik voor deze informatie is onder andere weer gave, records en autorisatie.

## <a name="get-a-token"></a>Een Token ophalen

Als uw webtoepassing alleen gebruikers stromen moet uitvoeren, kunt u de volgende gedeelten overs Laan. Deze secties zijn alleen van toepassing op webtoepassingen die geverifieerde aanroepen naar een web-API moeten maken en ook worden beveiligd door Azure AD B2C.

U kunt de autorisatie code die u hebt verkregen (met behulp van `response_type=code+id_token` ) voor een token voor de gewenste resource inwisselen door een `POST` aanvraag naar het `/token` eind punt te verzenden. In Azure AD B2C kunt u [toegangs tokens voor andere api's](access-tokens.md#request-a-token) zoals gebruikelijk aanvragen door hun bereik (en) op te geven in de aanvraag.

U kunt ook een toegangs token aanvragen voor de eigen back-end web-API van uw app door middel van het gebruik van de client-ID van de app als het aangevraagde bereik (wat resulteert in een toegangs token met die client-ID als ' doel groep '):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| bouw | Yes | De naam van uw Azure AD B2C-Tenant |
| verslaggev | Yes | De gebruikers stroom die is gebruikt om de autorisatie code op te halen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. Voeg deze para meter toe aan de query reeks, niet op de hoofd tekst van het bericht. |
| client_id | Yes | De toepassings-ID die de [Azure Portal](https://portal.azure.com/) toegewezen aan uw toepassing. |
| client_secret | Ja, in Web Apps | Het toepassings geheim dat is gegenereerd in de [Azure Portal](https://portal.azure.com/). Client geheimen worden gebruikt in deze stroom voor web-app-scenario's, waarbij de client veilig een client geheim kan opslaan. Voor scenario's met een systeem eigen app (open bare client) kunnen client geheimen niet veilig worden opgeslagen, dus niet gebruikt voor deze stroom. Als u een client geheim gebruikt, moet u het periodiek wijzigen. |
| code | Yes | De autorisatie code die u aan het begin van de gebruikers stroom hebt verkregen. |
| grant_type | Yes | Het type toekenning, dat `authorization_code` voor de autorisatie code stroom moet gelden. |
| redirect_uri | Yes | De `redirect_uri` para meter van de toepassing waarin u de autorisatie code hebt ontvangen. |
| scope | No | Een lijst met door spaties gescheiden bereiken. Het `openid` bereik geeft een machtiging aan voor het aanmelden van de gebruiker en het ophalen van gegevens over de gebruiker in de vorm van id_token-para meters. Het kan worden gebruikt om tokens te verkrijgen voor de eigen back-end web-API van uw toepassing, die wordt vertegenwoordigd door dezelfde toepassings-ID als de client. Het `offline_access` bereik geeft aan dat uw toepassing een vernieuwings token nodig heeft voor uitgebreide toegang tot resources. |

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| not_before | Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type | De waarde van het token type. `Bearer` is het enige type dat wordt ondersteund. |
| access_token | De ondertekende JWT-token die u hebt aangevraagd. |
| scope | De bereiken waarvoor het token geldig is. |
| expires_in | De tijds duur dat het toegangs token geldig is (in seconden). |
| refresh_token | Een OAuth 2,0-vernieuwings token. De toepassing kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens kunnen worden gebruikt om de toegang tot bronnen gedurende lange tijd te behouden. Het bereik `offline_access` moet zijn gebruikt in de autorisatie-en Token aanvragen om een vernieuwings token te kunnen ontvangen. |

Fout berichten zien er als volgt uit:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| fout | Een code die kan worden gebruikt voor het classificeren van typen fouten die optreden. |
| error_description | Een bericht dat kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="use-the-token"></a>Het token gebruiken

Nu u een toegangs token hebt aangeschaft, kunt u het token gebruiken in aanvragen voor uw back-end-web-Api's door dit op te nemen in de `Authorization` header:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Het token vernieuwen

ID-tokens verlopen in korte tijd. Vernieuw de tokens nadat deze verlopen om toegang te kunnen krijgen tot bronnen. U kunt een token vernieuwen door een andere `POST` aanvraag naar het `/token` eind punt te verzenden. Geef deze keer de `refresh_token` para meter op in plaats van de `code` para meter:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| bouw | Yes | De naam van uw Azure AD B2C-Tenant |
| verslaggev | Yes | De gebruikers stroom die is gebruikt om het oorspronkelijke vernieuwings token te verkrijgen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. Voeg deze para meter toe aan de query reeks, niet op de hoofd tekst van het bericht. |
| client_id | Yes | De toepassings-ID die de [Azure Portal](https://portal.azure.com/) toegewezen aan uw toepassing. |
| client_secret | Ja, in Web Apps | Het toepassings geheim dat is gegenereerd in de [Azure Portal](https://portal.azure.com/). Client geheimen worden gebruikt in deze stroom voor web-app-scenario's, waarbij de client veilig een client geheim kan opslaan. Voor scenario's met een systeem eigen app (open bare client) kunnen client geheimen niet veilig worden opgeslagen, dus niet gebruikt voor deze aanroep. Als u een client geheim gebruikt, moet u het periodiek wijzigen. |
| grant_type | Yes | Het type toekenning dat `refresh_token` voor dit deel van de autorisatie code stroom moet gelden. |
| refresh_token | Yes | Het oorspronkelijke vernieuwings token dat is verkregen in het tweede deel van de stroom. Het `offline_access` bereik moet worden gebruikt in de autorisatie-en Token aanvragen om een vernieuwings token te kunnen ontvangen. |
| redirect_uri | No | De `redirect_uri` para meter van de toepassing waarin u de autorisatie code hebt ontvangen. |
| scope | No | Een lijst met door spaties gescheiden bereiken. Het `openid` bereik geeft een machtiging aan voor het aanmelden van de gebruiker en het ophalen van gegevens over de gebruiker in de vorm van ID-tokens. Het kan worden gebruikt voor het verzenden van tokens naar de eigen back-end web-API van uw toepassing, die wordt vertegenwoordigd door dezelfde toepassings-ID als de client. Het `offline_access` bereik geeft aan dat uw toepassing een vernieuwings token nodig heeft voor uitgebreide toegang tot resources. |

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| not_before | Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type | De waarde van het token type. `Bearer` is het enige type dat wordt ondersteund. |
| access_token | De aangevraagde ondertekende JWT-token. |
| scope | Het bereik waarvoor het token geldig is. |
| expires_in | De tijds duur dat het toegangs token geldig is (in seconden). |
| refresh_token | Een OAuth 2,0-vernieuwings token. De toepassing kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens kunnen worden gebruikt om de toegang tot bronnen gedurende lange tijd te behouden. |

Fout berichten zien er als volgt uit:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| fout | Een code die kan worden gebruikt voor het classificeren van typen fouten die optreden. |
| error_description | Een bericht dat kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="send-a-sign-out-request"></a>Een afmeldings aanvraag verzenden

Wanneer u de gebruiker van de toepassing wilt ondertekenen, is het niet voldoende om de cookies van de toepassing te wissen of de sessie te beëindigen met de gebruiker. De gebruiker omleiden naar Azure AD B2C om u af te melden. Als u dit niet doet, kan de gebruiker mogelijk opnieuw worden geverifieerd bij uw toepassing zonder dat u de referenties opnieuw hoeft in te voeren. Zie [Azure AD B2C-sessie](session-behavior.md)voor meer informatie.

Als u de gebruiker wilt afmelden, moet u de gebruiker omleiden naar het `end_session` eind punt dat wordt vermeld in het OpenID Connect-meta gegevens document dat eerder is beschreven:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| bouw | Yes | De naam van uw Azure AD B2C-Tenant |
| verslaggev | Yes | De gebruikers stroom die u wilt gebruiken voor het ondertekenen van de gebruiker uit uw toepassing. |
| id_token_hint| No | Een eerder uitgegeven ID-token om aan het afmeldings eindpunt door te geven als hint voor de huidige geverifieerde sessie van de eind gebruiker met de client. Het `id_token_hint` zorgt ervoor dat de `post_logout_redirect_uri` URL van een geregistreerd antwoord in uw Azure AD B2C toepassings instellingen is. Zie [uw afleiding voor afmelding beveiligen](#secure-your-logout-redirect)voor meer informatie. |
| client_id | Geen | De toepassings-ID die de [Azure Portal](https://portal.azure.com/) toegewezen aan uw toepassing.<br><br>\**Dit is vereist bij het gebruik `Application` van de SSO-configuratie voor isolatie en het vereisen van een _id-token_ in de afmeldings aanvraag is ingesteld op `No` .* |
| post_logout_redirect_uri | No | De URL waarnaar de gebruiker wordt omgeleid na een geslaagde afmelding. Als deze niet is opgenomen, wordt in Azure AD B2C de gebruiker een Gene riek bericht weer gegeven. Tenzij u een opgeeft `id_token_hint` , moet u deze URL niet als antwoord-URL registreren in uw Azure AD B2C toepassings instellingen. |
| staat | No | Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De toepassing moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

### <a name="secure-your-logout-redirect"></a>Uw afmeldings omleiding beveiligen

Na afmelden wordt de gebruiker omgeleid naar de URI die is opgegeven in de `post_logout_redirect_uri` para meter, ongeacht de antwoord-url's die zijn opgegeven voor de toepassing. Als er echter een geldig `id_token_hint` wordt door gegeven en het **token voor het vereisen van een id in afmeldings aanvragen** is ingeschakeld, wordt door Azure AD B2C gecontroleerd of de waarde `post_logout_redirect_uri` overeenkomt met een van de geconfigureerde omleidings-uri's van de toepassing voordat de omleiding wordt uitgevoerd. Als er geen overeenkomende antwoord-URL voor de toepassing is geconfigureerd, wordt een fout bericht weer gegeven en wordt de gebruiker niet omgeleid.

Zie [sessie gedrag configureren in azure Active Directory B2C](session-behavior.md#secure-your-logout-redirect)voor het instellen van het vereiste id-token bij afmeldings aanvragen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-behavior.md).
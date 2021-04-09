---
title: Stroom van autorisatie code-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het bouwen van web-apps met behulp van Azure AD B2C en OpenID Connect Connect Authentication Protocol.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a6a993fdf4fd266afb9459fedd13412d8796e0a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611501"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2,0-autorisatie code stroom in Azure Active Directory B2C

U kunt de OAuth 2,0-autorisatie code toekenning gebruiken in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Met de Azure Active Directory B2C (Azure AD B2C)-implementatie van OAuth 2,0 kunt u registratie, aanmelding en andere identiteits beheer taken toevoegen aan uw apps met één pagina, mobiel en bureau blad. Dit artikel is taal onafhankelijk. In het artikel wordt beschreven hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van open-source bibliotheken. Als dat mogelijk is, wordt u aangeraden de ondersteunde micro soft Authentication libraries (MSAL) te gebruiken. Bekijk de voor beeld- [apps die gebruikmaken van MSAL](code-samples.md).

De OAuth 2,0-autorisatie code stroom wordt beschreven in [sectie 4,1 van de oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749). U kunt deze gebruiken voor verificatie en autorisatie in de meeste [toepassings typen](application-types.md), waaronder webtoepassingen, toepassingen met één pagina en systeem eigen geïnstalleerde toepassingen. U kunt de OAuth 2,0-autorisatie code stroom gebruiken om veilig toegangs tokens te verkrijgen en tokens te vernieuwen voor uw toepassingen, die kunnen worden gebruikt voor toegang tot bronnen die worden beveiligd door een [autorisatie server](protocols-overview.md).  Met het vernieuwings token kan de client nieuwe toegangs-en vernieuwings tokens verkrijgen zodra het toegangs token is verlopen, doorgaans na een uur.

Dit artikel is gericht op de OAuth 2,0-autorisatie code stroom voor **open bare clients** . Een open bare client is een client toepassing die niet vertrouwd kan worden om de integriteit van een geheim wachtwoord veilig te houden. Dit geldt ook voor toepassingen met één pagina, mobiele apps, bureaublad toepassingen en hoofd zakelijk elke toepassing die niet op een server wordt uitgevoerd.

> [!NOTE]
> Als u identiteits beheer wilt toevoegen aan een web-app met behulp van Azure AD B2C, gebruikt u [OpenID Connect Connect](openid-connect.md) in plaats van OAuth 2,0.

Azure AD B2C breidt de standaard OAuth 2,0-stromen uit om meer dan eenvoudige verificatie en autorisatie uit te voeren. Hiermee wordt de [gebruikers stroom](user-flow-overview.md)geïntroduceerd. Met gebruikers stromen kunt u OAuth 2,0 gebruiken om gebruikers ervaringen toe te voegen aan uw toepassing, zoals aanmelden, aanmelden en Profiel beheer. Id-providers die gebruikmaken van het OAuth 2,0-protocol zijn [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [github](identity-provider-github.md), [Google](identity-provider-google.md)en [LinkedIn](identity-provider-linkedin.md).

De HTTP-aanvragen in dit artikel proberen:

1. Vervang `{tenant}` door de naam van uw Azure AD B2C-tenant.
1. Vervang door `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` de app-id van een toepassing die u eerder hebt geregistreerd in uw Azure AD B2C-Tenant.
1. Vervang door `{policy}` de naam van een beleid dat u hebt gemaakt in uw Tenant, bijvoorbeeld `b2c_1_sign_in` .

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Omleidings-URI-installatie vereist voor apps met één pagina

De autorisatie code stroom voor toepassingen met één pagina vereist enkele aanvullende instellingen.  Volg de instructies voor [het maken van uw toepassing met één pagina](tutorial-register-spa.md) om de omleidings-URI correct te markeren als ingeschakeld voor CORS. Als u een bestaande omleidings-URI wilt bijwerken om CORS in te scha kelen, kunt u klikken op de prompt voor migratie in het gedeelte Web van het tabblad **verificatie** van de **app-registratie**. U kunt ook de **manifest editor van app-registraties** openen en het `type` veld voor de omleidings-URI instellen `spa` in de `replyUrlsWithType` sectie.

Het `spa` omleidings type is achterwaarts compatibel met de impliciete stroom. Apps die momenteel gebruikmaken van de impliciete stroom om tokens op te halen `spa` , kunnen zonder problemen naar het omleidings-URI-type worden verplaatst en blijven de impliciete stroom gebruiken.

## <a name="1-get-an-authorization-code"></a>1. een autorisatie code ophalen
De autorisatie code stroom begint met de client die de gebruiker omleidt naar het `/authorize` eind punt. Dit is het interactieve deel van de stroom, waar de gebruiker actie onderneemt. In deze aanvraag geeft de client in de `scope` para meter de machtigingen op die nodig zijn voor het verkrijgen van de gebruiker. De volgende drie voor beelden (met regel einden voor de Lees baarheid) gebruiken elk een andere gebruikers stroom.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
| verslaggev | Vereist | De gebruikers stroom die moet worden uitgevoerd. Geef de naam op van een gebruikers stroom die u hebt gemaakt in uw Azure AD B2C-Tenant. Bijvoorbeeld: `b2c_1_sign_in` , `b2c_1_sign_up` of `b2c_1_edit_profile` . |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com). |
| response_type |Vereist |Het antwoord type, dat `code` voor de autorisatie code stroom moet bevatten. |
| redirect_uri |Vereist |De omleidings-URI van uw app, waar verificatie reacties worden verzonden en ontvangen door uw app. De waarde moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd, behalve dat de URL moet worden gecodeerd. |
| scope |Vereist |Een lijst met door spaties gescheiden bereiken. Een enkele bereik waarde geeft aan Azure Active Directory (Azure AD) beide machtigingen die worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen. U kunt ook de `openid` scope gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| response_mode |Aanbevolen |De methode die u gebruikt om de resulterende autorisatie code terug te sturen naar uw app. Dit kan `query` , `form_post` , of `fragment` . |
| staat |Aanbevolen |Een waarde die in de aanvraag is opgenomen en die een teken reeks kan zijn van alle inhoud die u wilt gebruiken. Normaal gesp roken wordt een wille keurig gegenereerde unieke waarde gebruikt om vervalsing van aanvragen op meerdere sites te voor komen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is opgetreden. Bijvoorbeeld de pagina waarop de gebruiker zich bevond of de gebruikers stroom die werd uitgevoerd. |
| verschijnt |Optioneel |Het type gebruikers interactie dat is vereist. Op dit moment is de enige geldige waarde `login` die de gebruiker in staat stelt hun referenties in te voeren voor deze aanvraag. Eenmalige aanmelding wordt niet van kracht. |
| code_challenge  | Aanbevolen/vereist | Wordt gebruikt voor het beveiligen van autorisatie code subsidies via de bewijs code voor code Exchange (PKCE). Vereist als `code_challenge_method` is opgenomen. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. Dit wordt nu aanbevolen voor alle toepassings typen: systeem eigen apps, SPAs en vertrouwelijke clients, zoals web-apps. | 
| `code_challenge_method` | Aanbevolen/vereist | De methode die wordt gebruikt voor het coderen `code_verifier` van de voor de `code_challenge` para meter. Dit *moet* zijn `S256` , maar de spec staat het gebruik van toe `plain` als de client om de een of andere reden sha256 niet kan ondersteunen. <br/><br/>Als deze is uitgesloten, `code_challenge` wordt ervan uitgegaan dat de tekst zonder opmaak wordt gebruikt `code_challenge` . Micro soft Identity platform ondersteunt zowel `plain` als `S256` . Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. Dit is vereist voor [apps van één pagina die gebruikmaken van de autorisatie code stroom](tutorial-register-spa.md).|
| login_hint | No| Kan worden gebruikt om het veld voor de aanmeldings naam van de aanmeldings pagina vooraf in te vullen. Zie [vooraf invullen van de aanmeldings naam](direct-signin.md#prepopulate-the-sign-in-name)voor meer informatie.  |
| domain_hint | No| Biedt een hint voor het Azure AD B2C van de ID-provider voor sociale netwerken die moet worden gebruikt voor aanmelding. Als een geldige waarde is opgenomen, gaat de gebruiker rechtstreeks naar de aanmeldings pagina van de identiteits provider.  Zie voor meer informatie [aanmelden door sturen naar een sociale provider](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Aangepaste parameters | No| Aangepaste para meters die kunnen worden gebruikt met [aangepaste beleids regels](custom-policy-overview.md). Bijvoorbeeld een [dynamische URL voor aangepaste pagina-inhoud](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)of [claim conflicten voor sleutel waarden](claim-resolver-overview.md#oauth2-key-value-parameters). |

Op dit moment wordt de gebruiker gevraagd om de werk stroom van de gebruikers stroom te volt ooien. Dit kan betekenen dat de gebruiker die de gebruikers naam en het wacht woord invoert, zich aanmeldt met een sociale identiteit, zich aanmeldt voor de Directory of een ander aantal stappen uitvoert. Gebruikers acties zijn afhankelijk van de manier waarop de gebruikers stroom is gedefinieerd.

Nadat de gebruiker de gebruikers stroom heeft voltooid, retourneert Azure AD een reactie op uw app op basis van de waarde die u hebt gebruikt voor `redirect_uri` . Hierbij wordt gebruikgemaakt van de methode die is opgegeven in de `response_mode` para meter. Het antwoord is precies hetzelfde voor elk van de gebruikers actie scenario's, onafhankelijk van de uitgevoerde gebruikers stroom.

Een geslaagd antwoord dat `response_mode=query` er als volgt uitziet:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beschrijving |
| --- | --- |
| code |De autorisatie code die door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token voor een doel bron aan te vragen. Autorisatie codes zijn zeer korter. Normaal gesp roken verlopen ze na ongeveer 10 minuten. |
| staat |Zie de volledige beschrijving in de tabel in de voor gaande sectie. Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

Fout reacties kunnen ook worden verzonden naar de omleidings-URI, zodat de app deze op de juiste wijze kan afhandelen:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om de typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| staat |Zie de volledige beschrijving in de voor gaande tabel. Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="2-get-an-access-token"></a>2. een toegangs Token ophalen
Nu u een autorisatie code hebt aangeschaft, kunt u de voor een-token inwisselen voor `code` de beoogde resource door een post-aanvraag naar het `/token` eind punt te verzenden. In Azure AD B2C kunt u [toegangs tokens aanvragen voor andere api's](access-tokens.md#request-a-token) zoals gebruikelijk door hun bereik (en) op te geven in de aanvraag.

U kunt ook een toegangs token aanvragen voor de eigen back-end web-API van uw app door middel van het gebruik van de client-ID van de app als het aangevraagde bereik (wat resulteert in een toegangs token met die client-ID als ' doel groep '):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
|verslaggev| Vereist| De gebruikers stroom die is gebruikt om de autorisatie code op te halen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com).|
| client_secret | Ja, in Web Apps | Het toepassings geheim dat is gegenereerd in de [Azure Portal](https://portal.azure.com/). Client geheimen worden gebruikt in deze stroom voor web-app-scenario's, waarbij de client veilig een client geheim kan opslaan. Voor scenario's met een systeem eigen app (open bare client) kunnen client geheimen niet veilig worden opgeslagen en worden ze daarom niet in deze aanroep gebruikt. Als u een client geheim gebruikt, moet u het periodiek wijzigen. |
| grant_type |Vereist |Het type toekenning. Voor de autorisatie code stroom moet het toekennings type zijn `authorization_code` . |
| scope |Aanbevolen |Een lijst met door spaties gescheiden bereiken. Een enkele Scope waarde geeft aan dat er voor Azure AD beide machtigingen worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen.  U kunt ook de `openid` scope gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| code |Vereist |De autorisatie code die u hebt verkregen in het eerste gedeelte van de stroom. |
| redirect_uri |Vereist |De omleidings-URI van de toepassing waarvoor u de autorisatie code hebt ontvangen. |
| code_verifier | aanbevelingen | Hetzelfde code_verifier dat is gebruikt om de authorization_code op te halen. Vereist als PKCE is gebruikt in de aanvraag voor autorisatie code toekenning. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |

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
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type |De waarde van het token type. Het enige type dat door Azure AD wordt ondersteund, is Bearer. |
| access_token |De door u aangevraagde ondertekende JSON Web Token (JWT). |
| scope |De bereiken waarvoor het token geldig is. U kunt scopes ook gebruiken om tokens in de cache op te slaan voor later gebruik. |
| expires_in |De tijds duur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens zijn lang in het geleefde. U kunt ze gebruiken om de toegang tot resources te bewaren gedurende lange Peri Oden. Zie de [Azure AD B2C-token verwijzing](tokens-overview.md)voor meer informatie. |

Fout berichten zien er als volgt uit:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om de typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="3-use-the-token"></a>3. het token gebruiken
Nu u een toegangs token hebt aangeschaft, kunt u het token gebruiken in aanvragen voor uw back-end-web-Api's door dit op te nemen in de `Authorization` header:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Vernieuw het token
Toegangs tokens en ID-tokens zijn korte duur. Nadat deze zijn verlopen, moet u ze vernieuwen om toegang te blijven houden tot resources. Als u dit wilt doen, moet u een nieuwe POST-aanvraag indienen bij het `/token` eind punt. Geef deze keer de `refresh_token` volgende opties op in plaats van `code` :

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
|verslaggev |Vereist |De gebruikers stroom die is gebruikt om het oorspronkelijke vernieuwings token te verkrijgen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com). |
| client_secret | Ja, in Web Apps | Het toepassings geheim dat is gegenereerd in de [Azure Portal](https://portal.azure.com/). Client geheimen worden gebruikt in deze stroom voor web-app-scenario's, waarbij de client veilig een client geheim kan opslaan. Voor scenario's met een systeem eigen app (open bare client) kunnen client geheimen niet veilig worden opgeslagen en worden ze daarom niet in deze aanroep gebruikt. Als u een client geheim gebruikt, moet u het periodiek wijzigen. |
| grant_type |Vereist |Het type toekenning. Voor dit gedeelte van de autorisatie code stroom moet het toekennings type zijn `refresh_token` . |
| scope |Aanbevolen |Een lijst met door spaties gescheiden bereiken. Een enkele Scope waarde geeft aan dat er voor Azure AD beide machtigingen worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen.  U kunt ook de `openid` scope gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| redirect_uri |Optioneel |De omleidings-URI van de toepassing waarvoor u de autorisatie code hebt ontvangen. |
| refresh_token |Vereist |Het oorspronkelijke vernieuwings token dat u hebt verkregen in het tweede gedeelte van de stroom. |

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
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type |De waarde van het token type. Het enige type dat door Azure AD wordt ondersteund, is Bearer. |
| access_token |De ondertekende JWT die u hebt aangevraagd. |
| scope |De bereiken waarvoor het token geldig is. U kunt de scopes ook gebruiken om tokens in de cache op te slaan voor later gebruik. |
| expires_in |De tijds duur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens worden lang bewaard en kunnen worden gebruikt om de toegang tot resources te bewaren gedurende lange Peri Oden. Zie de [Azure AD B2C-token verwijzing](tokens-overview.md)voor meer informatie. |

Fout berichten zien er als volgt uit:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Uw eigen Azure AD B2C Directory gebruiken
Voer de volgende stappen uit om deze aanvragen zelf te proberen. Vervang de voorbeeld waarden die we in dit artikel hebben gebruikt met uw eigen waarden.

1. [Een Azure AD B2C Directory maken](tutorial-create-tenant.md). Gebruik de naam van uw directory in de aanvragen.
2. [Maak een toepassing](tutorial-register-applications.md) voor het verkrijgen van een toepassings-id en een omleidings-URI. Neem een systeem eigen client op in uw app.
3. [Maak uw gebruikers stromen](user-flow-overview.md) om de namen van uw gebruikers stroom te verkrijgen.

---
title: Micro soft Identity platform en OAuth 2,0-autorisatie code stroom | Azure
titleSuffix: Microsoft identity platform
description: Bouw webtoepassingen met behulp van de micro soft Identity platform-implementatie van het OAuth 2,0-verificatie protocol.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 580ec0761c997a0ee7611f7104aa48650c8573e7
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107409"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Micro soft Identity platform en OAuth 2,0-autorisatie code stroom

De OAuth 2,0-autorisatie code toekenning kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Met de micro soft Identity platform-implementatie van OAuth 2,0 kunt u aanmeldings-en API-toegang toevoegen aan uw mobiele en desktop-apps.

In dit artikel wordt beschreven hoe u vanuit elke taal rechtstreeks kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

De OAuth 2,0-autorisatie code stroom wordt beschreven in [sectie 4,1 van de oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749). Het wordt gebruikt om verificatie en autorisatie uit te voeren in het meren deel van de app-typen, waaronder [apps met één pagina](v2-app-types.md#single-page-apps-javascript), [Web apps](v2-app-types.md#web-apps)en [systeem eigen geïnstalleerde apps](v2-app-types.md#mobile-and-native-apps). Met de stroom kunnen apps veilig access_tokens ophalen dat kan worden gebruikt voor toegang tot bronnen die worden beveiligd door het micro soft Identity platform-eind punt, en voor het vernieuwen van tokens voor het verkrijgen van aanvullende access_tokens en ID-tokens voor de aangemelde gebruiker.

## <a name="protocol-diagram"></a>Protocol diagram

Op hoog niveau ziet de volledige verificatie stroom voor een toepassing er ongeveer als volgt uit:

![OAuth-verificatie code stroom](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Omleidings-URI-installatie vereist voor apps met één pagina

De autorisatie code stroom voor toepassingen met één pagina vereist enkele aanvullende instellingen.  Volg de instructies voor [het maken van uw toepassing met één pagina](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) om de omleidings-URI correct te markeren als ingeschakeld voor CORS. Als u een bestaande omleidings-URI wilt bijwerken om CORS in te scha kelen, opent u de manifest editor en stelt `type` u het veld voor de omleidings-URI `spa` in in de `replyUrlsWithType` sectie. U kunt ook klikken op de omleidings-URI in het gedeelte Web van het tabblad Verificatie en de Uri's selecteren waarnaar u wilt migreren met behulp van de autorisatie code stroom.

Het `spa` omleidings type is achterwaarts compatibel met de impliciete stroom. Apps die momenteel gebruikmaken van de impliciete stroom om tokens op te halen `spa` , kunnen zonder problemen naar het omleidings-URI-type worden verplaatst en blijven de impliciete stroom gebruiken.

Als u probeert de autorisatie code stroom te gebruiken en deze fout te zien:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Vervolgens moet u uw app-registratie bezoeken en de omleidings-URI voor uw app bijwerken zodat deze kan worden getypt `spa` .

## <a name="request-an-authorization-code"></a>Een autorisatie code aanvragen

De autorisatie code stroom begint met de client die de gebruiker omleidt naar het `/authorize` eind punt. In deze aanvraag vraagt de client de `openid` , `offline_access` , en `https://graph.microsoft.com/mail.read ` machtigingen van de gebruiker.  Sommige machtigingen zijn door de beheerder beperkt, bijvoorbeeld het schrijven van gegevens naar de directory van een organisatie met behulp van `Directory.ReadWrite.All` . Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatie gebruiker, ontvangt de gebruiker een fout bericht waarin staat dat ze niet zijn geautoriseerd om toestemming te geven voor de machtigingen van uw app. Als u toegang wilt aanvragen voor beheerders met beperkte bereiken, moet u deze rechtstreeks aanvragen bij een bedrijfs beheerder.  Lees [machtigingen met beheerders rechten](v2-permissions-and-consent.md#admin-restricted-permissions)voor meer informatie.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Klik op de onderstaande koppeling om deze aanvraag uit te voeren. Nadat u zich hebt aangemeld, moet uw browser worden omgeleid naar `https://localhost/myapp/` met een `code` in de adres balk.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Vereist/optioneel | Beschrijving |
|--------------|-------------|--------------|
| `tenant`    | vereist    | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common` , `organizations` , `consumers` en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.  |
| `client_id`   | vereist    | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen.  |
| `response_type` | vereist    | Moet `code` de autorisatie code stroom bevatten. Kan ook `id_token` `token` de [hybride stroom](#request-an-id-token-as-well-hybrid-flow)bevatten of gebruiken. |
| `redirect_uri`  | vereist | De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. Voor systeem eigen & mobiele apps moet u de standaard waarde van gebruiken `https://login.microsoftonline.com/common/oauth2/nativeclient` .   |
| `scope`  | vereist    | Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md) waarvan u wilt dat de gebruiker toestemming geeft.  In het `/authorize` gedeelte van de aanvraag kan dit meerdere resources omvatten, zodat uw app toestemming kan krijgen voor meerdere web-api's die u wilt aanroepen. |
| `response_mode`   | aanbevelingen | Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. Dit kan een van de volgende zijn:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` levert de code als een query reeks parameter op de omleidings-URI. Als u een ID-token met behulp van de impliciete stroom aanvraagt, kunt u niet gebruiken `query` zoals opgegeven in de [OpenID Connect-specificatie](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Als u alleen de code wilt aanvragen, kunt u `query` , `fragment` of gebruiken `form_post` . `form_post` Hiermee wordt een bericht met de code uitgevoerd naar de omleidings-URI. |
| `state`                 | aanbevelingen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van alle inhoud die u wilt. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. Met de waarde kan ook informatie over de status van de gebruiker in de app worden gecodeerd voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `prompt`  | optioneel    | Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn `login` , `none` , en `consent` .<br/><br/>- `prompt=login` dwingt de gebruiker de referenties op die aanvraag in te voeren, waarbij eenmalige aanmelding wordt genegeerd.<br/>- `prompt=none` is het tegenovergestelde: Hiermee zorgt u ervoor dat de gebruiker niet in een interactieve prompt wordt weer gegeven. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt door het micro soft Identity platform-eind punt een `interaction_required` fout geretourneerd.<br/>- `prompt=consent` het dialoog venster OAuth-toestemming wordt geactiveerd nadat de gebruiker zich heeft aangemeld, waarbij de gebruiker wordt gevraagd om machtigingen te verlenen aan de app.<br/>- `prompt=select_account` onderbreekt eenmalige aanmelding voor het aanbieden van accounts selectie alle accounts in een sessie of een eventueel onthouden account of een optie om ervoor te kiezen om een ander account samen te gebruiken.<br/> |
| `login_hint`  | optioneel    | Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker, als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding met de `preferred_username` claim.   |
| `domain_hint`  | optioneel    | Indien opgenomen, wordt het op e-mail gebaseerde detectie proces overs Laan dat door de gebruiker wordt uitgevoerd op de aanmeldings pagina, waardoor er iets meer gestroomlijnde gebruikers ervaring is, bijvoorbeeld door deze te verzenden naar hun federatieve id-provider. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren door de `tid` van een vorige aanmelding uit te pakken. Als de `tid` claim waarde is `9188040d-6c67-4c5b-b112-36a304b66dad` , moet u gebruiken `domain_hint=consumers` . Gebruik anders `domain_hint=organizations` .  |
| `code_challenge`  | Aanbevolen/vereist | Wordt gebruikt voor het beveiligen van autorisatie code subsidies via de bewijs code voor code Exchange (PKCE). Vereist als `code_challenge_method` is opgenomen. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. Dit wordt nu aanbevolen voor alle toepassings typen: systeem eigen apps, SPAs en vertrouwelijke clients, zoals web-apps. |
| `code_challenge_method` | Aanbevolen/vereist | De methode die wordt gebruikt voor het coderen `code_verifier` van de voor de `code_challenge` para meter. Dit *moet* zijn `S256` , maar de spec staat het gebruik van toe `plain` als de client om de een of andere reden sha256 niet kan ondersteunen. <br/><br/>Als deze is uitgesloten, `code_challenge` wordt ervan uitgegaan dat de tekst zonder opmaak wordt gebruikt `code_challenge` . Micro soft Identity platform ondersteunt zowel `plain` als `S256` . Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. Dit is vereist voor [apps van één pagina die gebruikmaken van de autorisatie code stroom](reference-third-party-cookies-spas.md).|


Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity platform-eind punt zorgt er ook voor dat de gebruiker heeft ingestemd met de machtigingen die in de `scope` query parameter worden aangegeven. Als de gebruiker niet heeft ingestemd met een van deze machtigingen, wordt de gebruiker gevraagd om toestemming te geven voor de vereiste machtigingen. Hier vindt u meer informatie over [machtigingen, toestemming en apps met meerdere tenants](v2-permissions-and-consent.md).

Zodra de gebruiker zich heeft geverifieerd en toestemming verleent, stuurt het micro soft Identity platform-eind punt een reactie op uw app op de aangegeven `redirect_uri` wijze, met behulp van de methode die is opgegeven in de `response_mode` para meter.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=query` ziet eruit als:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beschrijving  |
|-----------|--------------|
| `code` | Het authorization_code dat door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token aan te vragen voor de doel bron. Authorization_codes zijn korte tijd en verlopen doorgaans na ongeveer 10 minuten. |
| `state` | Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

U kunt ook een ID-token ontvangen als u er een hebt aangevraagd en de impliciete toekenning hebt ingeschakeld in de registratie van uw toepassing.  Dit wordt ook wel de [' hybride stroom '](#request-an-id-token-as-well-hybrid-flow)genoemd en wordt gebruikt door frameworks zoals ASP.net.

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving  |
|----------|------------------|
| `error`  | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fout codes voor verificatie eindpunt fouten

In de volgende tabel worden de verschillende fout codes beschreven die kunnen worden geretourneerd in de `error` para meter van het fout bericht.

| Foutcode  | Beschrijving    | Client actie   |
|-------------|----------------|-----------------|
| `invalid_request` | Protocol fout, zoals een ontbrekende vereiste para meter. | Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die doorgaans tijdens de eerste test is opgetreden. |
| `unauthorized_client` | De client toepassing mag geen autorisatie code aanvragen. | Deze fout treedt meestal op wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `access_denied`  | De resource-eigenaar heeft toestemming geweigerd  | De client toepassing kan de gebruiker hiervan op de hoogte stellen dat deze niet kan door gaan, tenzij de gebruiker ermee akkoord gaat. |
| `unsupported_response_type` | De autorisatie server biedt geen ondersteuning voor het antwoord type in de aanvraag. | Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die doorgaans tijdens de eerste test is opgetreden. Wanneer de [hybride stroom](#request-an-id-token-as-well-hybrid-flow)wordt weer gegeven, geeft u aan dat u de instelling id-token impliciete toekenning moet inschakelen voor de registratie van de client-app. |
| `server_error`  | Er is een onverwachte fout opgetreden op de server.| Voer de aanvraag opnieuw uit. Deze fouten kunnen worden veroorzaakt door tijdelijke voor waarden. De client toepassing kan bijvoorbeeld verklaren dat het antwoord van de gebruiker is vertraagd tot een tijdelijke fout. |
| `temporarily_unavailable`   | De server is tijdelijk niet actief om de aanvraag af te handelen. | Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |
| `invalid_resource`  | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. | Deze fout geeft aan dat de bron, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `login_required` | Er zijn te veel of geen gebruikers gevonden | De client heeft authenticatie op de achtergrond ( `prompt=none` ) aangevraagd, maar er is geen enkele gebruiker gevonden. Dit kan betekenen dat er meerdere gebruikers actief zijn in de sessie of dat er zich geen gebruikers bevinden. Hierbij wordt rekening gehouden met de gekozen Tenant (bijvoorbeeld als er twee Azure AD-accounts actief zijn en een Microsoft-account, en `consumers` wordt gekozen, wordt de stille verificatie uitgevoerd). |
| `interaction_required` | De aanvraag vereist een gebruikers interactie. | Er is een extra verificatie stap of toestemming vereist. Voer de aanvraag zonder uit `prompt=none` . |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Een ID-token ook aanvragen (hybride stroom)

Als u wilt weten wie de gebruiker is voordat een autorisatie code wordt ingewisseld, is het gebruikelijk dat toepassingen ook een ID-token aanvragen wanneer ze de autorisatie code aanvragen. Dit wordt de *hybride stroom* genoemd, omdat de impliciete toekenning wordt gemengd met de autorisatie code stroom. De hybride stroom wordt vaak gebruikt in web-apps die een pagina voor een gebruiker willen weer geven zonder dat de code wordt ingewisseld, met name [ASP.net](quickstart-v2-aspnet-core-webapp.md). Apps met één pagina en traditionele web-apps profiteren van een beperkte latentie in dit model.

De hybride stroom is hetzelfde als de eerder beschreven verificatie code stroom, met drie toevoegingen, die allemaal vereist zijn voor het aanvragen van een ID-token: nieuwe bereiken, een nieuwe response_type en een nieuwe `nonce` query parameter.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Bijgewerkte para meter | Vereist/optioneel | Beschrijving |
|---------------|-------------|--------------|
|`response_type`| Vereist | Het toevoegen van `id_token` geeft aan de server aan dat de toepassing een ID-token zou hebben in de reactie van het `/authorize` eind punt.  |
|`scope`| Vereist | Voor ID-tokens moet worden bijgewerkt met de ID-token bereik- `openid` , en optioneel `profile` en `email` . |
|`nonce`| Vereist|     Een waarde die is opgenomen in de aanvraag, gegenereerd door de app, die wordt opgenomen in de resulterende id_token als een claim. De app kan vervolgens deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige, unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. |
|`response_mode`| Aanbevolen | Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. De standaard instelling `query` is alleen voor een autorisatie code, maar `fragment` als de aanvraag een id_token bevat `response_type` .|

Het gebruik van `fragment` als antwoord modus kan problemen veroorzaken voor web-apps die de code van de omleiding lezen, omdat browsers het fragment niet door geven aan de webserver.  In deze situaties wordt het aanbevolen dat apps de `form_post` antwoord modus gebruiken om ervoor te zorgen dat alle gegevens naar de server worden verzonden. 

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` ziet eruit als:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parameter | Beschrijving  |
|-----------|--------------|
| `code` | De autorisatie code die door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token aan te vragen voor de doel bron. Autorisatie codes zijn korte tijd, doorgaans verlopen na ongeveer 10 minuten. |
| `id_token` | Een ID-token voor de gebruiker, uitgegeven via *impliciete toekenning*. Bevat een speciale `c_hash` claim die de hash van de `code` in dezelfde aanvraag is. |
| `state` | Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="request-an-access-token"></a>Een toegangstoken aanvragen

Nu u een authorization_code hebt aangeschaft en toestemming hebt gegeven door de gebruiker, kunt u de `code` voor een `access_token` naar de gewenste resource inwisselen. Doe dit door een `POST` aanvraag naar het `/token` eind punt te verzenden:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Vergeet niet om de te vervangen `code` ) [ ![ Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Vereist/optioneel | Beschrijving     |
|------------|-------------------|----------------|
| `tenant`   | vereist   | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common` , `organizations` , `consumers` en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.  |
| `client_id` | vereist  | De client-ID van de toepassing die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die is toegewezen aan uw app. |
| `grant_type` | vereist   | Moet `authorization_code` voor de autorisatie code stroom zijn.   |
| `scope`      | optioneel   | Een lijst met door spaties gescheiden bereiken. De scopes moeten allemaal van één resource zijn, samen met OIDC-bereiken ( `profile` , `openid` , `email` ). Raadpleeg [machtigingen, toestemming en bereiken](v2-permissions-and-consent.md)voor een gedetailleerdere uitleg van scopes. Dit is een micro soft-uitbrei ding van de autorisatie code stroom, bedoeld om apps toe te staan de resource te declareren waarvoor het token moet worden ingewisseld.|
| `code`          | vereist  | Het authorization_code dat u hebt verkregen in het eerste gedeelte van de stroom. |
| `redirect_uri`  | vereist  | Dezelfde redirect_uri waarde die is gebruikt om de authorization_code op te halen. |
| `client_secret` | vereist voor vertrouwelijke web-apps | Het toepassings geheim dat u hebt gemaakt in de app-registratie portal voor uw app. U moet het toepassings geheim niet gebruiken in een systeem eigen app of een app met één pagina omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten of webpagina's. Het is vereist voor web-apps en Web-Api's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de server zijde.  Net als bij alle hier beschreven para meters moet het client geheim URL-gecodeerd zijn voordat het wordt verzonden. Dit is een stap die meestal door de SDK wordt uitgevoerd. Zie voor meer informatie over URI-code ring de [generieke URI-syntaxis specificatie](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | aanbevelingen  | Hetzelfde code_verifier dat is gebruikt om de authorization_code op te halen. Vereist als PKCE is gebruikt in de aanvraag voor autorisatie code toekenning. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |

### <a name="successful-response"></a>Geslaagde reactie

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beschrijving   |
|---------------|------------------------------|
| `access_token`  | Het aangevraagde toegangs token. De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals een web-API.  |
| `token_type`    | Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer |
| `expires_in`    | Hoe lang het toegangs token geldig is (in seconden). |
| `scope`         | De bereiken waarvoor het access_token geldig is. Optioneel: dit is niet-standaard en als het token wordt wegge laten, geldt voor de scopes die zijn aangevraagd voor de eerste poot van de stroom. |
| `refresh_token` | Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende toegangs tokens te verkrijgen nadat het huidige toegangs token is verlopen. Refresh_tokens lang duren en kunnen worden gebruikt om de toegang tot resources gedurende lange tijd te bewaren. Raadpleeg de [volgende sectie](#refresh-the-access-token)voor meer informatie over het vernieuwen van een toegangs token. <br> **Opmerking:** Alleen opgegeven als `offline_access` het bereik is aangevraagd. |
| `id_token`      | Een JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, en vertrouwelijke clients kunnen dit voor verificatie gebruiken. Zie voor meer informatie over id_tokens [`id_token reference`](id-tokens.md) . <br> **Opmerking:** Alleen opgegeven als `openid` het bereik is aangevraagd. |

### <a name="error-response"></a>Fout bericht

Fout reacties zien er als volgt uit:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beschrijving    |
|-------------------|----------------|
| `error`       | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| `error_codes` | Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren.  |
| `timestamp`   | Het tijdstip waarop de fout is opgetreden. |
| `trace_id`    | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

### <a name="error-codes-for-token-endpoint-errors"></a>Fout codes voor token eindpunt fouten

| Foutcode         | Beschrijving        | Client actie    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protocol fout, zoals een ontbrekende vereiste para meter. | De aanvraag of app-registratie verhelpen en de aanvraag opnieuw verzenden   |
| `invalid_grant`    | De verificatie code of PKCE code Verifier is ongeldig of is verlopen. | Probeer een nieuwe aanvraag naar het `/authorize` eind punt en controleer of de para meter code_verifier juist is.  |
| `unauthorized_client` | De geverifieerde client is niet gemachtigd om dit type autorisatie toekenning te gebruiken. | Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `invalid_client` | Client verificatie is mislukt.  | De referenties van de client zijn niet geldig. De toepassings beheerder werkt de referenties bij om deze te herstellen.   |
| `unsupported_grant_type` | De autorisatie server biedt geen ondersteuning voor het type autorisatie toekenning. | Wijzig het toekennings type in de aanvraag. Dit type fout moet alleen tijdens de ontwikkeling optreden en tijdens de eerste test worden gedetecteerd. |
| `invalid_resource` | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. | Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD.  |
| `interaction_required` | Niet-standaard, aangezien de OIDC-specificatie dit alleen op het `/authorize` eind punt aanroept. De aanvraag vereist een gebruikers interactie. Zo is een extra verificatie stap vereist. | Voer de `/authorize` aanvraag opnieuw uit met dezelfde bereiken. |
| `temporarily_unavailable` | De server is tijdelijk niet actief om de aanvraag af te handelen. | Probeer de aanvraag opnieuw uit te voeren na een kleine vertraging. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |
|`consent_required` | De aanvraag vereist toestemming van de gebruiker. Deze fout is niet-standaard, omdat deze doorgaans alleen wordt geretourneerd op het `/authorize` eind punt per OIDC-specificatie. Retourneert wanneer een `scope` para meter is gebruikt op de inwissel stroom van de code die de client-app niet mag aanvragen.  | De client moet de gebruiker terugsturen naar het `/authorize` eind punt met het juiste bereik om toestemming te activeren. |
|`invalid_scope` | Het bereik dat door de app is aangevraagd, is ongeldig.  | Werk de waarde van de bereik parameter in de verificatie aanvraag bij naar een geldige waarde. |

> [!NOTE]
> Apps met één pagina ontvangen mogelijk een `invalid_request` fout melding die aangeeft dat het cross-Origin-token alleen is toegestaan voor de client-type toepassing met één pagina.  Dit geeft aan dat de omleidings-URI die wordt gebruikt om het token aan te vragen, niet is gemarkeerd als `spa` omleidings-URI.  Bekijk de [stappen](#redirect-uri-setup-required-for-single-page-apps) voor het registreren van de toepassing voor informatie over het inschakelen van deze stroom.

## <a name="use-the-access-token"></a>Het toegangs token gebruiken

Nu u een hebt aangeschaft `access_token` , kunt u het token gebruiken in aanvragen voor web-api's door dit op te nemen in de `Authorization` kop:

> [!TIP]
> Voer deze aanvraag uit in postman! (Vervang `Authorization` eerst de koptekst) [ ![ Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Het toegangs token vernieuwen

Access_tokens kort in de weg staan en u moet ze vernieuwen nadat ze verlopen zijn om door te gaan met de toegang tot resources. U kunt dit doen door een andere `POST` aanvraag in te dienen bij het `/token` eind punt, `refresh_token` in plaats van de `code` .  Vernieuwings tokens zijn geldig voor alle machtigingen die uw client al toestemming heeft gegeven, waardoor een vernieuwings token dat is uitgegeven op een aanvraag voor, `scope=mail.read` kan worden gebruikt om een nieuw toegangs token aan te vragen `scope=api://contoso.com/api/UseResource` .

Voor het vernieuwen van tokens voor web apps en systeem eigen apps zijn geen levens duur opgegeven. De levens duur van vernieuwings tokens is doorgaans relatief lang. In sommige gevallen verloopt het vernieuwen van tokens, worden deze ingetrokken of beschikt over onvoldoende machtigingen voor de gewenste actie. Uw toepassing moet verwachten en fouten afhandelen [die door het eind punt voor token uitgifte zijn geretourneerd](#error-codes-for-token-endpoint-errors) . Apps met één pagina krijgen echter een token met een levens duur van 24 uur en vereisen elke dag een nieuwe verificatie.  Dit kan op de achtergrond worden uitgevoerd in een IFRAME wanneer cookies van derden zijn ingeschakeld, maar moeten worden uitgevoerd in een frame op het hoogste niveau (volledige pagina navigatie of een pop-upvenster) in browsers zonder cookies van derden, zoals Safari.

Hoewel het vernieuwen van tokens niet wordt ingetrokken wanneer het wordt gebruikt om nieuwe toegangs tokens te verkrijgen, wordt u verwacht het oude vernieuwings token te negeren. De [OAuth 2,0 spec](https://tools.ietf.org/html/rfc6749#section-6) vertelt: ' de autorisatie server kan een nieuw vernieuwings token uitgeven. in dat geval moet de client het oude vernieuwings token verwijderen en vervangen door het nieuwe vernieuwings token. De autorisatie server kan het oude vernieuwings token intrekken nadat er een nieuw vernieuwings token is uitgegeven aan de client.

>[!IMPORTANT]
> Voor vernieuwings tokens die zijn verzonden naar een omleidings-URI die is geregistreerd als `spa` , verloopt het vernieuwings token na 24 uur. Bijkomende vernieuwings tokens die zijn verkregen met behulp van het eerste vernieuwings token, nemen deze verloop tijd over. Daarom moeten apps worden voor bereid om de autorisatie code stroom opnieuw uit te voeren met een interactieve verificatie om elke 24 uur een nieuw vernieuwings token te verkrijgen. Gebruikers hoeven hun referenties niet in te voeren en hebben meestal niet zelfs een UX, alleen een hernieuwde toepassing, maar de browser moet de aanmeldings pagina in een frame op het hoogste niveau bezoeken om de aanmeldings sessie te kunnen zien.  Dit wordt veroorzaakt door [privacy-functies in browsers die cookies van derden blok keren](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Vergeet niet om de te vervangen `refresh_token` ) [ ![ Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parameter     | Type           | Beschrijving        |
|---------------|----------------|--------------------|
| `tenant`        | vereist     | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common` , `organizations` , `consumers` en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.   |
| `client_id`     | vereist    | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `grant_type`    | vereist    | Moet `refresh_token` voor deze poot van de autorisatie code stroom zijn. |
| `scope`         | vereist    | Een lijst met door spaties gescheiden bereiken. De scopes die in deze leg worden aangevraagd, moeten gelijk zijn aan of een subset zijn van de bereiken die in de oorspronkelijke authorization_code aanvraag poot zijn aangevraagd. Als de scopes die in deze aanvraag zijn opgegeven meerdere bron servers beslaan, retourneert het micro soft Identity platform-eind punt een token voor de resource die in het eerste bereik is opgegeven. Raadpleeg [machtigingen, toestemming en bereiken](v2-permissions-and-consent.md)voor een gedetailleerdere uitleg van scopes. |
| `refresh_token` | vereist    | Het refresh_token dat u hebt verkregen in het tweede gedeelte van de stroom. |
| `client_secret` | vereist voor web-apps | Het toepassings geheim dat u hebt gemaakt in de app-registratie portal voor uw app. Het mag niet worden gebruikt in een systeem eigen app, omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en Web-Api's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de server zijde. Dit geheim moet URL-gecodeerd zijn. Zie voor meer informatie de specificatie van de [generieke URI-syntaxis](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beschrijving         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Het aangevraagde toegangs token. De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals een web-API. |
| `token_type`    | Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer |
| `expires_in`    | Hoe lang het toegangs token geldig is (in seconden).   |
| `scope`         | De bereiken waarvoor het access_token geldig is.    |
| `refresh_token` | Een nieuw OAuth 2,0-vernieuwings token. U moet het oude vernieuwings token vervangen door dit nieuwe vernieuwings token om ervoor te zorgen dat uw vernieuwings tokens zo lang mogelijk geldig blijven. <br> **Opmerking:** Alleen opgegeven als `offline_access` het bereik is aangevraagd.|
| `id_token`      | Een niet-ondertekende JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze moet niet afhankelijk zijn van autorisatie of beveiligings grenzen. Zie voor meer informatie over id_tokens [`id_token reference`](id-tokens.md) . <br> **Opmerking:** Alleen opgegeven als `openid` het bereik is aangevraagd. |

#### <a name="error-response"></a>Fout bericht

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beschrijving                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout.           |
| `error_codes` |Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren. |
| `timestamp` | Het tijdstip waarop de fout is opgetreden. |
| `trace_id` | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

Zie voor een beschrijving van de fout codes en de aanbevolen client actie [fout codes voor token eindpunt fouten](#error-codes-for-token-endpoint-errors).

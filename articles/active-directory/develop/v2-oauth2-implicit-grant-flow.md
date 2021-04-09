---
title: OAuth 2,0 impliciete toekennings stroom-het micro soft Identity-platform | Azure
description: Beveilig apps met één pagina met een impliciete stroom van micro soft Identity platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226485"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Micro soft Identity-platform en impliciete toekennings stroom

Het micro soft-identiteits platform ondersteunt de OAuth 2,0 impliciete toekennings stroom zoals beschreven in de [OAuth 2,0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.2). Het definiëren van de kenmerken van de impliciete toekenning is dat tokens (ID-tokens of toegangs tokens) direct worden geretourneerd vanuit het/authorize-eind punt in plaats van het/token-eind punt. Dit wordt vaak gebruikt als onderdeel van de [autorisatie code stroom](v2-oauth2-auth-code-flow.md), in wat de ' hybride stroom ' wordt genoemd: het id-Token ophalen voor de/authorize-aanvraag, samen met een autorisatie code.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>De voor keur voor de verificatie code stroom

Met de plannen voor het [verwijderen van cookies van derden uit browsers](reference-third-party-cookies-spas.md), **is de impliciete toekennings stroom niet langer een geschikte verificatie methode**.  De [stille SSO-functies](#getting-access-tokens-silently-in-the-background) van de impliciete stroom werken niet zonder cookies van derden, waardoor toepassingen worden onderbroken wanneer ze proberen om een nieuw token op te halen. We raden u ten zeerste aan dat alle nieuwe toepassingen gebruikmaken van de [autorisatie code stroom](v2-oauth2-auth-code-flow.md) die nu apps met één pagina ondersteunt in plaats van de impliciete stroom en dat bestaande apps uit de micro soft- [pagina ook beginnen met de migratie naar de autorisatie code stroom](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geschikte scenario's voor de impliciete toekenning van OAuth2

De impliciete toekenning is alleen betrouwbaar voor het eerste, interactieve deel van uw aanmeldings stroom, waarbij het gebrek aan [cookies](reference-third-party-cookies-spas.md) van derden niet van invloed kan zijn op uw toepassing. Deze beperking betekent dat u deze exclusief moet gebruiken als onderdeel van de hybride stroom, waarbij uw toepassing een code aanvraagt en een token van het autorisatie-eind punt. Dit zorgt ervoor dat uw toepassing een code ontvangt die kan worden ingewisseld voor een vernieuwings token, zodat de aanmeldings sessie van uw app in de loop van de tijd geldig blijft.

## <a name="protocol-diagram"></a>Protocol diagram

In het volgende diagram ziet u hoe de volledige impliciete aanmeldings stroom eruit ziet en de secties die volgen elke stap in meer detail beschrijven.

![Diagram waarin de impliciete aanmeldings stroom wordt weer gegeven](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>De aanmeldings aanvraag verzenden

Als u de gebruiker in de app wilt ondertekenen, kunt u een [OpenID Connect Connect](v2-protocols-oidc.md) -verificatie aanvraag verzenden en een `id_token` van de micro soft-identiteits platform ophalen.

> [!IMPORTANT]
> Als u een ID-token en/of een toegangs token wilt aanvragen, moet de registratie van de app op de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina de bijbehorende impliciete toekennings stroom hebben ingeschakeld, door **id-tokens** en **toegangs tokens** te selecteren in de sectie **impliciete toekenning en hybride stromen** . Als de functie niet is ingeschakeld, `unsupported_response` wordt er een fout geretourneerd: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Als u het aanmelden wilt testen met behulp van de impliciete stroom, klikt u op <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Nadat u zich hebt aangemeld, moet uw browser worden omgeleid naar `https://localhost/myapp/` met een `id_token` in de adres balk.
>

| Parameter | Type | Description |
| --- | --- | --- |
| `tenant` | vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common` , `organizations` , `consumers` en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `client_id` | vereist | De ID van de toepassing (client) die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die aan uw app is toegewezen. |
| `response_type` | vereist |Moet zijn inbegrepen `id_token` voor OpenID Connect Connect-aanmelding. Het kan ook de response_type omvatten `token` . Met `token` deze machtiging kan uw app direct een toegangs token ontvangen van het eind punt voor autorisatie zonder dat hiervoor een tweede aanvraag moet worden verzonden naar het toestemming-eind punt. Als u de `token` response_type gebruikt, `scope` moet de para meter een bereik bevatten dat aangeeft op welke resource het token moet worden uitgegeven (bijvoorbeeld gebruiker. read op Microsoft Graph). Het kan ook `code` in plaats van bevatten `token` om een autorisatie code op te geven voor gebruik in de [autorisatie code stroom](v2-oauth2-auth-code-flow.md). Deze id_token en antwoord code wordt ook wel de hybride stroom genoemd.  |
| `redirect_uri` | aanbevelingen |De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. |
| `scope` | vereist |Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md). Voor OpenID Connect Connect (id_tokens) moet deze het bereik bevatten `openid` , dat wordt omgezet in de machtiging ' Meld u aan ' in de gebruikers interface van de toestemming. U kunt eventueel ook de `email` en `profile` bereiken voor het verkrijgen van toegang tot aanvullende gebruikers gegevens toevoegen. Als er een toegangs token wordt aangevraagd, kunt u ook andere bereiken in deze aanvraag voor het aanvragen van een toestemming voor verschillende bronnen toevoegen. |
| `response_mode` | optioneel |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. De standaard instelling is een query alleen voor een toegangs token, maar fragment als de aanvraag een id_token bevat. |
| `state` | aanbevelingen |Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van alle inhoud die u wilt. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. De status wordt ook gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `nonce` | vereist |Een waarde die is opgenomen in de aanvraag, gegenereerd door de app, die wordt opgenomen in de resulterende id_token als een claim. De app kan vervolgens deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige, unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. Alleen vereist wanneer een id_token wordt aangevraagd. |
| `prompt` | optioneel |Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn ' aanmelding, ' none ', ' select_account ' en ' instemming '. `prompt=login` dwingt de gebruiker de referenties op die aanvraag in te voeren, waarbij eenmalige aanmelding wordt genegeerd. `prompt=none` is het tegenovergestelde: Hiermee zorgt u ervoor dat de gebruiker niet in een interactieve prompt wordt weer gegeven. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt een fout geretourneerd door het micro soft Identity-platform. `prompt=select_account` Hiermee wordt de gebruiker naar een account kiezer verzonden waarbij alle accounts die in de sessie zijn onthouden, worden weer gegeven. `prompt=consent` het dialoog venster OAuth-toestemming wordt geactiveerd nadat de gebruiker zich heeft aangemeld, waarbij de gebruiker wordt gevraagd om machtigingen te verlenen aan de app. |
| `login_hint`  |optioneel |Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens de herauthenticatie, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding met de `preferred_username` claim.|
| `domain_hint` | optioneel |Indien opgenomen, wordt het op e-mail gebaseerde detectie proces overs Laan dat de gebruiker op de aanmeldings pagina doorloopt, waardoor er iets meer gestroomlijnde gebruikers ervaring is. Deze para meter wordt meestal gebruikt voor line-of-Business-Apps die in één Tenant werken, waar ze een domein naam binnen een bepaalde Tenant opgeven, waarbij de gebruiker wordt doorgestuurd naar de Federation-provider voor die Tenant.  Houd er rekening mee dat met deze Hint wordt voor komen dat gasten zich kunnen aanmelden bij deze toepassing en wordt het gebruik van Cloud referenties zoals FIDO beperkt.  |

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity-platform zorgt er ook voor dat de gebruiker heeft ingestemd met de machtigingen die zijn aangegeven in de `scope` query parameter. Als de gebruiker heeft ingestemd met **geen** van deze machtigingen, wordt de gebruiker gevraagd om toestemming te geven voor de vereiste machtigingen. Zie [machtigingen, toestemming en multi tenant-apps](v2-permissions-and-consent.md)voor meer informatie.

Zodra de gebruiker de verificatie heeft uitgevoerd en toestemming verleent, stuurt het micro soft Identity-platform een reactie naar uw app op de aangegeven `redirect_uri` wijze met behulp van de methode die is opgegeven in de `response_mode` para meter.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie in `response_mode=fragment` en `response_type=id_token+code` lijkt op het volgende (met regel einden voor de Lees baarheid):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `code` | Inbegrepen bij `response_type` includes `code` . Dit is een autorisatie code die geschikt is voor gebruik in de [autorisatie code stroom](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Inbegrepen bij `response_type` includes `token` . Het toegangs token dat door de app is aangevraagd. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` |Inbegrepen bij `response_type` includes `token` . Is altijd `Bearer` . |
| `expires_in`|Inbegrepen bij `response_type` includes `token` . Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` |Inbegrepen bij `response_type` includes `token` . Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie voor meer informatie over id_tokens [`id_token reference`](id-tokens.md) . <br> **Opmerking:** Alleen opgegeven als `openid` het bereik is aangevraagd en `response_type` opgenomen `id_tokens` . |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Toegangs tokens op de achtergrond ontvangen

> [!Important]
> Dit deel van de impliciete stroom werkt waarschijnlijk niet voor uw toepassing omdat deze wordt gebruikt in verschillende browsers als gevolg [van het verwijderen van cookies van](reference-third-party-cookies-spas.md)derden.  Hoewel dit nog steeds werkt in chroom-gebaseerde browsers die zich niet in incognito bevinden, moeten ontwikkel aars dit deel van de stroom opnieuw gebruiken. In browsers die geen cookies van derden ondersteunen, ontvangt u een fout melding die aangeeft dat er geen gebruikers zijn aangemeld, omdat de sessie cookies van de aanmeldings pagina zijn verwijderd door de browser. 

Nu u de gebruiker hebt ondertekend in een app met één pagina, kunt u op de achtergrond toegangs tokens ontvangen voor het aanroepen van web-Api's die zijn beveiligd met micro soft Identity platform, zoals de [Microsoft Graph](https://developer.microsoft.com/graph). Zelfs als u al een token hebt ontvangen met behulp van de `token` response_type, kunt u deze methode gebruiken om tokens te verkrijgen aan aanvullende bronnen zonder dat u de gebruiker opnieuw hoeft aan te melden.

In de normale OpenID connect verbinding maken/OAuth-stroom kunt u dit doen door een aanvraag in te stellen voor het micro soft Identity platform- `/token` eind punt. U kunt de aanvraag in een verborgen iframe maken om nieuwe tokens voor andere web-Api's op te halen:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Zie [de aanmeldings aanvraag verzenden](#send-the-sign-in-request)voor meer informatie over de query parameters in de URL.

> [!TIP]
> Kopieer & de onderstaande aanvraag op een browser tabblad te plakken. (Vergeet niet om de waarden te vervangen `login_hint` door de juiste waarde voor uw gebruiker)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Houd er rekening mee dat dit ook werkt in browsers zonder ondersteuning van cookies van derden, omdat u dit rechtstreeks in een browser balk invoert, in tegens telling tot het openen in een IFRAME. 

Dankzij de `prompt=none` para meter kan deze aanvraag onmiddellijk slagen of mislukken en terugkeren naar uw toepassing. Het antwoord wordt verzonden naar uw app op de aangegeven `redirect_uri` wijze met behulp van de methode die is opgegeven in de `response_mode` para meter.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` ziet eruit als:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Inbegrepen bij `response_type` includes `token` . Het toegangs token dat de app heeft aangevraagd, in dit geval voor de Microsoft Graph. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` | Is altijd `Bearer` . |
| `expires_in` | Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` | Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). Inbegrepen bij `response_type` includes `id_token` . De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie de [ `id_token` verwijzing](id-tokens.md)voor meer informatie over id_tokens. <br> **Opmerking:** Alleen opgegeven als `openid` het bereik is aangevraagd. |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen. In het geval van is `prompt=none` een verwachte fout:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

Als u deze fout in de iframe-aanvraag ontvangt, moet de gebruiker zich opnieuw aanmelden om een nieuw token op te halen. U kunt ervoor kiezen om deze kwestie op een wille keurige manier af te handelen voor uw toepassing.

## <a name="refreshing-tokens"></a>Tokens vernieuwen

De impliciete toekenning biedt geen vernieuwings tokens. `id_token`De s en `access_token` s verlopen na een korte periode. Daarom moet uw app worden voor bereid om deze tokens regel matig te vernieuwen. Als u een van beide typen tokens wilt vernieuwen, kunt u dezelfde verborgen iframe-aanvraag van boven uitvoeren met behulp van de `prompt=none` para meter om het gedrag van het identiteits platform te bepalen. Als u een nieuwe wilt ontvangen `id_token` , moet u ervoor zorgen dat u `id_token` in de `response_type` en gebruikt, evenals `scope=openid` een `nonce` para meter.

In browsers die geen cookies van derden ondersteunen, resulteert dit in een fout melding die aangeeft dat er geen gebruiker is aangemeld. 

## <a name="send-a-sign-out-request"></a>Een aanvraag voor een afmelding verzenden

Met de OpenID Connect Connect `end_session_endpoint` kan uw app een aanvraag verzenden naar het micro soft Identity-platform om de sessie van een gebruiker te beëindigen en cookies te wissen die zijn ingesteld door het micro soft Identity-platform. Als u een gebruiker volledig wilt ondertekenen vanuit een webtoepassing, moet uw app een eigen sessie met de gebruiker beëindigen (meestal door het wissen van een token cache of het verwijderen van cookies), en vervolgens de browser om te leiden naar:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter | Type | Description |
| --- | --- | --- |
| `tenant` |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common` , `organizations` , `consumers` en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `post_logout_redirect_uri` | aanbevelingen | De URL waarnaar de gebruiker moet worden geretourneerd nadat de afmelding is voltooid. Deze waarde moet overeenkomen met een van de omleidings-Uri's die voor de toepassing zijn geregistreerd. Als dat niet het geval is, wordt de gebruiker een algemeen bericht weer gegeven door het micro soft Identity-platform. |

## <a name="next-steps"></a>Volgende stappen

* Ga naar de [MSAL js](sample-v2-code.md) -voor beelden om te beginnen met de code ring.
* Controleer de [autorisatie code stroom](v2-oauth2-auth-code-flow.md) als een nieuwer, beter alternatief voor de impliciete toekenning. 

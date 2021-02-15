---
title: Micro soft Identity platform scopes, permissions, & toestemming
description: Meer informatie over autorisatie in het micro soft Identity platform-eind punt, met inbegrip van scopes, machtigingen en toestemming.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102475"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Machtigingen en toestemming in het micro soft Identity-platform

Toepassingen die zijn geïntegreerd met het micro soft Identity platform, volgen een autorisatie model waarmee gebruikers en beheerders kunnen bepalen hoe gegevens toegankelijk zijn. De implementatie van het autorisatie model is bijgewerkt op het micro soft Identity-platform. Het wijzigt hoe een app moet communiceren met het micro soft Identity-platform. In dit artikel worden de basis concepten van dit autorisatie model beschreven, met inbegrip van scopes, machtigingen en toestemming.

## <a name="scopes-and-permissions"></a>Bereiken en machtigingen

Het micro soft Identity-platform implementeert het [OAuth 2,0](active-directory-v2-protocols.md) -autorisatie protocol. OAuth 2,0 is een methode waarmee een app van derden namens een gebruiker toegang heeft tot webhosten. Elke webhostige resource die wordt geïntegreerd met het micro soft Identity-platform heeft een resource-id of een URI voor de *toepassings-id*. 

Hier volgen enkele voor beelden van webtoepassingen die door micro soft worden gehost:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365-mail-API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Dit geldt ook voor alle resources van derden die zijn geïntegreerd met het micro soft Identity platform. Elk van deze resources kan ook een set machtigingen definiëren die kunnen worden gebruikt om de functionaliteit van die bron te verdelen in kleinere segmenten. [Microsoft Graph](https://graph.microsoft.com) heeft bijvoorbeeld machtigingen gedefinieerd om de volgende taken uit te voeren, onder andere:

* De agenda van een gebruiker lezen
* Schrijven naar de agenda van een gebruiker
* E-mail verzenden als gebruiker

Vanwege dit type machtigings definities heeft de resource een nauw keurige controle over de gegevens en hoe de API-functionaliteit wordt weer gegeven. Een app van derden kan deze machtigingen aanvragen bij gebruikers en beheerders, die de aanvraag moet goed keuren voordat de app toegang kan krijgen tot gegevens of namens de gebruiker kan handelen. 

Wanneer de functionaliteit van een resource wordt gesegmenteerd in kleine machtigingen sets, kunnen apps van derden worden gebouwd om alleen de machtigingen aan te vragen die ze nodig hebben om hun functie uit te voeren. Gebruikers en beheerders kunnen weten welke gegevens de app kan openen. En ze kunnen er zeker van zijn dat de app niet goed werkt met kwaad aardige doel einden. Ontwikkel aars moeten altijd het principe van minimale bevoegdheden gebruiken, maar alleen voor de benodigde machtigingen voor hun toepassingen.

In OAuth 2,0 worden deze typen machtigingen *sets genoemd.* Ze worden ook vaak *machtigingen* genoemd. In het micro soft-identiteits platform wordt een machtiging weer gegeven als een teken reeks waarde. Voor het Microsoft Graph voor beeld ziet u hier de teken reeks waarde voor elke machtiging:

* De agenda van een gebruiker lezen met behulp van `Calendars.Read`
* Schrijven naar de agenda van een gebruiker met behulp van `Calendars.ReadWrite`
* E-mail verzenden als gebruiker met behulp van `Mail.Send`

Een app vraagt meestal deze machtigingen door de scopes op te geven in aanvragen voor het micro soft Identity platform Authorization-eind punt. Sommige machtigingen met een hoge bevoegdheid kunnen echter alleen worden verleend via toestemming van de beheerder. Ze kunnen worden aangevraagd of verleend met behulp van het [eind punt voor toestemming](#admin-restricted-permissions)van de beheerder. Blijf lezen voor meer informatie.

## <a name="permission-types"></a>Machtigings typen

Het micro soft Identity-platform ondersteunt twee typen machtigingen: *gedelegeerde machtigingen* en *toepassings machtigingen*.

* **Gedelegeerde machtigingen** worden gebruikt door apps waarvoor een aangemelde gebruiker aanwezig is. Voor deze apps wordt de gebruiker of een beheerder gemachtigd om de machtigingen te ontvangen die door de app worden aangevraagd. De app is gemachtigd om te fungeren als de aangemelde gebruiker bij het aanroepen van de doel resource. 

    Sommige gedelegeerde machtigingen kunnen worden verzonden door niet-beheerders. Maar sommige machtigingen met een hoge bevoegdheden hebben toestemming van de [beheerder](#admin-restricted-permissions)nodig. Zie [Administrator role Permissions (Engelstalig) in azure Active Directory (Azure AD)](../roles/permissions-reference.md)voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.

* **Toepassings machtigingen** worden gebruikt door apps die worden uitgevoerd zonder dat er een aangemelde gebruiker aanwezig is, bijvoorbeeld apps die worden uitgevoerd als achtergrond Services of daemons. Alleen [een beheerder kan toestemming geven voor](#requesting-consent-for-an-entire-tenant) toepassings machtigingen.

_Efficiënte machtigingen_ zijn de machtigingen die uw app heeft wanneer er aanvragen worden gemaakt voor de doel bron. Het is belang rijk dat u begrijpt wat het verschil is tussen de gedelegeerde machtigingen en toepassings machtigingen die door uw app worden verleend, en de daad werkelijke machtigingen die uw app verleent wanneer het aanroepen van de doel bron.

- In het geval van gedelegeerde machtigingen zijn de _effectiefste machtigingen_ van uw app het snij punt van de gemachtigde machtigingen die de app heeft gekregen (op basis van toestemming) en de bevoegdheden van de gebruiker die momenteel is aangemeld. Uw app kan nooit meer machtigingen hebben dan de aangemelde gebruiker. 

   Binnen organisaties kunnen de bevoegdheden van de aangemelde gebruiker worden bepaald door beleid of door lidmaatschap van een of meer beheerders rollen. Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.

   Stel dat uw app de _gebruiker. readwrite. alle_ gedelegeerde machtigingen heeft gekregen. Deze machtiging verleent uw app in feite machtigingen om het profiel van elke gebruiker in een organisatie te lezen en bij te werken. Als de aangemelde gebruiker een globale beheerder is, kan uw app het profiel van elke gebruiker in de organisatie bijwerken. Als de aangemelde gebruiker echter geen beheerdersrol heeft, kan uw app alleen het profiel van de aangemelde gebruiker bijwerken. De profielen van andere gebruikers in de organisatie kunnen niet worden bijgewerkt, omdat de gebruiker die toestemming heeft om namens u te handelen, niet over deze bevoegdheden beschikt.

- Voor toepassings machtigingen zijn de _effectiefste machtigingen_ van uw app het volledige niveau van bevoegdheden die door de machtiging worden geïmpliceerd. Bijvoorbeeld een app met de machtiging _User. readwrite. alle_ van de toepassing kan het profiel van elke gebruiker in de organisatie bijwerken.

## <a name="openid-connect-scopes"></a>OpenID Connect Connect-scopes

De micro soft Identity platform-implementatie van OpenID Connect Connect heeft een aantal goed gedefinieerde bereiken die ook worden gehost op Microsoft Graph: `openid` ,, en `email` `profile` `offline_access` . De `address` Connect-en `phone` OpenID Connect-scopes worden niet ondersteund.

Als u de OpenID Connect Connect-scopes en een token aanvraagt, krijgt u een token om het [User info-eind punt](userinfo.md)aan te roepen.

### <a name="openid"></a>OpenID Connect

Als een app zich aanmeldt met behulp van [OpenID Connect Connect](active-directory-v2-protocols.md), moet deze het `openid` bereik aanvragen. Het `openid` bereik wordt weer gegeven op de pagina toestemming van het werk account als het **aanmeldings** recht. Op de pagina Persoonlijke Microsoft-account toestemming wordt deze weer gegeven als de **weer gave van uw profiel en verbinding maken met apps en services met behulp van uw Microsoft-account** machtiging. 

Met deze machtiging kan een app een unieke id voor de gebruiker ontvangen in de vorm van de `sub` claim. De machtiging geeft de app ook toegang tot het user info-eind punt. Het `openid` bereik kan worden gebruikt op het micro soft Identity platform token-eind punt voor het verkrijgen van ID-tokens. De app kan deze tokens gebruiken voor verificatie.

### <a name="email"></a>e-mail

Het `email` bereik kan worden gebruikt met het `openid` bereik en andere scopes. Het geeft de app toegang tot het primaire e-mail adres van de gebruiker in de vorm van de `email` claim. 

De `email` claim is alleen opgenomen in een token als er een e-mail adres is gekoppeld aan het gebruikers account, wat niet altijd het geval is. Als uw app gebruikmaakt `email` van het bereik, moet de app een aanvraag kunnen afhandelen waarin geen `email` claim voor komt in het token.

### <a name="profile"></a>profiel

Het `profile` bereik kan worden gebruikt met het `openid` bereik en elk ander bereik. Hiermee krijgt de app toegang tot een grote hoeveelheid informatie over de gebruiker. De informatie waartoe het toegang heeft, is inclusief, maar is niet beperkt tot, de gegeven naam, de voor keur, de gebruikers naam en de object-ID van de gebruiker. 

Zie de naslag informatie voor een volledige lijst met `profile` beschik bare claims in de `id_tokens` para meter voor [ `id_tokens` ](id-tokens.md)een specifieke gebruiker.

### <a name="offline_access"></a>offline_access

Het [ `offline_access` bereik](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) biedt uw app namens de gebruiker een langere periode toegang tot resources. Op de pagina toestemming wordt dit bereik weer gegeven als de **toegang beheren tot gegevens waartoe u toegang hebt gekregen** . 

Wanneer een gebruiker het bereik goedkeurt `offline_access` , kan uw app vernieuwings tokens ontvangen van het micro soft Identity platform-token-eind punt. Vernieuwings tokens zijn lang in het geleefde. Uw app kan nieuwe toegangs tokens verkrijgen als oudere versies verlopen.

> [!NOTE]
> Deze machtiging wordt momenteel weer gegeven op alle pagina's met toestemming, zelfs voor stromen die geen vernieuwings token bieden (zoals de [impliciete stroom](v2-oauth2-implicit-grant-flow.md)). Deze installatie heeft betrekking op scenario's waarbij een client in de impliciete stroom kan beginnen en vervolgens naar de code stroom gaat waarin een vernieuwings token wordt verwacht.

Op het micro soft-identiteits platform (aanvragen van het v 2.0-eind punt) moet uw app het bereik expliciet aanvragen `offline_access` om vernieuwings tokens te ontvangen. Als u dus een autorisatie code inwisselt in de [OAuth 2,0-autorisatie code stroom](active-directory-v2-protocols.md), ontvangt u alleen een toegangs token van het `/token` eind punt. 

Het toegangs token is voor korte tijd geldig. Deze verloopt doorgaans over een uur. Op dat moment moet uw app de gebruiker terug naar het `/authorize` eind punt omleiden om een nieuwe autorisatie code op te halen. Afhankelijk van het type app, moet de gebruiker tijdens deze omleiding mogelijk hun referenties opnieuw invoeren of opnieuw toestemming geven voor machtigingen.

Zie voor meer informatie over het verkrijgen en gebruiken van vernieuwings tokens de referentie voor het [micro soft Identity platform-protocol](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Toestemming van individuele gebruiker aanvragen

In een [OpenID Connect Connect-of OAuth 2,0](active-directory-v2-protocols.md) -autorisatie aanvraag kan een app de benodigde machtigingen aanvragen met behulp van de `scope` query parameter. Wanneer een gebruiker zich aanmeldt bij een app, stuurt de app bijvoorbeeld een aanvraag, zoals in het volgende voor beeld. (Regel einden worden toegevoegd voor de Lees baarheid.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

De `scope` para meter is een door spaties gescheiden lijst met gedelegeerde machtigingen die de app aanvraagt. Elke machtiging wordt aangegeven door de machtigings waarde toe te voegen aan de id van de resource (de URI van de toepassings-ID). In het voor beeld van de aanvraag moet de app toestemming hebben om de agenda van de gebruiker te lezen en e-mail te verzenden als de gebruiker.

Nadat de gebruiker zijn of haar referenties heeft ingevoerd, controleert het micro soft Identity-platform op een overeenkomende record van de toestemming van de *gebruiker*. Als de gebruiker in het verleden niet heeft ingestemd met een van de aangevraagde machtigingen en als de beheerder geen toestemming heeft gegeven namens de hele organisatie, vraagt het micro soft Identity-platform de gebruiker de aangevraagde machtigingen te verlenen.

Op dit moment wordt de machtiging `offline_access` (' toegang tot de gegevens die u hebt gegeven, behouden ') en `user.read` (' aanmelden en uw profiel lezen ') automatisch opgenomen in de eerste toestemming voor een toepassing.  Deze machtigingen zijn over het algemeen vereist voor de juiste app-functionaliteit. De `offline_access` machtiging geeft de app toegang tot het vernieuwen van tokens die essentieel zijn voor systeem eigen apps en web-apps. `user.read`Met de machtiging krijgt u toegang tot de `sub` claim. De client of app kan de gebruiker na verloop van tijd op de juiste wijze identificeren en elementaire-gebruikers gegevens openen.

![Voor beeld van een scherm opname waarin de toestemming van een werk account wordt vermeld.](./media/v2-permissions-and-consent/work_account_consent.png)

Wanneer de gebruiker de machtigings aanvraag goedkeurt, wordt de toestemming vastgelegd. De gebruiker hoeft niet opnieuw toestemming te geven wanneer hij zich later aanmeldt bij de toepassing.

## <a name="requesting-consent-for-an-entire-tenant"></a>Toestemming vragen voor een hele Tenant

Wanneer een organisatie een licentie of abonnement voor een toepassing koopt, wil de organisatie vaak proactief de toepassing instellen voor gebruik door alle leden van de organisatie. Als onderdeel van dit proces kan een beheerder toestemming verlenen voor de toepassing om namens een gebruiker in de Tenant actie te ondernemen. Als de beheerder toestemming verleent voor de hele Tenant, zien de gebruikers van de organisatie geen toestemming pagina voor de toepassing.

Voor het aanvragen van toestemming voor gedelegeerde machtigingen voor alle gebruikers in een Tenant, kan uw app gebruikmaken van het eind punt voor de beheerder.

Daarnaast moeten toepassingen het afstemmings eindpunt van de beheerder gebruiken om toepassings machtigingen aan te vragen.

## <a name="admin-restricted-permissions"></a>Beheerders machtigingen

Sommige machtigingen met hoge bevoegdheden in micro soft-resources kunnen worden ingesteld op *beheerder*. Hier volgen enkele voor beelden van dit soort machtigingen:

* Volledige profielen van alle gebruikers lezen met behulp van `User.Read.All`
* Gegevens schrijven naar de directory van een organisatie met behulp van `Directory.ReadWrite.All`
* Alle groepen in de directory van een organisatie lezen met behulp van `Groups.Read.All`

Hoewel een consumenten gebruiker een toepassing toegang kan verlenen tot dit soort gegevens, kunnen organisatie gebruikers geen toegang verlenen tot dezelfde set gevoelige Bedrijfs gegevens. Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatie gebruiker, ontvangt de gebruiker een fout bericht waarin staat dat ze niet zijn geautoriseerd om toestemming te geven voor de machtigingen van uw app.

Als uw app scopes voor beheerders machtigingen vereist, moet de beheerder van de organisatie toestemming geven namens de gebruikers van de organisatie. Uw app kan gebruikmaken van het afstemmings eindpunt van de beheerder om te voor komen dat gebruikers vragen worden weer gegeven die toestemming vragen voor machtigingen die ze niet kunnen verlenen. Het afstemmings eindpunt van de beheerder wordt in de volgende sectie besproken.

Als de toepassing gedelegeerde machtigingen met hoge bevoegdheden aanvraagt en een beheerder deze machtigingen verleent via het door de beheerder toestemmings eindpunt, wordt toestemming verleend voor alle gebruikers in de Tenant.

Als de toepassing toepassings machtigingen aanvraagt en een beheerder deze machtigingen verleent via het door de beheerder toestemmings eindpunt, wordt deze toekenning niet uitgevoerd namens een specifieke gebruiker. In plaats daarvan worden machtigingen *rechtstreeks* verleend aan de client toepassing. Deze typen machtigingen worden alleen gebruikt door daemon-services en andere niet-interactieve toepassingen die op de achtergrond worden uitgevoerd.

## <a name="using-the-admin-consent-endpoint"></a>Het afstemmings eindpunt van de beheerder gebruiken

Nadat u het beheerders toestemming-eind punt hebt gebruikt om beheerders toestemming te verlenen, bent u klaar. Gebruikers hoeven geen verdere actie te ondernemen. Nadat de beheerder toestemming heeft gegeven, kunnen gebruikers een toegangs token verkrijgen via een typische verificatie stroom. Het resulterende toegangs token heeft de toegestane machtigingen.

Wanneer een globale beheerder uw toepassing gebruikt en wordt omgeleid naar het toestemming eind punt, detecteert het micro soft Identity-platform de rol van de gebruiker. Er wordt gevraagd of de globale beheerder toestemming wil geven namens de volledige Tenant voor de machtigingen die u hebt aangevraagd. U kunt in plaats daarvan een speciaal beheerders toestemming-eind punt gebruiken om een beheerder te vragen om toestemming te verlenen namens de hele Tenant. Dit eind punt is ook nodig voor het aanvragen van toepassings machtigingen. Toepassings machtigingen kunnen niet worden aangevraagd met behulp van het toestaan van het eind punt.

Als u deze stappen volgt, kan uw app machtigingen aanvragen voor alle gebruikers in een Tenant, met inbegrip van beheerders beperkte bereiken. Deze bewerking is een hoge bevoegdheid. Gebruik de bewerking alleen als dat nodig is voor uw scenario.

Zie het voor [beeld van beheerders beperking](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) in github voor een voor beeld van een code waarmee de stappen worden geïmplementeerd.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>De machtigingen aanvragen in de portal voor app-registratie

In de app-registratie portal kunnen toepassingen een lijst weer geven met de machtigingen die ze nodig hebben, met inbegrip van gedelegeerde machtigingen en toepassings machtigingen. Met deze instelling wordt het gebruik van het `/.default` bereik en de **toestemming** van de beheerder voor de Azure Portal verleend.  

Over het algemeen moeten de machtigingen statisch worden gedefinieerd voor een bepaalde toepassing. Ze moeten een superset zijn van de machtigingen die de app dynamisch of incrementeel zal aanvragen.

> [!NOTE]
>Toepassings machtigingen kunnen alleen worden aangevraagd met behulp van [`/.default`](#the-default-scope) . Als uw app dus toepassings machtigingen nodig heeft, controleert u of deze worden vermeld in de app registratie-Portal.

De lijst met statisch aangevraagde machtigingen voor een toepassing configureren:

1. Ga naar uw toepassing in de <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal-app-registraties</a> Snelstartgids.
1. Selecteer een toepassing of [Maak een app](quickstart-register-app.md) als u dat nog niet hebt gedaan.
1. Selecteer op de pagina **overzicht** van de toepassing onder **beheren** de optie **API-machtigingen**  >  **een machtiging toevoegen**.
1. Selecteer **Microsoft Graph** in de lijst met beschik bare api's. Voeg vervolgens de machtigingen toe die uw app nodig heeft.
1. Selecteer **machtigingen toevoegen**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Aanbevolen: de gebruiker in aan uw app ondertekenen

Wanneer u een toepassing bouwt die gebruikmaakt van het door de beheerder toestemmings eindpunt, moet de app doorgaans een pagina of weer gave hebben waarin de beheerder de machtigingen van de app kan goed keuren. Deze pagina kan het volgende zijn:

* Onderdeel van de registratie stroom van de app.
* Onderdeel van de instellingen van de app.
* Een toegewezen stroom ' Connect '. 

In veel gevallen is het zinvol voor de app om deze weer gave ' verbinding maken ' alleen weer te geven nadat een gebruiker zich heeft aangemeld met een werk Microsoft-account of school Microsoft-account.

Wanneer u de gebruiker bij uw app ondertekent, kunt u de organisatie identificeren waartoe de beheerder behoort voordat u deze vraagt om de benodigde machtigingen goed te keuren. Hoewel deze stap niet strikt nood zakelijk is, kan het u helpen om een meer intuïtieve ervaring te creëren voor uw gebruikers in uw organisatie. 

Als u de gebruiker wilt ondertekenen in, volgt u de [zelf studies voor het micro soft Identity platform-protocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen van een adreslijst beheerder aanvragen

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, kunt u de gebruiker omleiden naar het micro soft Identity platform Administrator toestemmings eindpunt.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parameter        | Voorwaarde        | Beschrijving                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Vereist | De Directory-Tenant waarvan u toestemming wilt aanvragen. Dit kan worden gegeven in een GUID-of beschrijvende naam indeling. U kunt ook algemeen verwijzen naar organisaties, zoals wordt weer gegeven in het voor beeld. Gebruik ' common ' niet, omdat persoonlijke accounts geen toestemming van de beheerder kunnen bieden, behalve in de context van een Tenant. Gebruik, indien mogelijk, de Tenant-ID om te zorgen voor de beste compatibiliteit met persoonlijke accounts die tenants beheren. |
| `client_id` | Vereist | De client-ID van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `redirect_uri` | Vereist |De omleidings-URI waar u het antwoord voor uw app wilt laten afhandelen. Het moet exact overeenkomen met een van de omleidings-Uri's die u hebt geregistreerd in de app-registratie Portal. |
| `state` | Aanbevolen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van elke gewenste inhoud. Gebruik de status om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin deze zijn aangemeld. |
|`scope`        | Vereist        | Hiermee wordt de set machtigingen gedefinieerd die worden aangevraagd door de toepassing. Bereiken kunnen statisch (met [`/.default`](#the-default-scope) ) of dynamisch zijn.  Deze set kan de OpenID Connect Connect-scopes ( `openid` , `profile` , `email` ) bevatten. Als u toepassings machtigingen nodig hebt, moet u gebruiken `/.default` om de statisch geconfigureerde lijst met machtigingen aan te vragen.  |


Op dit moment heeft Azure AD een Tenant beheerder nodig om zich aan te melden om de aanvraag te volt ooien. De beheerder wordt gevraagd om alle machtigingen die u in de para meter hebt aangevraagd goed te keuren `scope` .  Als u een statische ()-waarde hebt gebruikt `/.default` , werkt deze als het eind punt v 1.0-beheerder toestemming en vraagt u toestemming aan voor alle scopes die zijn gevonden in de vereiste machtigingen voor de app.

#### <a name="successful-response"></a>Geslaagde reactie

Als de beheerder de machtigingen voor uw app goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beschrijving |
| --- | --- |
| `tenant` | De Directory-Tenant die uw toepassing heeft toegewezen aan de aangevraagde machtigingen, in GUID-indeling. |
| `state` | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in het token antwoord. Dit kan een teken reeks zijn van elke gewenste inhoud. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `admin_consent` | Wordt ingesteld op `True` . |

#### <a name="error-response"></a>Fout bericht

Als de beheerder de machtigingen voor uw app niet goed keuren, ziet de mislukte reactie er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor fout code die kan worden gebruikt voor het classificeren van typen fouten die optreden. Het kan ook worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een fout. |

Nadat u een geslaagde reactie van het afstemmings eindpunt van de beheerder hebt ontvangen, heeft uw app de aangevraagde machtigingen verkregen. Vervolgens kunt u een token aanvragen voor de gewenste resource.

## <a name="using-permissions"></a>Machtigingen gebruiken

Nadat de gebruiker heeft gestemd op machtigingen voor uw app, kan uw app toegangs tokens verkrijgen die de machtiging van de app voor toegang tot een bron in een bepaalde capaciteit vertegenwoordigen. Een toegangs token kan alleen worden gebruikt voor één resource. Maar versleuteld in het toegangs token is elke machtiging die uw app voor die resource heeft gekregen. Voor het verkrijgen van een toegangs token kan uw app een aanvraag indienen bij het micro soft Identity platform-token-eind punt, als volgt:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

U kunt het resulterende toegangs token gebruiken in HTTP-aanvragen voor de resource. Het is betrouwbaar dat de resource de juiste machtiging heeft om een specifieke taak uit te voeren.

Zie voor meer informatie over het OAuth 2,0-protocol en het verkrijgen van toegangs tokens de [referentie voor het micro soft Identity platform-eindpunt protocol](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Het/.default-bereik

U kunt het `/.default` bereik gebruiken om uw apps van het v 1.0-eind punt te migreren naar het micro soft Identity platform-eind punt. Het `/.default` bereik is ingebouwd voor elke toepassing die verwijst naar de statische lijst met machtigingen die zijn geconfigureerd voor de registratie van de toepassing. 

Een `scope` waarde van `https://graph.microsoft.com/.default` is functioneel hetzelfde als `resource=https://graph.microsoft.com` op het eind punt v 1.0. Als u het `https://graph.microsoft.com/.default` bereik in de aanvraag opgeeft, vraagt uw toepassing een toegangs token op dat de scopes bevat voor elke Microsoft Graph machtiging die u hebt geselecteerd voor de app in de app-registratie Portal. Het bereik wordt gemaakt met behulp van de resource-URI en `/.default` . Dus als de resource-URI is `https://contosoApp.com` , is het aangevraagde bereik `https://contosoApp.com/.default` .  Zie de [sectie over afsluitende slashes](#trailing-slash-and-default)voor gevallen waarin u een tweede slash moet toevoegen om het token correct aan te vragen.

Het `/.default` bereik kan worden gebruikt in een OAuth 2,0-stroom. Maar dit is nodig voor de stroom van namens [-](v2-oauth2-on-behalf-of-flow.md) en [client referenties](v2-oauth2-client-creds-grant-flow.md). U hebt deze ook nodig wanneer u het v2-beheerders toestemmings eindpunt gebruikt om toepassings machtigingen aan te vragen.

Clients kunnen statisch ( `/.default` ) toestemming en dynamische toestemming niet combi neren in één aanvraag. Daarom `scope=https://graph.microsoft.com/.default+mail.read` resulteert dit in een fout, omdat de bereik typen worden gecombineerd.

### <a name="default-and-consent"></a>/.default en toestemming

Het `/.default` bereik activeert ook het eindpunt gedrag van v 1.0 `prompt=consent` . Er wordt toestemming gevraagd voor alle machtigingen die de toepassing heeft geregistreerd, ongeacht de bron. Als deze is opgenomen als onderdeel van de aanvraag, `/.default` retourneert de scope een token dat de scopes voor de aangevraagde bron bevat.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default wanneer de gebruiker al toestemming heeft gegeven

De `/.default` Scope is functioneel identiek aan het gedrag van het `resource` -gerichte v 1.0-eind punt. Het bevat ook het gedrag van de toestemming van het eind punt v 1.0. Dat wil zeggen dat `/.default` een toestemming prompt alleen wordt geactiveerd als de gebruiker geen machtiging heeft verleend tussen de client en de resource. 

Als een dergelijke toestemming bestaat, bevat het geretourneerde token alle bereiken die de gebruiker heeft toegewezen voor die bron. Als er echter geen machtiging is verleend of als de `prompt=consent` para meter is ingesteld, wordt er een toestemming prompt weer gegeven voor alle scopes die door de client toepassing zijn geregistreerd.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Voor beeld 1: de gebruiker of Tenant beheerder heeft machtigingen verleend

In dit voor beeld heeft de gebruiker of een Tenant beheerder de `mail.read` machtigingen en `user.read` Microsoft Graph verleend aan de client. 

Als de client vraagt `scope=https://graph.microsoft.com/.default` , wordt er geen vraag naar toestemming weer gegeven, ongeacht de inhoud van de geregistreerde machtigingen van de client toepassing voor Microsoft Graph. Het geretourneerde token bevat de bereiken `mail.read` en `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Voor beeld 2: de gebruiker heeft geen machtigingen verleend tussen de client en de resource

In dit voor beeld heeft de gebruiker geen toestemming verleend tussen de client en Microsoft Graph. De client is geregistreerd voor de machtigingen `user.read` en `contacts.read` . Het is ook geregistreerd voor het Azure Key Vault bereik `https://vault.azure.net/user_impersonation` . 

Wanneer de client een token aanvraagt voor `scope=https://graph.microsoft.com/.default` , ziet de gebruiker een bestemmings pagina voor de `user.read` Scope, het `contacts.read` bereik en het Key Vault `user_impersonation` bereik. Het geretourneerde token bevat alleen de `user.read` en- `contacts.read` scopes. Deze kan alleen worden gebruikt voor Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Voor beeld 3: de gebruiker heeft toestemming gegeven, en de client vraagt meer bereiken aan

In dit voor beeld heeft de gebruiker al toestemming gegeven `mail.read` voor de client. De client is geregistreerd voor de `contacts.read` Scope. 

Wanneer de client een token aanvraagt met behulp van `scope=https://graph.microsoft.com/.default` en aanvragen van toestemming via `prompt=consent` , ziet de gebruiker een bestemmings pagina voor alle (en alleen) de machtigingen die de toepassing heeft geregistreerd. Het `contacts.read` bereik bevindt zich op de pagina met toestemming, maar is `mail.read` niet. Het geretourneerde token is voor Microsoft Graph. Het bevat `mail.read` en `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Het/.default-bereik gebruiken met de-client

In sommige gevallen kan een client zijn eigen bereik aanvragen `/.default` . In het volgende voor beeld ziet u dit scenario.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Dit code voorbeeld produceert een toestemmings pagina voor alle geregistreerde machtigingen als de voor gaande beschrijvingen van toestemming en van `/.default` toepassing zijn op het scenario. Vervolgens retourneert de code een `id_token` , in plaats van een toegangs token.  

Dit gedrag geldt voor enkele verouderde clients die worden verplaatst van Azure AD Authentication Library (ADAL) naar de micro soft Authentication Library (MSAL). Dit installatie programma *mag* niet worden gebruikt door nieuwe clients die gericht zijn op het micro soft Identity-platform.

### <a name="client-credentials-grant-flow-and-default"></a>Client referenties geven stroom en/.default  

Een ander gebruik van `/.default` is het aanvragen van toepassings machtigingen (of *rollen*) in een niet-interactieve toepassing, zoals een daemon-app die gebruikmaakt van de [client referenties](v2-oauth2-client-creds-grant-flow.md) toekennings stroom om een web-API aan te roepen.

Zie [app-rollen toevoegen in uw toepassing](howto-add-app-roles-in-azure-ad-apps.md)om toepassings machtigingen (rollen) voor een web-API te maken.

Aanvragen voor client referenties in uw client-app *moeten* bevatten `scope={resource}/.default` . Hier `{resource}` is de Web-API die uw app wil aanroepen. Het verlenen van een aanvraag voor client referenties met behulp van afzonderlijke toepassings machtigingen (rollen) wordt *niet* ondersteund. Alle toepassings machtigingen (rollen) die zijn verleend voor die Web-API, zijn opgenomen in het geretourneerde toegangs token.

Zie [een client toepassing configureren voor toegang tot een web-API](quickstart-configure-app-access-web-apis.md)voor informatie over het verlenen van toegang tot de toepassings machtigingen die u definieert, inclusief het verlenen van beheerders toestemming voor de toepassing.

### <a name="trailing-slash-and-default"></a>Afsluitende slash en/.default

Sommige bron-Uri's hebben een afsluitende schuine streep, bijvoorbeeld in `https://contoso.com/` plaats van `https://contoso.com` . De afsluitende schuine streep kan problemen veroorzaken met de validatie van tokens. Problemen treden voornamelijk op wanneer een token voor Azure Resource Manager () wordt aangevraagd `https://management.azure.com/` . In dit geval betekent een afsluitende slash op de bron-URI dat de slash aanwezig moet zijn wanneer het token wordt aangevraagd.  Als u dus een token aanvraagt voor `https://management.azure.com/` en gebruikt `/.default` , moet u een aanvraag indienen `https://management.azure.com//.default` (Let op de dubbele slash!). Als u in het algemeen controleert of het token wordt uitgegeven en of het token wordt afgewezen door de API die dit moet accepteren, moet u een tweede slash toevoegen en het opnieuw proberen. 

## <a name="troubleshooting-permissions-and-consent"></a>Problemen met machtigingen en toestemming

Zie [onverwachte fout bij het uitvoeren van een aanvraag voor een toepassing](../manage-apps/application-sign-in-unexpected-user-consent-error.md)voor informatie over het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

* [ID-tokens in het micro soft Identity-platform](id-tokens.md)
* [Toegangs tokens in het micro soft Identity-platform](access-tokens.md)

---
title: Scopes, machtigingen en toestemming van micro soft Identity platform
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
ms.openlocfilehash: da432ee3877af4de931ee6d55860b647090d8e3d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208774"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform

Toepassingen die zijn geïntegreerd met het Microsoft Identity Platform volgen een autorisatiemodel dat gebruikers en beheerders controle geeft over de toegang tot gegevens. De implementatie van het autorisatie model is bijgewerkt op het micro soft Identity platform-eind punt en verandert de manier waarop een app moet communiceren met het micro soft Identity-platform. In dit artikel worden de basis concepten van dit autorisatie model beschreven, met inbegrip van scopes, machtigingen en toestemming.

## <a name="scopes-and-permissions"></a>Bereiken en machtigingen

Het micro soft Identity-platform implementeert het [OAuth 2,0](active-directory-v2-protocols.md) -autorisatie protocol. OAuth 2,0 is een methode waarmee een app van derden namens een gebruiker toegang heeft tot webhosten. Elke webhostige resource die wordt geïntegreerd met het micro soft Identity-platform heeft een resource-id of een URI voor de *toepassings-id*. Enkele van de webhoste bronnen van micro soft zijn bijvoorbeeld:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365-mail-API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

We raden u ten zeerste aan om Microsoft Graph te gebruiken in plaats van Microsoft 365 e-mail-API, enzovoort.

Dit geldt ook voor alle resources van derden die zijn geïntegreerd met het micro soft Identity platform. Elk van deze resources kan ook een set machtigingen definiëren die kunnen worden gebruikt om de functionaliteit van die bron te verdelen in kleinere segmenten. [Microsoft Graph](https://graph.microsoft.com) heeft bijvoorbeeld machtigingen gedefinieerd om de volgende taken uit te voeren, onder andere:

* De agenda van een gebruiker lezen
* Schrijven naar de agenda van een gebruiker
* E-mail verzenden als gebruiker

Als u deze typen machtigingen definieert, heeft de resource een nauw keurige controle over de gegevens en hoe de API-functionaliteit wordt weer gegeven. Een app van derden kan deze machtigingen aanvragen bij gebruikers en beheerders, die de aanvraag moet goed keuren voordat de app toegang kan krijgen tot gegevens of namens de gebruiker kan handelen. Door de functionaliteit van de resource te segmenteren in kleinere machtigingen sets, kunnen apps van derden worden gebouwd om alleen de specifieke machtigingen aan te vragen die ze nodig hebben om hun functie uit te voeren. Gebruikers en beheerders kunnen precies weten met welke gegevens de app toegang heeft, en ze kunnen er beter op vertrouwen dat ze niet goed werken met kwaad aardige intentie. Ontwikkel aars moeten altijd op het beginsel van de minimale bevoegdheid worden gevraagd, zodat alleen de benodigde machtigingen voor hun toepassingen kunnen worden gebruikt.

In OAuth 2,0 worden deze typen machtigingen *scopes* genoemd. Ze worden ook vaak *machtigingen* genoemd. Een machtiging wordt weer gegeven in het micro soft Identity-platform als een teken reeks waarde. Als u doorgaat met het Microsoft Graph-voor beeld, is de teken reeks waarde voor elke machtiging:

* De agenda van een gebruiker lezen met behulp van `Calendars.Read`
* Schrijven naar de agenda van een gebruiker met behulp van `Calendars.ReadWrite`
* E-mail verzenden als gebruiker met behulp van `Mail.Send`

Een app vraagt meestal deze machtigingen door de scopes op te geven in aanvragen voor het micro soft Identity platform Authorization-eind punt. Bepaalde machtigingen met een hoge bevoegdheid kunnen echter alleen worden verleend via toestemming van de beheerder en aangevraagd/verleend met behulp van het [eind punt voor toestemming](#admin-restricted-permissions)van de beheerder. Lees verder voor meer informatie.

## <a name="permission-types"></a>Machtigings typen

Micro soft Identity platform ondersteunt twee typen machtigingen: **gedelegeerde machtigingen** en **toepassings machtigingen**.

* **Gedelegeerde machtigingen** worden gebruikt door apps waarvoor een aangemelde gebruiker aanwezig is. Voor deze apps is de gebruiker of een beheerder in staat om de machtigingen die door de app worden aangevraagd en de app gedelegeerde toestemming te laten fungeren als de aangemelde gebruiker bij het aanroepen van de doel bron. Sommige gedelegeerde machtigingen kunnen worden toegestuurd aan door gebruikers zonder beheerders rechten, maar sommige machtigingen met een hogere bevoegdheid zijn vereist voor de [beheerder](#admin-restricted-permissions). Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.

* **Toepassings machtigingen** worden gebruikt door apps die worden uitgevoerd zonder een aangemelde gebruiker. bijvoorbeeld: apps die worden uitgevoerd als achtergrond Services of daemons.  Toepassings machtigingen kunnen alleen worden [gezonden door een beheerder](#requesting-consent-for-an-entire-tenant).

_Efficiënte machtigingen_ zijn de machtigingen die uw app heeft wanneer er aanvragen worden gedaan voor de doel bron. Het is belang rijk dat u begrijpt wat het verschil is tussen de gedelegeerde en toepassings machtigingen die uw app krijgt en de juiste machtigingen wanneer u aanroepen naar de doel bron.

- Voor gedelegeerde machtigingen zijn de _effectiefste machtigingen_ van uw app het ten minste privileged snij punt van de gedelegeerde machtigingen die de app heeft gekregen (via toestemming) en de bevoegdheden van de gebruiker die momenteel is aangemeld. Uw app kan nooit meer machtigingen hebben dan de aangemelde gebruiker. De machtigingen van de aangemelde gebruiker kunnen in organisaties worden bepaald door beleid of door lidmaatschap in een of meer beheerdersrollen. Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.

   Stel dat uw app de _gebruiker. readwrite. alle_ gedelegeerde machtigingen heeft gekregen. Deze machtiging verleent uw app in feite machtigingen om het profiel van elke gebruiker in een organisatie te lezen en bij te werken. Als de aangemelde gebruiker een globale beheerder is, kan uw app het profiel van elke gebruiker in de organisatie bijwerken. Als de aangemelde gebruiker zich echter niet in een beheerdersrol bevindt, kan uw app alleen het profiel van de aangemelde gebruiker bijwerken. De app kan geen profielen van andere gebruikers in de organisatie bijwerken omdat de gebruiker namens welke de app machtigingen heeft om te handelen niet over deze rechten beschikt.

- Voor toepassings machtigingen zijn de _effectiefste machtigingen_ van uw app het volledige niveau van bevoegdheden die door de machtiging worden geïmpliceerd. Bijvoorbeeld een app met de machtiging _User. readwrite. alle_ van de toepassing kan het profiel van elke gebruiker in de organisatie bijwerken.

## <a name="openid-connect-scopes"></a>OpenID Connect Connect-scopes

De micro soft Identity platform-implementatie van OpenID Connect Connect heeft een aantal goed gedefinieerde bereiken die ook worden gehost op de Microsoft Graph: `openid` ,, en `email` `profile` `offline_access` . De `address` Connect-en `phone` OpenID Connect-scopes worden niet ondersteund.

Als u de OIDC-bereiken en een token aanvraagt, krijgt u een token om het [User info-eind punt](userinfo.md)aan te roepen.

### <a name="openid"></a>OpenID Connect

Als een app zich aanmeldt met behulp van [OpenID Connect Connect](active-directory-v2-protocols.md), moet deze het `openid` bereik aanvragen. Het `openid` bereik wordt op de pagina toestemming van het werk account weer gegeven als de machtiging ' aanmelden bij ' en op de pagina persoonlijke Microsoft-account toestemming als de machtiging uw profiel weer geven en verbinding maken met apps en services met uw Microsoft-account. Met deze machtiging kan een app een unieke id voor de gebruiker ontvangen in de vorm van de `sub` claim. Het geeft ook de app toegang tot het user info-eind punt. Het `openid` bereik kan worden gebruikt op het micro soft Identity platform token-eind punt voor het verkrijgen van ID-tokens, die door de app kunnen worden gebruikt voor verificatie.

### <a name="email"></a>e-mail

Het `email` bereik kan worden gebruikt met het `openid` bereik en andere. Het geeft de app toegang tot het primaire e-mail adres van de gebruiker in de vorm van de `email` claim. De `email` claim is alleen opgenomen in een token als er een e-mail adres is gekoppeld aan het gebruikers account, wat niet altijd het geval is. Als de scope gebruikmaakt van het `email` bereik, moet uw app worden voor bereid voor het afhandelen van een geval waarin de `email` claim niet in het token bestaat.

### <a name="profile"></a>profiel

Het `profile` bereik kan worden gebruikt met het `openid` bereik en andere. Hiermee krijgt de app toegang tot een aanzienlijke hoeveelheid informatie over de gebruiker. De informatie waartoe het toegang heeft, is inclusief, maar is niet beperkt tot, de gegeven naam, de voor keur, de gebruikers naam en de object-ID van de gebruiker. Zie de [ `id_tokens` verwijzing](id-tokens.md)voor een volledige lijst met de profiel claims die beschikbaar zijn in de para meter id_tokens voor een specifieke gebruiker.

### <a name="offline_access"></a>offline_access

Het [ `offline_access` bereik](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) biedt uw app namens de gebruiker een langere periode toegang tot resources. Op de pagina toestemming wordt dit bereik weer gegeven als de machtiging ' toegang tot gegevens waartoe u toegang hebt verleend '. Wanneer een gebruiker het bereik goedkeurt `offline_access` , kan uw app vernieuwings tokens ontvangen van het micro soft Identity platform-token-eind punt. Vernieuwings tokens zijn lang in het geleefde. Uw app kan nieuwe toegangs tokens verkrijgen als oudere versies verlopen.

> [!NOTE]
> Deze machtiging wordt weer gegeven op alle toestemmings schermen, zelfs voor stromen die geen vernieuwings token bieden (de [impliciete stroom](v2-oauth2-implicit-grant-flow.md)). Dit is bedoeld om scenario's te behandelen waarbij een client in de impliciete stroom kan beginnen en vervolgens naar de code stroom gaan waar een vernieuwings token wordt verwacht.

Op het micro soft-identiteits platform (aanvragen van het v 2.0-eind punt) moet uw app het bereik expliciet aanvragen `offline_access` om vernieuwings tokens te ontvangen. Dit betekent dat wanneer u een autorisatie code inwisselt in de [OAuth 2,0-autorisatie code stroom](active-directory-v2-protocols.md), alleen een toegangs token van het `/token` eind punt ontvangt. Het toegangs token is voor korte tijd geldig. Het toegangs token verloopt doorgaans over een uur. Op dat moment moet uw app de gebruiker terug naar het `/authorize` eind punt omleiden om een nieuwe autorisatie code op te halen. Afhankelijk van het type app, moet de gebruiker tijdens deze omleiding mogelijk hun referenties opnieuw invoeren of opnieuw toestemming geven voor machtigingen.

Zie voor meer informatie over het verkrijgen en gebruiken van vernieuwings tokens de referentie voor het [micro soft Identity platform-protocol](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Toestemming van individuele gebruiker aanvragen

In een [OpenID Connect Connect-of OAuth 2,0](active-directory-v2-protocols.md) -autorisatie aanvraag kan een app de benodigde machtigingen aanvragen met behulp van de `scope` query parameter. Wanneer bijvoorbeeld een gebruiker zich aanmeldt bij een app, stuurt de app een aanvraag zoals het volgende voor beeld (met regel einden toegevoegd voor de Lees baarheid):

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

Nadat de gebruiker zijn of haar referenties heeft ingevoerd, controleert het micro soft Identity platform-eind punt een overeenkomende record van de toestemming van de *gebruiker*. Als de gebruiker zich in het verleden niet heeft ingestemd met een van de aangevraagde machtigingen, noch een beheerder heeft ingestemd met deze machtigingen namens de hele organisatie, wordt de gebruiker door het micro soft Identity platform-eind punt gevraagd om de aangevraagde machtigingen te verlenen.

Op dit moment worden de `offline_access` machtigingen ("toegang tot gegevens waartoe u toegang hebt verleend") en `user.read` (waarbij u zich aanmeldt en uw profiel lezen ") automatisch opgenomen in de eerste toestemming voor een toepassing.  Deze machtigingen zijn over het algemeen vereist voor de juiste functionaliteit van de app: `offline_access` geeft de app toegang tot het vernieuwen van tokens, kritiek voor systeem eigen en web-apps, terwijl `user.read` u toegang hebt tot de claim, waardoor de gebruiker in de loop van de `sub` tijd en toegang kan krijgen tot elementaire gebruikers gegevens.

![Voor beeld van een scherm opname met de toestemming van een werk account](./media/v2-permissions-and-consent/work_account_consent.png)

Wanneer de gebruiker de machtigings aanvraag goedkeurt, wordt de toestemming vastgelegd en hoeft de gebruiker niet opnieuw toestemming te geven voor volgende aanmeldingen bij de toepassing.

## <a name="requesting-consent-for-an-entire-tenant"></a>Toestemming vragen voor een hele Tenant

Wanneer een organisatie een licentie of abonnement voor een toepassing koopt, wil de organisatie vaak de toepassing proactief instellen voor gebruik door alle leden van de organisatie. Als onderdeel van dit proces kan een beheerder toestemming verlenen voor de toepassing om namens een gebruiker in de Tenant actie te ondernemen. Als de beheerder toestemming verleent voor de hele Tenant, zien de gebruikers van de organisatie geen toestemming pagina voor de toepassing.

Voor het aanvragen van toestemming voor gedelegeerde machtigingen voor alle gebruikers in een Tenant, kan uw app gebruikmaken van het eind punt voor de beheerder.

Daarnaast moeten toepassingen het afstemmings eindpunt van de beheerder gebruiken om toepassings machtigingen aan te vragen.

## <a name="admin-restricted-permissions"></a>Beheerders machtigingen

Sommige machtigingen met hoge bevoegdheden in het micro soft-ecosysteem kunnen worden ingesteld op *beheerder*. Voor beelden van dit soort machtigingen zijn onder andere:

* Volledige profielen van alle gebruikers lezen met behulp van `User.Read.All`
* Gegevens schrijven naar de directory van een organisatie met behulp van `Directory.ReadWrite.All`
* Alle groepen in de directory van een organisatie lezen met behulp van `Groups.Read.All`

Hoewel een consumenten gebruiker een toepassing toegang kan verlenen tot dit soort gegevens, zijn de gebruikers van de organisatie beperkt tot het verlenen van toegang tot dezelfde set gevoelige Bedrijfs gegevens. Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatie gebruiker, ontvangt de gebruiker een fout bericht waarin staat dat ze niet zijn geautoriseerd om toestemming te geven voor de machtigingen van uw app.

Als uw app toegang vereist tot door de beheerder beperkte bereiken voor organisaties, moet u deze rechtstreeks aanvragen bij een bedrijfs beheerder, ook via het uitnodigings eindpunt van de beheerder, dat hierna wordt beschreven.

Als de toepassing machtigingen met hoge bevoegdheden heeft aangevraagd en een beheerder deze machtigingen verleent via het eind punt van de beheerder, wordt toestemming verleend voor alle gebruikers in de Tenant.

Als de toepassing toepassings machtigingen aanvraagt en een beheerder deze machtigingen verleent via het door de beheerder toestemmings eindpunt, wordt deze toekenning niet uitgevoerd namens een specifieke gebruiker. In plaats daarvan worden machtigingen *rechtstreeks* verleend aan de client toepassing. Deze typen machtigingen worden alleen gebruikt door daemon-services en andere niet-interactieve toepassingen die op de achtergrond worden uitgevoerd.

## <a name="using-the-admin-consent-endpoint"></a>Het afstemmings eindpunt van de beheerder gebruiken

Nadat u toestemming van de beheerder hebt verleend met behulp van het uitnodigings eindpunt van de beheerder, hebt u de toestemming van de beheerder voltooid. gebruikers hoeven geen verdere aanvullende acties uit te voeren. Nadat de beheerder toestemming heeft gegeven, kunnen gebruikers een toegangs token verkrijgen via een typische verificatie stroom en krijgt het resulterende toegangs token de machtigings machtigingen.

Wanneer een bedrijfs beheerder uw toepassing gebruikt en wordt omgeleid naar het toestemming eind punt, detecteert micro soft Identity-platform de rol van de gebruiker en vragen ze om toestemming te geven namens de volledige Tenant voor de machtigingen die u hebt aangevraagd. Er is echter ook een speciaal beheerders toestemmings eindpunt dat u kunt gebruiken als u proactief wilt aanvragen dat een beheerder toestemming geeft namens de hele Tenant. Het gebruik van dit eind punt is ook nodig voor het aanvragen van toepassings machtigingen (die niet kunnen worden aangevraagd met het toestemming eind punt).

Als u deze stappen volgt, kan uw app machtigingen aanvragen voor alle gebruikers in een Tenant, met inbegrip van beheerders beperkte bereiken. Dit is een bewerking met hoge bevoegdheden en mag alleen worden uitgevoerd als dat nodig is voor uw scenario.

Zie het voor beeld van een [beheerders beperking](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)voor meer informatie over het code voorbeeld dat de stappen implementeert.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>De machtigingen aanvragen in de portal voor app-registratie

Toepassingen kunnen weten welke machtigingen ze nodig hebben (zowel gedelegeerde als toepassing) in de app-registratie Portal.  Dit maakt het gebruik van het `/.default` bereik en Azure Portal de optie ' toestemming van beheerder verlenen ' toe.  Over het algemeen is het best practice om ervoor te zorgen dat de machtigingen die statisch voor een bepaalde toepassing zijn gedefinieerd, een superset zijn van de machtigingen die ze dynamisch/incrementeel aanvragen.

> [!NOTE]
>Toepassings machtigingen kunnen alleen worden aangevraagd via het gebruik van [`/.default`](#the-default-scope) , dus als uw app toepassings machtigingen nodig heeft, controleert u of deze worden vermeld in de app registratie-Portal.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>De lijst met statisch aangevraagde machtigingen voor een toepassing configureren

1. Ga naar uw toepassing in de <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal-app-registraties <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Snelstartgids.
1. Selecteer een toepassing of [Maak een app](quickstart-register-app.md) als u dat nog niet hebt gedaan.
1. Selecteer op de pagina **overzicht** van de toepassing onder **beheren** de optie **API-machtigingen**  >  **een machtiging toevoegen**.
1. Selecteer **Microsoft Graph** in de lijst met beschik bare api's en voeg vervolgens de machtigingen toe die uw app nodig heeft.
1. Selecteer **machtigingen toevoegen**.

### <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: Onderteken de gebruiker in uw app

Wanneer u een toepassing bouwt die gebruikmaakt van het door de beheerder toestemmings eindpunt, moet de app doorgaans een pagina of weer gave hebben waarin de beheerder de machtigingen van de app kan goed keuren. Deze pagina kan deel uitmaken van de registratie stroom van de app, een deel van de instellingen van de app of een specifieke stroom ' Connect ' zijn. In veel gevallen is het zinvol voor de app om deze weer gave ' verbinding maken ' alleen weer te geven nadat een gebruiker zich heeft aangemeld met een werk-of school Microsoft-account.

Wanneer u de gebruiker aan uw app ondertekent, kunt u de organisatie waarvan de beheerder deel uitmaakt, identificeren voordat u deze vraagt om de benodigde machtigingen goed te keuren. Hoewel het niet strikt nood zakelijk is, kan het u helpen een meer intuïtieve ervaring te creëren voor de gebruikers van uw organisatie. Als u de gebruiker wilt ondertekenen in, volgt u onze [zelf studies voor het micro soft Identity platform-protocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen van een adreslijst beheerder aanvragen

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, kunt u de gebruiker omleiden naar het micro soft Identity platform *Administrator toestemmings eindpunt*.

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
| `tenant` | Vereist | De Directory-Tenant waarvan u toestemming wilt aanvragen. Kan worden weer gegeven in de indeling GUID of beschrijvende naam of in het algemeen waarnaar wordt verwezen met organisaties zoals in het voor beeld. Gebruik ' common ' niet, omdat persoonlijke accounts geen toestemming van de beheerder kunnen bieden, behalve in de context van een Tenant. Gebruik, indien mogelijk, de Tenant-ID om te zorgen voor optimale compatibiliteit met persoonlijke accounts die tenants beheren. |
| `client_id` | Vereist | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `redirect_uri` | Vereist |De omleidings-URI waar u het antwoord voor uw app wilt laten afhandelen. Het moet exact overeenkomen met een van de omleidings-Uri's die u hebt geregistreerd in de app-registratie Portal. |
| `state` | Aanbevolen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van elke gewenste inhoud. Gebruik de status om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin deze zijn aangemeld. |
|`scope`        | Vereist        | Hiermee wordt de set machtigingen gedefinieerd die worden aangevraagd door de toepassing. Dit kan statisch zijn (met [`/.default`](#the-default-scope) ) of dynamische bereiken.  Dit kan de OIDC-bereiken ( `openid` , `profile` ,) bevatten `email` . Als u toepassings machtigingen nodig hebt, moet u gebruiken `/.default` om de statisch geconfigureerde lijst met machtigingen aan te vragen.  |


Op dit moment heeft Azure AD een Tenant beheerder nodig om zich aan te melden om de aanvraag te volt ooien. De beheerder wordt gevraagd om alle machtigingen die u in de para meter hebt aangevraagd goed te keuren `scope` .  Als u een statische ()- `/.default` waarde hebt gebruikt, werkt deze als het eind punt v 1.0-beheerder toestemming en vraagt u toestemming aan voor alle scopes die zijn gevonden in de vereiste machtigingen voor de app.

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

Als de beheerder de machtigingen voor uw app niet goedkeurt, ziet de mislukte reactie er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een fout. |

Nadat u een geslaagde reactie van het afstemmings eindpunt van de beheerder hebt ontvangen, heeft uw app de aangevraagde machtigingen verkregen. Vervolgens kunt u een token aanvragen voor de gewenste resource.

## <a name="using-permissions"></a>Machtigingen gebruiken

Nadat de gebruiker heeft gestemd op machtigingen voor uw app, kan uw app toegangs tokens verkrijgen die de machtiging van uw app voor toegang tot een resource in sommige capaciteit vertegenwoordigen. Een toegangs token kan alleen voor één resource worden gebruikt, maar de code ring in het toegangs token is elke machtiging die uw app voor die bron heeft gekregen. Voor het verkrijgen van een toegangs token kan uw app een aanvraag indienen bij het micro soft Identity platform-token-eind punt, als volgt:

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

U kunt het `/.default` bereik gebruiken om uw apps van het v 1.0-eind punt te migreren naar het micro soft Identity platform-eind punt. Dit is een ingebouwd bereik voor elke toepassing die verwijst naar de statische lijst met machtigingen die zijn geconfigureerd voor de registratie van de toepassing. Een `scope` waarde van `https://graph.microsoft.com/.default` is functioneel hetzelfde als de eind punten van de v 1.0 `resource=https://graph.microsoft.com` . Hierbij wordt een token aangevraagd met de scopes op Microsoft Graph dat de toepassing is geregistreerd voor in de Azure Portal.  Het is gemaakt met behulp van de resource-URI + `/.default` (bijvoorbeeld als de resource-URI is `https://contosoApp.com` , dan is het aangevraagde bereik `https://contosoApp.com/.default` ).  Zie de [sectie over afsluitende slashes](#trailing-slash-and-default) voor gevallen waarin u een tweede slash moet toevoegen om het token correct aan te vragen.

Het/.default-bereik kan worden gebruikt in een OAuth 2,0-stroom, maar is wel nodig in de stroom [voor namens-](v2-oauth2-on-behalf-of-flow.md) en [client referenties](v2-oauth2-client-creds-grant-flow.md)en bij gebruik van het v2-eind punt voor de beheerder om toepassings machtigingen aan te vragen.

Clients kunnen statisch ( `/.default` ) en dynamische toestemming niet combi neren in één aanvraag. Daarom `scope=https://graph.microsoft.com/.default+mail.read` resulteert dit in een fout als gevolg van de combi natie van bereik typen.

### <a name="default-and-consent"></a>/.default en toestemming

Het `/.default` bereik activeert ook het eindpunt gedrag van v 1.0 `prompt=consent` . Er wordt toestemming gevraagd voor alle machtigingen die zijn geregistreerd door de toepassing, ongeacht de bron. Als onderdeel van de aanvraag, `/.default` retourneert de scope een token dat de scopes voor de aangevraagde bron bevat.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default wanneer de gebruiker al toestemming heeft gegeven

Omdat `/.default` is functioneel identiek aan het gedrag van de `resource` gecentreerde v 1.0-eind punten, wordt het gedrag van de toestemming van het eind punt van de v 1.0 ook meegebracht. Dit leidt er alleen toe dat er `/.default` een toestemming wordt gevraagd als er geen machtiging is verleend tussen de client en de resource door de gebruiker. Als een dergelijke toestemming bestaat, wordt er een token geretourneerd dat alle scopes bevat die de gebruiker voor die bron heeft verleend. Als er echter geen machtiging is verleend of als de `prompt=consent` para meter is ingesteld, wordt er een toestemming prompt weer gegeven voor alle scopes die zijn geregistreerd door de client toepassing.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Voor beeld 1: de gebruiker of Tenant beheerder heeft machtigingen verleend

In dit voor beeld heeft de gebruiker (of een Tenant beheerder) de client de Microsoft Graph machtigingen `mail.read` en `user.read` . Als de client een aanvraag indient `scope=https://graph.microsoft.com/.default` , wordt er geen vraag naar toestemming weer gegeven, ongeacht de inhoud van de geregistreerde machtigingen van de client toepassingen voor Microsoft Graph. Er wordt een token geretourneerd dat de scopes `mail.read` en bevat `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Voor beeld 2: de gebruiker heeft geen machtigingen verleend tussen de client en de resource

In dit voor beeld bestaat er geen toestemming voor de gebruiker tussen de client en Microsoft Graph. De client is geregistreerd voor de `user.read` `contacts.read` machtigingen en, evenals het Azure Key Vault bereik `https://vault.azure.net/user_impersonation` . Wanneer de client een token aanvraagt voor `scope=https://graph.microsoft.com/.default` , ziet de gebruiker een venster voor toestemming voor de `user.read` , `contacts.read` en de Key Vault `user_impersonation` scopes. Het geretourneerde token heeft alleen de- `user.read` en- `contacts.read` scopes en kan alleen worden gebruikt tegen Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Voor beeld 3: de gebruiker heeft toestemming gegeven en er wordt extra scopes aangevraagd door de client

In dit voor beeld heeft de gebruiker al toestemming gegeven `mail.read` voor de client. De client is geregistreerd voor de `contacts.read` Scope tijdens de registratie. Wanneer de client een aanvraag doet voor een token met behulp van `scope=https://graph.microsoft.com/.default` en toestemming vraagt `prompt=consent` , ziet de gebruiker een venster voor toestemming voor alle (en alleen) de machtigingen die zijn geregistreerd door de toepassing. `contacts.read` is aanwezig in het venster voor toestemming, maar `mail.read` niet. Het geretourneerde token is voor Microsoft Graph en bevat `mail.read` en `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Het/.default-bereik gebruiken met de-client

Een speciaal geval van het `/.default` bereik bestaat, waarbij een client zijn eigen bereik aanvraagt `/.default` . In het volgende voor beeld ziet u dit scenario.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Dit produceert een venster voor toestemming voor alle geregistreerde machtigingen (indien van toepassing op basis van de bovenstaande beschrijvingen van toestemming en `/.default` ) en retourneert vervolgens een id_token in plaats van een toegangs token.  Dit gedrag bestaat voor bepaalde verouderde clients die van ADAL naar MSAL worden verplaatst en **mogen niet** worden gebruikt door nieuwe clients die gericht zijn op het micro soft Identity platform-eind punt.

### <a name="client-credentials-grant-flow-and-default"></a>Client referenties geven stroom en/.default

Een ander gebruik van `/.default` is wanneer u toepassings machtigingen (of *rollen*) aanvraagt in een niet-interactieve toepassing, zoals een daemon-app die gebruikmaakt van de [client referenties](v2-oauth2-client-creds-grant-flow.md) toekennings stroom om een web-API aan te roepen.

Zie [How to: app-rollen toevoegen in uw toepassing](howto-add-app-roles-in-azure-ad-apps.md)om toepassings machtigingen (rollen) voor een web-API te maken.

Aanvragen voor client referenties in uw client-app **moeten** bevatten `scope={resource}/.default` , waarbij `{resource}` de Web-API is die uw app wil aanroepen. Het uitgeven van een aanvraag voor client referenties met afzonderlijke toepassings machtigingen (rollen) wordt **niet** ondersteund. Alle toepassings machtigingen (rollen) die zijn verleend voor die Web-API, worden opgenomen in het geretourneerde toegangs token.

Als u toegang wilt verlenen tot de toepassings machtigingen die u definieert, met inbegrip van het verlenen van beheerders toestemming voor de toepassing, raadpleegt u [Quick Start: een client toepassing configureren voor toegang tot een web-API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Afsluitende slash en/.default

Sommige bron-Uri's hebben een afsluitende slash ( `https://contoso.com/` in plaats `https://contoso.com` van), waardoor er problemen kunnen ontstaan met de validatie van tokens.  Dit kan voornamelijk optreden bij het aanvragen van een token voor Azure Resource Management ( `https://management.azure.com/` ), dat een afsluitende slash op de resource-URI heeft en vereist dat deze aanwezig is wanneer het token wordt aangevraagd.  Daarom moet u, wanneer u een token aanvraagt voor `https://management.azure.com/` en gebruikt `/.default` , een aanvraag indienen om `https://management.azure.com//.default` de dubbele slash te noteren.

In het algemeen: als u hebt gevalideerd dat het token wordt uitgegeven en het token wordt geweigerd door de API die deze moet accepteren, moet u een tweede slash toevoegen en het opnieuw proberen. Dit doet zich voor omdat de aanmeldings server een token verzendt met de doel groep die overeenkomt met de Uri's in de `scope` para meter, waarbij het `/.default` einde wordt verwijderd.  Als u de afsluitende slash verwijdert, wordt de aanmeldings server nog steeds verwerkt en wordt deze gevalideerd op basis van de resource-URI, zelfs als deze niet meer overeenkomen. Dit is niet-standaard en mag niet worden vertrouwd door uw toepassing.

## <a name="troubleshooting-permissions-and-consent"></a>Problemen met machtigingen en toestemming

Als u of de gebruikers van uw toepassing onverwachte fouten zien tijdens het toestemming proces, raadpleegt u dit artikel voor het oplossen van problemen: [onverwachte fout bij het uitvoeren van de toestemming voor een toepassing](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Volgende stappen

* [ID-tokens | Micro soft Identity-platform](id-tokens.md)
* [Toegangs tokens | Micro soft Identity-platform](access-tokens.md)

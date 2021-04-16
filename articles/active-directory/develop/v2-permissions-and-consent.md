---
title: Microsoft Identity Platform-scopes, machtigingen, & toestemming
description: Meer informatie over autorisatie in het Microsoft Identity Platform-eindpunt, waaronder scopes, machtigingen en toestemming.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515580"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Machtigingen en toestemming in het Microsoft Identity Platform

Toepassingen die zijn geïntegreerd met het Microsoft Identity Platform, volgen een autorisatiemodel waarmee gebruikers en beheerders kunnen bepalen hoe gegevens kunnen worden gebruikt. De implementatie van het autorisatiemodel is bijgewerkt op het Microsoft Identity Platform. Het verandert de manier waarop een app moet communiceren met het Microsoft Identity Platform. In dit artikel worden de basisconcepten van dit autorisatiemodel beschreven, waaronder scopes, machtigingen en toestemming.

## <a name="scopes-and-permissions"></a>Scopes en machtigingen

Het Microsoft Identity Platform implementeert het [OAuth 2.0-autorisatieprotocol.](active-directory-v2-protocols.md) OAuth 2.0 is een methode waarmee een app van derden namens een gebruiker toegang kan krijgen tot webbronnen. Elke webresource die kan worden geïntegreerd met het Microsoft Identity Platform heeft een resource-id of *toepassings-id-URI.* 

Hier zijn enkele voorbeelden van microsoft-resources die op het web worden gehost:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 Mail-API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Hetzelfde geldt voor resources van derden die zijn geïntegreerd met het Microsoft Identity Platform. Elk van deze resources kan ook een set machtigingen definiëren die kunnen worden gebruikt om de functionaliteit van die resource in kleinere segmenten te verdelen. Een voorbeeld: [Microsoft Graph](https://graph.microsoft.com) machtigingen voor het uitvoeren van de volgende taken, onder andere:

* De agenda van een gebruiker lezen
* Schrijven naar de agenda van een gebruiker
* E-mail verzenden als gebruiker

Vanwege deze typen machtigingsdefinities heeft de resource een fijnfheidscontrole over de gegevens en hoe API-functionaliteit beschikbaar wordt gemaakt. Een app van derden kan deze machtigingen aanvragen bij gebruikers en beheerders, die de aanvraag moeten goedkeuren voordat de app toegang kan krijgen tot gegevens of namens een gebruiker kan handelen. 

Wanneer de functionaliteit van een resource is geseed in kleine machtigingensets, kunnen apps van derden worden gebouwd om alleen de machtigingen aan te vragen die ze nodig hebben om hun functie uit te voeren. Gebruikers en beheerders kunnen weten tot welke gegevens de app toegang heeft. En ze kunnen er zeker van zijn dat de app zich niet gedraagt met kwaadaardige bedoelingen. Ontwikkelaars moeten zich altijd houden aan het principe van de minste bevoegdheden, door alleen te vragen om de machtigingen die ze nodig hebben om hun toepassingen te laten functioneren.

In OAuth 2.0 worden deze typen machtigingensets *scopes genoemd.* Ze worden ook vaak aangeduid als *machtigingen*. In het Microsoft Identity Platform wordt een machtiging weergegeven als een tekenreekswaarde. Een app vraagt de machtigingen aan die nodig zijn door de machtiging op te geven in de `scope` queryparameter. Identiteitsplatform ondersteunt verschillende goed gedefinieerde [OpenID Connect-scopes](#openid-connect-scopes) en op resources gebaseerde machtigingen (elke machtiging wordt aangegeven door de machtigingswaarde toe te OpenID Connect aan de id of URI van de toepassings-id van de resource). De machtigingsreeks wordt bijvoorbeeld gebruikt om machtigingen aan te vragen voor het lezen van `https://graph.microsoft.com/Calendars.Read` agenda's van gebruikers in Microsoft Graph.

Een app vraagt deze machtigingen meestal aan door de scopes op te geven in aanvragen voor het Microsoft Identity Platform-autoriteits-eindpunt. Sommige machtigingen met hoge bevoegdheden kunnen echter alleen worden verleend via toestemming van de beheerder. Ze kunnen worden aangevraagd of verleend met behulp van het [eindpunt voor beheerders toestemming.](#admin-restricted-permissions) Blijf lezen voor meer informatie.

## <a name="permission-types"></a>Machtigingstypen

Het Microsoft Identity Platform ondersteunt twee typen machtigingen: *gedelegeerde machtigingen* en *toepassingsmachtigingen.*

* **Gedelegeerde machtigingen** worden gebruikt door apps met een aangemelde gebruiker. Voor deze apps geeft de gebruiker of beheerder toestemming voor de machtigingen die de app aanvraagt. De app krijgt de machtiging om te fungeren als de aangemelde gebruiker wanneer deze de doelresource aanroept. 

    Sommige gedelegeerde machtigingen kunnen worden verleend door niet-beheerder. Maar voor sommige machtigingen met hoge bevoegdheden is toestemming [van de beheerder vereist.](#admin-restricted-permissions) Zie Machtigingen voor beheerdersrollen in Azure Active Directory (Azure AD) voor meer informatie over welke beheerdersrollen toestemming kunnen geven [voor gedelegeerde machtigingen.](../roles/permissions-reference.md)

* **Toepassingsmachtigingen** worden gebruikt door apps die worden uitgevoerd zonder een aangemelde gebruiker die aanwezig is, bijvoorbeeld apps die worden uitgevoerd als achtergrondservices of daemons. Alleen [een beheerder kan toestemming geven voor toepassingsmachtigingen.](#requesting-consent-for-an-entire-tenant)

_Effectieve machtigingen_ zijn de machtigingen die uw app heeft wanneer er aanvragen worden ingediend bij de doelresource. Het is belangrijk om het verschil te begrijpen tussen de gedelegeerde machtigingen en toepassingsmachtigingen die aan uw app worden verleend en de effectieve machtigingen die uw app krijgt wanneer deze de doelresource aanroept.

- Voor gedelegeerde machtigingen  zijn de effectieve machtigingen van uw app het minst bevoegde snijpunt van de gedelegeerde machtigingen die aan de app zijn verleend (met toestemming) en de bevoegdheden van de momenteel aangemelde gebruiker. Uw app kan nooit meer machtigingen hebben dan de aangemelde gebruiker. 

   Binnen organisaties kunnen de bevoegdheden van de aangemelde gebruiker worden bepaald door beleid of door lidmaatschap van een of meer beheerdersrollen. Zie Machtigingen voor beheerdersrollen in Azure AD voor meer informatie over welke beheerdersrollen toestemming kunnen geven voor [gedelegeerde machtigingen.](../roles/permissions-reference.md)

   Stel bijvoorbeeld dat aan uw app de gedelegeerde machtiging _User.ReadWrite.All_ is verleend. Deze machtiging verleent uw app in feite machtigingen om het profiel van elke gebruiker in een organisatie te lezen en bij te werken. Als de aangemelde gebruiker een globale beheerder is, kan uw app het profiel van elke gebruiker in de organisatie bijwerken. Als de aangemelde gebruiker echter geen beheerdersrol heeft, kan uw app alleen het profiel van de aangemelde gebruiker bijwerken. De profielen van andere gebruikers in de organisatie kunnen niet worden bijgewerkt omdat de gebruiker die namens de gebruiker is machtigingen heeft om te handelen, deze bevoegdheden niet heeft.

- Voor toepassingsmachtigingen zijn _de effectieve machtigingen_ van uw app het volledige niveau van bevoegdheden dat door de machtiging wordt geïmpliceerd. Een app met de _toepassingsmachtiging User.ReadWrite.All_ kan bijvoorbeeld het profiel van elke gebruiker in de organisatie bijwerken.

## <a name="openid-connect-scopes"></a>OpenID Connect bereik

De Microsoft Identity Platform-implementatie van OpenID Connect heeft enkele goed gedefinieerde scopes die ook worden gehost op Microsoft Graph: `openid` , `email` , en `profile` `offline_access` . De `address` OpenID Connect en worden niet `phone` ondersteund.

Als u de OpenID Connect en een token aanvraagt, krijgt u een token om het [UserInfo-eindpunt aan te roepen.](userinfo.md)

### <a name="openid"></a>Openid

Als een app zich met behulp [van](active-directory-v2-protocols.md)OpenID Connect, moet het bereik worden `openid` opgevraagd. Het `openid` bereik wordt op de toestemmingspagina van het werkaccount weergegeven als de machtiging **Aanmelden.** Op de pagina Microsoft-account persoonlijke toestemming wordt het weergegeven als Uw profiel weergeven en verbinding maken met apps en services met behulp van uw **Microsoft-account** machtiging. 

Met deze machtiging kan een app een unieke id voor de gebruiker ontvangen in de vorm van de `sub` claim. De machtiging geeft de app ook toegang tot het UserInfo-eindpunt. Het bereik kan worden gebruikt op het eindpunt van het `openid` Microsoft Identity Platform-token om id-tokens te verkrijgen. De app kan deze tokens gebruiken voor verificatie.

### <a name="email"></a>e-mail

Het `email` bereik kan worden gebruikt met het bereik en andere `openid` scopes. Het geeft de app toegang tot het primaire e-mailadres van de gebruiker in de vorm van de `email` claim. 

De claim is alleen opgenomen in een token als een e-mailadres is gekoppeld aan het `email` gebruikersaccount, wat niet altijd het geval is. Als uw app gebruikmaakt van het bereik, moet de app een case kunnen afhandelen waarin er geen claim in het `email` `email` token bestaat.

### <a name="profile"></a>profiel

Het `profile` bereik kan worden gebruikt met het bereik en een ander `openid` bereik. Het geeft de app toegang tot een grote hoeveelheid informatie over de gebruiker. De informatie die kan worden gebruikt, omvat, maar is niet beperkt tot, de voornaam, achternaam, voorkeursgebruikersnaam en object-id van de gebruiker. 

Zie de naslag voor een volledige lijst van de claims die beschikbaar zijn in de parameter voor `profile` `id_tokens` een specifieke [ `id_tokens` gebruiker.](id-tokens.md)

### <a name="offline_access"></a>offline_access

Het [ `offline_access` bereik](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) geeft uw app langere tijd namens de gebruiker toegang tot resources. Op de toestemmingspagina wordt dit bereik weergegeven als Toegang behouden tot gegevens **die u toegang hebt verleend tot de** machtiging. 

Wanneer een gebruiker het bereik goedkeurt, kan uw app vernieuwingstokens ontvangen van het eindpunt van het `offline_access` Microsoft Identity Platform-token. Vernieuwingstokens hebben een lange duur. Uw app kan nieuwe toegangstokens krijgen als oudere verlopen.

> [!NOTE]
> Deze machtiging wordt momenteel weergegeven op alle toestemmingspagina's, zelfs voor stromen die geen vernieuwings token bieden (zoals de [impliciete stroom](v2-oauth2-implicit-grant-flow.md)). Deze installatie heeft betrekking op scenario's waarbij een client binnen de impliciete stroom kan beginnen en vervolgens naar de codestroom kan gaan waar een vernieuwings token wordt verwacht.

Op het Microsoft Identity Platform (aanvragen voor het v2.0-eindpunt) moet uw app expliciet het bereik aanvragen om vernieuwingstokens `offline_access` te ontvangen. Wanneer u dus een autorisatiecode inwisselt in de [OAuth 2.0-autorisatiecodestroom,](active-directory-v2-protocols.md)ontvangt u alleen een toegangsteken van `/token` het eindpunt. 

Het toegangsteken is korte tijd geldig. Deze verloopt meestal binnen één uur. Op dat moment moet uw app de gebruiker terugleiden naar het eindpunt om `/authorize` een nieuwe autorisatiecode op te halen. Afhankelijk van het type app moet de gebruiker tijdens deze omleiding mogelijk opnieuw referenties invoeren of opnieuw toestemming geven voor machtigingen.

Zie de naslaginformatie over het Microsoft [Identity Platform-protocol](active-directory-v2-protocols.md)voor meer informatie over het verkrijgen en gebruiken van vernieuwingstokens.

## <a name="requesting-individual-user-consent"></a>Persoonlijke toestemming van de gebruiker aanvragen

In een OpenID Connect of [OAuth 2.0-autorisatieaanvraag](active-directory-v2-protocols.md) kan een app de machtigingen aanvragen die nodig zijn met behulp van de `scope` queryparameter. Wanneer een gebruiker zich bijvoorbeeld bij een app meldt, verzendt de app een aanvraag zoals in het volgende voorbeeld. (Regelbreaks worden toegevoegd voor de leesbaarheid.)

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

De `scope` parameter is een door spatie gescheiden lijst met gedelegeerde machtigingen die de app aanvraagt. Elke machtiging wordt aangegeven door de machtigingswaarde toe te kennen aan de resource-id (de URI van de toepassings-id). In het aanvraagvoorbeeld moet de app toestemming hebben om de agenda van de gebruiker te lezen en e-mail te verzenden als de gebruiker.

Nadat de gebruiker zijn of haar referenties heeft invoert, controleert het Microsoft Identity Platform op een overeenkomende record van *toestemming van de gebruiker.* Als de gebruiker in het verleden geen toestemming heeft gegeven voor een van de aangevraagde machtigingen en als de beheerder niet namens de hele organisatie toestemming heeft gegeven voor deze machtigingen, vraagt het Microsoft Identity Platform de gebruiker om de aangevraagde machtigingen te verlenen.

Op dit moment worden de machtigingen ('Toegang behouden tot gegevens waar u toegang toe hebt verleend') en 'U aanmelden en uw profiel lezen', automatisch opgenomen in de eerste toestemming voor een `offline_access` `user.read` toepassing.  Deze machtigingen zijn doorgaans vereist voor de juiste app-functionaliteit. De `offline_access` machtiging geeft de app toegang tot vernieuwingstokens die essentieel zijn voor native apps en web-apps. De `user.read` machtiging geeft toegang tot de `sub` claim. Hierdoor kan de client of app de gebruiker na een periode correct identificeren en toegang krijgen tot elementaire gebruikersgegevens.

![Voorbeeld van een schermopname van toestemming voor een werkaccount.](./media/v2-permissions-and-consent/work_account_consent.png)

Wanneer de gebruiker de machtigingsaanvraag goedkeurt, wordt toestemming vastgelegd. De gebruiker hoeft geen toestemming meer te geven wanneer deze zich later bij de toepassing aanmelden.

## <a name="requesting-consent-for-an-entire-tenant"></a>Toestemming aanvragen voor een volledige tenant

Wanneer een organisatie een licentie of abonnement voor een toepassing koopt, wil de organisatie de toepassing vaak proactief instellen voor gebruik door alle leden van de organisatie. Als onderdeel van dit proces kan een beheerder toestemming geven voor de toepassing om te handelen namens een gebruiker in de tenant. Als de beheerder toestemming verleent voor de hele tenant, zien de gebruikers van de organisatie geen toestemmingspagina voor de toepassing.

Als u toestemming wilt vragen voor gedelegeerde machtigingen voor alle gebruikers in een tenant, kan uw app gebruikmaken van het eindpunt voor beheerdersmachtigingen.

Daarnaast moeten toepassingen gebruikmaken van het eindpunt voor beheerders toestemming om toepassingsmachtigingen aan te vragen.

## <a name="admin-restricted-permissions"></a>Beperkte beheerdersmachtigingen

Sommige machtigingen met hoge bevoegdheden in Microsoft-resources kunnen worden ingesteld op *beperkte beheerdersrechten.* Hier zijn enkele voorbeelden van dit soort machtigingen:

* Alle volledige profielen van de gebruiker lezen met behulp van `User.Read.All`
* Gegevens naar de directory van een organisatie schrijven met behulp van `Directory.ReadWrite.All`
* Alle groepen in de directory van een organisatie lezen met behulp van `Groups.Read.All`

Hoewel een consument een toepassing toegang kan verlenen tot dit soort gegevens, kunnen organisatiegebruikers geen toegang verlenen tot dezelfde set gevoelige bedrijfsgegevens. Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatiegebruiker, ontvangt de gebruiker een foutbericht met de melding dat deze niet gemachtigd is om toestemming te geven voor de machtigingen van uw app.

Als uw app scopes vereist voor beheerdersmachtigingen, moet de beheerder van een organisatie toestemming geven voor deze scopes namens de gebruikers van de organisatie. Om te voorkomen dat gebruikers om toestemming vragen voor machtigingen die ze niet kunnen verlenen, kan uw app gebruikmaken van het eindpunt voor beheerders toestemming. Het eindpunt voor beheerdersmachtiging wordt in de volgende sectie behandeld.

Als de toepassing gedelegeerde machtigingen met hoge bevoegdheden aanvraagt en een beheerder deze machtigingen verleent via het eindpunt voor beheerdersmachtigingen, wordt toestemming verleend aan alle gebruikers in de tenant.

Als de toepassing toepassingsmachtigingen aanvraagt en een beheerder deze machtigingen verleent via het toestemmings-eindpunt van de beheerder, wordt deze toekenning niet namens een specifieke gebruiker uitgevoerd. In plaats daarvan krijgt de clienttoepassing rechtstreeks *machtigingen.* Deze typen machtigingen worden alleen gebruikt door daemonservices en andere niet-interactieve toepassingen die op de achtergrond worden uitgevoerd.

## <a name="using-the-admin-consent-endpoint"></a>Het eindpunt voor beheerdersmachtiging gebruiken

Nadat u het eindpunt voor beheerders toestemming hebt gebruikt om beheerders toestemming te verlenen, bent u klaar. Gebruikers hoeven geen verdere actie te ondernemen. Nadat toestemming van de beheerder is verleend, kunnen gebruikers een toegangsteken verkrijgen via een typische auth-stroom. Het resulterende toegangs token heeft de machtigingen die zijn verleend.

Wanneer een globale beheerder uw toepassing gebruikt en wordt omgeleid naar het geautoriseerde eindpunt, detecteert het Microsoft-identiteitsplatform de rol van de gebruiker. Er wordt gevraagd of de globale beheerder namens de hele tenant toestemming wil geven voor de machtigingen die u hebt aangevraagd. U kunt in plaats daarvan een toegewezen eindpunt voor beheerders toestemming gebruiken om proactief een beheerder aan te vragen om namens de hele tenant toestemming te verlenen. Dit eindpunt is ook nodig voor het aanvragen van toepassingsmachtigingen. Toepassingsmachtigingen kunnen niet worden aangevraagd met behulp van het geautoriseerde eindpunt.

Als u deze stappen volgt, kan uw app machtigingen aanvragen voor alle gebruikers in een tenant, inclusief beperkte beheerbereiken. Deze bewerking heeft een hoge bevoegdheid. Gebruik de bewerking alleen als dat nodig is voor uw scenario.

Zie het voorbeeld met beperkte beheerbereiken [in](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) GitHub voor een codevoorbeeld waarin de stappen worden geïmplementeerd.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>De machtigingen aanvragen in de portal voor app-registratie

In de portal voor app-registratie kunnen toepassingen een lijst maken met de machtigingen die ze nodig hebben, waaronder zowel gedelegeerde machtigingen als toepassingsmachtigingen. Met deze installatie kunt u het bereik en de `/.default` Azure Portal van de optie Beheerders toestemming **verlenen.**  

Over het algemeen moeten de machtigingen statisch worden gedefinieerd voor een bepaalde toepassing. Ze moeten een superset zijn van de machtigingen die de app dynamisch of incrementeel aanvraagt.

> [!NOTE]
>Toepassingsmachtigingen kunnen alleen worden aangevraagd met het gebruik van [`/.default`](#the-default-scope) . Dus als uw app toepassingsmachtigingen nodig heeft, moet u ervoor zorgen dat deze worden vermeld in de portal voor app-registratie.

De lijst met statisch aangevraagde machtigingen voor een toepassing configureren:

1. Ga naar uw toepassing in <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal - App-registraties</a> quickstart-ervaring.
1. Selecteer een toepassing of [maak een app](quickstart-register-app.md) als u dat nog niet hebt gedaan.
1. Selecteer op de pagina Overzicht **van de** toepassing onder **Beheren** de optie **API-machtigingen**  >  **Een machtiging toevoegen.**
1. Selecteer **Microsoft Graph** in de lijst met beschikbare API's. Voeg vervolgens de machtigingen toe die uw app nodig heeft.
1. Selecteer **Machtigingen toevoegen.**

### <a name="recommended-sign-the-user-in-to-your-app"></a>Aanbevolen: De gebruiker aanmelden bij uw app

Wanneer u een toepassing bouwt die gebruikmaakt van het eindpunt voor beheerders toestemming, heeft de app doorgaans een pagina of weergave nodig waarin de beheerder de machtigingen van de app kan goedkeuren. Deze pagina kan het volgende zijn:

* Onderdeel van de aanmeldingsstroom van de app.
* Onderdeel van de instellingen van de app.
* Een toegewezen verbindingsstroom. 

In veel gevallen is het zinvol dat de app deze weergave 'verbinding maken' alleen we weergeven nadat een gebruiker zich heeft aangemeld met een Microsoft-account of schoolaccount Microsoft-account.

Wanneer u de gebruiker bij uw app aan melden, kunt u de organisatie identificeren waarvan de beheerder deel uitmaken voordat u hen vraagt de benodigde machtigingen goed te keuren. Hoewel deze stap niet strikt noodzakelijk is, kan deze u helpen een meer intuïtieve ervaring te creëren voor de gebruikers van uw organisatie. 

Volg de zelfstudies voor [het Microsoft Identity Platform-protocol](active-directory-v2-protocols.md)om de gebruiker aan te melden.

### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen aanvragen bij een directorybeheerder

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, kunt u de gebruiker omleiden naar het Microsoft Identity Platform-eindpunt voor beheerdersmachtigingen.

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
| `tenant` | Vereist | De directory-tenant bij wie u toestemming wilt aanvragen. Deze kan worden opgegeven in een GUID- of gebruiksvriendelijke naamindeling. Of er kan algemeen naar worden verwezen met organisaties, zoals te zien is in het voorbeeld. Gebruik niet 'algemeen', omdat persoonlijke accounts geen beheerdersmachtiging kunnen geven, behalve in de context van een tenant. Gebruik de tenant-id indien mogelijk om de beste compatibiliteit te garanderen met persoonlijke accounts die tenants beheren. |
| `client_id` | Vereist | De toepassings-id (client-id) [die Azure Portal - App-registraties-ervaring](https://go.microsoft.com/fwlink/?linkid=2083908) die aan uw app is toegewezen. |
| `redirect_uri` | Vereist |De omleidings-URI waar het antwoord naar uw app moet worden verzonden. Deze moet exact overeenkomen met een van de omleidings-URI's die u hebt geregistreerd in de portal voor app-registratie. |
| `state` | Aanbevolen | Een waarde die is opgenomen in de aanvraag die ook wordt geretourneerd in de tokenreactie. Dit kan een tekenreeks zijn van elke inhoud die u wilt. Gebruik de status om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag heeft plaatsgevonden, zoals de pagina of weergave waarin deze zich hebben voorgedaan. |
|`scope`        | Vereist        | Hiermee definieert u de set machtigingen die wordt aangevraagd door de toepassing. Scopes kunnen statisch (met [`/.default`](#the-default-scope) ) of dynamisch zijn.  Deze set kan de OpenID Connect scopes ( `openid` , , , ) `profile` `email` bevatten. Als u toepassingsmachtigingen nodig hebt, moet u gebruiken om de statisch geconfigureerde lijst `/.default` met machtigingen aan te vragen.  |


Op dit moment heeft Azure AD een tenantbeheerder nodig om zich aan te melden om de aanvraag te voltooien. De beheerder wordt gevraagd om alle machtigingen goed te keuren die u hebt aangevraagd in de `scope` parameter .  Als u een statische waarde ( ) hebt gebruikt, werkt deze als het v1.0-eindpunt voor beheerders toestemming en vraagt u toestemming aan voor alle scopes die zijn gevonden in de vereiste machtigingen voor `/.default` de app.

#### <a name="successful-response"></a>Geslaagd antwoord

Als de beheerder de machtigingen voor uw app goedkeurt, ziet het antwoord er als volgende uit:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beschrijving |
| --- | --- |
| `tenant` | De directory-tenant die uw toepassing de machtigingen heeft verleend die zijn aangevraagd, in GUID-indeling. |
| `state` | Een waarde die is opgenomen in de aanvraag die ook wordt geretourneerd in het token-antwoord. Het kan een tekenreeks zijn van elke inhoud die u wilt. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatieaanvraag heeft plaatsgevonden, zoals de pagina of weergave waarin deze zich hebben voorgedaan. |
| `admin_consent` | Wordt ingesteld op `True` . |

#### <a name="error-response"></a>Foutbericht

Als de beheerder de machtigingen voor uw app niet goedkeurt, ziet het mislukte antwoord er als volgende uit:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodereeks die kan worden gebruikt voor het classificeren van typen fouten die optreden. Het kan ook worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek foutbericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofdoorzaak van een fout. |

Nadat u een geslaagd antwoord hebt ontvangen van het eindpunt voor beheerders toestemming, heeft uw app de machtigingen gekregen die zijn aangevraagd. Vervolgens kunt u een token aanvragen voor de resource die u wilt.

## <a name="using-permissions"></a>Machtigingen gebruiken

Nadat de gebruiker toestemming heeft gegeven voor machtigingen voor uw app, kan uw app toegangstokens verkrijgen die staan voor de machtiging van de app voor toegang tot een resource in een bepaalde capaciteit. Een toegangs token kan slechts voor één resource worden gebruikt. Maar gecodeerd in het toegangsken is elke machtiging die aan uw app is verleend voor die resource. Als u een toegangs token wilt verkrijgen, kan uw app een aanvraag indienen bij het eindpunt van het Microsoft Identity Platform-token, zoals:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

U kunt het resulterende toegangs token in HTTP-aanvragen voor de resource gebruiken. Het geeft aan de resource op betrouwbare wijze aan dat uw app de juiste machtiging heeft om een specifieke taak uit te voeren.

Zie voor meer informatie over het OAuth 2.0-protocol en het verkrijgen van toegangstokens de naslaginformatie over het Eindpuntprotocol van [het Microsoft-identiteitsplatform.](active-directory-v2-protocols.md)

## <a name="the-default-scope"></a>Het /.default-bereik

U kunt het bereik gebruiken om uw apps te migreren van het `/.default` v1.0-eindpunt naar het Microsoft Identity Platform-eindpunt. Het bereik is ingebouwd voor elke toepassing die verwijst naar de statische lijst met machtigingen die `/.default` zijn geconfigureerd voor de registratie van de toepassing. 

De `scope` waarde van is functioneel hetzelfde als op het `https://graph.microsoft.com/.default` `resource=https://graph.microsoft.com` v1.0-eindpunt. Door het bereik in de aanvraag op te geven, vraagt uw toepassing een toegangsportal aan dat bereiken bevat voor elke Microsoft Graph-machtiging die u hebt geselecteerd voor de app in de portal voor `https://graph.microsoft.com/.default` app-registratie. Het bereik wordt samengesteld met behulp van de resource-URI en `/.default` . Dus als de resource-URI is, is `https://contosoApp.com` het aangevraagde bereik `https://contosoApp.com/.default` .  Voor gevallen waarin u een tweede slash moet opnemen om het token correct aan te vragen, zie de [sectie over het volgen van slashes.](#trailing-slash-and-default)

Het `/.default` bereik kan worden gebruikt in elke OAuth 2.0-stroom. Dit is echter wel nodig in de [stroom On-Behalf-Of en](v2-oauth2-on-behalf-of-flow.md) [clientreferenties.](v2-oauth2-client-creds-grant-flow.md) U hebt deze ook nodig wanneer u het v2-eindpunt voor beheerders toestemming gebruikt om toepassingsmachtigingen aan te vragen.

Clients kunnen geen statische ( `/.default` ) toestemming en dynamische toestemming combineren in één aanvraag. Dit `scope=https://graph.microsoft.com/.default+mail.read` resulteert in een fout omdat bereiktypen worden gecombineerd.

### <a name="default-and-consent"></a>/.default en toestemming

Het bereik activeert ook het gedrag van `/.default` het v1.0-eindpunt `prompt=consent` voor . Er wordt toestemming gevraagd voor alle machtigingen die de toepassing heeft geregistreerd, ongeacht de resource. Als deze is opgenomen als onderdeel van de aanvraag, retourneert het bereik een token dat de scopes voor de `/.default` aangevraagde resource bevat.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default wanneer de gebruiker al toestemming heeft gegeven

Het `/.default` bereik is functioneel identiek aan het gedrag van het `resource` -centric v1.0-eindpunt. Deze voert ook het toestemmingsgedrag van het v1.0-eindpunt uit. Dat wil zeggen `/.default` dat er alleen een toestemmingsprompt wordt gegeven als de gebruiker geen toestemming heeft verleend tussen de client en de resource. 

Als een dergelijke toestemming bestaat, bevat het geretourneerde token alle scopes die de gebruiker voor die resource heeft verleend. Als er echter geen machtiging is verleend of als de parameter is opgegeven, wordt er een toestemmingsprompt weergegeven voor alle scopes die door de `prompt=consent` clienttoepassing zijn geregistreerd.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Voorbeeld 1: De gebruiker of tenantbeheerder heeft machtigingen verleend

In dit voorbeeld heeft de gebruiker of een tenantbeheerder de machtigingen `mail.read` en Microsoft Graph verleend aan de `user.read` client. 

Als de client om vraagt, wordt er geen toestemmingsprompt weergegeven, ongeacht de inhoud van de geregistreerde machtigingen van de `scope=https://graph.microsoft.com/.default` clienttoepassing voor Microsoft Graph. Het geretourneerde token bevat de scopes `mail.read` en `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Voorbeeld 2: De gebruiker heeft geen machtigingen verleend tussen de client en de resource

In dit voorbeeld heeft de gebruiker geen toestemming verleend tussen de client en Microsoft Graph. De client heeft zich geregistreerd voor de `user.read` machtigingen en `contacts.read` . Het is ook geregistreerd voor het Azure Key Vault bereik `https://vault.azure.net/user_impersonation` . 

Wanneer de client een token voor aanvraagt, ziet de gebruiker een toestemmingspagina voor het bereik, het bereik en `scope=https://graph.microsoft.com/.default` `user.read` Key Vault `contacts.read` `user_impersonation` bereik. Het geretourneerde token bevat alleen de `user.read` `contacts.read` scopes en . Deze kan alleen worden gebruikt voor Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Voorbeeld 3: De gebruiker heeft toestemming gegeven en de client vraagt meer scopes aan

In dit voorbeeld heeft de gebruiker al toestemming gegeven voor `mail.read` de client. De client heeft zich geregistreerd voor het `contacts.read` bereik. 

Wanneer de client een token aanvraagt met behulp van en toestemming aanvraagt via , ziet de gebruiker een toestemmingspagina voor alle (en alleen) de machtigingen die de `scope=https://graph.microsoft.com/.default` `prompt=consent` toepassing heeft geregistreerd. Het `contacts.read` bereik staat op de toestemmingspagina, maar is dat `mail.read` niet. Het geretourneerde token is voor Microsoft Graph. Deze bevat `mail.read` en `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Het /.default-bereik gebruiken met de client

In sommige gevallen kan een client een eigen bereik `/.default` aanvragen. In het volgende voorbeeld wordt dit scenario gedemonstreerd.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Dit codevoorbeeld produceert een toestemmingspagina voor alle geregistreerde machtigingen als de voorgaande beschrijvingen van toestemming van toepassing `/.default` zijn op het scenario. Vervolgens retourneert de code een `id_token` , in plaats van een toegangs token.  

Dit gedrag is geschikt voor sommige verouderde clients die worden verplaatst van Azure AD Authentication Library (ADAL) naar de Microsoft Authentication Library (MSAL). Deze installatie *mag niet worden* gebruikt door nieuwe clients die zijn gericht op het Microsoft Identity Platform.

### <a name="client-credentials-grant-flow-and-default"></a>Clientreferenties verlenen stroom en /.default  

Een ander gebruik van is het aanvragen van toepassingsmachtigingen (of rollen) in een niet-actieve toepassing, zoals een daemon-app die gebruikmaakt van de stroom voor het verlenen van clientreferenties om een `/.default` web-API aan te roepen.  [](v2-oauth2-client-creds-grant-flow.md)

Zie App-rollen toevoegen in uw toepassing voor het maken van toepassingsmachtigingen [(rollen) voor een web-API.](howto-add-app-roles-in-azure-ad-apps.md)

Aanvragen voor clientreferenties in uw client-app *moeten* `scope={resource}/.default` bevatten. Hier is `{resource}` de web-API die uw app wil aanroepen. Het uitgeven van een aanvraag voor clientreferenties met behulp van afzonderlijke toepassingsmachtigingen (rollen) wordt *niet* ondersteund. Alle toepassingsmachtigingen (rollen) die zijn verleend voor die web-API zijn opgenomen in het geretourneerde toegangs token.

Zie Configure a client application to access a web API (Een clienttoepassing configureren voor toegang tot [een web-API)](quickstart-configure-app-access-web-apis.md)om toegang te verlenen tot de toepassingsmachtigingen die u definieert, inclusief het verlenen van beheerders toestemming voor de toepassing.

### <a name="trailing-slash-and-default"></a>Schuine streep en /.default

Sommige resource-URI's hebben een schuine streep vooruit, bijvoorbeeld in `https://contoso.com/` plaats van `https://contoso.com` . De schuine streep kan problemen veroorzaken met de validatie van het token. Problemen treden voornamelijk op wanneer een token wordt aangevraagd voor Azure Resource Manager ( `https://management.azure.com/` ). In dit geval betekent een slash op de resource-URI dat de slash aanwezig moet zijn wanneer het token wordt aangevraagd.  Dus wanneer u een token aanvraagt voor en gebruikt, moet u een aanvraag indienen (let op `https://management.azure.com/` de dubbele `/.default` `https://management.azure.com//.default` slash). Als u controleert of het token wordt uitgegeven en als het token wordt afgewezen door de API die het moet accepteren, kunt u overwegen een tweede slash toe te voegen en het opnieuw te proberen. 

## <a name="troubleshooting-permissions-and-consent"></a>Problemen met machtigingen en toestemming oplossen

Zie Onverwachte fout bij het uitvoeren van toestemming voor een toepassing voor het oplossen [van problemen.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)

## <a name="next-steps"></a>Volgende stappen

* [Id-tokens in het Microsoft Identity Platform](id-tokens.md)
* [Toegangstokens in het Microsoft Identity Platform](access-tokens.md)

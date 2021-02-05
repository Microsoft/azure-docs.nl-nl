---
title: Ontwikkelaars richtlijnen voor voorwaardelijke toegang Azure Active Directory
titleSuffix: Microsoft identity platform
description: Richt lijnen voor ontwikkel aars en scenario's voor voorwaardelijke toegang tot Azure AD en het micro soft Identity-platform.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: e4021f0ca2c1c9ca9434744a4aebb4b7938315f0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584227"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Ontwikkelaars richtlijnen voor voorwaardelijke toegang Azure Active Directory

De functie voor voorwaardelijke toegang in Azure Active Directory (Azure AD) biedt een van de volgende manieren om uw app te beveiligen en een service te beveiligen. Met voorwaardelijke toegang kunnen ontwikkel aars en zakelijke klanten services op talrijke manieren beveiligen, waaronder:

* [Meervoudige verificatie](../authentication/concept-mfa-howitworks.md)
* Alleen intune-geregistreerde apparaten toegang verlenen tot specifieke services
* Gebruikers locaties en IP-bereiken beperken

Zie het artikel [Wat is voorwaardelijke toegang](../conditional-access/overview.md)? voor meer informatie over de volledige mogelijkheden van voorwaardelijke toegang.

Voor ontwikkel aars die apps bouwen voor Azure AD, laat dit artikel zien hoe u voorwaardelijke toegang kunt gebruiken. u leert ook hoe u toegang krijgt tot bronnen waarvoor u geen controle hebt over het beleid voor voorwaardelijke toegang. In dit artikel vindt u ook de implicaties van voorwaardelijke toegang in de namens-of-stroom, Web-apps, toegang tot Microsoft Graph en het aanroepen van Api's.

Er wordt uitgegaan van kennis van apps met [één](quickstart-register-app.md) en [meerdere tenants](howto-convert-app-to-be-multi-tenant.md) en [algemene verificatie patronen](./authentication-vs-authorization.md) .

> [!NOTE]
> Voor deze functie hebt u een Azure AD Premium P1-licentie nodig. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory/) als u een licentie zoekt die bij uw vereisten past.
> Klanten met een [Microsoft 365 Business-licentie](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot de functies voor voorwaardelijke toegang.

## <a name="how-does-conditional-access-impact-an-app"></a>Wat is het effect van voorwaardelijke toegang op een app?

### <a name="app-types-impacted"></a>Beïnvloede app-typen

In de meeste gevallen wordt de werking van een app door voorwaardelijke toegang niet gewijzigd of zijn wijzigingen van de ontwikkelaar vereist. In bepaalde gevallen, wanneer een app indirect of op een andere manier een token voor een service aanvraagt, vereist een app code wijzigingen voor het verwerken van problemen met voorwaardelijke toegang. Het kan net zo eenvoudig zijn als het uitvoeren van een interactieve aanmeldings aanvraag.

Met name de volgende scenario's vereisen code voor het verwerken van problemen met voorwaardelijke toegang:

* Apps die namens de stroom uitvoeren
* Apps die toegang krijgen tot meerdere Services/bronnen
* Apps met één pagina met MSAL.js
* Web Apps het aanroepen van een resource

Beleid voor voorwaardelijke toegang kan worden toegepast op de app, maar kan ook worden toegepast op een web-API die toegang heeft tot uw app. Voor meer informatie over het configureren van beleid voor voorwaardelijke toegang, Zie [Quick Start: MFA vereisen voor specifieke apps met Azure Active Directory voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md).

Afhankelijk van het scenario kan een zakelijke klant op elk gewenst moment beleid voor voorwaardelijke toegang Toep assen en verwijderen. Voor een goede werking van uw app wanneer er een nieuw beleid wordt toegepast, implementeert u de verwerking van de controle. De volgende voor beelden illustreren de verwerking van challenges.

### <a name="conditional-access-examples"></a>Voor beelden van voorwaardelijke toegang

Voor sommige scenario's is code wijzigingen vereist voor het afhandelen van voorwaardelijke toegang terwijl andere werken. Hier volgen enkele scenario's die gebruikmaken van voorwaardelijke toegang om multi-factor Authentication uit te voeren die inzicht biedt in het verschil.

* U bouwt een iOS-app met één Tenant en past een beleid voor voorwaardelijke toegang toe. De app meldt zich aan bij een gebruiker en vraagt geen toegang tot een API aan. Wanneer de gebruiker zich aanmeldt, wordt het beleid automatisch geactiveerd en moet de gebruiker multi-factor Authentication (MFA) uitvoeren.
* U bouwt een systeem eigen app die gebruikmaakt van een middle-tier-service voor toegang tot een stroomafwaartse API. Een Enter prise-klant bij het bedrijf dat deze app gebruikt, past een beleid toe op de downstream API. Wanneer een eind gebruiker zich aanmeldt, vraagt de systeem eigen app toegang tot de middelste laag en verzendt het token. De middelste laag voert namens stroom uitvoeringen uit om toegang aan te vragen tot de downstream API. Op dit punt wordt een uitdaging voor claims gepresenteerd aan de middelste laag. De middelste laag stuurt de uitdaging terug naar de systeem eigen app, die moet voldoen aan het beleid voor voorwaardelijke toegang.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph heeft speciale overwegingen bij het bouwen van apps in omgevingen met voorwaardelijke toegang. Over het algemeen werken de mechanismen van voorwaardelijke toegang hetzelfde, maar de beleids regels die uw gebruikers zien, worden gebaseerd op de onderliggende gegevens die uw app aanvraagt vanuit de grafiek.

Met name alle Microsoft Graph-bereiken vertegenwoordigen een gegevensset die afzonderlijk beleids regels kan Toep assen. Omdat aan het beleid voor voorwaardelijke toegang de specifieke gegevens sets zijn toegewezen, dwingt Azure AD beleids regels voor voorwaardelijke toegang af op basis van de gegevens in de grafiek, in plaats van Graph zelf.

Als een app bijvoorbeeld de volgende Microsoft Graph bereiken aanvraagt,

```
scopes="Bookings.Read.All Mail.Read"
```

Een app kan hun gebruikers verwachten alle beleids regels te voldoen die zijn ingesteld op boekingen en uitwisseling. Sommige bereiken kunnen worden toegewezen aan meerdere gegevens sets als hiermee toegang wordt verleend.

### <a name="complying-with-a-conditional-access-policy"></a>Voldoen aan het beleid voor voorwaardelijke toegang

Voor verschillende app-topologieën wordt een beleid voor voorwaardelijke toegang geëvalueerd wanneer de sessie tot stand is gebracht. Als beleid voor voorwaardelijke toegang wordt toegepast op de granulatie van apps en services, is het punt waarop het wordt aangeroepen, afhankelijk van het scenario dat u wilt uitvoeren.

Als uw app probeert toegang te krijgen tot een service met beleid voor voorwaardelijke toegang, kan er een uitdaging voor voorwaardelijke toegang optreden. Deze uitdaging wordt gecodeerd in de `claims` para meter die wordt geleverd in een antwoord van Azure AD. Hier volgt een voor beeld van deze Challenge-para meter:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Ontwikkel aars kunnen deze uitdaging volgen en toevoegen aan een nieuwe aanvraag voor Azure AD. Door deze status door te geven, wordt de eind gebruiker gevraagd om een actie uit te voeren die nodig is om te voldoen aan het beleid voor voorwaardelijke toegang. In de volgende scenario's worden de details van de fout en het uitpakken van de para meter uitgelegd.

## <a name="scenarios"></a>Scenario's

### <a name="prerequisites"></a>Vereisten

Voorwaardelijke toegang van Azure AD is een functie die deel uitmaakt van [Azure AD Premium](../fundamentals/active-directory-whatis.md). Klanten met een [Microsoft 365 Business-licentie](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot de functies voor voorwaardelijke toegang.

### <a name="considerations-for-specific-scenarios"></a>Overwegingen voor specifieke scenario's

De volgende informatie is alleen van toepassing op deze scenario's voor voorwaardelijke toegang:

* Apps die namens de stroom uitvoeren
* Apps die toegang krijgen tot meerdere Services/bronnen
* Apps met één pagina met MSAL.js

In de volgende secties worden algemene scenario's besproken die complexer zijn. Het basis principe van beleid is voorwaardelijke toegang wordt geëvalueerd op het moment dat het token wordt aangevraagd voor de service waarop een beleid voor voorwaardelijke toegang is toegepast.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: app die namens de stroom uitvoert

In dit scenario bespreken we het geval waarin een systeem eigen app een webservice/API aanroept. Deze service voert op zijn beurt de stroom ' namens-van ' uit om een downstream-service aan te roepen. In ons geval hebben we ons beleid voor voorwaardelijke toegang toegepast op de downstream-service (Web API 2) en gebruiken we een systeem eigen app in plaats van een server/daemon-app.

![App die het stroom diagram namens u uitvoert](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

De eerste token aanvraag voor web API 1 vraagt niet de eind gebruiker om multi-factor Authentication als Web-API 1 mogelijk niet altijd de downstream API. Zodra Web API 1 een token namens de gebruiker voor web API 2 probeert aan te vragen, mislukt de aanvraag omdat de gebruiker niet is aangemeld met multi-factor Authentication.

Azure AD retourneert een HTTP-antwoord met een aantal interessante gegevens:

> [!NOTE]
> In dit geval is dit een multi-factor Authentication-fout beschrijving, maar er is een breed scala van `interaction_required` mogelijke beschik bare voorwaardelijke toegang.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

In Web API 1 ondervangt we de fout `error=interaction_required` en sturen we de `claims` uitdaging terug naar de desktop-app. Op dat moment kan de bureau blad-app een nieuwe `acquireToken()` aanroep doen en de `claims` uitdaging toevoegen als een extra para meter voor de query teken reeks. Voor deze nieuwe aanvraag moet de gebruiker multi-factor Authentication uitvoeren en dit nieuwe token vervolgens terugsturen naar Web API 1 en de namens-stroom volt ooien.

Zie het voor [beeld van .net-code](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-)als u dit scenario wilt uitproberen. Hierin ziet u hoe u de claim Challenge teruggeeft van Web API 1 naar de systeem eigen app en een nieuwe aanvraag maakt in de client-app.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: app toegang tot meerdere services

In dit scenario wordt stapsgewijs uitgelegd in welke gevallen een web-app toegang heeft tot twee services waarvoor een voorwaardelijk toegangs beleid is toegewezen. Afhankelijk van de logica van uw app kan er een pad zijn waarin uw app geen toegang tot beide webservices vereist. In dit scenario speelt de volg orde waarin u een token aanvraagt een belang rijke rol in de ervaring van de eind gebruiker.

We gaan ervan uit dat we Web Service A en B en Web Service B hebben toegepast op het beleid voor voorwaardelijke toegang. Hoewel de eerste interactieve verificatie aanvraag toestemming vereist voor beide services, is het beleid voor voorwaardelijke toegang in geen geval vereist. Als de app een token aanvraagt voor Web Service B, wordt het beleid geactiveerd en worden de volgende aanvragen voor de webservice ook als volgt uitgevoerd.

![App toegang tot een stroom diagram met meerdere services](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Als de app aanvankelijk een token aanvraagt voor webservices, roept de eind gebruiker het beleid voor voorwaardelijke toegang niet aan. Hierdoor kan de app-ontwikkelaar de ervaring van de eind gebruiker beheren en het beleid voor voorwaardelijke toegang niet afdwingen om in alle gevallen te worden aangeroepen. Het lastigste geval is als de app vervolgens een token aanvraagt voor Web Service B. Op dit moment moet de eind gebruiker voldoen aan het beleid voor voorwaardelijke toegang. Wanneer de app probeert `acquireToken` , kan de volgende fout worden gegenereerd (Zie het volgende diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App toegang tot meerdere services die een nieuw token aanvragen](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Als de app gebruikmaakt van de MSAL-bibliotheek, wordt een fout bij het ophalen van het token altijd opnieuw geprobeerd. Wanneer deze interactieve aanvraag plaatsvindt, heeft de eind gebruiker de mogelijkheid om te voldoen aan de voorwaardelijke toegang. Dit geldt alleen als de aanvraag a `AcquireTokenSilentAsync` of `PromptBehavior.Never` in welk geval de app een interactieve aanvraag moet uitvoeren ```AcquireToken``` om de eind gebruiker te voorzien van de kans dat deze aan het beleid voldoet.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Scenario: een app met één pagina (SPA) met MSAL.js

In dit scenario wordt de zaak door lopen wanneer we een app met één pagina (SPA) hebben, met behulp van MSAL.js om een beveiligde web-API met voorwaardelijke toegang aan te roepen. Dit is een eenvoudige architectuur, maar er zijn enkele nuances die rekening moeten houden bij het ontwikkelen van voorwaardelijke toegang.

In MSAL.js zijn er enkele functies die tokens verkrijgen: `loginPopup()` , `acquireTokenSilent(...)` , `acquireTokenPopup(…)` , en `acquireTokenRedirect(…)` .

* `loginPopup()` Hiermee wordt een ID-token opgehaald via een interactieve aanmeldings aanvraag, maar geen toegangs tokens voor een service ontvangen (inclusief een beveiligde web-API met voorwaardelijke toegang).
* `acquireTokenSilent(…)` kan vervolgens worden gebruikt om een toegangs token op de achtergrond te verkrijgen, wat betekent dat de gebruikers interface in geen enkele omstandigheid wordt weer gegeven.
* `acquireTokenPopup(…)` en `acquireTokenRedirect(…)` worden beide gebruikt voor het interactief aanvragen van een token voor een bron, wat betekent dat ze altijd de AANMELDINGS gebruikersinterface weer geven.

Wanneer een app een toegangs token nodig heeft om een web-API aan te roepen, wordt geprobeerd een `acquireTokenSilent(…)` . Als de token sessie is verlopen of als er een beleid voor voorwaardelijke toegang moet worden nageleefd, mislukt de functie *acquireToken* en gebruikt `acquireTokenPopup()` of. de app `acquireTokenRedirect()` .

![App met één pagina met behulp van een MSAL-stroom diagram](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Laten we een voor beeld bekijken met ons scenario voor voorwaardelijke toegang. De eind gebruiker heeft alleen op de site gelandd en heeft geen sessie. U kunt een `loginPopup()` aanroep uitvoeren, een ID-Token ophalen zonder multi-factor Authentication. Vervolgens komt de gebruiker voor een knop die vereist dat de app gegevens van een web-API aanvraagt. De app probeert een aanroep uit te voeren `acquireTokenSilent()` , maar er is een fout opgetreden omdat de gebruiker nog geen multi-factor Authentication heeft uitgevoerd en moet voldoen aan het beleid voor voorwaardelijke toegang.

Azure AD stuurt de volgende HTTP-reactie terug:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Onze app moet worden onderschept `error=interaction_required` . De toepassing kan vervolgens ofwel `acquireTokenPopup()` of `acquireTokenRedirect()` op dezelfde resource gebruiken. De gebruiker is gedwongen een multi-factor Authentication uit te voeren. Nadat de gebruiker de multi-factor Authentication heeft voltooid, wordt er door de app een nieuw toegangs token uitgegeven voor de aangevraagde resource.

Als u dit scenario wilt uitproberen, raadpleegt u ons [js Spa-code voorbeeld](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md). Dit code voorbeeld maakt gebruik van het beleid voor voorwaardelijke toegang en de Web-API die u eerder hebt geregistreerd met een JS SPA om dit scenario te demonstreren. Hier ziet u hoe u de claim Challenge goed kunt afhandelen en een toegangs token krijgt dat kan worden gebruikt voor uw web-API. U kunt ook het voor beeld van de algemene [Angular.js code](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) uitchecken voor hulp bij een hoek Spa

## <a name="see-also"></a>Zie ook

* Zie [voorwaardelijke toegang in azure Active Directory](../conditional-access/overview.md)voor meer informatie over de mogelijkheden.
* Zie voor [beelden](sample-v2-code.md)voor meer Azure ad-code voorbeelden.
* Zie het [overzicht van micro soft-verificatie bibliotheek](msal-overview.md)voor meer informatie over de MSAL-SDK en toegang tot de referentie documentatie.
* Zie [gebruikers aanmelden met het multi tenant-patroon](howto-convert-app-to-be-multi-tenant.md)voor meer informatie over scenario's met meerdere tenants.
* Meer informatie over [voorwaardelijke toegang en het beveiligen van de toegang tot IOT-apps](/azure/architecture/example-scenario/iot-aad/iot-aad).
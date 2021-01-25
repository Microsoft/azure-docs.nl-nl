---
title: Micro soft Identity platform-verificatie stromen & app-scenario's | Azure
description: Meer informatie over toepassings scenario's voor het micro soft-identiteits platform, inclusief verificatie-identiteiten, het verkrijgen van tokens en het aanroepen van beveiligde Api's.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: c441d8e9fa5a3375b1ec4cd3e68d92e92738261d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755779"
---
# <a name="authentication-flows-and-application-scenarios"></a>Verificatiestromen en app-scenario's

Het micro soft Identity-platform ondersteunt verificatie voor verschillende soorten moderne toepassings architecturen. Alle architecturen zijn gebaseerd op de industrie standaard protocollen [OAuth 2,0 en OpenID Connect Connect](active-directory-v2-protocols.md). Met behulp van de [verificatie bibliotheken voor het micro soft-identiteits platform](reference-v2-libraries.md)verifiëren toepassingen identiteiten en verkrijgen tokens voor toegang tot beveiligde api's.

In dit artikel worden de verificatie stromen en de toepassings scenario's beschreven waarin ze worden gebruikt.

## <a name="application-categories"></a>Toepassings Categorieën

Tokens kunnen worden verkregen uit verschillende soorten toepassingen, waaronder:

- Web-apps
- Mobiele apps
- Desktop-apps
- Web-API's

Tokens kunnen ook worden verkregen door apps die worden uitgevoerd op apparaten die geen browser hebben of die worden uitgevoerd op de Internet of Things (IoT).

In de volgende secties worden de categorieën toepassingen beschreven.

### <a name="protected-resources-vs-client-applications"></a>Beveiligde bronnen versus client toepassingen

Verificatie scenario's hebben twee activiteiten:

- **Beveiligings tokens ophalen voor een beveiligde web-API**: we raden u aan door [micro soft ondersteunde client bibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) te gebruiken voor het verkrijgen van tokens. In het bijzonder wordt de micro soft Authentication Library (MSAL)-familie aangeraden.
- **Een web-API of web-app beveiligen**: een uitdaging voor het beveiligen van deze resources is het valideren van het beveiligings token. Op sommige platforms biedt micro soft [middleware-bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Met gebruikers of zonder gebruikers

De meeste verificatie scenario's verkrijgen tokens namens aangemelde gebruikers.

![Scenario's met gebruikers](media/scenarios/scenarios-with-users.svg)

Er zijn echter ook daemon-apps. In deze scenario's verkrijgen toepassingen tokens namens zichzelf zonder gebruiker.

![Scenario's met daemon-apps](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Toepassingen met één pagina, open bare client en vertrouwelijke client

Beveiligings tokens kunnen worden verkregen door meerdere typen toepassingen. Deze toepassingen worden vaak onderverdeeld in de volgende drie categorieën. Elk wordt gebruikt met verschillende bibliotheken en objecten.

- **Toepassingen met één pagina**: ook wel Spas genoemd, zijn dit web-apps waarin tokens worden verkregen door een Java script-of type script-app die in de browser wordt uitgevoerd. Veel moderne apps hebben een toepassing met één pagina aan de front-end die voornamelijk is geschreven in Java script. De toepassing maakt vaak gebruik van een kader zoals hoek, reageren of Vue. MSAL.js is de enige micro soft-verificatie bibliotheek die ondersteuning biedt voor toepassingen met één pagina.

- **Open bare client toepassingen**: apps in deze categorie, zoals de volgende typen, altijd aanmelden gebruikers:
  - Bureau blad-apps die web-Api's aanroepen namens aangemelde gebruikers
  - Mobiele apps
  - Apps die worden uitgevoerd op apparaten die geen browser hebben, zoals toepassingen die op IoT worden uitgevoerd
  
- **Vertrouwelijke client toepassingen**: apps in deze categorie zijn onder andere:
  - Web-apps die een web-API aanroepen
  - Web-Api's die een web-API aanroepen
  - Daemon-apps, zelfs wanneer deze zijn geïmplementeerd als een console service, zoals een Linux-daemon of een Windows-service

### <a name="sign-in-audience"></a>Aanmeldingsdoelgroep

De beschik bare verificatie stromen variëren afhankelijk van de doel groep van de aanmelding. Sommige stromen zijn alleen beschikbaar voor werk-of school accounts. Anderen zijn beschikbaar voor werk-of school accounts en voor persoonlijke micro soft-accounts.

Zie [supported account types](v2-supported-account-types.md#account-type-support-in-authentication-flows)(Engelstalig) voor meer informatie.

## <a name="application-scenarios"></a>Toepassingsscenario's

Het micro soft Identity-platform ondersteunt verificatie voor deze app-architecturen:

- Toepassingen met één pagina
- Web-apps
- Web-API's
- Mobiele apps
- Systeemeigen apps
- Daemon-apps
- Apps aan de server zijde

Toepassingen gebruiken de verschillende verificatie stromen voor het aanmelden van gebruikers en het ophalen van tokens om beveiligde Api's aan te roepen.

### <a name="single-page-application"></a>Toepassing met één pagina

Veel moderne web-apps zijn gebouwd als toepassingen met één pagina aan de client zijde. Deze toepassingen gebruiken Java script of een framework zoals hoek, vue en reageren. Deze toepassingen worden uitgevoerd in een webbrowser.

Toepassingen met één pagina verschillen van traditionele web-apps aan de server zijde in termen van verificatie kenmerken. Door gebruik te maken van het micro soft-identiteits platform kunnen toepassingen met één pagina gebruikers aanmelden en tokens verkrijgen voor toegang tot back-end-services of Web-Api's. Het micro soft Identity-platform biedt twee toekennings typen voor Java script-toepassingen: 

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Een toepassings auth met één pagina](media/scenarios/spa-app-auth.svg) | ![Een toepassing met één pagina impliciet](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Web-app die een gebruiker aanmeldt

![Een web-app die een gebruiker aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)

Een web-app helpen beveiligen die zich in een gebruiker aanmeldt:

- Als u in .NET ontwikkelt, gebruikt u ASP.NET of ASP.NET Core met ASP.NET OpenID Connect Connect middleware. Het beveiligen van een resource omvat het valideren van het beveiligings token, dat wordt uitgevoerd door de [Identity model-extensies voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) en niet MSAL-bibliotheken.

- Als u in Node.js ontwikkelt, gebruikt u [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Zie voor meer informatie [Web-app die gebruikers aantekent](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Een web-app die zich aanmeldt bij een gebruiker en een web-API namens de gebruiker aanroept

![Een web-app die web-Api's aanroept](media/scenarios/web-app.svg)

Als u een web-API van een web-app namens een gebruiker wilt aanroepen, gebruikt u de autorisatie code stroom en slaat u de aangeschafte tokens op in de token cache. Als dat nodig is, vernieuwt MSAL tokens en de controller wordt op de achtergrond tokens opgehaald uit de cache.

Zie [Web-app die web-api's aanroept](scenario-web-app-call-api-overview.md)voor meer informatie.

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Bureau blad-app die een web-API aanroept namens een aangemelde gebruiker

Voor een bureau blad-app voor het aanroepen van een web-API waarmee gebruikers zich aanmelden, gebruikt u de interactieve methoden voor het verkrijgen van tokens van MSAL. Met deze interactieve methoden kunt u de UI-ervaring voor aanmelden beheren. MSAL maakt gebruik van een webbrowser voor deze interactie.

![Een bureau blad-app die een web-API aanroept](media/scenarios/desktop-app.svg)

Er is een andere mogelijkheid voor door Windows gehoste toepassingen op computers die zijn toegevoegd aan een Windows-domein of door Azure Active Directory (Azure AD). Deze toepassingen kunnen een token op de achtergrond verkrijgen door gebruik te maken van [geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa).

Toepassingen die worden uitgevoerd op een apparaat zonder browser, kunnen nog steeds een API aanroepen namens een gebruiker. De gebruiker moet zich aanmelden op een ander apparaat met een webbrowser om te verifiëren. Voor dit scenario moet u de [code stroom](https://aka.ms/msal-net-device-code-flow)van het apparaat gebruiken.

![Stroom voor apparaatcode](media/scenarios/device-code-flow-app.svg)

Hoewel het niet wordt aangeraden om het te gebruiken, is de werk [stroom gebruikers naam en wacht woord](scenario-desktop-acquire-token.md#username-and-password) beschikbaar in open bare client toepassingen. Deze stroom is nog steeds nodig in sommige scenario's zoals DevOps.

Met de gebruikers naam en het wacht woord stroom beperkt u uw toepassingen. Zo kunnen toepassingen zich niet aanmelden bij een gebruiker die multi-factor Authentication of het hulp programma voor voorwaardelijke toegang in azure AD moet gebruiken. Uw toepassingen profiteren ook niet van eenmalige aanmelding. Verificatie met de gebruikers naam/wacht woord stroom verloopt tegen de principes van moderne authenticatie en wordt alleen voor verouderde redenen verschaft.

Als u wilt dat de token cache persistent blijft, kunt u in bureau blad-apps de token cache- [serialisatie](scenario-desktop-acquire-token.md#file-based-token-cache)aanpassen. Door het implementeren van [Dual token cache-serialisatie](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)kunt u achterwaarts compatibele en forward-compatibele token caches gebruiken. Deze tokens ondersteunen vorige generaties van verificatie bibliotheken. Voor beelden van specifieke bibliotheken zijn Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) versie 3 en versie 4.

Zie voor meer informatie [bureau blad-app die web-api's aanroept](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Mobiele app die een web-API aanroept namens een interactieve gebruiker

Net als bij een bureau blad-app roept een mobiele app de interactieve methoden voor het verkrijgen van tokens van MSAL op om een token te verkrijgen voor het aanroepen van een web-API.

![Een mobiele app die een web-API aanroept](media/scenarios/mobile-app.svg)

MSAL iOS en MSAL Android gebruiken standaard de systeem webbrowser. U kunt ze echter ook gebruiken om in plaats daarvan de Inge sloten webweergave in te richten. Er zijn specifieke kenmerken die afhankelijk zijn van het mobiele platform: Universeel Windows-platform (UWP), iOS of Android.

In sommige scenario's, zoals die voor voorwaardelijke toegang tot een apparaat-ID of een apparaatregistratie, moet een Broker op het apparaat worden geïnstalleerd. Voor beelden van makelaars zijn micro soft bedrijfsportal op Android en Microsoft Authenticator op Android en iOS. MSAL kan nu communiceren met brokers. Zie voor meer informatie over Brokers gebruikmaken [van makelaars op Android en IOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Zie voor meer informatie [mobiele app die web-api's aanroept](scenario-mobile-overview.md).

> [!NOTE]
> Een mobiele app die gebruikmaakt van MSAL. iOS, MSAL. Voor Android-of MSAL.NET op Xamarin kunnen app-beveiligings beleid worden toegepast. Het beleid kan bijvoorbeeld voor komen dat een gebruiker beveiligde tekst kopieert. De mobiele app wordt beheerd door intune en wordt door intune herkend als een beheerde app. Zie [Microsoft intune app SDK Overview](/intune/app-sdk)(Engelstalig) voor meer informatie.
>
> De [intune app SDK](/intune/app-sdk-get-started) is gescheiden van MSAL-bibliotheken en communiceert zelf met Azure AD.

### <a name="protected-web-api"></a>Beveiligde web-API

U kunt het micro soft-identiteits platform gebruiken om webservices te beveiligen, zoals de REST Web-API van uw app. Een beveiligde web-API wordt aangeroepen via een toegangs token. Het token helpt de gegevens van de API te beveiligen en inkomende aanvragen te verifiëren. De aanroeper van een web-API voegt een toegangs token toe in de autorisatie-header van een HTTP-aanvraag.

Als u uw ASP.NET of ASP.NET Core Web-API wilt beveiligen, moet u het toegangs token valideren. Voor deze validatie gebruikt u de ASP.NET JWT-middleware. De validatie wordt uitgevoerd door de [Identity model-uitbrei dingen voor de .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotheek en niet door MSAL.net.

Zie [Protected Web API](scenario-protected-web-api-overview.md)(Engelstalig) voor meer informatie.

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Web-API die een andere web-API aanroept namens een gebruiker

Uw app moet een token voor de downstream Web-API verkrijgen om uw beveiligde web-API een andere web-API te kunnen aanroepen namens een gebruiker. Dergelijke aanroepen worden soms *service-to-service* -aanroepen genoemd. Web-Api's die andere web-Api's aanroepen, moeten aangepaste cache-serialisatie bieden.

![Een web-API waarmee een andere web-API wordt aangeroepen](media/scenarios/web-api.svg)

Zie [Web API voor het aanroepen van web-api's](scenario-web-api-call-api-overview.md)voor meer informatie.

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Daemon-app die een web-API aanroept in de naam van de daemon

Apps die langlopende processen hebben of die werken zonder tussen komst van de gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde web-Api's. Een dergelijke app kan tokens verifiëren en ophalen met behulp van de identiteit van de app. De app bewijst zijn identiteit door gebruik te maken van een client geheim of certificaat.

U kunt dergelijke daemon-apps schrijven die een token voor de aanroepende app verkrijgen door gebruik te maken van de methoden voor het verkrijgen van [client referenties](scenario-daemon-acquire-token.md#acquiretokenforclient-api) in MSAL. Voor deze methoden is een client geheim vereist dat u toevoegt aan de app-registratie in azure AD. De App deelt het geheim vervolgens met de aangeroepen daemon. Voor beelden van dergelijke geheimen zijn toepassings wachtwoorden, bevestiging van certificaten en client verklaringen.

![Een daemon-app die wordt aangeroepen door andere apps en Api's](media/scenarios/daemon-app.svg)

Zie voor meer informatie [daemon-toepassing die web-api's aanroept](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenario's en ondersteunde verificatie stromen

U gebruikt verificatie stromen om de toepassings scenario's voor het aanvragen van tokens te implementeren. Er is geen een-op-een-toewijzing tussen toepassings scenario's en verificatie stromen.

Scenario's met betrekking tot het verkrijgen van tokens zijn ook toegewezen aan OAuth 2,0-verificatie stromen. Zie [OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity-platform](active-directory-v2-protocols.md)voor meer informatie.

<table>
 <thead>
  <tr><th>Scenario</th> <th>Gedetailleerd scenario door lopen</th> <th>OAuth 2,0-stroom en Grant</th> <th>Doelgroep</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App met één pagina</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatie code</a> met PKCE</td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App met één pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Impliciet</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Web-app waarmee gebruikers worden aangemeld</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatiecode</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web-app die web-API's aanroept</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatiecode</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Desktop-app die web-API's aanroept</a></td>
   <td>Interactief door gebruik te maken van <a href="v2-oauth2-auth-code-flow.md">autorisatie code</a> met PKCE</td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td>Geïntegreerde Windows-authenticatie</td>
   <td>Werk- of schoolaccounts</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Apparaatcode</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobiele app die web-API's aanroept</a></td>
   <td>Interactief door gebruik te maken van <a href="v2-oauth2-auth-code-flow.md">autorisatie code</a> met PKCE</td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Daemon-app die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Client referenties</a></td>
   <td>Alleen app-machtigingen die geen gebruiker hebben en alleen worden gebruikt in azure AD-organisaties</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Web-API die web-API's aanroept</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Namens-van</a></td>
   <td>Werk-of school accounts en persoonlijke accounts</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenario's en ondersteunde platforms en talen

Micro soft-verificatie bibliotheken ondersteunen meerdere platforms:

- Javascript
- .NET Framework
- .NET Core
- Windows 10-UWP
- Xamarin.iOS
- Xamarin.Android
- Systeem eigen iOS
- macOS
- Systeem eigen Android
- Java
- Python

U kunt ook verschillende talen gebruiken om uw toepassingen te bouwen.

> [!NOTE]
> Sommige toepassings typen zijn niet beschikbaar op elk platform.

In de kolom Windows van de volgende tabel wordt elke keer dat er .NET core wordt vermeld, .NET Framework ook mogelijk. Deze laatste wordt wegge laten om te voor komen dat de tabel onoverzichtelijk wordt.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App met één pagina](scenario-spa-overview.md) <br/>[![Verificatie van de app met één pagina](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [App met één pagina](scenario-spa-overview.md) <br/>[![Impliciete app met één pagina](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md) <br/>[![Een web-app die gebruikers aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web-app die web-API's aanroept](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python
| [Desktop-app die web-API's aanroept](scenario-desktop-overview.md) <br/> <br/>[ ![ Bureau blad-app die web-api's](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ aanroept Toestel code stroom](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/objectief C of SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Mobiele app die web-API's aanroept](scenario-mobile-overview.md) <br/> [![Mobiele app die web-API's aanroept](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objectief C of SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-apps](scenario-daemon-overview.md) <br/> [![Daemon-apps](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Web-API die web-API's aanroept](scenario-web-api-call-api-overview.md) <br/><br/> [![Web-API die web-API's aanroept](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Zie voor meer informatie [door micro soft ondersteunde bibliotheken per OS/taal](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [basis principes van verificatie](./authentication-vs-authorization.md) en [toegangs tokens in het micro soft Identity-platform](access-tokens.md).
* Meer informatie over [het beveiligen van de toegang tot IOT-apps](/azure/architecture/example-scenario/iot-aad/iot-aad).

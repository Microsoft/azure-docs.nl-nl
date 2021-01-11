---
title: Meer informatie over MSAL | Azure
titleSuffix: Microsoft identity platform
description: Met de micro soft Authentication Library (MSAL) kunnen ontwikkel aars van toepassingen tokens verkrijgen om beveiligde web-Api's aan te roepen. Deze web-Api's kunnen de Microsoft Graph, andere micro soft-Api's, Web-Api's van derden of uw eigen web-API zijn. MSAL ondersteunt meerdere toepassings architecturen en-platforms.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2b6266f308fce75f136aa4a2482c5b1be6eedb8e
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063362"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Overzicht van de micro soft Authentication Library (MSAL)
Met de micro soft Authentication Library (MSAL) kunnen ontwikkel aars [tokens](developer-glossary.md#security-token) verkrijgen van het micro soft Identity platform-eind punt om gebruikers te verifiëren en beveiligde web-api's te gebruiken. Het kan worden gebruikt om veilige toegang te bieden tot Microsoft Graph, andere micro soft-Api's, Web-Api's van derden of uw eigen web-API. MSAL ondersteunt veel verschillende toepassings architecturen en platformen, waaronder .NET, java script, Java, Python, Android en iOS.

MSAL biedt u een groot aantal manieren om tokens te verkrijgen, met een consistente API voor een aantal platforms. Het gebruik van MSAL biedt de volgende voor delen:

* U hoeft niet rechtstreeks de OAuth-bibliotheken of code te gebruiken voor het protocol in uw toepassing.
* Verwerft tokens namens een gebruiker of namens een toepassing (indien van toepassing op het platform).
* Houdt een token cache bij en vernieuwt tokens voor u wanneer ze bijna verlopen. U hoeft het token verloop niet zelf te verwerken.
* Helpt u de doel groep op te geven die u wilt dat uw toepassing zich aanmeldt (uw organisatie, verschillende organisaties, werk-en school-en micro soft-accounts, sociale identiteiten met Azure AD B2C, gebruikers in soevereine en nationale Clouds).
* Helpt u bij het instellen van uw toepassing vanuit configuratie bestanden.
* Helpt u bij het oplossen van problemen met uw app door uitzonde ringen, logboek registratie en telemetrie te tonen.

## <a name="application-types-and-scenarios"></a>Typen apps en scenario's
Met MSAL kunt u een token verkrijgen van een aantal toepassings typen: webtoepassingen, Web-Api's, apps met één pagina (Java script), mobiele en systeem eigen toepassingen, en daemons en server toepassingen.

MSAL kan worden gebruikt in veel toepassings scenario's, waaronder de volgende:

* [Toepassingen met één pagina (Java script)](scenario-spa-overview.md)
* [Gebruikers die zich aanmelden bij een web-app](scenario-web-app-sign-user-overview.md)
* [Webtoepassing voor het aanmelden bij een gebruiker en het aanroepen van een web-API namens de gebruiker](scenario-web-app-call-api-overview.md)
* [Een web-API beveiligen zodat alleen geverifieerde gebruikers er toegang tot hebben](scenario-protected-web-api-overview.md)
* [Web-API die een andere stroomafwaartse Web-API aanroept namens de aangemelde gebruiker](scenario-web-api-call-api-overview.md)
* [Bureaublad toepassing die een web-API aanroept namens de aangemelde gebruiker](scenario-desktop-overview.md)
* [Mobiele toepassing die een web-API aanroept namens de gebruiker die zich interactief aanmeldt](scenario-mobile-overview.md).
* [Desktop/service daemon-toepassing die een web-API aanroept namens zichzelf](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Talen en frameworks

| Bibliotheek | Ondersteunde platforms en frameworks|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET core, Xamarin Android, Xamarin iOS, Universeel Windows-platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Java script-type script frameworks zoals AngularJS, Ember.js of Durandal.js|
| [MSAL voor Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL voor iOS en macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS en macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Verschillen tussen ADAL en MSAL

Active Directory Authentication Library (ADAL) wordt geïntegreerd met het eind punt van Azure AD voor ontwikkel aars (v 1.0), waarbij MSAL integreert met het micro soft Identity platform (v 2.0)-eind punt. Het eind punt v 1.0 ondersteunt werk accounts, maar geen persoonlijke accounts. Het v 2.0-eind punt is het combineert van persoonlijke micro soft-accounts en werk accounts in één verificatie systeem. Daarnaast kunt u met MSAL ook verificaties voor Azure AD B2C ophalen.

Lees voor meer informatie over [het migreren naar MSAL.net van ADAL.net](msal-net-migration.md) en [migratie naar MSAL.js vanuit ADAL.js](msal-compare-msal-js-and-adal-js.md).

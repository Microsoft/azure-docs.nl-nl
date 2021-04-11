---
title: Codevoorbeelden voor Azure Active Directory v1.0 | Microsoft Docs
description: Biedt een index van codevoorbeelden voor Azure Active Directory (v1.0-eindpunt), geordend op scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 4d2b1a60856877c24d61379a688ea630e506a848
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075233"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Codevoorbeelden voor Azure Active Directory (v1.0-eindpunt)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

U kunt Microsoft Azure Active Directory (Azure AD) gebruiken om functionaliteit voor verificatie en autorisatie aan uw webtoepassingen en web-API's toe te voegen.

In deze sectie vindt u koppelingen naar voorbeelden die u kunt gebruiken voor meer informatie over het Azure AD v1.0-eindpunt. Deze voorbeelden laten zien hoe het werkt en bevatten ook codefragmenten die u kunt gebruiken in uw toepassingen. Op de pagina met codevoorbeelden vindt u gedetailleerde leesmij-onderwerpen om u te helpen bij de vereisten, installatie en configuratie. De code bevat ook opmerkingen om u te helpen de kritieke secties te begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in codevoorbeelden voor Azure AD v2, raadpleegt u [v2.0-codevoorbeelden per scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Zie [Verificatiescenario's voor Azure AD](v1-authentication-scenarios.md) voor meer informatie over het basisscenario voor elk type voorbeeld.

U kunt ook bijdragen aan onze voorbeelden op GitHub. Zie [Voorbeelden en documentatie voor Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory) voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In dit voorbeeld ziet u hoe u een toepassing met één pagina schrijft die wordt beveiligd met Azure AD.

| Platform | Roept een eigen API aan | Roept een andere web-API aan |
|--|--|--|
| ![In deze afbeelding ziet u het JavaScript-logo](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![In deze afbeelding ziet u het AngularJS-logo](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Webtoepassingen

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webtoepassingen voor het aanmelden van gebruikers, het aanroepen van Microsoft Graph of een web-API met de id van de gebruiker

De volgende voorbeelden illustreren webtoepassingen waarmee gebruikers worden aangemeld. Sommige van deze toepassingen roepen ook Microsoft Graph of uw eigen web-API aan namens de aangemelde gebruiker.

| Platform | Meldt alleen gebruikers aan | Roept Microsoft Graph aan | Roept een andere ASP.NET- of ASP.NET Core 2.0-web-API aan |
|--|--|--|--|
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(Microsoft Azure AD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![In deze afbeelding ziet u het ASP.NET Framework-logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (Microsoft Azure AD Graph) |
| ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo-python.png) |  | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo-java.png) |  | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![In deze afbeelding ziet u het PHP-logo](media/sample-v2-code/logo-php.png) |  | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webtoepassingen die op rollen gebaseerd toegangsbeheer (autorisatie) demonstreren

De volgende voorbeelden laten zien hoe u op rollen gebaseerd toegangsbeheer (RBAC) implementeert. RBAC wordt gebruikt om de machtigingen van bepaalde functies in een webtoepassing te beperken tot bepaalde gebruikers. De gebruikers zijn gemachtigd, als ze behoren tot een **Azure AD-groep** of een bepaalde **toepassingsrol** hebben.

| Platform | Voorbeeld | Beschrijving |
|--|--|--|
| ![In deze afbeelding ziet u het ASP.NET Framework-logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Een .NET 4.5 MVC-web-app die gebruikmaakt van Azure AD-**rollen** voor autorisatie |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Openbare bureaublad- en mobiele clienttoepassingen die Microsoft Graph of een web-API aanroepen

In de volgende voorbeelden ziet u openbare clienttoepassingen (bureaublad- of mobiele toepassingen) die Microsoft Graph of een web-API aanroepen namens een gebruiker. Afhankelijk van de apparaten en platformen, kunnen gebruikers door toepassingen op verschillende manieren worden aangemeld (stromen/toekenningen):

- Interactief
- Op de achtergrond (met geïntegreerde Windows-verificatie in Windows, of met een gebruikersnaam en wachtwoord)
- Door de interactieve aanmelding te delegeren naar een ander apparaat (apparaatcodestroom op apparaten zonder webbesturingselementen)

Clienttoepassing | Platform | Stroom/Toekenning | Roept Microsoft Graph aan | Roept een ASP.NET- of ASP.NET Core 2.x-web-API aan
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureaublad (WPF)           | ![In deze afbeelding ziet u het .NET/C#-logo](media/sample-v2-code/logo-net.png)  | Interactief | Onderdeel van [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobiel (UWP)            | .![In deze afbeelding ziet u het .NET/C#/UWP-logo](media/sample-v2-code/logo-windows.png)   | Interactief | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> In dit voorbeeld wordt [WAM](/windows/uwp/security/web-account-manager) gebruikt, niet [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-toepassing die ADAL.NET gebruikt voor het aanroepen van een web-API met één tenant) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-toepassing die ADAL.NET gebruikt voor het aanroepen van een web-API met meerdere tenants)|
Mobiel (Android, iOS, UWP)   | ![In deze afbeelding ziet u het .NET/C#-logo (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interactief | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobiel (Android)           | ![In deze afbeelding ziet u het Android-logo](media/sample-v2-code/logo-android.png) | Interactief |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobiel (iOS)           | ![In deze afbeelding ziet u iOS/Objective C of Swift](media/sample-v2-code/logo-ios.png) | Interactief |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Bureaublad (console)          | ![In deze afbeelding ziet u het .NET/C#-logo](media/sample-v2-code/logo-net.png) | Gebruikersnaam en wachtwoord </p>  Geïntegreerde Windows-authenticatie | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Bureaublad (console)          | ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo-java.png) | Gebruikersnaam en wachtwoord | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Bureaublad (console)           | ![In deze afbeelding ziet u het .NET Core/C#-logo](media/sample-v2-code/logo-netcore.png) | Stroom voor apparaatcode | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-toepassingen (toegang tot web-API's met de id van de toepassing)

In de volgende voorbeelden ziet u bureaublad- of webtoepassingen die Microsoft Graph of een web-API aanroepen zonder gebruiker (met de toepassings-id).

Clienttoepassing | Platform | Stroom/Toekenning | Roept een ASP.NET- of ASP.NET Core 2.0-web-API aan
------------------ | -------- | ---------- | -------------------- 
Daemon-app (console)          | ![In deze afbeelding ziet u het .NET Framework-logo](media/sample-v2-code/logo-netframework.png) | Clientreferenties met app-geheim of app-certificaat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-app (console)         | ![In deze afbeelding ziet u het .NET Core-logo](media/sample-v2-code/logo-netcore.png) | Clientreferenties met certificaat| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-web-app  | ![In deze afbeelding ziet u het .NET Framework-logo](media/sample-v2-code/logo-netframework.png) | Clientreferenties | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web-API's

### <a name="web-api-protected-by-azure-active-directory"></a>Web-API beveiligd met Azure Active Directory

In het volgende voorbeeld ziet u hoe u een Node.js-web-API beveiligt met Azure AD.

In de vorige secties van dit artikel vindt u ook andere voorbeelden van een clienttoepassing die een ASP.NET- of ASP.NET Core-**web-API** **aanroept**. Deze voorbeelden worden niet opnieuw vermeld in deze sectie, maar u vindt ze in de laatste kolom van de boven- of onderstaande tabellen

| Platform | Voorbeeld |
|--------|-------------------|
| ![In deze afbeelding ziet u het Node.js-logo](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web-API die Microsoft Graph of een andere web-API aanroept

In de volgende voorbeelden ziet u een web-API die een andere web-API aanroept. Het tweede voorbeeld laat zien hoe u voorwaardelijke toegang kunt verwerken.

| Platform |  Roept Microsoft Graph aan | Roept een andere ASP.NET- of ASP.NET Core 2.0-web-API aan |
| -------- |  --------------------- | ------------------------- |
| ![In deze afbeelding ziet u het ASP.NET Framework-logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Overige Microsoft Graph-voorbeelden

Zie de [voorbeelden en zelfstudies van de Microsoft Graph-community](https://github.com/microsoftgraph/msgraph-community-samples) voor voorbeelden en zelfstudies die verschillende gebruikspatronen voor de Microsoft Graph-API demonstreren, met inbegrip van verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Ontwikkelaarshandleiding voor Azure Active Directory](v1-overview.md)
- [Azure Active Directory Authentication Libraries (ADAL)](active-directory-authentication-libraries.md)
- [Conceptuele en naslaginformatie over de Microsoft Graph API](/graph/use-the-api)

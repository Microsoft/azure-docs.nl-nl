---
title: Micro soft Identity platform-verificatie bibliotheken
description: Compatibele client bibliotheken en middleware-bibliotheken voor servers, samen met verwante bibliotheek-, bron-en voorbeeld koppelingen, voor het micro soft Identity-platform.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 51b60d7b81d7402f69415b79cd575f51915dc38f
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756666"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Micro soft Identity platform-verificatie bibliotheken

Het [micro soft Identity-platform ](../azuread-dev/azure-ad-endpoint-comparison.md) biedt ondersteuning voor de industrie standaard OAuth 2,0 en openid connect Connect 1,0-protocollen. De micro soft Authentication Library (MSAL) is ontworpen voor gebruik met het micro soft Identity-platform. U kunt ook open-source bibliotheken gebruiken die ondersteuning bieden voor OAuth 2,0 en OpenID Connect Connect 1,0.

U wordt aangeraden bibliotheken te gebruiken die zijn geschreven door protocol domein experts die een SDL-methodologie (Security Development Lifecycle) volgen. Deze methodologieën bevatten [de methoden die micro soft volgt][Microsoft-SDL]. Als u code voor de protocollen wilt, moet u een methodologie zoals micro soft SDL volgen. Let op de beveiligings overwegingen in de standaard specificaties van elk protocol.

> [!NOTE]
> Zoekt u de Azure Active Directory Authentication Library (ADAL)? Bekijk de [hand leiding](../azuread-dev/active-directory-authentication-libraries.md)voor de ADAL-bibliotheek.

## <a name="types-of-libraries"></a>Typen bibliotheken

Het micro soft Identity-platform werkt met twee typen bibliotheken:

* **Client bibliotheken**: native clients en servers gebruiken client bibliotheken om toegangs tokens te verkrijgen voor het aanroepen van een bron, zoals Microsoft Graph.
* **Server middleware-bibliotheken**: Web-apps gebruiken server middleware-bibliotheken voor aanmelding door gebruikers. Web-Api's gebruiken middleware-bibliotheken voor servers om tokens te valideren die door systeem eigen clients of door andere servers worden verzonden.

## <a name="library-support"></a>Bibliotheek ondersteuning

Bibliotheken zijn beschikbaar in twee ondersteunings Categorieën:

* **Ondersteund door micro soft**: micro soft biedt oplossingen voor deze bibliotheken en heeft SDL op deze tape wisselaars aangehouden.
* **Compatibel**: micro soft heeft deze bibliotheken getest in de basis scenario's en heeft bevestigd dat ze werken met het micro soft-identiteits platform. Micro soft biedt geen oplossingen voor deze bibliotheken en heeft geen beoordeling van deze bibliotheken uitgevoerd. Problemen en functie aanvragen moeten worden omgeleid naar het open-source project van de bibliotheek.

Zie de volgende secties voor een lijst met bibliotheken die samen werken met het micro soft-identiteits platform.

## <a name="microsoft-supported-client-libraries"></a>Door micro soft ondersteunde client bibliotheken

Gebruik bibliotheken voor client verificatie voor het verkrijgen van een token voor het aanroepen van een beveiligde web-API.

| Platform | Bibliotheek | Downloaden | Broncode | Voorbeeld | Naslaginformatie | Conceptueel document | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![Javascript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [App met één pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Verwijzing](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Conceptuele documenten](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL hoek | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Hoek SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Verwijzing](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Conceptuele documenten](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktop-app](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview&preserve-view=true) |[Conceptuele documenten](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Pictogram .NET-kern geheugen](media/sample-v2-code/logo_NETCore.png) | Micro soft Identity Web  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web) |[GitHub](https://github.com/AzureAD/microsoft-identity-web) | [Voorbeelden](https://aka.ms/ms-id-web/samples) | [Micro soft. Identity. Web](/dotnet/api/microsoft.identity.web?view=azure-dotnet-preview&preserve-view=true) |[Conceptuele documenten](https://aka.ms/ms-id-web/conceptual-doc) | [Roadmap](https://github.com/AzureAD/microsoft-identity-web/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Veranderen](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Verwijzing](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Veranderen](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS en macOS | MSAL iOS en macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [IOS-app](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macOS-app](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Verwijzing](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Conceptuele documenten](msal-overview.md) | |
|![Android/java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Centrale opslag plaats](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-app](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Conceptuele documenten](msal-overview.md) |[Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Door micro soft ondersteunde server middleware-bibliotheken

Gebruik middleware-bibliotheken om webtoepassingen en Web-Api's te beveiligen. Web-apps of Web-Api's die zijn geschreven met ASP.NET of ASP.NET Core de middleware-bibliotheken gebruiken.

| Platform | Bibliotheek | Downloaden | Broncode | Voorbeeld | Naslaginformatie
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET beveiliging |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-app](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-naslag informatie](/dotnet/api/?view=aspnetcore-2.0&preserve-view=true) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Identity model-extensies voor .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-app](quickstart-v2-aspnet-webapp.md) |[Verwijzing](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet&preserve-view=true) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Pass Port |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-app](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Door micro soft ondersteunde bibliotheken per OS/taal

In de periode van ondersteunde besturings systemen versus talen is de toewijzing als volgt:

| Platform    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![Javascript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Swift <br> Objective-C |            |            | [MSAL voor iOS en macOS](msal-overview.md) | [MSAL voor iOS en macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.js | Pass Port. node | Pass Port. node | Pass Port. node |

Zie ook [scenario's voor ondersteunde platforms en talen](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Compatibele clientbibliotheken

| Platform | Bibliotheeknaam | Geteste versie | Broncode | Voorbeeld |
|:---:|:---:|:---:|:---:|:---:|
|![Javascript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versie 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | Versie 3.0.3 |[Vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe java](https://github.com/scribejava/scribejava) | [Versie 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect Connect-bibliotheek](https://github.com/GluuFederation/oxAuth) | [Versie 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect Connect-bibliotheek](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Aanvragen-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versie 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Aanvragen-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID Connect-client](https://github.com/panva/node-openid-client) | [Versie 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID Connect-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [De PHP competitie oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versie 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Verificatie van systeem eigen app reageren](https://github.com/FormidableLabs/react-native-app-auth) | [Versie 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Verificatie van systeem eigen app reageren](https://github.com/FormidableLabs/react-native-app-auth) | |

U kunt gebruikmaken van het micro soft-identiteits platform voor een bibliotheek die voldoet aan de normen. Het is belang rijk om te weten waar u naar ondersteuning moet gaan:

* Neem contact op met de eigenaar van de bibliotheek voor problemen en nieuwe functie aanvragen in bibliotheek code.
* Neem contact op met micro soft voor problemen en nieuwe functie aanvragen in de service-side protocol-implementatie.
* [Een functie aanvraag indienen](https://feedback.azure.com/forums/169401-azure-active-directory) voor aanvullende functies die u wilt weer geven in het protocol.
* [Een ondersteunings aanvraag maken](../../azure-portal/supportability/how-to-create-azure-support-request.md) als u een probleem vindt waarbij het micro soft-identiteits platform niet compatibel is met OAuth 2,0 of openid connect Connect 1,0.

## <a name="related-content"></a>Gerelateerde inhoud

Zie voor meer informatie over het micro soft-identiteits platform het [overzicht van micro soft Identity platform][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/

---
title: 'Azure Active Directory B2C: codevoorbeelden | Microsoft Docs'
description: Codevoorbeelden voor mobiele toepassingen, desktop- en webtoepassingen, en toepassingen met één pagina van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b09587d90024a8c376be8b0d93f7ef7b6cc51a1e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008481"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C: codevoorbeelden

In de volgende tabellen ziet u koppelingen naar voorbeelden voor toepassingen, waaronder iOS, Android, .NET en Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobiele apps en desktop-apps

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Een iOS-voorbeeld in Swift waarmee Azure AD B2C-gebruikers worden geverifieerd en een API wordt aangeroepen met behulp van OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Een eenvoudige Android-app die laat zien hoe u MSAL kunt gebruiken om gebruikers te verifiëren via Active Directory B2C en toegang kunt krijgen tot een Web-API met behulp van de resulterende tokens. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Een voor beeld van hoe u een bibliotheek van derden kunt gebruiken om een iOS-toepassing te bouwen in doelstelling-C waarmee micro soft-identiteits gebruikers worden geverifieerd bij onze Azure AD B2C-identiteits service. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Een voor beeld van hoe u een bibliotheek van derden kunt gebruiken om een Android-toepassing te bouwen die micro soft-identiteits gebruikers verifieert bij onze B2C Identity-service en een web-API aanroept met OAuth 2,0-toegangs tokens. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Een voorbeeld van hoe een gebruiker wordt aangemeld bij een WPF-toepassing (Windows Desktop .NET) via Azure AD B2C, een toegangstoken wordt verkregen met behulp van MSAL.NET en een API wordt aangeroepen. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om gebruikers te verifiëren via Active Directory B2C en toegang kunt krijgen tot een Web-API met behulp van de resulterende tokens. |

## <a name="web-apps-and-apis"></a>Web-apps en API's

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Een gecombineerd voorbeeld voor een .NET-webtoepassing waarmee een .NET Web-API wordt aangeroepen, waarbij beide zijn beveiligd via Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Een ASP.NET Core-webtoepassing die gebruikmaakt van OpenID Connect voor het aanmelden van gebruikers bij Azure AD B2C. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Een ASP.NET Core-webtoepassing waarmee een gebruiker wordt aangemeld via Azure AD B2C, een toegangstoken wordt verkregen met behulp van MSAL.NET en een API wordt aangeroepen. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Een Node.js-app die een snelle en eenvoudige manier biedt om een webtoepassing in te stellen met Express met behulp van OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Een kleine Node.js Web-API voor Azure AD B2C die laat zien hoe u uw Web-API kunt beveiligen en een B2C-toegangstoken kunt accepteren met behulp van passport.js. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Laat zien hoe u B2C van Microsoft Identity Platform integreert met een Python-webtoepassing.  |

## <a name="single-page-apps"></a>Apps met één pagina

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [MS-Identity-java script-reageren: zelf studie](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | Een toepassing met één pagina (SPA) die een web-API aanroept. De verificatie wordt uitgevoerd met Azure AD B2C met behulp van MSAL reageren. In dit voorbeeld wordt de autorisatiecodestroom met PKCE gebruikt. |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Een toepassing met één pagina (SPA) die een web-API aanroept. Verificatie wordt uitgevoerd met Azure AD B2C door gebruik te maken van MSAL.js. In dit voorbeeld wordt de autorisatiecodestroom met PKCE gebruikt. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Een toepassing met één pagina (SPA) die een web-API aanroept. Verificatie wordt uitgevoerd met Azure AD B2C door gebruik te maken van MSAL.js. In dit voor beeld wordt de impliciete stroom gebruikt.|
| [Java script-nodejs-beheer](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Een toepassing met één pagina (SPA) die Microsoft Graph aanroept voor het beheren van gebruikers in een B2C-map. Verificatie wordt uitgevoerd met Azure AD B2C door gebruik te maken van MSAL.js. In dit voorbeeld wordt de autorisatiecodestroom met PKCE gebruikt.|

## <a name="consoledaemon-apps"></a>Console/daemon-apps

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [Java script-nodejs-beheer](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Een Node.js en Express console daemon-toepassing die Microsoft Graph aanroept met een eigen identiteit voor het beheren van gebruikers in een B2C Directory. De verificatie wordt uitgevoerd met Azure AD B2C met behulp van het knoop punt MSAL. In dit voor beeld wordt de autorisatie code stroom gebruikt.|
| [dotnetcore-B2C-account-beheer](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Een .NET core-console toepassing die Microsoft Graph aanroept met een eigen identiteit voor het beheren van gebruikers in een B2C Directory. De verificatie wordt uitgevoerd met Azure AD B2C met behulp van MSAL.NET. In dit voor beeld wordt de autorisatie code stroom gebruikt.|

## <a name="saml-test-application"></a>SAML-testtoepassing

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | De SAML-testtoepassing voor het testen van Azure AD B2C is geconfigureerd om te fungeren als SAML-id-provider. |

## <a name="api-connectors"></a>API-connectors

De volgende tabellen bieden koppelingen naar codevoorbeelden voor het gebruik van web-API's in uw gebruikersstroom met behulp van [API-connectors](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Quickstarts voor Azure Functions

| Voorbeeld                                                                                                                          | Beschrijving                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Dit .NET Core Azure Function-voorbeeld laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Dit Azure Function-voorbeeld voor Node.js laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Dit Azure Function-voorbeeld voor Python laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren.    |


### <a name="automated-fraud-protection-services--captcha"></a>Automatische fraudebeschermingsservices & CAPTCHA
| Voorbeeld                                                                                                            | Beschrijving                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Fraude- en misbruikbescherming van Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | In dit voor beeld ziet u hoe u uw gebruikers aanmelding kunt beveiligen met de arkose Labs-service voor fraude bestrijding en misbruik. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Dit voor beeld laat zien hoe u uw gebruikers aanmelding kunt beveiligen met behulp van een reCAPTCHA-uitdaging om geautomatiseerd misbruik te voor komen. |


### <a name="identity-verification"></a>Identiteitverificatie

| Voorbeeld                                                                                                            | Beschrijving                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | In dit voorbeeld ziet u hoe u een gebruikersidentiteit kunt verifiëren als onderdeel van uw aanmeldingsstromen, door een API-connector te gebruiken om te integreren met IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | In dit voorbeeld ziet u hoe u een gebruikersidentiteit kunt verifiëren als onderdeel van uw aanmeldingsstromen, door een API-connector te gebruiken om te integreren met Experian. |


### <a name="other"></a>Anders

| Voorbeeld                                                                                                            | Beschrijving                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Uitnodigingscode](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | In dit voorbeeld ziet u hoe u de registratie van specifieke doelgroepen kunt beperken met behulp van uitnodigingscodes.|
| [Communityvoorbeelden van API-connector](https://github.com/azure-ad-b2c/api-connector-samples) | Deze opslagplaats bevat door de community onderhouden voorbeelden van scenario's die door API-connectors worden ingeschakeld.|

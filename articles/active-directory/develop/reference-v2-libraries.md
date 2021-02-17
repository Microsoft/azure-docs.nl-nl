---
title: Micro soft-identiteits platform verificatie bibliotheken | Azure
description: Lijst met client bibliotheken en middleware die compatibel zijn met het micro soft Identity-platform. Gebruik deze bibliotheken om ondersteuning toe te voegen voor gebruikers aanmelding (verificatie) en beveiligde web API-toegang (autorisatie) voor uw toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 228a15e9e9e27cbcfd71d4762db2f4ab9f6dfffe
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560153"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Micro soft Identity platform-verificatie bibliotheken

De volgende tabellen tonen ondersteuning van micro soft-verificatie bibliotheek voor verschillende toepassings typen. Deze bevatten koppelingen naar de bron code van de bibliotheek, waar u het pakket voor het project van uw app ophaalt, en of de bibliotheek gebruikers aanmelding (verificatie) ondersteunt, toegang tot beveiligde web-Api's (autorisatie) of beide.

Het micro soft-identiteits platform is gecertificeerd door de OpenID Connect-basis als een [gecertificeerde OpenID Connect-provider](https://openid.net/certification/). Als u liever een andere bibliotheek dan de micro soft Authentication Library (MSAL) of een andere door micro soft ondersteunde bibliotheek gebruikt, kiest u er een met een [gecertificeerde OpenID Connect Connect-implementatie](https://openid.net/developers/certified/).

Als u uw eigen implementatie op protocol niveau van [OAuth 2,0 of OpenID Connect Connect 1,0](active-directory-v2-protocols.md)hand matig wilt uitvoeren, moet u rekening houden met de beveiligings overwegingen voor elke standaard specificatie en een SDL-methodologie (Software Development Lifecycle) volgen, zoals [micro soft sdl][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Toepassing met één pagina (SPA)

Een toepassing met één pagina wordt volledig uitgevoerd op het browser oppervlak en de pagina gegevens (HTML, CSS en Java script) worden dynamisch of op basis van de laad tijd van de toepassing opgehaald. Het kan Web-Api's aanroepen om te communiceren met back-end-gegevens bronnen.

Omdat de code van een beveiligd-wachtwoord verificatie volledig in de browser wordt uitgevoerd, wordt dit beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

| Taal/Framework | Project op<br/>GitHub                                                                                                    | Pakket                                                                      | Slaag<br/>helpen                             | Gebruikers aanmelden                                         | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL hoek 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
| Angular              | [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Zelfstudie](tutorial-v2-angular.md)              | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
| Javascript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Zelfstudie](tutorial-v2-javascript-auth-code.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Javascript           | [MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)              | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)     | [Zelfstudie](tutorial-v2-javascript-spa.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| React                | [MSAL reageren](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

## <a name="web-application"></a>Webtoepassing

Een webtoepassing voert code uit op een server waarmee HTML, CSS en Java script worden gegenereerd en verzonden naar de webbrowser van een gebruiker. De identiteit van de gebruiker wordt behouden als een sessie tussen de browser van de gebruiker (de front-end) en de webserver (de back-end).

Omdat de code van een webtoepassing wordt uitgevoerd op de webserver, wordt deze beschouwd als een *vertrouwelijke client* waarop geheimen veilig kunnen worden opgeslagen.

| Taal/Framework | Project op<br/>GitHub                                                                                     | Pakket                                                                                                    | Slaag<br/>helpen                               | Gebruikers aanmelden                                            | Toegang tot Web-Api's                                                    | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y]    | Algemene beschikbaarheid                                                           |
| ASP.NET Core         | [ASP.NET beveiliging](/aspnet/core/security/)                                                                | [Micro soft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![Bibliotheek kan geen toegangs tokens aanvragen voor beveiligde web-Api's.][n] | Algemene beschikbaarheid                                                           |
| ASP.NET Core         | [Micro soft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y]    | Algemene beschikbaarheid                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Snelstartgids](quickstart-v2-java-webapp.md)        | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y]    | Algemene beschikbaarheid                                                           |
| Node.js              | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-knoop punt](https://www.npmjs.com/package/@azure/msal-node)                                                | [Snelstartgids](quickstart-v2-nodejs-webapp-msal.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y]    | Algemene beschikbaarheid                                               |
| Node.js              | [Azure AD Pass Port](https://github.com/AzureAD/passport-azure-ad)                                         | [Pass Port-Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Snelstartgids](quickstart-v2-nodejs-webapp.md)      | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Snelstartgids](quickstart-v2-python-webapp.md)      | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y]    | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y]    | Algemene beschikbaarheid                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

## <a name="desktop-application"></a>Bureaublad toepassing

Een bureaublad toepassing is doorgaans binaire (gecompileerde) code die een gebruikers interface bedient en die is bedoeld om te worden uitgevoerd op het bureau blad van een gebruiker.

Omdat een bureaublad toepassing op het bureau blad van de gebruiker wordt uitgevoerd, wordt deze beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

| Taal/Framework | Project op<br/>GitHub                                                                                     | Pakket                                                                               | Slaag<br/>helpen                        | Gebruikers aanmelden                                         | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Vangst             | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | [Zelfstudie](tutorial-v2-nodejs-desktop.md)   | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| macOS (SWIFT/obj-C)  | [MSAL voor iOS en macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Zelfstudie](tutorial-v2-ios.md)             | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Zelfstudie](tutorial-v2-windows-uwp.md)     | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Zelfstudie](tutorial-v2-windows-desktop.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

## <a name="mobile-application"></a>Mobiele toepassing

Een mobiele toepassing is doorgaans een binaire (gecompileerde) code die een gebruikers interface oppereert en die is bedoeld om te worden uitgevoerd op het mobiele apparaat van een gebruiker.

Omdat een mobiele toepassing wordt uitgevoerd op het mobiele apparaat van de gebruiker, wordt deze beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

| Platform          | Project op<br/>GitHub                                                                          | Pakket                                                                               | Slaag<br/>helpen                    | Gebruikers aanmelden                                         | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Snelstartgids](quickstart-v2-android.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| iOS (SWIFT/obj-C) | [MSAL voor iOS en macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Zelfstudie](tutorial-v2-ios.md)         | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

## <a name="service--daemon"></a>Service/daemon

Services en daemons worden vaak gebruikt voor server-naar-server en andere communicatie zonder toezicht (ook wel *headless*). Omdat er geen gebruiker is op het toetsen bord om referenties in te voeren of toestemming te geven voor toegang tot resources, verifiëren deze toepassingen zichzelf, niet als gebruiker, bij het aanvragen van geautoriseerde toegang tot de resources van een web-API.

Een service of daemon die op een server wordt uitgevoerd, wordt beschouwd als een *vertrouwelijke client* waarop de geheimen veilig kunnen worden opgeslagen.

| Taal/Framework | Project op<br/>GitHub                                                                 | Pakket                                                                                | Slaag<br/>helpen                           | Gebruikers aanmelden                                            | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Snelstartgids](quickstart-v2-netcore-daemon.md) | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Knooppunt               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-knoop punt](https://www.npmjs.com/package/@azure/msal-node)  | [Snelstartgids](quickstart-v2-nodejs-console.md)  | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | —  | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de micro soft Authentication Library (MSAL)](msal-overview.md)voor meer informatie over de micro soft-verificatie bibliotheek.

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

---
title: Xamarin iOS-overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin iOS met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064484"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Overwegingen voor het gebruik van Xamarin iOS met MSAL.NET

Wanneer u de micro soft Authentication Library voor .NET (MSAL.NET) op Xamarin iOS gebruikt, moet u het volgende doen:

- De functie overschrijven en implementeren `OpenUrl` in `AppDelegate` .
- Sleutel hanger groepen inschakelen.
- Het delen van token cache inschakelen.
- Toegang tot sleutel hanger inschakelen.
- Bekende problemen met iOS 12 en iOS 13 en verificatie begrijpen.

## <a name="implement-openurl"></a>OpenUrl implementeren

Overschrijf de `OpenUrl` methode van de `FormsApplicationDelegate` afgeleide klasse en de aanroep `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Hier volgt een voorbeeld:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Voer ook de volgende taken uit:

* Definieer een omleidings-URI-schema.
* Machtigingen vereisen voor uw app voor het aanroepen van een andere app.
* Een specifiek formulier voor de omleidings-URI hebben.
* [Registreer een omleidings-URI](quickstart-register-app.md#add-a-redirect-uri) in de Azure Portal.

### <a name="enable-keychain-access"></a>Sleutel hanger toegang inschakelen

Als u de toegang tot sleutel keten wilt inschakelen, moet u ervoor zorgen dat uw toepassing een toegangs groep voor de sleutel hanger heeft. U kunt de toegangs groep voor de sleutel hanger instellen wanneer u uw toepassing maakt met behulp van de `WithIosKeychainSecurityGroup()` API.

Stel de toegangs groep voor de sleutel hanger in voor al uw toepassingen om te profiteren van de cache en eenmalige aanmelding (SSO).

In dit voor beeld van de installatie wordt gebruikgemaakt van MSAL 4. x:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Schakel ook de toegang tot sleutel hanger in het `Entitlements.plist` bestand in. Gebruik de volgende toegangs groep of uw eigen toegangs groep.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Wanneer u de `WithIosKeychainSecurityGroup()` API gebruikt, voegt MSAL automatisch uw beveiligings groep toe aan het einde van de *Team-ID* () van de toepassing `AppIdentifierPrefix` . MSAL voegt uw beveiligings groep toe omdat wanneer u uw toepassing in Xcode bouwt, dit hetzelfde doet. Daarom moeten de rechten in het `Entitlements.plist` bestand worden vermeld `$(AppIdentifierPrefix)` vóór de toegangs groep voor de sleutel hanger.

Zie de documentatie voor IOS- [rechten](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)voor meer informatie.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Het delen van tokens in de cache inschakelen voor iOS-toepassingen

Vanaf MSAL 2. x kunt u een toegangs groep voor de sleutel hanger opgeven om de token cache in meerdere toepassingen te behouden. Met deze instelling kunt u de token cache delen tussen verschillende toepassingen die dezelfde toegangs groep voor de sleutel hanger hebben. U kunt de token cache delen tussen [ADAL.net](https://aka.ms/adal-net) toepassingen, MSAL.net Xamarin. IOS-toepassingen en systeem eigen IOS-toepassingen die zijn ontwikkeld in [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) of [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Door de token cache te delen, kunt u eenmalige aanmelding (SSO) toestaan voor alle toepassingen die gebruikmaken van de toegangs groep voor de sleutel hanger.

Als u deze cache wilt delen, gebruikt u de `WithIosKeychainSecurityGroup()` methode om de toegangs groep voor de sleutel hanger in te stellen op dezelfde waarde in alle toepassingen die dezelfde cache delen. In het eerste code voorbeeld in dit artikel ziet u hoe u de-methode gebruikt.

Eerder in dit artikel hebt u geleerd dat MSAL toegevoegd `$(AppIdentifierPrefix)` Wanneer u de `WithIosKeychainSecurityGroup()` API gebruikt. MSAL voegt dit element toe omdat de team-ID `AppIdentifierPrefix` ervoor zorgt dat alleen toepassingen die door dezelfde uitgever zijn gemaakt, de sleutel hanger toegang kunnen delen.

> [!NOTE]
> De `KeychainSecurityGroup` eigenschap is afgeschaft. Gebruik `iOSKeychainSecurityGroup` in plaats daarvan de eigenschap. Het `TeamId` voor voegsel is niet vereist wanneer u gebruikt `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator gebruiken

Uw toepassing kan Microsoft Authenticator als een Broker gebruiken om het volgende in te scha kelen:

- **SSO**: wanneer u SSO inschakelt, hoeven uw gebruikers zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: gebruik apparaat-id om te verifiëren door toegang te krijgen tot het certificaat van het apparaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek. Uw toepassing is gereed als de Tenant beheerders voorwaardelijke toegang met betrekking tot de apparaten inschakelen.
- **Verificatie** van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. De Broker controleert de omleidings-URL.

Zie [Microsoft Authenticator of Microsoft Intune bedrijfsportal in Xamarin IOS-en Android-toepassingen gebruiken](msal-net-use-brokers-with-xamarin-apps.md)voor meer informatie over het inschakelen van een Broker.

## <a name="known-issues-with-ios-12-and-authentication"></a>Bekende problemen met iOS 12 en verificatie

Micro soft heeft een [beveiligings advies](https://github.com/aspnet/AspNetCore/issues/4647) uitgebracht over een incompatibiliteit tussen IOS 12 en een type verificatie. De incompatibiliteit verbreekt sociale, WSFed-en OIDC-aanmeldingen. Het beveiligings advies helpt u inzicht te krijgen in het verwijderen van ASP.NET-beveiligings beperkingen van uw toepassingen om ze compatibel te maken met iOS 12.

Wanneer u MSAL.NET-toepassingen op Xamarin iOS ontwikkelt, ziet u mogelijk een oneindige lus wanneer u zich probeert aan te melden bij websites van iOS 12. Dit gedrag is vergelijkbaar met dit ADAL-probleem op GitHub: [oneindige lus wanneer u zich probeert aan te melden bij de website vanaf IOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Mogelijk ziet u ook een afbreek ASP.NET Core OIDC-verificatie met iOS 12 Safari. Zie voor meer informatie dit [webkit-probleem](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Bekende problemen met iOS 13 en verificatie

Als voor uw app voorwaardelijke toegang of ondersteuning voor certificaat verificatie is vereist, kunt u uw app gebruiken om te communiceren met de Microsoft Authenticator Broker-app. MSAL is vervolgens verantwoordelijk voor het verwerken van aanvragen en antwoorden tussen uw toepassing en Microsoft Authenticator.

Op iOS 13 heeft Apple een verbrekings-API-wijziging aangebracht door de mogelijkheid van de toepassing om de bron toepassing te lezen, te verwijderen bij het ontvangen van een reactie van een externe toepassing via aangepaste URL-schema's.

De Apple-documentatie voor [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) Staten:

> *Als de aanvraag afkomstig is van een andere app die deel uitmaakt van uw team, wordt de waarde van deze sleutel door UIKit ingesteld op de ID van de app. Als de team-ID van de oorspronkelijke app afwijkt van de team-ID van de huidige app, is de waarde van de sleutel Nil.*

Deze wijziging is opgesplitst voor MSAL omdat deze is vertrouwd op het `UIApplication.SharedApplication.OpenUrl` verifiëren van de communicatie tussen MSAL en de app Microsoft Authenticator.

Daarnaast is de ontwikkelaar op iOS 13 verplicht een presentatie controller te bieden bij het gebruik van `ASWebAuthenticationSession` .

Uw app wordt beïnvloed als u bouwt met Xcode 11 en u iOS Broker of gebruikt `ASWebAuthenticationSession` .

In dergelijke gevallen gebruikt u [MSAL.net 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) om geslaagde verificatie in te scha kelen.

### <a name="additional-requirements"></a>Aanvullende vereisten

- Wanneer u de meest recente MSAL-bibliotheken gebruikt, moet u ervoor zorgen dat **Microsoft Authenticator versie 6.3.19 +** op het apparaat is geïnstalleerd.
- Wanneer u een update uitvoert op MSAL.NET 4.4.0 +, werkt u de `LSApplicationQueriesSchemes` gegevens in het bestand *info. plist* bij en voegt u het `msauthv3` volgende toe:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Toevoegen `msauthv3` aan *info. plist* is nodig om de aanwezigheid van de nieuwste Microsoft Authenticator-app op het apparaat te detecteren dat IOS 13 ondersteunt.

## <a name="report-an-issue"></a>Een probleem melden

Als u vragen hebt of een probleem wilt melden dat u hebt gevonden in MSAL.NET, opent u een probleem in de [AzureAD/micro soft-Authentication-Library-voor-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) -opslag plaats op github.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over eigenschappen voor Xamarin iOS de para graaf [IOS-specifieke overwegingen](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) van het README.MD-bestand van het volgende voor beeld:

Voorbeeld | Platform | Beschrijving
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universeel Windows-platform (UWP) | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het Azure AD 2,0-eind punt. De app laat ook zien hoe u het resulterende token gebruikt voor toegang tot Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

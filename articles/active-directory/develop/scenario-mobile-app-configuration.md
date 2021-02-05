---
title: Mobiele apps configureren die web-Api's aanroepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van de code van uw mobiele app voor het aanroepen van een web-API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1187c768a54dd04d25b6de0e6785ebb81a7dfc24
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584428"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Een mobiele app configureren die web-Api's aanroept

Nadat u de toepassing hebt gemaakt, leert u hoe u de code kunt configureren met behulp van de app-registratie parameters. Mobiele toepassingen bieden een aantal complexe aspecten die betrekking hebben op de aanpassing aan het maken van hun Framework.

## <a name="find-msal-support-for-mobile-apps"></a>MSAL-ondersteuning voor mobiele apps zoeken

De volgende typen micro soft Authentication Library (MSAL) ondersteunen mobiele apps.

MSAL | Description
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Wordt gebruikt voor het ontwikkelen van draag bare toepassingen. MSAL.NET ondersteunt de volgende platforms voor het bouwen van een mobiele toepassing: Universeel Windows-platform (UWP), Xamarin. iOS en Xamarin. Android.
![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Wordt gebruikt voor het ontwikkelen van systeem eigen iOS-toepassingen met behulp van objectief-C of SWIFT.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Wordt gebruikt voor het ontwikkelen van systeem eigen Android-toepassingen in Java voor Android.

## <a name="instantiate-the-application"></a>De toepassing instantiëren

### <a name="android"></a>Android

Mobiele toepassingen gebruiken de- `PublicClientApplication` klasse. U kunt dit als volgt instantiëren:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobiele toepassingen op iOS moeten een exemplaar van de `MSALPublicClientApplication` klasse maken. Gebruik de volgende code om een instantie van de klasse te maken.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Aanvullende MSALPublicClientApplicationConfig-eigenschappen](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) kunnen de standaard instantie overschrijven, een omleidings-URI opgeven of het gedrag van het opslaan van MSAL-tokens wijzigen.

### <a name="xamarin-or-uwp"></a>Xamarin of UWP

In deze sectie wordt uitgelegd hoe u de toepassing kunt instantiëren voor Xamarin. iOS-, Xamarin. Android-en UWP-apps.

#### <a name="instantiate-the-application"></a>De toepassing instantiëren

In Xamarin of UWP is de eenvoudigste manier om de toepassing te instantiëren door de volgende code te gebruiken. In deze code `ClientId` is de GUID van de geregistreerde app.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Met aanvullende `With<Parameter>` methoden wordt de gebruikers interface van de bovenliggende UI ingesteld, de standaard instantie vervangen, een client naam en-versie voor telemetrie opgegeven, een omleidings-URI opgeven en opgeven welke HTTP-Factory moet worden gebruikt. De HTTP-Factory kan bijvoorbeeld worden gebruikt om proxy's te verwerken en telemetrie en logboek registratie op te geven.

De volgende secties bevatten meer informatie over het instantiëren van de toepassing.

##### <a name="specify-the-parent-ui-window-or-activity"></a>De bovenliggende gebruikers interface, het venster of de activiteit opgeven

Geef op Android de bovenliggende activiteit door voordat u interactieve verificatie doet. In iOS, wanneer u een Broker, Pass-in gebruikt `ViewController` . Op dezelfde manier kunt u in het bovenliggende venster het beste door geven aan UWP. U geeft deze door wanneer u het token aanschaft. Maar wanneer u de app maakt, kunt u ook een call back opgeven als een gemachtigde die als resultaat wordt geretourneerd `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

In Android raden we u aan om te gebruiken [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . De resulterende `PublicClientApplication` opbouw functie code ziet er als volgt uit:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Meer para meters voor het bouwen van apps zoeken

`PublicClientApplicationBuilder`Zie de [lijst methoden](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)voor een lijst met alle methoden die beschikbaar zijn op.

`PublicClientApplicationOptions`Zie de [referentie documentatie](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)voor een beschrijving van alle opties die worden weer gegeven in.

## <a name="tasks-for-xamarin-ios"></a>Taken voor Xamarin iOS

Als u MSAL.NET gebruikt op Xamarin iOS, voert u de volgende taken uit.

* [De functie overschrijven en implementeren `OpenUrl` in `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Sleutel hanger groepen inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Het delen van token cache inschakelen](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Sleutel hanger toegang inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Zie [Xamarin](msal-net-xamarin-ios-considerations.md)voor meer informatie.

## <a name="tasks-for-msal-for-ios-and-macos"></a>Taken voor MSAL voor iOS en macOS

Deze taken zijn nodig wanneer u MSAL gebruikt voor iOS en macOS:

* [De `openURL` call back implementeren](#brokered-authentication-for-msal-for-ios-and-macos)
* [Toegangs groepen voor sleutel hanger inschakelen](howto-v2-keychain-objc.md)
* [Browsers en WebView aanpassen](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Taken voor Xamarin. Android

Als u Xamarin. Android gebruikt, voert u de volgende taken uit:

- [Controleer of het besturings element terugkeert naar MSAL nadat het interactieve deel van de verificatie stroom is beëindigd](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Het Android-manifest bijwerken](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [De Inge sloten webweergave gebruiken (optioneel)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Problemen oplossen indien nodig](msal-net-xamarin-android-considerations.md#troubleshooting)

Zie [Xamarin. Android-overwegingen](msal-net-xamarin-android-considerations.md)voor meer informatie.

Zie [Xamarin. Android-specifieke overwegingen met MSAL.net](msal-net-system-browser-android-considerations.md)voor overwegingen met betrekking tot de browsers op Android.

#### <a name="tasks-for-uwp"></a>Taken voor UWP

Op UWP kunt u bedrijfs netwerken gebruiken. In de volgende secties worden de taken beschreven die u in het bedrijfs scenario moet volt ooien.

Zie [UWP-specifieke overwegingen met MSAL.net](msal-net-uwp-considerations.md)voor meer informatie.

## <a name="configure-the-application-to-use-the-broker"></a>De toepassing configureren voor het gebruik van de Broker

Op Android en iOS bieden makelaars de volgende opties:

- **Eenmalige aanmelding (SSO)**: u kunt SSO gebruiken voor apparaten die zijn geregistreerd bij Azure Active Directory (Azure AD). Wanneer u SSO gebruikt, hoeven uw gebruikers zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: met deze instelling schakelt u beleids regels voor voorwaardelijke toegang in die betrekking hebben op Azure ad-apparaten. Het verificatie proces maakt gebruik van het certificaat van het apparaat dat is gemaakt toen het apparaat werd toegevoegd aan de werk plek.
- **Verificatie** van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. Vervolgens wordt de Broker gecontroleerd.

### <a name="enable-the-broker-on-xamarin"></a>De Broker inschakelen op Xamarin

Als u de Broker op Xamarin wilt inschakelen, gebruikt `WithBroker()` u de para meter wanneer u de-methode aanroept `PublicClientApplicationBuilder.CreateApplication` . `.WithBroker()`Is standaard ingesteld op True.

Volg de stappen in de [sectie Xamarin. IOS](#enable-brokered-authentication-for-xamarin-ios) in dit artikel om brokered Authentication in te scha kelen voor Xamarin. IOS.

### <a name="enable-the-broker-for-msal-for-android"></a>De Broker inschakelen voor MSAL voor Android

Zie [brokered Authentication on Android](msal-android-single-sign-on.md)voor meer informatie over het inschakelen van een Broker op Android.

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>De Broker inschakelen voor MSAL voor iOS en macOS

Brokered Authentication is standaard ingeschakeld voor Azure AD-scenario's in MSAL voor iOS en macOS.

De volgende secties bevatten instructies voor het configureren van uw toepassing voor ondersteuning voor brokered-verificatie voor MSAL voor Xamarin. iOS of MSAL voor iOS en macOS. In de twee sets met instructies verschillen een deel van de stappen.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Brokered Authentication inschakelen voor Xamarin iOS

Volg de stappen in deze sectie om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen

Broker-ondersteuning is standaard uitgeschakeld. U schakelt deze in voor een afzonderlijke `PublicClientApplication` klasse. Gebruik de `WithBroker()` para meter bij het maken van de `PublicClientApplication` klasse via `PublicClientApplicationBuilder` . De `WithBroker()` para meter is standaard ingesteld op True.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL.NET de Broker aanroept, wordt de Broker vervolgens teruggebeld naar uw toepassing. De methode wordt teruggebeld met behulp van de `AppDelegate.OpenUrl` . Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. U kunt dit gedrag instellen door het `AppDelegate.cs` bestand bij te werken om de methode te overschrijven, zoals in de volgende code wordt weer gegeven.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart. Het is een kans om de reactie van de Broker te verwerken en om het verificatie proces te volt ooien dat MSAL.NET is gestart.

#### <a name="step-3-set-a-uiviewcontroller"></a>Stap 3: een UIViewController () instellen

Voor Xamarin iOS hoeft u normaal gesp roken geen object venster in te stellen. Maar in dit geval moet u deze zo instellen dat u antwoorden kunt verzenden en ontvangen vanuit een Broker. Als u een object venster wilt instellen, `AppDelegate.cs` stelt u in op een `ViewController` .

Voer de volgende stappen uit om het object venster in te stellen:

1. `AppDelegate.cs`Stel in de in `App.RootViewController` op een nieuw `UIViewController()` . Deze instelling zorgt ervoor dat de aanroep naar de Broker bevat `UIViewController` . Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Gebruik op de `AcquireTokenInteractive` aanroep `.WithParentActivityOrWindow(App.RootViewController)` . Geef de verwijzing door voor het object venster dat u gaat gebruiken. Hier volgt een voorbeeld:

    In `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    In `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    In de `AcquireToken` aanroep:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>Stap 4: een URL-schema registreren

MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en retour neren het Broker-antwoord terug naar uw app. Als u de retour ronding wilt volt ooien, registreert u het URL-schema van uw app in het `Info.plist` bestand.

Voer de volgende stappen uit om het URL-schema van uw app te registreren:

1. Voor voegsel `CFBundleURLSchemes` met `msauth` .
1. Voeg `CFBundleURLName` aan het einde toe. Volg dit patroon:

   `$"msauth.(BundleId)"`

   Hier kunt `BundleId` u uw apparaat uniek identificeren. Als dat zo `BundleId` is `yourcompany.xforms` , is uw URL-schema `msauth.com.yourcompany.xforms` .

  
      Dit URL-schema wordt onderdeel van de omleidings-URI die uw app op unieke wijze identificeert wanneer het de reactie van de Broker ontvangt.

   ```XML
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Stap 5: toevoegen aan de sectie LSApplicationQueriesSchemes

MSAL wordt gebruikt `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 heeft Apple de schema's vergrendeld die een toepassing kan opvragen.

Voeg toe `msauthv2` aan de `LSApplicationQueriesSchemes` sectie van het `Info.plist` bestand, zoals in het volgende code voorbeeld:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Brokered authenticatie voor MSAL voor iOS en macOS

Brokered Authentication is standaard ingeschakeld voor Azure AD-scenario's.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Stap 1: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL voor iOS en macOS de Broker aanroept, wordt de Broker teruggebeld naar uw toepassing met behulp van de `openURL` methode. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken met het aanroepen van MSAL. Stel deze mogelijkheid in door het `AppDelegate.m` bestand bij te werken om de methode te overschrijven, zoals in de volgende code voorbeelden wordt weer gegeven.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Als u hebt aangenomen `UISceneDelegate` op Ios 13 of hoger, plaatst u de retour AANROEP MSAL `scene:openURLContexts:` in `UISceneDelegate` plaats daarvan. MSAL `handleMSALResponse:sourceApplication:` moet slechts één keer worden aangeroepen voor elke URL.

Zie de [Apple-documentatie](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)voor meer informatie.

#### <a name="step-2-register-a-url-scheme"></a>Stap 2: een URL-schema registreren

MSAL voor iOS en macOS gebruikt Url's om de Broker aan te roepen en retour neren het Broker-antwoord naar uw app. Als u de retour ronding wilt volt ooien, registreert u een URL-schema voor uw app in het `Info.plist` bestand.

Een schema voor uw app registreren:

1. Een voor voegsel voor uw aangepaste URL-schema met behulp van `msauth` .

1. Voeg uw bundel-id toe aan het einde van het schema. Volg dit patroon:

   `$"msauth.(BundleId)"`

   Hier kunt `BundleId` u uw apparaat uniek identificeren. Als dat zo `BundleId` is `yourcompany.xforms` , is uw URL-schema `msauth.com.yourcompany.xforms` .

    Dit URL-schema wordt onderdeel van de omleidings-URI die uw app op unieke wijze identificeert wanneer het de reactie van de Broker ontvangt. Zorg ervoor dat de omleidings-URI in de indeling `msauth.(BundleId)://auth` is geregistreerd voor uw toepassing in de [Azure Portal](https://portal.azure.com).

   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Stap 3: LSApplicationQueriesSchemes toevoegen

Voeg toe `LSApplicationQueriesSchemes` aan het toestaan van aanroepen naar de Microsoft Authenticator-app, als deze is geïnstalleerd.

> [!NOTE]
> Het `msauthv3` schema is vereist wanneer uw app wordt gecompileerd met Xcode 11 en hoger.

Hier volgt een voor beeld van hoe u kunt toevoegen `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Brokered-verificatie voor Xamarin. Android

Zie voor meer informatie over het inschakelen van een Broker op Android [brokered Authentication on Xamarin. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario om [een token te verkrijgen](scenario-mobile-acquire-token.md).
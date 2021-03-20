---
title: Xamarin-apps migreren met behulp van brokers naar MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van Xamarin iOS-apps die gebruikmaken van Microsoft Authenticator van ADAL.NET naar MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89177428"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>IOS-toepassingen migreren die gebruikmaken van Microsoft Authenticator van ADAL.NET naar MSAL.NET

U gebruikt de Azure Active Directory Authentication Library voor .NET (ADAL.NET) en de iOS-Broker. Nu is het tijd om te migreren naar de [micro soft Authentication Library](msal-overview.md) voor .net (MSAL.net), die ondersteuning biedt voor de Broker op Ios vanaf versie 4,3 en hoger.

Waar moet u beginnen? Dit artikel helpt u bij het migreren van uw Xamarin iOS-app van ADAL naar MSAL.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Xamarin iOS-app hebt die is geïntegreerd met de iOS-Broker. Als dat niet het geval is, gaat u rechtstreeks naar MSAL.NET en begint u met het implementeren van de Broker. Raadpleeg [deze documentatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)voor meer informatie over het aanroepen van de IOS-broker in MSAL.net met een nieuwe toepassing.

## <a name="background"></a>Achtergrond

### <a name="what-are-brokers"></a>Wat zijn makelaars?

Makelaars zijn toepassingen die door micro soft worden verschaft op Android en iOS. (Zie de app [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) op Ios en Android en de intune-bedrijfsportal-app op Android.)

Ze inschakelen:

- Eenmalige aanmelding.
- Apparaat-id, die wordt vereist door een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md). Zie [Apparaatbeheer](../conditional-access/concept-conditional-access-conditions.md#device-platforms)voor meer informatie.
- Verificatie van de toepassings-id. Dit is ook vereist in sommige bedrijfs scenario's. Zie [intune-Mobile Application Management (mam) (Engelstalig)](/intune/mam-faq)voor meer informatie.

## <a name="migrate-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

### <a name="step-1-enable-the-broker"></a>Stap 1: de Broker inschakelen

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
In ADAL.NET is Broker-ondersteuning ingeschakeld op basis van context per verificatie. Het is standaard uitgeschakeld. U moest een

`useBroker` vlag toevoegen aan True in de `PlatformParameters` constructor om de Broker aan te roepen:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
Daarnaast stelt u in de platformspecifieke code, in dit voor beeld in de pagina renderer voor iOS, het `useBroker`
vlag toevoegen aan waar:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Neem vervolgens de para meters op in de aanroep voor het verkrijgen van een token:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource,
                              ClientId,
                              new Uri(RedirectURI),
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
In MSAL.NET is de ondersteuning van Broker ingeschakeld op basis van één PublicClientApplication. Het is standaard uitgeschakeld. Als u deze wilt inschakelen, gebruikt u de

`WithBroker()` para meter (standaard ingesteld op True) om de Broker aan te roepen:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
In de aanroep voor het verkrijgen van een token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Stap 2: Stel een UIViewController in ()
In ADAL.NET hebt u een UIViewController door gegeven als onderdeel van `PlatformParameters` . (Zie het voor beeld in stap 1.) In MSAL.NET wordt een object venster gebruikt om ontwikkel aars meer flexibiliteit te geven, maar dit is niet vereist in het gebruik van gewoon iOS. Als u de Broker wilt gebruiken, stelt u het object venster in om antwoorden te verzenden en te ontvangen van de Broker.
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Er wordt een UIViewController door gegeven aan

`PlatformParameters` in het iOS-specifieke platform.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET voert u twee dingen uit om het object venster voor iOS in te stellen:

1. `AppDelegate.cs`Stel in `App.RootViewController` op een nieuw `UIViewController()` .
Deze toewijzing zorgt ervoor dat er een UIViewController is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Gebruik in de AcquireTokenInteractive-aanroep `.WithParentActivityOrWindow(App.RootViewController)` en geef in de verwijzing naar het object venster dat u gaat gebruiken.

**Bijvoorbeeld:**

In `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
In de aanroep voor het verkrijgen van een token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back
Zowel ADAL als MSAL bellen de Broker en de broker in zet de app terug naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` klasse. Raadpleeg [deze documentatie](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)voor meer informatie.

Er zijn hier geen wijzigingen tussen ADAL.NET en MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Stap 4: een URL-schema registreren
ADAL.NET en MSAL.NET gebruiken Url's om de Broker aan te roepen en retour neren de Broker respons terug naar de app. Registreer het URL-schema in het `Info.plist` bestand voor uw app als volgt:

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Het URL-schema is uniek voor uw app.
</td><td>
Kan

`CFBundleURLSchemes` naam moet bevatten

`msauth.`

Als voor voegsel, gevolgd door uw `CFBundleURLName`

Bijvoorbeeld: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Dit URL-schema wordt onderdeel van de omleidings-URI die wordt gebruikt om de app op unieke wijze te identificeren wanneer deze het antwoord van de Broker ontvangt.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 5: de Broker-id toevoegen aan de sectie LSApplicationQueriesSchemes

ADAL.NET en MSAL.NET beide gebruiken `-canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. Voeg als volgt de juiste id voor de iOS-Broker toe aan de sectie LSApplicationQueriesSchemes van het bestand info. plist:

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Gebruikt

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Gebruikt

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>Stap 6: de omleidings-URI registreren in de Azure Portal

ADAL.NET en MSAL.NET voegen zowel een extra vereiste voor de omleidings-URI toe als deze de Broker doel heeft. Registreer de omleidings-URI met uw toepassing in de Azure Portal.
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Voorbeeld:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Voorbeeld:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Zie [stap 7: een omleidings-URI toevoegen aan de registratie van uw app](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration)voor meer informatie over het registreren van de omleidings-URI in de Azure Portal.

### <a name="step-7-set-the-entitlementsplist"></a>**Stap 7: Stel de rechten in. plist**

Toegang tot sleutel hanger inschakelen in het bestand *rechten. plist* :

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Zie [toegang tot sleutel hanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie over het inschakelen van de toegang tot de sleutel hanger.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Xamarin IOS-specifieke overwegingen met MSAL.net](msal-net-xamarin-ios-considerations.md).
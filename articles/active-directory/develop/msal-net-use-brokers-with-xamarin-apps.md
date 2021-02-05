---
title: Brokers gebruiken met Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van Xamarin iOS-toepassingen die gebruikmaken van de Microsoft Authenticator en de micro soft-verificatie bibliotheek voor .NET (MSAL.NET). Meer informatie over hoe u migreert van Azure AD Authentication Library voor .NET (ADAL.NET) naar de micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.openlocfilehash: ee9f374d110115b224c48853f26037dc461e5fa0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583838"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator of Intune-bedrijfsportal gebruiken in Xamarin-toepassingen

Op Android en iOS kunnen brokers als Microsoft Authenticator en de Android-specifieke Microsoft Intune bedrijfsportal inschakelen:

- **Eenmalige aanmelding (SSO)**: gebruikers hoeven zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: de Broker heeft toegang tot het certificaat van het apparaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek.
- **Verificatie** van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. De Broker verifieert de URL.

Als u een van deze functies wilt inschakelen, gebruikt `WithBroker()` u de para meter wanneer u de-methode aanroept `PublicClientApplicationBuilder.CreateApplication` . De `.WithBroker()` para meter is standaard ingesteld op True.

De installatie van in de micro soft Authentication Library voor .NET (MSAL.NET) gebrokerde verificatie is afhankelijk van het platform:

* [iOS-toepassingen](#brokered-authentication-for-ios)
* [Android-toepassingen](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Brokered authenticatie voor iOS

Gebruik de volgende stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) . Als u klaar bent met iOS 13, kunt u overwegen meer te weten te komen over [de laatste API-wijziging van Apple](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen

U moet Broker ondersteuning inschakelen voor afzonderlijke exemplaren van `PublicClientApplication` . Ondersteuning is standaard uitgeschakeld. Wanneer u maakt `PublicClientApplication` via `PublicClientApplicationBuilder` , gebruikt u de `WithBroker()` para meter zoals in het volgende voor beeld wordt getoond. De `WithBroker()` para meter is standaard ingesteld op True.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Stap 2: toegang tot sleutel hanger inschakelen

Als u de toegang tot de sleutel keten wilt inschakelen, moet u een toegangs groep voor de sleutel hanger hebben voor uw toepassing. U kunt de `WithIosKeychainSecurityGroup()` API gebruiken om de toegangs groep voor de sleutel hanger in te stellen wanneer u uw toepassing maakt:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Zie [toegang tot sleutel hanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL.NET de Broker aanroept, wordt de Broker teruggebeld naar uw toepassing via de `OpenUrl` methode van de- `AppDelegate` klasse. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. Als u deze samen werking wilt inschakelen, werkt u het *AppDelegate.cs* -bestand bij om de volgende methode te overschrijven.

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

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart. Het wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat MSAL.NET is gestart.

### <a name="step-4-set-uiviewcontroller"></a>Stap 4: set UIViewController ()

Stel in het *AppDelegate.cs* -bestand nog steeds een object venster in. Normaal gesp roken hoeft u het object venster niet in te stellen voor Xamarin iOS, maar u hebt wel een object venster nodig voor het verzenden en ontvangen van antwoorden van de Broker.

Het object venster instellen:

1. Stel in het bestand *AppDelegate.cs* in `App.RootViewController` op een nieuw `UIViewController()` . Deze toewijzing zorgt ervoor dat de aanroep naar de Broker bevat `UIViewController` . Als deze instelling onjuist is toegewezen, kan deze fout optreden:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive`Gebruik `.WithParentActivityOrWindow(App.RootViewController)` en klik vervolgens in de verwijzing naar het object venster dat u wilt gebruiken.

    In *app.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    In *AppDelegate.cs*:

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

### <a name="step-5-register-a-url-scheme"></a>Stap 5: een URL-schema registreren

MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en vervolgens het Broker-antwoord te retour neren aan uw app. Als u de retour ronding wilt volt ooien, registreert u een URL-schema voor uw app in het bestand *info. plist* .

De `CFBundleURLSchemes` naam moet `msauth.` als voor voegsel bevatten. Volg het voor voegsel met `CFBundleURLName` .

In het URL-schema is `BundleId` de unieke identificatie van de app: `$"msauth.(BundleId)"` . Als dat zo `BundleId` is `com.yourcompany.xforms` , is het URL-schema `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Dit URL-schema wordt onderdeel van de omleidings-URI waarmee uw app uniek wordt geïdentificeerd wanneer de reactie van de Broker wordt ontvangen.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 6: de Broker-id toevoegen aan de sectie LSApplicationQueriesSchemes

MSAL wordt gebruikt `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 heeft Apple de schema's vergrendeld die een toepassing kan opvragen.

Voeg toe `msauthv2` aan de `LSApplicationQueriesSchemes` sectie van het bestand *info. plist* , zoals in het volgende voor beeld:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Stap 7: een omleidings-URI toevoegen aan de app-registratie

Wanneer u de Broker gebruikt, heeft de omleidings-URI een extra vereiste. De omleidings-URI _moet_ de volgende indeling hebben:

```csharp
$"msauth.{BundleId}://auth"
```

Hier volgt een voorbeeld:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

U ziet dat de omleidings-URI overeenkomt met de `CFBundleURLSchemes` naam die u hebt opgenomen in het bestand *info. plist* .

Voeg de omleidings-URI toe aan de registratie van de app in de [Azure Portal](https://portal.azure.com). Als u een correct opgemaakte omleidings-URI wilt genereren, gebruikt u **app-registraties** in de Azure Portal om de brokered omleidings-URI uit de bundel-id te genereren.

**De omleidings-URI genereren:**

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Selecteer **Azure Active Directory**  >  **app-registraties** > de geregistreerde app
1. **Verificatie** selecteren  >  **een platform toevoegen**  >  **IOS/macOS**
1. Voer uw bundel-ID in en selecteer vervolgens **configureren**.

    Kopieer de gegenereerde omleidings-URI die wordt weer gegeven in het tekstvak **omleidings-URI** voor opname in de code:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="instellingen van het iOS-platform met de gegenereerde omleidings-URI in Azure Portal":::
1. Selecteer **gereed** om de generatie van de omleidings-URI te volt ooien.

## <a name="brokered-authentication-for-android"></a>Brokered authenticatie voor Android

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen

Broker-ondersteuning is per `PublicClientApplication` basis ingeschakeld. Het is standaard uitgeschakeld. Gebruik de `WithBroker()` para meter (standaard ingesteld op True) bij het maken van de `IPublicClientApplication` via `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL.NET de Broker aanroept, roept de Broker op zijn beurt terug naar uw toepassing met de- `OnActivityResult()` methode. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing het resultaat routeren naar MSAL.NET.

Stuur het resultaat naar de- `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` methode door de methode te overschrijven `OnActivityResult()` , zoals hier wordt weer gegeven:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Deze methode wordt aangeroepen wanneer de Broker-toepassing wordt gestart en wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat door MSAL.NET is gestart.

### <a name="step-3-set-an-activity"></a>Stap 3: een activiteit instellen

Als u brokered Authentication wilt inschakelen, stelt u een activiteit in zodat MSAL de reactie van en naar de broker kan verzenden en ontvangen. Als u dit wilt doen, geeft u de activiteit (meestal de `MainActivity` ) op voor `WithParentActivityOrWindow(object parent)` het bovenliggende object.

Bijvoorbeeld in de aanroep van `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Stap 4: een omleidings-URI toevoegen aan de registratie van uw app

MSAL maakt gebruik van Url's om de Broker aan te roepen en vervolgens terug te gaan naar uw app. Als u deze retour ronding wilt volt ooien, registreert u een **omleidings-URI** voor uw app met behulp van de [Azure Portal](https://portal.azure.com).

De indeling van de omleidings-URI voor uw toepassing is afhankelijk van het certificaat dat wordt gebruikt voor het ondertekenen van de APK. Bijvoorbeeld:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Het laatste deel van de URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , is de met base64 gecodeerde versie van de hand tekening waarmee de APK is ondertekend. Als u uw app in Visual Studio ontwikkelt en u fouten opspoort in uw code zonder de APK te ondertekenen met een specifiek certificaat, wordt de APK door Visual Studio ondertekend voor fout opsporing. Wanneer Visual Studio de APK op deze manier ondertekent, geeft het een unieke hand tekening voor de machine waarop het is gebouwd. Telkens wanneer u uw app op een andere machine bouwt, moet u de omleidings-URI in de code van de toepassing bijwerken en de registratie van de toepassing in de Azure Portal om te verifiëren met MSAL.

Tijdens het opsporen van fouten kan er een MSAL-uitzonde ring (of logboek bericht) worden weer gegeven met de mede deling dat de omleidings-URI onjuist is. **De uitzonde ring of het logboek bericht geeft ook de omleidings-URI aan die u moet gebruiken** met de huidige machine waarvoor u fouten wilt opsporen. U kunt de verschafte omleidings-URI gebruiken om uw app te blijven ontwikkelen, zolang u de omleidings-URI in code bijwerkt en de meegeleverde omleidings-URI aan de registratie van de app in de Azure Portal toevoegt.

Wanneer u klaar bent om uw code te volt ooien, werkt u de omleidings-URI in de code bij en de registratie van de toepassing in de Azure Portal om de hand tekening van het certificaat te gebruiken waarmee u de APK ondertekent.

In de praktijk houdt u rekening met het toevoegen van een omleidings-URI voor elk lid van uw ontwikkel team, *plus* een omleidings-URI voor de door de productie ondertekende versie van de APK.

U kunt de hand tekening zelf berekenen, vergelijkbaar met de manier waarop MSAL dit doet:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

U kunt ook de hand tekening voor uw pakket verkrijgen met het **hulp programma** voor het gebruik van de volgende opdrachten:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Stap 5 (optioneel): terugvallen op de systeem browser

Als MSAL is geconfigureerd voor het gebruik van de Broker, maar de Broker niet is geïnstalleerd, zal MSAL terugvallen op het gebruik van een webweergave (een browser). MSAL probeert te verifiëren met behulp van de standaard systeem browser op het apparaat. Dit kan mislukken omdat de omleidings-URI is geconfigureerd voor de Broker en de systeem browser niet weet hoe u deze kunt gebruiken om terug te gaan naar MSAL. Als u de fout wilt voor komen, kunt u een *intentie filter* configureren met de Broker omleidings-URI die u in stap 4 hebt gebruikt.

Wijzig het manifest van de toepassing om het intentie filter toe te voegen:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Als u bijvoorbeeld een omleidings-URI van hebt `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , moet het manifest eruit zien als het volgende XML-code fragment.

De voorwaartse slash ( `/` ) voor de hand tekening in de `android:path` waarde is **vereist**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Als alternatief kunt u MSAL configureren om terug te vallen op de Inge sloten browser, die niet afhankelijk is van een omleidings-URI:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Tips voor het oplossen van problemen met Android brokered-verificatie

Hier volgen enkele tips voor het vermijden van problemen wanneer u brokered-verificatie implementeert op Android:

- **Omleidings-URI** : Voeg een omleidings-URI toe aan de registratie van uw toepassing in de [Azure Portal](https://portal.azure.com/). Een ontbrekende of onjuiste omleidings-URI is een veelvoorkomend probleem bij ontwikkel aars.
- **Broker-versie** : Installeer de mini maal vereiste versie van de Broker-apps. Een van deze twee apps kan worden gebruikt voor brokered-verificatie op Android.
  - [Intune-bedrijfsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (versie 5.0.4689.0 of hoger)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (versie 6.2001.0140 of hoger).
- **Broker-prioriteit** : MSAL communiceert met de *eerste Broker* die op het apparaat is geïnstalleerd wanneer er meerdere brokers zijn geïnstalleerd.

    Voor beeld: als u Microsoft Authenticator voor het eerst installeert en vervolgens Intune-bedrijfsportal installeert, wordt de brokered-verificatie *alleen* uitgevoerd op de Microsoft Authenticator.
- **Logboeken** : als u een probleem ondervindt met brokered Authentication, kunt u de logboeken van de Broker raadplegen om de oorzaak te achterhalen.
  - Microsoft Authenticator logboeken weer geven:

    1. Selecteer de menu knop in de rechter bovenhoek van de app.
    1. Selecteer **Help**-  >  **Logboeken verzenden** logboeken  >  **weer geven**.
    1. Selecteer **Alles kopiëren** om de Broker logboeken te kopiëren naar het klem bord van het apparaat.

    De beste manier om fouten op te lossen met deze logboeken is door ze naar uzelf te sturen en ze weer te geven op uw ontwikkel computer. Het kan zijn dat u de logboeken op uw computer gemakkelijker kunt parseren in plaats van op het apparaat zelf. U kunt ook een test editor op Android gebruiken om de logboeken op te slaan als een tekst bestand en vervolgens een USB-kabel gebruiken om het bestand naar een computer te kopiëren.

  - Intune-bedrijfsportal logboeken weer geven:

    1. Selecteer de menu knop in de linkerbovenhoek van de app
    1.   >  **Diagnostische gegevens** voor instellingen selecteren
    1. Selecteer **Logboeken kopiëren** om de Broker-logboeken te kopiëren naar de SD-kaart van het apparaat.
    1. Verbind het apparaat met een computer met behulp van een USB-kabel om de logboeken op uw ontwikkel machine weer te geven.

    Zodra u de logboeken hebt, kunt u ze doorzoeken op uw verificatie pogingen via de correlatie-ID. De correlatie-ID is gekoppeld aan elke verificatie aanvraag. Als u fouten wilt vinden die worden geretourneerd door het micro soft Identity platform-verificatie-eind punt, zoekt u naar `AADSTS` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [overwegingen voor het gebruik van universeel Windows-platform met MSAL.net](msal-net-uwp-considerations.md).

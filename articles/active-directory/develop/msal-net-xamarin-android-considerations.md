---
title: Xamarin Android-code configuratie en probleem oplossing (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin Android met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 34f2b146dda6e739f977c4894b5ec333c79d74d4
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063430"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Configuratie vereisten en tips voor probleem oplossing voor Xamarin Android met MSAL.NET

Er zijn verschillende configuratie wijzigingen die u moet aanbrengen in uw code bij gebruik van Xamarin Android met de micro soft-verificatie bibliotheek voor .NET (MSAL.NET). In de volgende secties worden de vereiste wijzigingen beschreven, gevolgd door een sectie voor het [oplossen van problemen](#troubleshooting) , waarmee u een aantal van de meest voorkomende problemen kunt voor komen.

## <a name="set-the-parent-activity"></a>De bovenliggende activiteit instellen

Stel op Xamarin Android de bovenliggende activiteit in, zodat het token wordt geretourneerd na de interactie:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL.NET 4,2 en hoger kunt u deze functie ook instellen op het niveau van [PublicClientApplication][PublicClientApplication]. Gebruik hiervoor een call back:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Als u [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)gebruikt, moet de [`PublicClientApplication`][PublicClientApplication] code van de opbouw functie er ongeveer als volgt uitzien:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Controleer of het besturings element terugkeert naar MSAL

Wanneer het interactieve gedeelte van de verificatie stroom eindigt, keert u terug naar MSAL door de te vervangen [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] methode.

Roep MSAL aan in uw onderdrukking. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` methode om de controle te retour neren aan MSAL aan het einde van het interactieve deel van de verificatie stroom.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Het Android-manifest bijwerken

Het *AndroidManifest.xml* bestand moet de volgende waarden bevatten:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

Vervang de waarde door de naam van het pakket dat u hebt geregistreerd in het Azure Portal `android:host=` . Vervang de sleutel-hash die u hebt geregistreerd in het Azure Portal voor de `android:path=` waarde. De hash van de hand tekening mag *geen* URL-code ring zijn. Zorg ervoor dat er een voorloop back slash ( `/` ) aan het begin van uw hand tekening-hash wordt weer gegeven.

U kunt ook [de activiteit in code maken in](/xamarin/android/platform/android-manifest#the-basics) plaats van *AndroidManifest.xml* hand matig te bewerken. Als u de activiteit in code wilt maken, maakt u eerst een klasse die het `Activity` kenmerk en het `IntentFilter` kenmerk bevat.

Hier volgt een voor beeld van een klasse die de waarden van het XML-bestand vertegenwoordigt:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. x-manifest

Xamarin. Forms 4.3. x genereert code waarmee het `package` kenmerk wordt ingesteld `com.companyname.{appName}` in *AndroidManifest.xml*. Als u `DataScheme` als gebruikt `msal{client_id}` , wilt u mogelijk de waarde wijzigen zodat deze overeenkomt met de waarde van de `MainActivity.cs` naam ruimte.

## <a name="use-the-embedded-web-view-optional"></a>De Inge sloten webweergave gebruiken (optioneel)

MSAL.NET maakt standaard gebruik van de webbrowser van het systeem. Met deze browser kunt u eenmalige aanmelding (SSO) ophalen met behulp van webtoepassingen en andere apps. In sommige zeldzame gevallen kan het nodig zijn dat uw systeem gebruikmaakt van een Inge sloten webweergave.

Dit code voorbeeld laat zien hoe u een Inge sloten webweergave instelt:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Zie voor meer informatie [webbrowsers gebruiken voor MSAL.net](msal-net-web-browsers.md) en [Xamarin Android-systeem browser overwegingen](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="general-tips"></a>Algemene tips

- Werk het bestaande MSAL.NET NuGet-pakket bij naar de [nieuwste versie van MSAL.net](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Controleer of Xamarin. Forms de meest recente versie heeft.
- Controleer of Xamarin. Android. support. V4 zich op de meest recente versie bevindt.
- Zorg ervoor dat alle Xamarin. Android. Support-pakketten de meest recente versie hebben.
- De toepassing opschonen of opnieuw bouwen.
- In Visual Studio kunt u het maximum aantal parallelle project builds instellen op **1**. Hiertoe selecteert u **Opties**  >  **projecten en oplossingen**  >  **bouwen en voert** u het  >  **maximum aantal parallelle projecten-builds** uit.
- Als u bouwt vanaf de opdracht regel en de opdracht wordt gebruikt `/m` , verwijdert u dit element uit de opdracht.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Fout: de naam AuthenticationContinuationHelper bestaat niet in de huidige context

Als een fout aangeeft dat `AuthenticationContinuationHelper` niet aanwezig is in de huidige context, is het bestand *Android. csproj \** mogelijk niet goed bijgewerkt met Visual Studio. Soms bevat het bestandspad in het `<HintPath>` element onjuist in `netstandard13` plaats van `monoandroid90` .

Dit voor beeld bevat een juist bestandspad:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie het voor beeld van een [Xamarin Mobile-toepassing die gebruikmaakt van micro soft Identity platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). De volgende tabel bevat een overzicht van de relevante gegevens in het Leesmij-bestand.

| Voorbeeld | Platform | Beschrijving |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Een eenvoudige Xamarin. Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het Azure AD 2,0-eind punt. De app laat ook zien hoe u toegang krijgt tot Microsoft Graph en hoe het resulterende token wordt weer gegeven. <br>![Diagram van de verificatie stroom](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity

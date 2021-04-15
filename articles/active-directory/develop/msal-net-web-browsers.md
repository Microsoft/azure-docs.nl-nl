---
title: Webbrowsers (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Xamarin Android met de Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478757"
---
# <a name="using-web-browsers-msalnet"></a>Webbrowsers gebruiken (MSAL.NET)

Webbrowsers zijn vereist voor interactieve verificatie. Standaard biedt MSAL.NET ondersteuning voor de [systeemwebbrowser](#system-web-browser-on-xamarinios-xamarinandroid) op Xamarin.iOS en Xamarin.Android. Maar u kunt de [Embedded-webbrowser](#enable-embedded-webviews-on-ios-and-android) ook inschakelen, afhankelijk van uw vereisten (UX, eenmalige aanmelding (SSO), beveiliging) in [Xamarin.iOS-](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) en [Xamarin.Android-apps.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) En u kunt zelfs [dynamisch kiezen welke](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) webbrowser u wilt gebruiken op basis van de aanwezigheid van Chrome of een browser die aangepaste Chrome-tabbladen ondersteunt in Android. MSAL.NET ondersteunt alleen de systeembrowser in .NET Core-desktoptoepassingen.

## <a name="web-browsers-in-msalnet"></a>Webbrowsers in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interactie vindt plaats in een webbrowser

Het is belangrijk om te begrijpen dat bij het interactief ophalen van een token de inhoud van het dialoogvenster niet wordt geleverd door de bibliotheek, maar door de STS (Security Token Service). Het verificatie-eindpunt stuurt wat HTML en JavaScript terug waarmee de interactie wordt bestuurd, die wordt weergegeven in een webbrowser of webbesturingselement. Het toestaan van de STS voor het afhandelen van de HTML-interactie heeft veel voordelen:

- Het wachtwoord (als er een is getypt) wordt nooit opgeslagen door de toepassing, noch door de verificatiebibliotheek.
- Hiermee kunt u omleidingen naar andere id-providers (bijvoorbeeld aanmelden met een werkaccount of een persoonlijk account met MSAL, of met een sociaal account met Azure AD B2C).
- Hiermee kan de STS voorwaardelijke toegang bepalen, bijvoorbeeld door de gebruiker [multi-factor authentication (MFA)](../authentication/concept-mfa-howitworks.md) te laten doen tijdens de verificatiefase (door een pincode voor Windows Hello in te stellen, op zijn telefoon te worden aangeroepen of in een verificatie-app op de telefoon). In gevallen waarin de vereiste meervoudige verificatie nog niet is ingesteld, kan de gebruiker deze just-in-time instellen in hetzelfde dialoogvenster.  De gebruiker voert zijn mobiele telefoonnummer in en wordt begeleid bij het installeren van een verificatietoepassing en het scannen van een QR-tag om zijn of haar account toe te voegen. Deze servergestuurde interactie is een fantastische ervaring!
- Hiermee kunnen gebruikers hun wachtwoord in hetzelfde dialoogvenster wijzigen wanneer het wachtwoord is verlopen (met aanvullende velden voor het oude en nieuwe wachtwoord).
- Hiermee schakelt u de huisstijl van de tenant of de toepassing (afbeeldingen) in die wordt beheerd door de Azure AD-tenantbeheerder/toepassingseigenaar.
- Hiermee kunnen de gebruikers toestemming geven om de toepassing toegang te geven tot resources/bereiken in hun naam, net na de verificatie.

### <a name="embedded-vs-system-web-ui"></a>Ingesloten versus webinterface van systeem

MSAL.NET is een bibliotheek met meerdere frameworks en heeft frameworkspecifieke code voor het hosten van een browser in een UI-besturingselement (in .NET Classic wordt bijvoorbeeld WinForms gebruikt, op Xamarin worden systeemeigen mobiele besturingselementen enzovoort gebruikt). Dit besturingselement wordt `embedded` webinterface genoemd. U kunt ook MSAL.NET de browser van het systeemsysteem te openen.

Over het algemeen is het raadzaam om de standaardinstelling van het platform te gebruiken. Dit is doorgaans de systeembrowser. De systeembrowser is beter in het onthouden van de gebruikers die zich eerder hebben aangemeld. Als u dit gedrag wilt wijzigen, gebruikt u `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>In een oogopslag

| Framework        | Ingesloten | Systeem | Standaard |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ja | Ja^ | Ingesloten |
| .NET Core     | Nee | Ja^ | Systeem |
| .NET Standard | Nee | Ja^ | Systeem |
| UWP | Ja | Nee | Ingesloten |
| Xamarin.Android | Ja | Ja  | Systeem |
| Xamarin.iOS | Ja | Ja  | Systeem |
| Xamarin.Mac| Ja | Nee | Ingesloten |

^ Vereist " http://localhost " omleidings-URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systeemwebbrowser op Xamarin.iOS, Xamarin.Android

Standaard ondersteunt MSAL.NET de systeemwebbrowser op Xamarin.iOS, Xamarin.Android en .NET Core. Voor alle platforms die de gebruikersinterface bieden (dat wil zeggen, niet .NET Core), wordt er een dialoogvenster aangeboden door de bibliotheek die een webbrowserbesturingselement insluit. MSAL.NET maakt ook gebruik van een ingesloten webweergave voor .NET Desktop en WAB voor het UWP-platform. Het maakt echter standaard gebruik van de **systeemwebbrowser** voor Xamarin iOS- en Xamarin Android-toepassingen. In iOS wordt zelfs de webweergave gekozen die moet worden gebruikt, afhankelijk van de versie van het besturingssysteem (iOS12, iOS11 en eerder).

Het gebruik van de systeembrowser heeft het grote voordeel van het delen van de SSO-status met andere toepassingen en met webtoepassingen zonder dat u een broker nodig hebt (bedrijfsportal/Authenticator). De systeembrowser is standaard gebruikt in MSAL.NET voor de Xamarin iOS- en Xamarin Android-platforms, omdat op deze platforms de webbrowser van het systeem het hele scherm in beslag neemt en de gebruikerservaring beter is. De webweergave van het systeem is niet te onderscheiden van een dialoogvenster. In iOS moet de gebruiker echter mogelijk toestemming geven dat de browser de toepassing terugroept, wat lastig kan zijn.

## <a name="system-browser-experience-on-net"></a>Systeembrowserervaring op .NET 

In .NET Core start MSAL.NET systeembrowser als een afzonderlijk proces. MSAL.NET heeft geen controle over deze browser, maar zodra de gebruiker de verificatie heeft gedaan, wordt de webpagina zodanig omgeleid dat MSAL.NET de URI kan onderscheppen.

U kunt ook apps configureren die zijn geschreven voor .NET Classic of .NET 5 om deze browser te gebruiken door het volgende op te geven:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan niet detecteren of de gebruiker weg navigeert of gewoon de browser sluit. Apps die deze techniek gebruiken, worden aangemoedigd om een time-out te definiëren (via `CancellationToken` ). We raden een time-out van ten minste een paar minuten aan om rekening te houden met gevallen waarin de gebruiker wordt gevraagd het wachtwoord te wijzigen of meervoudige verificatie uit te voeren.

### <a name="how-to-use-the-default-os-browser"></a>De standaardbesturingssysteembrowser gebruiken

MSAL.NET moet luisteren en de code onderscheppen die AAD verzendt wanneer de gebruiker klaar is met verificatie `http://localhost:port` (zie [Autorisatiecode](v2-oauth2-auth-code-flow.md) voor meer informatie)

De systeembrowser inschakelen:

1. Configureer tijdens de registratie `http://localhost` van de app als een omleidings-URI (momenteel niet ondersteund door B2C)
2. Wanneer u uw PublicClientApplication maakt, geeft u deze omleidings-URI op:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Als u `http://localhost` configureert, MSAL.NET een willekeurige open poort en gebruikt u deze.

### <a name="linux-and-mac"></a>Linux en MAC

In Linux opent MSAL.NET de standaardbrowser van het besturingssysteem met behulp van het hulpprogramma xdg-open. Als u problemen wilt oplossen, moet u het hulpprogramma uitvoeren vanuit een terminal, bijvoorbeeld `xdg-open "https://www.bing.com"` . Op de Mac wordt de browser geopend door aan `open <url>` teroepen.

### <a name="customizing-the-experience"></a>De ervaring aanpassen

> [!NOTE]
> Aanpassing is beschikbaar in MSAL.NET 4.1.0 of hoger.

MSAL.NET kan reageren met een HTTP-bericht wanneer een token wordt ontvangen of als er een fout is opgetreden. U kunt een HTML-bericht weergeven of omleiden naar een URL van uw keuze:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Een specifieke browser openen (experimenteel)

U kunt de manier aanpassen waarop MSAL.NET browser wordt geopend. In plaats van de standaardbrowser te gebruiken, kunt u bijvoorbeeld het openen van een specifieke browser forcen:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP maakt geen gebruik van System WebView

Voor desktoptoepassingen leidt het starten van een Systeemwebweergave echter tot een subpargebruikerservaring, omdat de gebruiker de browser ziet, waar mogelijk al andere tabbladen zijn geopend. En wanneer verificatie is gebeurd, krijgen de gebruikers een pagina waarin ze wordt gevraagd dit venster te sluiten. Als de gebruiker niet oplet, kan deze het hele proces sluiten (inclusief andere tabbladen die geen verband houden met de verificatie). Als u de systeembrowser op het bureaublad gebruikt, moet u ook lokale poorten openen en er naar luisteren. Dit kan geavanceerde machtigingen voor de toepassing vereisen. U, als ontwikkelaar, gebruiker of beheerder, is mogelijk onvoorwaardef over deze vereiste.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Ingesloten webweergaven inschakelen op iOS en Android

U kunt ook ingesloten webweergaven inschakelen in Xamarin.iOS- en Xamarin.Android-apps. Vanaf MSAL.NET 2.0.0-preview biedt MSAL.NET ook ondersteuning voor het gebruik van de **ingesloten** webweergaveoptie. Voor ADAL.NET is ingesloten webweergave de enige optie die wordt ondersteund.

Als ontwikkelaar die gebruik MSAL.NET voor Xamarin, kunt u ervoor kiezen om ingesloten webweergaven of systeembrowsers te gebruiken. Dit is uw keuze, afhankelijk van de gebruikerservaring en beveiligingsproblemen die u wilt bereiken.

Momenteel biedt MSAL.NET nog geen ondersteuning voor de Android- en iOS-brokers. Om eenmalige aanmelding (SSO) te bieden, is de systeembrowser mogelijk nog steeds een betere optie. Ondersteuning van brokers met de ingesloten webbrowser staat op de MSAL.NET backlog.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Verschillen tussen ingesloten webweergave en systeembrowser
Er zijn enkele visuele verschillen tussen ingesloten webweergave en systeembrowser in MSAL.NET.

**Interactieve aanmelding met MSAL.NET met behulp van de ingesloten webweergave:**

![ingesloten](media/msal-net-web-browsers/embedded-webview.png)

**Interactief aanmelden met MSAL.NET met de System Browser:**

![Systeembrowser](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Ontwikkelaarsopties

Als ontwikkelaar die gebruik MSAL.NET, hebt u verschillende opties voor het weergeven van het interactieve dialoogvenster vanuit STS:

- **Systeembrowser.** De systeembrowser is standaard ingesteld in de bibliotheek. Als u Android gebruikt, leest [u systeembrowsers](msal-net-system-browser-android-considerations.md) voor specifieke informatie over welke browsers worden ondersteund voor verificatie. Wanneer u de systeembrowser in Android gebruikt, raden we u aan dat het apparaat een browser heeft die aangepaste Chrome-tabbladen ondersteunt.  Anders kan de verificatie mislukken.
- **Ingesloten webweergave.** Als u alleen ingesloten webweergave in MSAL.NET, bevat de `AcquireTokenInteractively` parametersbouwer een `WithUseEmbeddedWebView()` -methode.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Kiezen tussen ingesloten webbrowser of systeembrowser in Xamarin.iOS

In uw iOS-app kunt u de `AppDelegate.cs` initialiseren `ParentWindow` in `null` . Deze wordt niet gebruikt in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Kiezen tussen ingesloten webbrowser of systeembrowser op Xamarin.Android

In uw Android-app kunt u de bovenliggende activiteit instellen, zodat het verificatieresultaat `MainActivity.cs` weer wordt gebruikt:

```csharp
 App.ParentWindow = this;
```

Klik vervolgens in `MainPage.xaml.cs` de :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>De aanwezigheid van aangepaste tabbladen op Xamarin.Android detecteren

Als u de systeemwebbrowser wilt gebruiken om eenmalige aanmelding in te stellen voor de apps die worden uitgevoerd in de browser, maar u zich zorgen maakt over de gebruikerservaring voor Android-apparaten die geen browser met ondersteuning voor aangepaste tabbladen hebben, hebt u de mogelijkheid om te beslissen door de methode aan te roepen `IsSystemWebViewAvailable()` in `IPublicClientApplication` . Deze methode retourneert als PackageManager aangepaste tabbladen detecteert en als deze niet `true` zijn gedetecteerd op het `false` apparaat.

Op basis van de waarde die door deze methode wordt geretourneerd en uw vereisten, kunt u een beslissing nemen:

- U kunt een aangepast foutbericht retourneren aan de gebruiker. Bijvoorbeeld: "Installeer Chrome om door te gaan met verificatie" -OR-
- U kunt teruggaan naar de optie ingesloten webweergave en de gebruikersinterface starten als een ingesloten webweergave.

In de onderstaande code ziet u de ingesloten webweergaveoptie:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core biedt geen ondersteuning voor interactieve verificatie met een ingesloten browser

Voor .NET Core is het interactief verkrijgen van tokens alleen beschikbaar via de systeemwebbrowser, niet met ingesloten webweergaven. .NET Core biedt nog geen gebruikersinterface.
Als u de browserervaring met de webbrowser van het systeem wilt aanpassen, kunt u de [IWithCustomUI-interface](scenario-desktop-acquire-token.md#withcustomwebui) implementeren en zelfs uw eigen browser leveren.

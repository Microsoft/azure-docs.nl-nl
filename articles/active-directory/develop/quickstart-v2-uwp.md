---
title: 'Snelstart: Gebruikers aanmelden en Microsoft Graph aanroepen in een Universele Windows-platform-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze snelstart leert u hoe met een universele Windows-platform (UWP)-toepassing een toegangstoken kan worden opgehaald en een API kan worden aangeroepen die is beveiligd door een Microsoft-identiteitsplatform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 8a016aa6690243ef2b933a10bba44af7cfe81a5b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583108"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Quickstart: De Microsoft Graph-API aanroepen vanuit de Universeel Windows-platformtoepasing (UWP)

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een UWP-toepassing (Universeel Windows-platform), en een toegangstoken kunnen krijgen om de Microsoft Graph API aan te roepen. 

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Vereisten
>
> * Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> [!div renderon="docs" class="sxs-lookup"]
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure Portal - App-registraties<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app toe te voegen aan de oplossing:
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com**.
> 1. Selecteer **Registreren** om de toepassing te maken en noteer vervolgens de **Toepassings(client)-id** die u in een latere stap gebruikt.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** > **Mobiele en desktoptoepassingen**.
> 1. Selecteer `https://login.microsoftonline.com/common/oauth2/nativeclient` onder **Omleidings-URI's**.
> 1. Selecteer **Configureren**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Stap 1: De toepassing configureren
> Voor het code voorbeeld in deze Snelstartgids werkt u een **omleidings-URI** van toe `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-uwp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-visual-studio-project"></a>Stap 2: Download het Visual Studio-project

> [!div renderon="docs"]
> [Download het Visual Studio-project](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit met Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Stap 3: Het Visual Studio-project configureren
>
> 1. Pak het zip-archief uit in een lokale map in de buurt van de hoofdmap van uw station. Bijvoorbeeld in **C:\Azure-Samples**.
> 1. Open het project in Visual Studio. Installeer de **Universeel Windows-platform-ontwikkeling**-werkbelasting en eventuele afzonderlijke SDK-onderdelen, als u hierom wordt gevraagd.
> 1. Wijzig in *MainPage.Xaml.cs* de waarde van de variabele `ClientId` naar de **Toepassings(client)-id** van de toepassing die u eerder hebt geregistreerd.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    U kunt de **Toepassings(client)-id** vinden in het deelvenster **Overzicht** van de app in het de Azure-portal (**Azure Active Directory** > **App-registraties** >  *{Uw app-registratie}* ).
> 1. Maak en selecteer vervolgens een nieuw zelfondertekend testcertificaat voor het pakket:
>     1. Dubbelklik in de **Solution Explorer** op het bestand *Package.appxmanifest*.
>     1. Selecteer **Verpakken** > **Certificaat kiezen...**  > **Maken...** .
>     1. Voer een wachtwoord in en selecteer **OK**.
>     1. Selecteer **Selecteren uit bestand...** , en selecteer vervolgens het bestand *Native_UWP_V2_TemporaryKey.pfx* dat u zojuist hebt gemaakt en selecteer **OK**.
>     1. Sluit het bestand *Package.appxmanifest* (selecteer **OK** als u wordt gevraagd het bestand op te slaan).
>     1. Klik in de **Solution Explorer** met de rechtermuisknop op het project **Native_UWP_V2** en selecteer **Eigenschappen**.
>     1. Selecteer **Ondertekenen** en selecteer vervolgens het pfx-bestand dat u hebt gemaakt in de vervolgkeuzelijst **Kies een sleutel met een sterke naam**.

#### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

Om de voorbeeldtoepassing uit te voeren op uw lokale computer:

1. Kies op de werkbalk van Visual Studio het juiste platform (waarschijnlijk **x64** of **x86**, niet ARM). Het doelapparaat moet worden gewijzigd van *Apparaat* naar *Lokale machine*.
1. Selecteer **Fouten opsporen** > **Starten zonder foutopsporing**.
    
    Als u hierom wordt gevraagd, moet u mogelijk eerst **Ontwikkelaarsmodus** inschakelen en vervolgens opnieuw **Starten zonder foutopsporing** om de app te starten.

Wanneer het venster van de app wordt weergegeven, kunt u de knop **Microsoft Graph-API aanroepen** selecteren, uw referenties invoeren en akkoord gaan met de machtigingen die door de toepassing worden aangevraagd. Als dit lukt, worden in de toepassing bepaalde tokengegevens en andere gegevens weergegeven die zijn verkregen van de aanroep van de Microsoft Graph-API.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en beveiligingstokens aan te vragen. De beveiligings tokens worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door het micro soft Identity-platform. U kunt MSAL installeren door de volgende opdracht uit te voeren in *Package Manager Console* van Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

Vervolgens wordt MSAL geïnitialiseerd met de volgende code:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

De waarde van `ClientId` is de **Toepassings(client)-id** voor de toepassing die u hebt geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal.

### <a name="requesting-tokens"></a>Tokens aanvragen

MSAL biedt twee methoden om tokens in een UWP-app te verkrijgen: `AcquireTokenInteractive` en `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

In sommige situaties moeten gebruikers met het micro soft-identiteits platform communiceren via een pop-upvenster om hun referenties te valideren of om toestemming te geven. Voorbeelden zijn:

- De eerste keer dat gebruikers zich aanmelden bij de toepassing
- Wanneer gebruikers mogelijk hun referenties opnieuw moeten opgeven omdat het wachtwoord is verlopen
- Wanneer via de toepassing toegang wordt aangevraagd tot een resource waarvoor de gebruiker toestemming moet geven
- Wanneer tweeledige verificatie is vereist

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

De parameter `scopes` bevat de bereiken die worden aangevraagd, zoals `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Gebruik de methode `AcquireTokenSilent` om tokens op te halen voor toegang tot beveiligde resources na de eerste methode `AcquireTokenInteractive`. U wilt niet dat de gebruiker telkens wanneer deze toegang nodig heeft tot een resource, de referenties moet laten valideren. In de meeste gevallen wilt u tokens ophalen en verlengen zonder tussenkomst van de gebruiker

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` bevat de bereiken die worden aangevraagd, bijvoorbeeld `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's.
* `firstAccount` geeft het eerste gebruikersaccount in de cache op (MSAL biedt ondersteuning voor meerdere gebruikers in één app).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelfstudie voor Windows-bureaublad voor een volledige stapsgewijze handleiding voor het bouwen van toepassingen en nieuwe functies, waaronder een volledige uitleg van deze quickstart.

> [!div class="nextstepaction"]
> [UWP - Zelfstudie voor het aanroepen van Graph API](tutorial-v2-windows-uwp.md)

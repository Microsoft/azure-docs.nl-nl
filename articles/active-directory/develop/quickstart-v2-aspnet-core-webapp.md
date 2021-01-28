---
title: 'Snelstart: Aanmelding met Microsoft toevoegen aan een ASP.NET Core-web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe een app Microsoft-aanmelding implementeert in een ASP.NET Core-web-app met behulp van OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 05e14b5bdc2f603ffe802b12ed33b7b57be25b69
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938204"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: aanmelding met Microsoft toevoegen aan een ASP.NET Core-web-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen worden aangemeld met een ASP.NET Core-web-app.  

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Vereisten
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) of [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure Portal - App-registraties<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Voer een **omleidings-URI** van in `https://localhost:44321/` .
> 1. Selecteer **Registreren**.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer onder **omleidings-uri's** de optie **URI toevoegen** en voer vervolgens in `https://localhost:44321/signin-oidc` .
> 1. Voer een **URL in voor het afmelden** van het kanaal van `https://localhost:44321/signout-oidc` .
> 1. Selecteer **id-tokens** onder **Impliciete toekenning**.
> 1. Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> De voorbeeld code van deze Snelstartgids vereist een **omleidings-URI** van `https://localhost:44321/` en `https://localhost:44321/signin-oidc` en een **URL voor het afmelden van het voor kanaal** van `https://localhost:44321/signout-oidc` . Tokens voor aanvraag-id's worden uitgegeven door het autorisatie-eind punt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-aspnet-core-project"></a>Stap 2: uw ASP.NET Core-project downloaden

> [!div renderon="docs"]
> [De ASP.NET Core-oplossing downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Voer het project uit.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Stap 3: uw ASP.NET Core-project configureren
> 1. Pak het zip-archief uit in een lokale map in de buurt van de hoofdmap van uw station. Bijvoorbeeld in *C:\Azure-Samples*.
> 1. Open de oplossing in Visual Studio.
> 1. Open het bestand *appSettings.json* en wijzig het volgende:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Vervang `Enter_the_Application_Id_here`met de **Toepassings(client)-ID** voor de toepassing die is geregistreerd in de Azure-portal. U vindt de **toepassings-id (client-id)** op de pagina **Overzicht** van de app.
>    - Vervang `common` door een van de volgende opties:
>       - Als uw toepassing **Accounts alleen in deze organisatiemap** ondersteunt, vervangt u deze waarde door de **Map (tenant)-ID**  (een GUID) of  **tenantnaam** (bijvoorbeeld, `contoso.onmicrosoft.com`). U vindt de **Directory (Tenant)-ID** op de pagina **Overzicht** van de apps.
>       - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>       - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
>
> Wijzig voor deze Snelstart geen andere waarden in het bestand  *appSettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Stap 4: de toepassing bouwen en uitvoeren
>
> Bouw en voer de app in Visual Studio uit door het menu **Foutopsporing** te selecteren > **Foutopsporing starten** of door op de `F5`-toets te drukken.
>
> U wordt gevraagd om uw referenties op te geven, vervolgens wordt u gevraagd om toestemming te geven voor de machtigingen die uw app nodig heeft. Selecteer **Accepteren** op de toestemmingprompt.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Dialoogvenster Toestemming met de machtigingen die de app aanvraagt bij de > gebruiker":::
>
> Nadat u de gewenste machtigingen hebt opgegeven, wordt in de app weer gegeven dat u bent aangemeld met uw referenties voor Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Webbrowser met de actieve web-app en de gebruiker die is aangemeld":::

## <a name="more-information"></a>Meer informatie

Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en ook als u aanmelding wilt toevoegen aan een bestaande ASP.NET Core-toepassing.

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Opstartklasse

De *Microsoft.AspNetCore.Authentication*-middleware maakt gebruik van een `Startup` klasse die wordt uitgevoerd wanneer in het hostingproces het volgende wordt geïnitialiseerd:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Met de methode `AddAuthentication()` wordt de service geconfigureerd voor het toevoegen van verificatie op basis van cookies. Deze verificatie wordt gebruikt in browserscenario's en om vragen te sturen naar OpenID Connect.

Met de regel die `.AddMicrosoftIdentityWebApp` bevat, wordt het Microsoft-identiteitsplatform toegevoegd aan uw toepassing. Deze wordt vervolgens geconfigureerd om u aan te melden met het micro soft-identiteits platform op basis van de informatie in de `AzureAD` sectie van de *appsettings.jsin* het configuratie bestand:

| Sleutel *appSettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Toepassings(client)-ID van de toepassing die is geregistreerd in de Azure-portal.                                                                                       |
| `Instance`             | STS-eindpunt (Security Token Service) voor de gebruiker te verifiëren. Deze waarde is doorgaans `https://login.microsoftonline.com/`, wat de openbare Azure-cloud aangeeft. |
| `TenantId`             | De naam van uw tenant of de tenant-ID (een GUID) of *algemene* om gebruikers met werk- of schoolaccounts of persoonlijke Microsoft-accounts aan te melden.                             |

De methode `Configure()` bevat twee belangrijke methoden, `app.UseAuthentication()` en `app.UseAuthorization()`, waarmee de benoemde functionaliteit kan worden ingeschakeld. In de `Configure()`-methode moet u ook de routes van Microsoft Identity Web registreren met ten minste één aanroep naar `endpoints.MapControllerRoute()` of `endpoints.MapControllers()`.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Een controller of de methode van een controller beveiligen

U kunt een controller of controllermethoden beveiligen met behulp van het kenmerk `[Authorize]`. Met dit kenmerk wordt toegang tot de controller of methoden beperkt door alleen geverifieerde gebruikers toe te staan. Dit betekent dat de verificatiecontrole kan worden gestart om de controller te benaderen als de gebruiker niet is geverifieerd.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

De GitHub-opslagplaats die deze ASP.NET Core-handleiding bevat, bevatten instructies en meer codevoorbeelden die laten zien hoe u het volgende kunt doen:

- Verificatie toevoegen aan een nieuwe ASP.NET Core Web-toepassing
- Microsoft Graph, andere Microsoft-API's of uw eigen web-API's aanroepen
- Autorisatie toevoegen
- Gebruikers aanmelden bij nationale clouds of met sociale identiteiten

> [!div class="nextstepaction"]
> [ASP.NET Core Web-app-handleidingen op GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

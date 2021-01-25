---
title: 'Quickstart: ASP.NET Core-web-app om gebruikers aan te melden en Microsoft Graph aan te roepen | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leer u hoe een app Microsoft.Identity.Web gebruikt om Microsoft-aanmelding te implementeren in een ASP.NET Core-web-app met behulp van OpenID Connect, en hoe de app Microsoft Graph aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 8e54f71ef58b3ea76a5fe55347a1caa173046320
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754497"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Quickstart: ASP.NET Core-web-app om gebruikers aan te melden en namens hen Microsoft Graph aan te roepen

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen worden aangemeld met een ASP.NET Core-web-app hoe deze web-app Microsoft Graph aanroept.  

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Vereisten
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) of [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal - App-registraties<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
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
> 1. Voer een **omleidings-URI** van in `https://localhost:44321/signin-oidc` .
> 1. Selecteer **Registreren**.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Voer een **Afmeldings-URL** van in `https://localhost:44321/signout-oidc` .
> 1. Selecteer **Opslaan**.
> 1. Selecteer onder **Beheren** achtereenvolgens **Certificaten en geheimen** > **Nieuw clientgeheim**.
> 1. Voer een **Beschrijving** in, bijvoorbeeld `clientsecret1`.
> 1. Selecteer **Over 1 jaar** als vervaldatum voor het geheim.
> 1. Selecteer **Toevoegen** en noteer onmiddellijk de **Waarde** voor het geheim voor gebruik in een latere stap. De waarde voor het geheim wordt *nooit opnieuw weergegeven* en kan niet op enige andere manier worden verkregen. Sla deze op een veilige locatie op, zoals u ook met elk ander wachtwoord zou doen.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voor een juiste werking van het codevoorbeeld uit deze quickstart moet u antwoord-URL's toevoegen als `https://localhost:44321/signin-oidc`. Voeg de Afmeldings-URL toe als `https://localhost:44321/signout-oidc`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-aspnet-core-project"></a>Stap 2: De ASP.NET Core-oplossing downloaden

> [!div renderon="docs"]
> [De ASP.NET Core-oplossing downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Voer het project uit.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

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
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Vervang `Enter_the_Application_Id_here`met de **Toepassings(client)-ID** voor de toepassing die is geregistreerd in de Azure-portal. U vindt de **toepassings-id (client-id)** op de pagina **Overzicht** van de app.
>    - Vervang `common` door een van de volgende opties:
>       - Als uw toepassing **Accounts alleen in deze organisatiemap** ondersteunt, vervangt u deze waarde door de **Map (tenant)-ID**  (een GUID) of  **tenantnaam** (bijvoorbeeld, `contoso.onmicrosoft.com`). U vindt de **Directory (Tenant)-ID** op de pagina **Overzicht** van de apps.
>       - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>       - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
>    - Vervang `Enter_the_Client_Secret_Here` door het **Clientgeheim** dat u hebt gemaakt en genoteerd in een eerdere stap.
> 
> Wijzig voor deze Snelstart geen andere waarden in het bestand  *appSettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Stap 4: de toepassing bouwen en uitvoeren
>
> Bouw en voer de app in Visual Studio uit door het menu **Foutopsporing** te selecteren > **Foutopsporing starten** of door op de `F5`-toets te drukken.
>
> U wordt gevraagd om uw referenties op te geven, vervolgens wordt u gevraagd om toestemming te geven voor de machtigingen die uw app nodig heeft. Selecteer **Accepteren** op de toestemmingprompt.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Dialoogvenster Toestemming met de machtigingen die de app aanvraagt bij de > gebruiker":::
>
> Nadat u toestemming hebt gegeven voor de gewenste machtigingen, is in de app te zien dat u bent aangemeld met uw Azure Active Directory-referenties. In de sectie API-resultaat op de pagina ziet u uw e-mailadres. Dit adres is opgehaald met behulp van Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Webbrowser met de actieve web-app en de gebruiker die is aangemeld":::

## <a name="about-the-code"></a>Over de code

Deze sectie biedt een overzicht van de code die is vereist om gebruikers aan te melden en namens hen de Microsoft Graph-API aan te roepen. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn, en ook als u aanmelding wilt toevoegen aan een bestaande ASP.NET Core-toepassing en Microsoft Graph wilt aanroepen. Er wordt gebruikgemaakt van [Microsoft.Identity.Web](microsoft-identity-web.md). Dit is een wrapper rondom [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Opstartklasse

De *Microsoft.AspNetCore.Authentication*-middleware maakt gebruik van een `Startup` klasse die wordt uitgevoerd wanneer in het hostingproces het volgende wordt geïnitialiseerd:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Met de methode `AddAuthentication()` wordt de service geconfigureerd voor het toevoegen van verificatie op basis van cookies. Deze verificatie wordt gebruikt in browserscenario's en om vragen te sturen naar OpenID Connect.

Met de regel die `.AddMicrosoftIdentityWebApp` bevat, wordt het Microsoft-identiteitsplatform toegevoegd aan uw toepassing. Deze wordt geleverd met [Microsoft.Identity.Web](microsoft-identity-web.md). Deze wordt vervolgens geconfigureerd om u aan te melden met het micro soft-identiteits platform op basis van de informatie in de `AzureAD` sectie van de *appsettings.jsin* het configuratie bestand:

| Sleutel *appSettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Toepassings(client)-ID van de toepassing die is geregistreerd in de Azure-portal.                                                                                       |
| `Instance`             | STS-eindpunt (Security Token Service) voor de gebruiker te verifiëren. Deze waarde is doorgaans `https://login.microsoftonline.com/`, wat de openbare Azure-cloud aangeeft. |
| `TenantId`             | De naam van uw tenant of de tenant-ID (een GUID) of *algemene* om gebruikers met werk- of schoolaccounts of persoonlijke Microsoft-accounts aan te melden.                             |

Met de methode `EnableTokenAcquisitionToCallDownstreamApi` kan uw toepassing een token verkrijgen om beveiligde web-API's aan te roepen. Met `AddMicrosoftGraph` kunnen uw controllers of Razor-pagina's rechtstreeks profiteren. Met de methoden `GraphServiceClient` (door afhankelijkheidsinjectie) en `AddInMemoryTokenCaches` kan de app profiteren van een tokencache.

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

U kunt een controller of de bijbehorende methoden beveiligen door het `[Authorize]`-kenmerk toe te passen op de klasse van de controller of een of meer van de bijbehorende methoden. Met dit `[Authorize]`-kenmerk wordt de toegang beperkt, doordat alleen geverifieerde gebruikers toegang krijgen. Als de gebruiker nog niet is geverifieerd, kan een verificatievraag worden gestart om toegang te krijgen tot de controller. In deze quickstart worden de bereiken gelezen uit het configuratiebestand:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

De GitHub-opslagplaats met het ASP.NET Core-codevoorbeeld waarnaar wordt verwezen in deze quickstart, bevat instructies en meer codevoorbeelden die laten zien hoe u het volgende kunt doen:

- Verificatie toevoegen aan een nieuwe ASP.NET Core Web-toepassing
- Microsoft Graph, andere Microsoft-API's of uw eigen web-API's aanroepen
- Autorisatie toevoegen
- Gebruikers aanmelden bij nationale clouds of met sociale identiteiten

> [!div class="nextstepaction"]
> [ASP.NET Core Web-app-handleidingen op GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

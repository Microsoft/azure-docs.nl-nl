---
title: 'Snelstart: Aanmelding met Microsoft toevoegen aan een ASP.NET Core-web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe een app micro soft-aanmelding implementeert op een ASP.NET Core web-app met behulp van OpenID Connect Connect
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
ms.openlocfilehash: d0d3154d123b5e073a4eadf976d5259d51972da8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436478"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: aanmelding met Microsoft toevoegen aan een ASP.NET Core-web-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen worden aangemeld met een ASP.NET Core-web-app.  

> [!div renderon="docs"]
> In het volgende diagram ziet u hoe de voor beeld-app werkt:
>
> ![Diagram van de interactie tussen de webbrowser, de web-app en het micro soft Identity-platform in de voor beeld-app.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Vereisten
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) of [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>De app registreren en downloaden
> U hebt twee opties om uw toepassing te bouwen: automatische of hand matige configuratie.
>
> ### <a name="automatic-configuration"></a>Automatische configuratie
> Als u uw app automatisch wilt configureren en vervolgens het code voorbeeld wilt downloaden, voert u de volgende stappen uit:
>
> 1. Ga naar de <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">pagina Azure portal voor app-registratie</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies voor het downloaden en automatisch configureren van uw nieuwe toepassing met één klik.
>
> ### <a name="manual-configuration"></a>Handmatige configuratie
> Als u uw toepassings-en code voorbeeld hand matig wilt configureren, gebruikt u de volgende procedures.
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter voor **adres lijst en abonnementen** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de Tenant te selecteren waarin u de toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer bij **naam** een naam in voor uw toepassing. Voer bijvoorbeeld **AspNetCore-Quick** start in. Gebruikers van uw app krijgen deze naam te zien en u kunt deze later wijzigen.
> 1. Voer voor **omleidings-URI** in **https://localhost:44321/signin-oidc** .
> 1. Selecteer **Registreren**.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Voer voor de **afmeldings-URL voor front Channel** in **https://localhost:44321/signout-oidc** .
> 1. Onder **impliciete toekenning en hybride stromen** selecteert u **id-tokens**.
> 1. Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Het code voorbeeld in deze Snelstartgids werkt als volgt:
> - Voer voor **omleidings-URI** **https://localhost:44321/** en in **https://localhost:44321/signin-oidc** .
> - Voer voor de **afmeldings-URL voor front Channel** in **https://localhost:44321/signout-oidc** . 
>
> Het autorisatie-eind punt geeft tokens van aanvraag-ID uit.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-aspnet-core-project"></a>Stap 2: De ASP.NET Core-oplossing downloaden

> [!div renderon="docs"]
> [De ASP.NET Core-oplossing downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Voer het project uit.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en kan worden uitgevoerd.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Stap 3: uw ASP.NET Core-project configureren
> 1. Pak het zip-archief uit in een lokale map in de buurt van de hoofdmap van uw station. U kunt bijvoorbeeld extra heren in *C:\Azure-samples*.
> 
>    U kunt het archief het beste uitpakken in een directory in de buurt van de hoofdmap van uw station om fouten te voor komen die worden veroorzaakt door beperkingen van de padlengte in Windows.
> 1. Open de oplossing in Visual Studio.
> 1. Open de *appsettings.jsin* het bestand en wijzig de volgende code:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Vervang door `Enter_the_Application_Id_here` de client-id van de toepassing die u hebt geregistreerd in het Azure Portal. U vindt de waarde voor de **toepassings-id (client)** op de pagina **overzicht** van de app.
>    - Vervang `common` door een van de volgende opties:
>       - Als uw toepassing **alleen accounts in deze organisatie Directory** ondersteunt, vervangt u deze waarde door de map (TENANT) ID (GUID) of de naam van de Tenant (bijvoorbeeld `contoso.onmicrosoft.com` ). U kunt de waarde **Directory-id (Tenant)** vinden op de **overzichts** pagina van de app.
>       - Als uw toepassing **accounts in een organisatorische Directory** ondersteunt, vervangt u deze waarde door `organizations` .
>       - Als uw toepassing **alle Microsoft-account gebruikers** ondersteunt, verlaat u deze waarde als `common` .
>
> Wijzig voor deze Snelstart geen andere waarden in het bestand  *appSettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Stap 4: de toepassing bouwen en uitvoeren
>
> Bouw en voer de app in Visual Studio uit door het menu **fout opsporing** te selecteren > **fout opsporing te starten** of door op de toets F5 te drukken.
>
> U wordt gevraagd om uw referenties op te geven en u wordt vervolgens gevraagd toestemming te geven voor de machtigingen die uw app nodig heeft. Selecteer **Accepteren** op de toestemmingprompt.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Scherm afbeelding van het dialoog venster toestemming met de machtigingen die de app bij de gebruiker aanvraagt.":::
>
> Nadat u toestemming hebt gegeven voor de aangevraagde machtigingen, geeft de app aan dat u bent aangemeld met uw Azure Active Directory referenties.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Scherm afbeelding van een webbrowser waarin de actieve web-app en de aangemelde gebruiker worden weer gegeven.":::

## <a name="more-information"></a>Meer informatie

Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en hoe u aanmelden toevoegt aan een bestaande ASP.NET Core-toepassing.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
>
> ![Diagram van de interactie tussen de webbrowser, de web-app en het micro soft Identity-platform in de voor beeld-app.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Opstartklasse

De middleware *micro soft. AspNetCore. Authentication* gebruikt een `Startup` klasse die wordt uitgevoerd wanneer het hosting proces wordt gestart:

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

De- `AddAuthentication()` methode configureert de service om verificatie op basis van cookies toe te voegen. Deze verificatie wordt gebruikt in browser scenario's en voor het instellen van de uitdaging om verbinding te maken met OpenID Connect.

Op de regel die bevat, `.AddMicrosoftIdentityWebApp` wordt micro soft Identity platform-verificatie toegevoegd aan uw toepassing. De toepassing wordt vervolgens geconfigureerd voor het aanmelden van gebruikers op basis van de volgende informatie in het `AzureAD` gedeelte van de *appsettings.js* in het configuratie bestand:

| Sleutel *appSettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Toepassings(client)-ID van de toepassing die is geregistreerd in de Azure-portal.                                                                                       |
| `Instance`             | STS-eindpunt (Security Token Service) voor de gebruiker te verifiëren. Deze waarde is doorgaans `https://login.microsoftonline.com/`, wat de openbare Azure-cloud aangeeft. |
| `TenantId`             | De naam van uw Tenant of de Tenant-ID (een GUID) of `common` voor het aanmelden van gebruikers met werk-of school accounts of micro soft-persoonlijke accounts.                             |

De methode `Configure()` bevat twee belangrijke methoden, `app.UseAuthentication()` en `app.UseAuthorization()`, waarmee de benoemde functionaliteit kan worden ingeschakeld. In de `Configure()` -methode moet u ook micro soft Identity Web routes registreren met ten minste één aanroep naar `endpoints.MapControllerRoute()` of een aanroep naar `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Kenmerk voor het beveiligen van een controller of methoden

U kunt een controller-of controller methode beveiligen met behulp van het- `[Authorize]` kenmerk. Dit kenmerk beperkt de toegang tot de controller of methoden door alleen geverifieerde gebruikers toe te staan. Een verificatie controle kan vervolgens worden gestart om toegang te krijgen tot de controller als de gebruiker niet is geverifieerd.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

De GitHub-opslagplaats die deze ASP.NET Core-handleiding bevat, bevatten instructies en meer codevoorbeelden die laten zien hoe u het volgende kunt doen:

- Voeg verificatie toe aan een nieuwe ASP.NET Core-webtoepassing.
- Microsoft Graph, andere micro soft-Api's of uw eigen web-Api's aanroepen.
- Autorisatie toevoegen.
- Meld gebruikers aan bij nationale Clouds of met sociale identiteiten.

> [!div class="nextstepaction"]
> [ASP.NET Core Web-app-handleidingen op GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

---
title: 'Quickstart: Een ASP.NET Core Web-API beveiligen met het microsoft-identiteitsplatform | Azure'
titleSuffix: Microsoft identity platform
description: In deze Snelstartgids downloadt en wijzigt u een codevoorbeeld dat laat zien hoe u een ASP.NET Core Web-API kunt beveiligen met behulp van het microsoft-identiteitsplatform voor autorisatie.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720828"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Snelstartgids: een ASP.NET Core Web-API beveiligen met het micro soft Identity-platform

In deze Snelstartgids downloadt u een ASP.NET Core voor beeld van een web-API-code en bekijkt u hoe het de toegang tot de resource beperkt tot alleen geautoriseerde accounts. Het voorbeeld biedt ondersteuning voor de autorisatie van persoonlijke Microsoft-accounts en accounts in elke willekeurige Azure AD-organisatie (Azure Active Directory).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Vereisten
>
> - Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> - [Azure Active Directory-tenant](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) of [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Stap 1: Registreer de toepassing
>
> Registreer eerst de Web-API in uw Azure AD-tenant en voeg een bereik toe door de volgende stappen uit te voeren:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter voor **adres lijst en abonnementen** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de Tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer bij **naam** een naam in voor uw toepassing. Voer bijvoorbeeld **AspNetCoreWebApi-Quick** start in. Gebruikers van uw app krijgen deze naam te zien en u kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **Beheren** **Een API beschikbaar maken** > **Een bereik toevoegen**. Accepteer de standaard waarde voor de URI van de **toepassings-id** door **opslaan en door gaan** te selecteren en voer de volgende gegevens in:
>    - **Naam van bereik**: `access_as_user`
>    - **Wie kan toestemming verlenen?** : **Beheerders en gebruikers**
>    - **Weergavenaam van beheerderstoestemming**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschrijving van beheerderstoestemming**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Weergavenaam van gebruikerstoestemming**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschrijving van gebruikerstoestemming**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Status** **Ingeschakeld**
> 1. Selecteer **Bereik toevoegen** om de toevoeging van het bereik te volt ooien.

## <a name="step-2-download-the-aspnet-core-project"></a>Stap 2: De ASP.NET Core-oplossing downloaden

> [!div renderon="docs"]
> [De ASP.NET Core-oplossing downloaden](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) van GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Stap 3: Het ASP.NET Core-project configureren
>
> In deze stap configureert u de voorbeeld code zodanig dat deze werkt met de app-registratie die u eerder hebt gemaakt.
>
> 1. Pak het .ziparchief uit in een map in de buurt van de hoofdmap van uw station. U kunt bijvoorbeeld extra heren in *C:\Azure-samples*.
>
>    U kunt het archief het beste uitpakken in een directory in de buurt van de hoofdmap van uw station om fouten te voor komen die worden veroorzaakt door beperkingen van de padlengte in Windows.
>
> 1. Open de oplossing in de map *webapi* in de code-editor.
> 1. Open de *appsettings.jsin* het bestand en wijzig de volgende code:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Vervang door `Enter_the_Application_Id_here` de client-id van de toepassing die u hebt geregistreerd in het Azure Portal. U vindt de toepassings-ID (client) op de **overzichts** pagina van de app.
>    - Vervang `Enter_the_Tenant_Info_Here` door een van de volgende opties:
>       - Als uw toepassing **alleen accounts in deze organisatie Directory** ondersteunt, vervangt u deze waarde door de map (TENANT) ID (GUID) of Tenant naam (bijvoorbeeld `contoso.onmicrosoft.com` ). U kunt de Directory-ID (Tenant) vinden op de **overzichts** pagina van de app.
>       - Als uw toepassing **accounts in een organisatorische Directory** ondersteunt, vervangt u deze waarde door `organizations` .
>       - Als uw toepassing **alle Microsoft-account gebruikers** ondersteunt, verlaat u deze waarde als `common` .
>
> Wijzig voor deze Snelstart geen andere waarden in het bestand *appSettings.json*.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

De Web-API ontvangt een token van een clienttoepassing en de code in de Web-API valideert het token. Dit scenario wordt gedetailleerd beschreven in  [Scenario: Beveiligde web-API

### <a name="startup-class"></a>Opstartklasse

De middleware *micro soft. AspNetCore. Authentication* gebruikt een `Startup` klasse die wordt uitgevoerd wanneer het hosting proces wordt gestart. In de methode `ConfigureServices` wordt de `AddMicrosoftIdentityWebApi`extensiemethode geleverd door *Microsoft.Identity.Web* aangeroepen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

Met de methode `AddAuthentication()` wordt de service geconfigureerd voor het toevoegen van JwtBearer-gebaseerde verificatie.

De regel die `.AddMicrosoftIdentityWebApi` de micro soft Identity platform-autorisatie bevat, wordt toegevoegd aan uw web-API. Het wordt vervolgens geconfigureerd voor het valideren van de toegangs tokens die zijn uitgegeven door het micro soft Identity-platform, op basis van de informatie in de `AzureAD` sectie van de *appsettings.jsin* het configuratie bestand:

| Sleutel *appSettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Toepassings(client)-ID van de toepassing die is geregistreerd in de Azure-portal.                                                                                       |
| `Instance`             | STS-eindpunt (Security Token Service) voor de gebruiker te verifiëren. Deze waarde is doorgaans `https://login.microsoftonline.com/`, wat de openbare Azure-cloud aangeeft. |
| `TenantId`             | De naam van uw Tenant of de Tenant-ID (een GUID) of `common` voor het aanmelden van gebruikers met werk-of school accounts of persoonlijke micro soft-accounts.                             |

De methode `Configure()` bevat twee belangrijke methoden, `app.UseAuthentication()` en `app.UseAuthorization()`, waarmee de benoemde functionaliteit wordt ingeschakeld:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Een controller, een controller methode of een Scheer pagina beveiligen

U kunt een controller-of controller methode beveiligen met behulp van het- `[Authorize]` kenmerk. Dit kenmerk beperkt de toegang tot de controller of methoden door alleen geverifieerde gebruikers toe te staan. Een verificatie test kan worden gestart om toegang te krijgen tot de controller als de gebruiker niet is geverifieerd.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validatie van het bereik in de controller

Met de code in de API wordt gecontroleerd of de vereiste scopes zich in het token bevinden met behulp van `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

De GitHub-opslagplaats met dit ASP.NET Core Web API-codevoorbeeld bevat instructies en meer codevoorbeelden die laten zien hoe u het volgende kunt doen:

- Voeg verificatie toe aan een nieuwe ASP.NET Core Web-API.
- De Web-API aanroepen vanuit een bureaublad toepassing.
- Roept downstream-Api's aan, zoals Microsoft Graph en andere micro soft-Api's.

> [!div class="nextstepaction"]
> [ASP.NET Core Web API-handleidingen op GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)

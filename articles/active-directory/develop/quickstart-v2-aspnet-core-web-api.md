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
ms.openlocfilehash: aa0a001f9c35202939eeb4a7752803b998a3acf7
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562012"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Quickstart: Een ASP.NET Core Web-API beveiligen met het microsoft-identiteitsplatform

In deze quickstart downloadt u een ASP.NET Core-web-API-codevoorbeeld en bekijkt u de bijbehorende code die de toegang tot resources beperkt tot alleen geautoriseerde accounts. Het voorbeeld biedt ondersteuning voor de autorisatie van persoonlijke Microsoft-accounts en accounts in elke willekeurige Azure AD-organisatie (Azure Active Directory).

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
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** en vervolgens **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **Beheren** **Een API beschikbaar maken**.
> 1. Selecteer **Een scope toevoegen** en selecteer **Opslaan en doorgaan** om de standaard **Toepassings-ID-URI** te accepteren.
> 1. Voer in het deelvenster **Een bereik toevoegen** de volgende waarden in:
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

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Stap 3: Het ASP.NET Core-project configureren
>
> In deze stap configureert u de voorbeeldcode zodanig dat deze werkt met de app-registratie die u eerder hebt gemaakt.
>
> 1. Pak het .ziparchief uit in een map in de buurt van de hoofdmap van uw station. Bijvoorbeeld in *C:\Azure-Samples*.
> 1. Open de oplossing in de map *webapi* in de code-editor.
> 1. Open het bestand *appSettings.json* en wijzig het volgende:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Vervang `Enter_the_Application_Id_here`met de **Toepassings(client)-ID** voor de toepassing die is geregistreerd in de Azure-portal. U vindt de **toepassings-id (client-id)** op de pagina **Overzicht** van de app.
>    - Vervang `Enter_the_Tenant_Info_Here` door een van de volgende opties:
>       - Als uw toepassing **Accounts alleen in deze organisatiemap** ondersteunt, vervangt u deze waarde door de **Map (tenant)-ID**  (een GUID) of  **tenantnaam** (bijvoorbeeld, `contoso.onmicrosoft.com`). U vindt de **Directory (Tenant)-ID** op de pagina **Overzicht** van de apps.
>       - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>       - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
>
> Wijzig voor deze Snelstart geen andere waarden in het bestand *appSettings.json*.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

De Web-API ontvangt een token van een clienttoepassing en de code in de Web-API valideert het token. Dit scenario wordt gedetailleerd beschreven in  [Scenario: Beveiligde web-API

### <a name="startup-class"></a>Opstartklasse

De *Microsoft.AspNetCore.Authentication*-middleware maakt gebruik van een `Startup` klasse die wordt uitgevoerd wanneer in het hostingproces het volgende wordt geïnitialiseerd: In de methode `ConfigureServices` wordt de `AddMicrosoftIdentityWebApi`extensiemethode geleverd door *Microsoft.Identity.Web* aangeroepen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

Met de methode `AddAuthentication()` wordt de service geconfigureerd voor het toevoegen van JwtBearer-gebaseerde verificatie.

De regel met `.AddMicrosoftIdentityWebApi` voegt Microsoft-identiteitsplatform-autorisatie toe aan uw web-API. Daarna wordt het geconfigureerd voor het valideren van de toegangstokens die zijn uitgegeven door het  Microsoft-identiteitsplatform-eindpunt op basis van de informatie in de sectie `AzureAD` van het bestand *appSettings.json*:

| Sleutel *appSettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Toepassings(client)-ID van de toepassing die is geregistreerd in de Azure-portal.                                                                                       |
| `Instance`             | STS-eindpunt (Security Token Service) voor de gebruiker te verifiëren. Deze waarde is doorgaans `https://login.microsoftonline.com/`, wat de openbare Azure-cloud aangeeft. |
| `TenantId`             | De naam van uw tenant of de tenant-ID (een GUID) of *algemene* om gebruikers met werk- of schoolaccounts of persoonlijke Microsoft-accounts aan te melden.                             |

De methode `Configure()` bevat twee belangrijke methoden, `app.UseAuthentication()` en `app.UseAuthorization()`, waarmee de benoemde functionaliteit wordt ingeschakeld:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Een controller, een controller-methode of een Razor-pagina beveiligen

U kunt een controller of controllermethoden beveiligen met behulp van het kenmerk `[Authorize]`. Met dit kenmerk wordt toegang tot de controller of methoden beperkt door alleen geverifieerde gebruikers toe te staan. Dit betekent dat de verificatiecontrole kan worden gestart om de controller te benaderen als de gebruiker niet is geverifieerd.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>Het bereik in de controller valideren

De code in de API controleert vervolgens of de vereiste bereiken zich in het token bevinden met behulp van `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
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

- Verificatie toevoegen aan een nieuwe ASP.NET Core Web-API
- De Web-API aanroepen vanuit een bureaubladtoepassing
- Downstream-API's aanroepen, zoals Microsoft Graph en andere Microsoft-API's

> [!div class="nextstepaction"]
> [ASP.NET Core Web API-handleidingen op GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)

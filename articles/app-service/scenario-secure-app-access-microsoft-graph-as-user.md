---
title: 'Zelfstudie: Web-app krijgt toegang tot Microsoft Graph als de gebruiker | Azure'
description: In deze zelfstudie leert u hoe u toegang tot gegevens in Microsoft Graph krijgt voor een aangemelde gebruiker.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 3413c1a3f27b48c60ae730ad230c653928702faa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063380"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Zelfstudie: Toegang tot Microsoft Graph krijgen vanuit een beveiligde app als de gebruiker

Ontdek hoe u toegang tot Microsoft Graph krijgt vanuit een web-app die wordt uitgevoerd in Azure App Service.

:::image type="content" alt-text="Diagram waarin de toegang tot Microsoft Graph wordt weergegeven." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

U wilt toegang tot Microsoft Graph vanuit uw web-app toevoegen en een actie uitvoeren als de aangemelde gebruiker. In deze sectie wordt beschreven hoe u gedelegeerde machtigingen aan de web-app verleent en de profielgegevens van de aangemelde gebruiker ophaalt uit Azure AD (Azure Active Directory).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Gedelegeerde machtigingen verlenen aan een web-app.
> * Microsoft Graph vanuit een web-app aanroepen voor een aangemelde gebruiker.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een webtoepassing die wordt uitgevoerd in Azure App Service waarvoor de [module App Service-verificatie/-autorisatie is ingeschakeld](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>De front-end toegang verlenen om Microsoft Graph aan te roepen

Nu u verificatie en autorisatie hebt ingeschakeld in uw web-app, is de web-app geregistreerd bij het Microsoft-identiteitsplatform en wordt deze ondersteund door een Azure AD-toepassing. In deze stap geeft u de web-app machtigingen voor toegang tot Microsoft Graph voor de gebruiker. (Strikt genomen geeft u de Azure AD-toepassing van de web-app de machtigingen voor toegang tot de AD-toepassing van Microsoft Graph voor de gebruiker.)

Selecteer in het menu van de [Azure-portal](https://portal.azure.com) de optie **Azure Active Directory**, of zoek en selecteer **Azure Active Directory** op een willekeurige pagina.

Selecteer **App-registraties** > **Toepassingen in eigendom** > **Alle toepassingen in deze map weergeven**. Selecteer de naam van uw web-app en selecteer vervolgens **API-machtigingen**.

Selecteer **Een machtiging toevoegen** en selecteer vervolgens Microsoft-API's en Microsoft Graph.

Selecteer **Gedelegeerde machtigingen** en selecteer vervolgens **User.Read** in de lijst. Selecteer **Machtigingen toevoegen**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>App Service configureren zodat een bruikbaar toegangstoken wordt geretourneerd

De web-app beschikt nu over de vereiste machtigingen voor toegang tot Microsoft Graph als de aangemelde gebruiker. In deze stap configureert u App Service-verificatie en -autorisatie zodat u een bruikbaar toegangstoken hebt voor toegang tot Microsoft Graph. Voor deze stap hebt u de client-/app-id van de downstreamservice (Microsoft Graph) nodig. De app-id voor Microsoft Graph is *00000003-0000-0000-c000-000000000000*.

> [!IMPORTANT]
> Als u App Service niet configureert om een bruikbaar toegangstoken te retourneren, ziet u de foutmelding ```CompactToken parsing failed with error code: 80049217``` wanneer u Microsoft Graph-API's in uw code aanroept.

Ga naar [Azure Resource Explorer](https://resources.azure.com/) en gebruik de resourcestructuur om de web-app te zoeken. De resource-URL moet er ongeveer zo uitzien: `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`.

De Azure Resource Explorer wordt nu geopend met uw web-app geselecteerd in de resourcestructuur. Selecteer boven aan de pagina **Lezen/schrijven** om de Azure-resources te kunnen bewerken.

Bekijk in de linkernavigatiebalk de gegevens voor **config** > **authsettings**.

Selecteer in de weergave **authsettings** de optie **Bewerken**. Stel ```additionalLoginParams``` in op de volgende JSON-tekenreeks met behulp van de client-id die u hebt gekopieerd.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Sla de instellingen op door **PUT** te selecteren. Het kan enkele minuten duren voordat deze instelling is doorgevoerd. Uw web-app is nu geconfigureerd voor toegang tot Microsoft Graph met het juiste toegangstoken. Als u dit niet goed configureert, retourneert Microsoft Graph een foutmelding waarin staat dat de indeling van het compacte token incorrect is.

## <a name="call-microsoft-graph-net"></a>Microsoft Graph (.NET) aanroepen

Uw web-app heeft nu de vereiste machtigingen en voegt tevens de client-id van Microsoft Graph toe aan de aanmeldingsparameters. De web-app maakt gebruik van de [Microsoft.Identity.Web-bibliotheek](https://github.com/AzureAD/microsoft-identity-web/) om een toegangstoken op te halen voor verificatie bij Microsoft Graph. In versie 1.2.0 en later integreert de Microsoft.Identity.Web-bibliotheek met de module App Service-verificatie/-autorisatie en kan de bibliotheek daarnaast worden uitgevoerd. Microsoft.Identity.Web detecteert dat de web-app in App Service wordt gehost en haalt het toegangstoken op uit de module App Service-verificatie/-autorisatie. Het toegangstoken wordt vervolgens aan geverifieerde aanvragen doorgegeven met de Microsoft Graph-API.

Zie het [voorbeeld op GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf) als u deze code wilt bekijken als onderdeel van een voorbeeldtoepassing.

> [!NOTE]
> De Microsoft.Identity.Web-bibliotheek is niet vereist in de web-app voor basisverificatie/-autorisatie of om aanvragen te verifiëren met Microsoft Graph. Het is mogelijk om [downstream-API's veilig aan te roepen](tutorial-auth-aad.md#call-api-securely-from-server-code) terwijl alleen de module App Service-verificatie/-autorisatie is ingeschakeld.
> 
> De module App Service-verificatie/-autorisatie is echter ontworpen voor eenvoudigere verificatiescenario's. Voor ingewikkeldere scenario's (bijvoorbeeld de verwerking van aangepaste claims) hebt u de Microsoft.Identity.Web-bibliotheek of [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) nodig. Aan het begin is er wat meer instellings- en configuratiewerk, maar de Microsoft.Identity.Web-bibliotheek kan naast de module App Service-verificatie/-autorisatie worden uitgevoerd. Later, wanneer uw web-app ingewikkeldere scenario's moet verwerken, kunt u de module App Service-verificatie/-autorisatie uitschakelen en zal Microsoft.Identity.Web al onderdeel van uw app zijn.

### <a name="install-client-library-packages"></a>Clientbibliotheekpakketten installeren

Installeer de [micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) -en [micro soft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet-pakketten in uw project met behulp van de opdracht regel interface van .net core of de Package Manager-console in Visual Studio.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Open een opdrachtregel en ga naar de map die uw projectbestand bevat.

Voer de installatieopdrachten uit.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Package Manager](#tab/package-manager)

Open het project/de oplossing in Visual Studio en open de console met de opdracht **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager Console**.

Voer de installatieopdrachten uit.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

In het bestand *Startup.cs* wordt Microsoft.Identity.Web aan uw web-app toegevoegd met de methode ```AddMicrosoftIdentityWebApp```. Met de methode ```AddMicrosoftGraph``` wordt Microsoft Graph-ondersteuning toegevoegd.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* specificeert de configuratie voor de Microsoft.Identity.Web-bibliotheek. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Azure Active Directory** in het portalmenu, en selecteer vervolgens **App-registraties**. Selecteer de app-registratie die is gemaakt toen u de module App Service-verificatie/-autorisatie hebt ingeschakeld. (De app-registratie heeft, als het goed is, dezelfde naam als de web-app.) U kunt de tenant-id en client-id vinden op de overzichtspagina van de app-registratie. De domeinnaam vindt u op de Azure AD-overzichtspagina voor uw tenant.

*Graph* specificeert het Microsoft Graph-eindpunt en de initiële bereiken die de app nodig heeft.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

Het volgende voorbeeld laat zien hoe u Microsoft Graph aanroept als de aangemelde gebruiker en hoe u wat gebruikersgegevens ophaalt. Het ```GraphServiceClient```-object wordt in de controller geïnjecteerd en verificatie is voor u geconfigureerd via de Microsoft.Identity.Web-bibliotheek.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie en de web-app of bijbehorende resources niet meer nodig hebt, kunt u [de gemaakte resources opschonen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Gedelegeerde machtigingen verlenen aan een web-app.
> * Microsoft Graph vanuit een web-app aanroepen voor een aangemelde gebruiker.

> [!div class="nextstepaction"]
> [App Service krijgt toegang tot Microsoft Graph als de app](scenario-secure-app-access-microsoft-graph-as-app.md)
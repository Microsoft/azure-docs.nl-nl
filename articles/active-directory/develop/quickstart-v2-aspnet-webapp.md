---
title: 'Snelstart: Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe u micro soft-aanmelding kunt implementeren in een ASP.NET-Web-app met behulp van OpenID Connect Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: eb57be94e460241e3cacbe2dd20c071504a9222a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209761"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Quickstart: Aanmelding voor Microsoft Identity Platform toevoegen aan een ASP.NET-web-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers uit elke willekeurige Azure AD-organisatie (Azure Active Directory) kunnen worden aangemeld met een ASP.NET-web-app. 

> [!div renderon="docs"]
> In het volgende diagram ziet u hoe de voor beeld-app werkt:
>
> ![Diagram van de interactie tussen de webbrowser, de web-app en het micro soft Identity-platform in de voor beeld-app.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Vereisten
>
> * Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>De app registreren en downloaden
> U hebt twee opties om uw toepassing te bouwen: automatische of hand matige configuratie.
>
> ### <a name="automatic-configuration"></a>Automatische configuratie
> Als u uw app automatisch wilt configureren en vervolgens het code voorbeeld wilt downloaden, voert u de volgende stappen uit:
>
> 1. Ga naar de <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">pagina Azure portal voor app-registratie</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies voor het downloaden en automatisch configureren van uw nieuwe toepassing met één klik.
>
> ### <a name="manual-configuration"></a>Handmatige configuratie
> Als u uw toepassings-en code voorbeeld hand matig wilt configureren, gebruikt u de volgende procedures.
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter voor **adres lijst en abonnementen** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de Tenant te selecteren waarin u de toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer bij **naam** een naam in voor uw toepassing. Voer bijvoorbeeld **ASPNET-Quick** start in. Gebruikers van uw app krijgen deze naam te zien en u kunt deze later wijzigen.
> 1. Voeg **https://localhost:44368/** de **omleidings-URI** toe en selecteer **registreren**.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer in de sectie **impliciete toekenning en hybride stromen** **id-tokens**.
> 1. Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voor het code voorbeeld in deze Snelstartgids werkt u **https://localhost:44368/** voor **omleidings-URI**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![De configuratie ](media/quickstart-v2-aspnet-webapp/green-check.png) van uw toepassing is al geconfigureerd met dit kenmerk.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> [De Visual Studio 2019-oplossing downloaden](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Voer het project uit met behulp van Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Stap 3: Uw Visual Studio-project uitvoeren

1. Pak het zip-bestand uit naar een lokale map die zich dicht bij de hoofdmap bevindt. U kunt bijvoorbeeld extra heren naar *C:\Azure-samples*.
   
   U kunt het archief het beste uitpakken in een directory in de buurt van de hoofdmap van uw station om fouten te voor komen die worden veroorzaakt door beperkingen van de padlengte in Windows.
2. Open de oplossing in Visual Studio (*AppModelv2-webapp-OpenIDConnect-dotnet. SLN*).
3. Afhankelijk van de versie van Visual Studio, moet u mogelijk met de rechter muisknop op het project **AppModelv2-webapp-OpenIDConnect-DotNet** klikken en vervolgens **NuGet-pakketten herstellen** selecteren.
4. Open de Package Manager-console door   >  **andere Windows**  >  **Package Manager-console** weer geven te selecteren. Voer vervolgens `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` uit.

> [!div renderon="docs"]
> 5. Bewerk *Web.config* en vervang de para meters `ClientId` , `Tenant` en door `redirectUri` :
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    In die code:
>
>    - `Enter_the_Application_Id_here` is de toepassings-ID (client) van de app-registratie die u eerder hebt gemaakt. Zoek de ID van de toepassing (client) op de **overzichts** pagina van de app in **App-registraties** in het Azure Portal.
>    - `Enter_the_Tenant_Info_Here` is een van de volgende opties:
>      - Als uw toepassing **alleen mijn organisatie** ondersteunt, vervangt u deze waarde door de Directory-id (Tenant) of Tenant naam (bijvoorbeeld `contoso.onmicrosoft.com` ). Zoek de Directory-ID (Tenant) op de **overzichts** pagina van de app in **App-registraties** in het Azure Portal.
>      - Als uw toepassing **accounts in een organisatorische Directory** ondersteunt, vervangt u deze waarde door `organizations` .
>      - Als uw toepassing **alle Microsoft-account gebruikers** ondersteunt, vervangt u deze waarde door `common` .
>    - `redirectUri` is de **omleidings-URI** die u eerder in **app-registraties** hebt ingevoerd in de Azure Portal.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Meer informatie

Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en hoe u aanmelden toevoegt aan een bestaande ASP.NET-toepassing.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
>
> ![Diagram van de interactie tussen de webbrowser, de web-app en het micro soft Identity-platform in de voor beeld-app.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN Middleware NuGet-pakketten

U kunt de verificatie pijplijn instellen met verificatie op basis van cookies met behulp van OpenID Connect Connect in ASP.NET met OWIN middleware-pakketten. U kunt deze pakketten installeren door de volgende opdrachten uit te voeren in de Package Manager-console in Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Opstart klasse OWIN

De OWIN-middleware gebruikt een *opstart klasse* die wordt uitgevoerd wanneer het hosting proces wordt gestart. In deze Snelstartgids bevindt het *Startup.cs* -bestand zich in de hoofdmap. De volgende code toont de para meters die deze Quick Start gebruikt:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Waar  | Beschrijving |
> |---------|---------|
> | `ClientId`     | De toepassings-ID van de toepassing die is geregistreerd in de Azure Portal. |
> | `Authority`    | Het STS-eind punt (Security Token Service) voor de gebruiker die moet worden geverifieerd. Het is doorgaans `https://login.microsoftonline.com/{tenant}/v2.0` voor de open bare Cloud. In die URL is *{Tenant}* de naam van uw Tenant, uw Tenant-id of `common` een verwijzing naar het gemeen schappelijke eind punt. (Het gemeen schappelijke eind punt wordt gebruikt voor multi tenant-toepassingen.) |
> | `RedirectUri`  | De URL waar gebruikers na verificatie worden verzonden op basis van het micro soft-identiteits platform. |
> | `PostLogoutRedirectUri`     | De URL waar gebruikers worden verzonden na het afmelden. |
> | `Scope`     | De lijst met bereiken die worden aangevraagd, gescheiden door spaties. |
> | `ResponseType`     | De aanvraag die de reactie van de verificatie heeft, bevat een autorisatie code en een ID-token. |
> | `TokenValidationParameters`     | Een lijst met parameters voor de validatie van tokens. In dit geval `ValidateIssuer` wordt ingesteld op `false` om aan te geven dat deze aanmeldingen kan accepteren vanuit een persoonlijk, werk-of school account type. |
> | `Notifications`     | Een lijst met gemachtigden die kunnen worden uitgevoerd op `OpenIdConnect` berichten. |


> [!NOTE]
> De instelling `ValidateIssuer = false` is een vereenvoudiging voor deze quickstart. Valideer de uitgever in echte toepassingen. Bekijk de voorbeelden om te begrijpen hoe u dat kunt doen.

### <a name="authentication-challenge"></a>Verificatie vraag

U kunt afdwingen dat een gebruiker zich aanmeldt door een verificatievraag aan te vragen in uw controller:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Het aanvragen van een verificatie vraag met deze methode is optioneel. Normaal gesp roken gebruikt u deze methode als u wilt dat een weer gave toegankelijk is voor geverifieerde en niet-geverifieerde gebruikers. U kunt controllers ook beveiligen met behulp van de methode die wordt beschreven in de volgende sectie.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Kenmerk voor het beveiligen van een controller of controller acties

U kunt een controller of controller acties beveiligen met behulp van het- `[Authorize]` kenmerk. Dit kenmerk beperkt de toegang tot de controller of acties door alleen geverifieerde gebruikers toegang te verlenen tot de acties in de controller. Er wordt automatisch een verificatie test uitgevoerd wanneer een niet-geverifieerde gebruiker probeert toegang te krijgen tot een van de acties of controllers die door het kenmerk zijn gedecoreerd `[Authorize]` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de ASP.NET-zelf studie voor een volledige stapsgewijze hand leiding voor het bouwen van toepassingen en nieuwe functies, inclusief een volledige uitleg van deze Snelstartgids.

> [!div class="nextstepaction"]
> [Aanmelding toevoegen aan een ASP.NET-web-app](tutorial-v2-asp-webapp.md)

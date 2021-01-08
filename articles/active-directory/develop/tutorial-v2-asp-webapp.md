---
title: 'Zelfstudie: Een ASP.NET-web-app maken die gebruikmaakt van het Microsoft Identity-platform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie maakt u een ASP.NET-webtoepassing die gebruikmaakt van het Microsoft Identity platform en OWIN middleware om gebruikersaanmelding in te schakelen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: f0620c4d373ee96c87b575ae7eb173b2f16eecb2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013648"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Zelfstudie: Aanmelding bij Microsoft toevoegen aan een ASP.NET-web-app

In deze zelfstudie bouwt u een ASP.NET MVC-web-app die gebruikers aanmeldt met behulp van de Open Web Interface voor .NET-middleware (OWIN) en het Microsoft-identiteitsplatform.

Wanneer u deze handleiding hebt voltooid, kunt u aanmeldingen met persoonlijke accounts van bijvoorbeeld outlook.com en live.com aanvaarden. Daarnaast kunnen ook werk- en schoolaccounts van elk bedrijf of elke organisatie geïntegreerd met het Microsoft Identity Platform zich aanmelden bij uw app.

In deze zelfstudie:

> [!div class="checklist"]
> * Een *ASP.NET*-webtoepassingsproject maken in Visual Studio
> * De Open web interface voor .NET (OWIN) middleware-onderdelen toevoegen
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * De app registreren in de Azure-portal
> * De app testen

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) met **ASP.NET- en webontwikkelworkload**

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app werkt die wordt gegenereerd in deze gids

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

De voorbeeldtoepassing die u maakt is gebaseerd op een scenario waar u de browser gebruikt om een ASP.NET-website te openen die een gebruiker vraagt om zich te verifiëren via een aanmeldingsknop. In dit scenario vindt het grootste gedeelte van het werk om de webpagina weer te geven plaats aan de serverzijde.

## <a name="libraries"></a>Bibliotheken

Deze gids maakt gebruik van de volgende bibliotheken:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware die een toepassing in staat stelt om OpenIdConnect te gebruiken voor verificatie|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware die een toepassing in staat stelt om een gebruikerssessie te onderhouden met behulp van cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware waarmee op OWIN gebaseerde toepassingen kunnen worden uitgevoerd op Internet Information Services (IIS) met behulp van de pijplijn van de ASP.NET-aanvraag|

## <a name="set-up-your-project"></a>Uw project instellen

Dit onderdeel beschrijft hoe u de verificatiepijplijn kunt installeren en configureren via de OWIN-middleware in een ASP.NET-project met behulp van OpenID Connect.

> Wilt u liever het Visual Studio-project uit dit voorbeeld downloaden? [Download een project](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip), en ga door naar de stap [Uw toepassing registreren](#register-your-application) om het codevoorbeeld te configureren alvorens het uit te voeren.

### <a name="create-your-aspnet-project"></a>Uw ASP.NET-project maken

1. In Visual Studio: Ga naar **Bestand** > **Nieuw** > **Project**.
2. Selecteer onder **Visual C#\Web** de optie **ASP.NET-webtoepassing (.NET Framework)** .
3. Geef de toepassing een naam en selecteer **OK**.
4. Selecteer **Leeg** en schakel vervolgens het selectievakje in om **MVC**-verwijzingen toe te voegen.

## <a name="add-authentication-components"></a>Onderdelen voor verificatie toevoegen

1. In Visual Studio: Ga naar **Hulpprogramma's** > **Nuget Package Manager** > **Package Manager-console**.
2. Voeg *NuGet-pakketten voor OWIN-middleware* toe door het volgende te typen in het Package Manager Console-venster:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Over deze bibliotheken
Deze bibliotheken maken SSO (eenmalige aanmelding) met behulp van OpenID Connect mogelijk via verificatie op basis van cookies. Nadat de verificatie is voltooid en het token dat de gebruiker vertegenwoordigt, is verzonden naar de toepassing, wordt met OWIN-middleware een sessiecookie gemaakt. In de browser wordt deze cookie vervolgens gebruikt in volgende aanvragen. Op deze manier hoeft de gebruiker het wachtwoord niet opnieuw te typen en is er geen aanvullende verificatie meer vereist.

## <a name="configure-the-authentication-pipeline"></a>De verificatiepijplijn configureren

De volgende stappen worden gebruikt om een opstartklasse van OWIN-middleware te maken om OpenID Connect-verificatie te configureren. Deze klasse wordt automatisch uitgevoerd wanneer uw IIS-proces opstart.

> [!TIP]
> Ga als volgt te werk als uw project geen `Startup.cs`-bestand bevat in de hoofdmap:
> 1. Klik met de rechtermuisknop op de hoofdmap van het project en selecteer vervolgens Toevoegen  > **Nieuw item** > **OWIN-opstartklasse**.<br/>
> 2. Noem deze **Startup.cs**.
>
>> Zorg dat de geselecteerde klasse een OWIN-opstartklasse is, en geen C#-standaardklasse. Bevestig dit door te controleren of u [assembly: OwinStartup(typeof({NameSpace}.Startup))] ziet boven de naamruimte.

1. Voeg de referenties *OWIN* en *Microsoft.IdentityModel* toe aan Startup.cs:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Vervang de opstartklasse door de volgende code:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> De instelling `ValidateIssuer = false` is een vereenvoudiging voor deze quickstart. In echte toepassingen moet u de verlener valideren.
> Bekijk de voorbeelden om te ontdekken hoe u dat kunt doen.

### <a name="more-information"></a>Meer informatie

De parameters die u opgeeft in *OpenIDConnectAuthenticationOptions*, dienen als coördinaten waarmee de toepassing kan communiceren met Microsoft Identity Platform. Omdat de OpenID Connect-middleware gebruikmaakt van cookies, moet u ook op de achtergrond cookieverificatie instellen zoals in de vorige code wordt weergegeven. De waarde *ValidateIssuer* zorgt ervoor dat de toegang via OpenIdConnect niet wordt beperkt tot één specifieke organisatie.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Een controller toevoegen om aanmeldings- en afmeldingsaanvragen te verwerken

Om een nieuwe controller om de methoden voor aanmelden en afmelden beschikbaar te maken, volgt u deze stappen:

1.  Klik met de rechtermuisknop op de map **Controllers** en selecteer **Toevoegen** > **Controller**.
2.  Selecteer **MVC-controller (.NET-versie) – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **HomeController** en selecteer **Toevoegen**.
5.  Voeg OWIN-referenties toe aan de klasse:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Voeg de volgende twee methoden voor aanmelden en afmelden toe aan de controller door een verificatievraag te starten:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>De startpagina van de app waarop gebruikers zich kunnen aanmelden maken

Maak in Visual Studio een nieuwe weergave om de aanmeldknop toe te voegen en gebruikersgegevens weer te geven na de verificatie:

1.  Klik met de rechtermuisknop op de map **Weergaven\Startpagina** en selecteer **Weergave toevoegen**.
2.  Geef een naam op voor de nieuwe weergave **Index**.
3.  Voeg de volgende HTML toe aan het bestand. Deze bevat de aanmeldknop:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

### <a name="more-information"></a>Meer informatie
Met deze pagina wordt een aanmeldknop toegevoegd, in de SVG-indeling met een zwarte achtergrond:<br/>![Aanmelden met Microsoft-knop](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Ga voor meer soorten aanmeldknoppen naar de [Huisstijlrichtlijnen](./howto-add-branding-in-azure-ad-apps.md "Huisstijlrichtlijnen").

## <a name="add-a-controller-to-display-users-claims"></a>Voeg een controller toe om de claims van gebruikers weer te geven
Deze controller demonstreert het gebruik van het kenmerk `[Authorize]` om een controller te beveiligen. Met dit kenmerk wordt de toegang tot de controller beperkt doordat alleen geverifieerde gebruikers toegang krijgen. In de volgende code wordt het kenmerk gebruikt om gebruikersclaims weer te geven die zijn opgehaald als onderdeel van de aanmelding:

1.  Klik met de rechtermuisknop op de map **Controllers** en selecteer vervolgens **Toevoegen** > **Controller**.
2.  Selecteer **MVC-controller {versie} – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **ClaimsController**.
5.  Vervang de code van de controllerklasse door de volgende code. Dit voegt het kenmerk `[Authorize]` toe aan de klasse:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user's first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

### <a name="more-information"></a>Meer informatie
Vanwege het gebruik van het kenmerk `[Authorize]` kunnen alle methoden van deze controller alleen worden uitgevoerd als de gebruiker is geverifieerd. Als een niet-geverifieerde gebruiker toegang probeert te krijgen tot de controller, wordt met OWIN een verificatievraag gestart en wordt de gebruiker gedwongen zich te verifiëren. De voorgaande code onderzoekt de lijst met claims voor specifieke gebruikerskenmerken die deel uitmaken van de id-token van de gebruiker. Deze kenmerken omvatten de volledige naam en gebruikersnaam van de gebruiker, en het globale onderwerp voor de gebruikers-id. Ze omvatten ook de *tenant-id*. Dit is de id voor de organisatie van de gebruiker.

## <a name="create-a-view-to-display-the-users-claims"></a>Een weergave maken om de claims van de gebruiker weer te geven

Maak in Visual Studio een nieuwe weergave om de claims van de gebruiker weer te geven op een webpagina:

1.  Klik met de rechtermuisknop op de map **Weergaven\Claims** en selecteer vervolgens **Weergave toevoegen**.
2.  Geef een naam op voor de nieuwe weergave **Index**.
3.  Voeg de volgende HTML toe aan het bestand:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Uw toepassing registreren

Om de toepassing te registreren en de registratiegegevens van de toepassing toe te voegen aan uw oplossing, heeft u twee opties:

### <a name="option-1-express-mode"></a>Optie 1: Snelle modus

Volg deze stappen om snel uw toepassing te registreren.

1. Ga naar het nieuwe deelvenster [Azure-portal, App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.

### <a name="option-2-advanced-mode"></a>Optie 2: Geavanceerde modus

Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:

1. Open Visual Studio en doe het volgende:
   1. Selecteer het project in Solution Explorer en bekijk het venster Eigenschappen (druk op F4 als u het venster Eigenschappen niet ziet).
   1. Wijzig SSL ingeschakeld in `True`.
   1. Klik met de rechtermuisknop op het project in Visual Studio, selecteer **Eigenschappen** en selecteer vervolgens het tabblad **Web**. Wijzig in het onderdeel **Servers** de instelling **Project-URL** instelling voor de **SSL-URL**.
   1. Kopieer de SSL-URL. In de volgende stap voegt u deze URL toe aan de lijst met omleidings-URI's in de registratieportal.<br/><br/>![Projecteigenschappen](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
   
1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Voeg de SSL-URL die u in stap 1 in Visual Studio hebt gekopieerd (bijvoorbeeld `https://localhost:44368/`) toe aan **Omleidings-URI**.
1. Selecteer **Registreren**.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer in de sectie **Impliciete toekenning** de optie **Id-tokens** en selecteer vervolgens **Opslaan**.
1. Voeg het volgende toe aan het bestand web.config, dat zich bevindt in de hoofdmap in het onderdeel `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Vervang `ClientId` door de toepassings-id die u zojuist hebt geregistreerd.
1. Vervang `redirectUri` door de SSL-URL van het project.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen in Visual Studio, drukt u F5 om het project uit te voeren. De browser wordt geopend op de locatie http://<span></span>localhost:{port} en u ziet de knop **Aanmelden met Microsoft**. Selecteer de knop om het aanmeldingsproces te starten.

Wanneer u klaar bent om uw test uit te voeren, gebruikt u een Azure AD-account (werk- of schoolaccount) of een persoonlijk Microsoft-account (<span>live.</span>com of <span>outlook.</span>com) om u aan te melden.

![Aanmelden met Microsoft-knop die wordt weergegeven op de browseraanmeldingspagina in de browser](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Aanmelden bij uw Microsoft-account](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform

Toepassingen die zijn geïntegreerd met het Microsoft Identity Platform volgen een autorisatiemodel dat gebruikers en beheerders controle geeft over de toegang tot gegevens. Nadat een gebruiker zich verifieerd met Microsoft Identity Platform om toegang te krijgen tot deze toepassing, wordt hij gevraagd om toestemming te geven voor de machtigingen die de toepassing vraagt ('Uw basisprofiel weergeven' en 'Toegang onderhouden tot gegevens waarvoor u deze toegang hebt verleend'). Nadat hij deze machtigingen heeft aanvaard, gaat de gebruiker door naar de resultaten van de toepassing. Het is echter mogelijk dat de gebruiker een pagina **Beheerderstoestemming is nodig** te zien krijgt als een van de volgende dingen gebeurt:

- De ontwikkelaar van de toepassing voegt extra machtigingen toe waarvoor **Beheerderstoestemming** nodig is.
- Of de tenant is geconfigureerd (in **Bedrijfstoepassingen -> Gebruikersinstellingen**), waarbij gebruikers geen toestemming kunnen geven om apps in hun naam toegang te geven tot bedrijfsgegevens.

Bekijk [Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform](./v2-permissions-and-consent.md) voor meer informatie.

### <a name="view-application-results"></a>Resultaten van de toepassing weergeven

Nadat u zich hebt aangemeld, wordt de gebruiker omgeleid naar de startpagina van uw website. De startpagina is de HTTPS-URL die is opgegeven in de registratiegegevens van uw toepassing in het Microsoft-portal voor toepassingsregistratie. De startpagina bevat een welkomstbericht *'Hello \<user>'* , een koppeling om u af te melden en een koppeling om de claims van de gebruiker weer te geven. De koppeling voor de claims van de gebruiker maakt verbinding met de claimscontroller die u eerder heeft gemaakt.

### <a name="view-the-users-claims"></a>De claims van gebruiker weergeven

Om de claims van de gebruiker weer te geven, selecteert u de koppeling om naar de controllerweergave te gaan die enkel beschikbaar is voor geverifieerde gebruikers.

#### <a name="view-the-claims-results"></a>De claimresultaten weergeven

Wanneer u naar de controllerweergave gaat, ziet u normaal gezien een tabel met de basiseigenschappen voor de gebruiker:

|Eigenschap |Waarde |Beschrijving |
|---|---|---|
|**Naam** |Volledige naam van gebruiker | De voor- en achternaam van de gebruiker
|**Gebruikersnaam** |gebruiker<span>@domain.com</span> | De gebruikersnaam die is gebruikt om de gebruiker te identificeren|
|**Onderwerp** |Onderwerp |Een tekenreeks die de gebruiker op unieke wijze identificeerd op het internet|
|**Tenant ID** |Guid | Een **guid** die de Azure AD-organisatie van de gebruiker op unieke wijze vertegenwoordigt|

Daarnaast ziet u een tabel met alle claims in de verificatieaanvraag. Zie de [lijst met claims die zich in een ID-token bevinden](./id-tokens.md) voor meer informatie.

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Toegang tot een methode die een kenmerk Autoriseren heeft uitproberen (optioneel)

Om als anoniem gebruiker toegang tot een controller die beveiligd wordt door het kenmerk `Authorize` uit te proberen, volgt u deze stappen:

1. Selecteer de koppeling om de gebruiker af te melden en de afmelding te voltooien.
2. Typ in uw browser http://<span></span>localhost:{port}/claims om toegang te krijgen tot uw controller die beveiligd wordt door het kenmerk `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Verwachte resultaten na toegang tot een beveiligde controller

U wordt gevraagd om u te verifiëren om de beveiligde controllerweergave te gebruiken.

## <a name="advanced-options"></a>Geavanceerde opties

### <a name="protect-your-entire-website"></a>De hele website beveiligen

Om uw volledige website te beveiligen, voegt u in het bestand **Global.asax** het kenmerk `AuthorizeAttribute` toe aan de filter `GlobalFilters` in de methode `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Beperken wie zich kan aanmelden bij uw toepassing

Wanneer u een toepassing bouwt zoals beschreven in deze handleiding, dan aanvaardt uw toepassing aanmeldingen van persoonlijke accounts (inclusief outlook.com, live.com en andere), evenals werk- en schoolaccounts van bedrijven of organisaties die zijn geïntegreerd met het Microsoft-identiteitsplatform. Dit is een aanbevolen optie voor SaaS-toepassingen.

Als u gebruikerstoegang wilt beperken voor uw toepassing, zijn er meerdere opties beschikbaar.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Optie 1: Toegang tot de toepassing beperken tot alleen gebruikers uit het Active Directory-exemplaar van één bepaalde organisatie (één tenant)

Deze optie wordt vaak gebruikt voor *LOB-toepassingen*: Als u wilt dat in de toepassing alleen aanmeldingen worden geaccepteerd die horen bij een specifiek Azure AD-exemplaar (inclusief *gastaccounts* van dit exemplaar), volgt u deze stappen:

1. Wijzig in het bestand web.config de waarde voor de parameter `Tenant` van `Common` naar de tenantnaam van de organisatie, zoals `contoso.onmicrosoft.com`.
2. Stel in uw [OWIN-opstartklasse](#configure-the-authentication-pipeline) het argument `ValidateIssuer` in op `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Optie 2: Toegang beperken voor gebruikers in een specifieke lijst met organisaties

U kunt aanmeldingstoegang beperken tot enkel gebruikersaccounts van een Azure AD-organisatie in de lijst met toegestane organisaties:
1. Stel in uw [OWIN-opstartklasse](#configure-the-authentication-pipeline) het argument `ValidateIssuer` in op `true`.
2. Stel de waarde van de parameter `ValidIssuers` in op de lijst met toegestane organisaties.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Optie 3: Een aangepaste methode gebruiken om uitgevers te valideren

U kunt een aangepaste methode implementeren om uitgevers te valideren met behulp van de parameter **IssuerValidator**. Als u meer informatie wilt over het gebruik van deze parameter, bekijk dan de [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters)-klasse.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanroepen van beveiligde web-API's vanuit web-apps met het Microsoft Identity-platform:

> [!div class="nextstepaction"]
> [Web-apps die web-API's aanroepen](scenario-web-app-sign-user-overview.md)

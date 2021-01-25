---
title: Een web-app configureren die zich aanmeldt bij gebruikers | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-app die wordt aangemeld bij gebruikers (code configuratie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 45f3a066283a921f60909a4aa3cfdc76f3faad06
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753264"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Web-app die gebruikers aanmeldt: code configuratie

Meer informatie over het configureren van de code voor uw web-app die wordt aangemeld bij gebruikers.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotheken voor het beveiligen van web-apps

<!-- This section can be in an include for web app and web APIs -->
De bibliotheken die worden gebruikt voor het beveiligen van een web-app (en een web-API) zijn:

| Platform | Bibliotheek | Beschrijving |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Identiteits model-uitbrei dingen voor .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Micro soft Identity model-extensies voor .NET, die rechtstreeks worden gebruikt door ASP.NET en ASP.NET Core, stelt een reeks dll-bestanden voor die worden uitgevoerd op zowel .NET Framework als .NET core. Vanuit een ASP.NET-of ASP.NET Core-Web-App kunt u de token validatie controleren met behulp van de **TokenValidationParameters** -klasse (met name in sommige partner scenario's). In de praktijk wordt de complexiteit ingekapseld in de [micro soft. Identity. Web](https://aka.ms/ms-identity-web) -bibliotheek |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Ondersteuning voor Java-webtoepassingen |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Ondersteuning voor python-webtoepassingen |

Selecteer het tabblad dat overeenkomt met het platform waarin u bent geïnteresseerd:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de [Stapsgewijze zelf studie voor de ASP.net core-web-app, hoofd stuk 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Raadpleeg deze zelf studie voor meer informatie over de volledige implementatie.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="java"></a>[Java](#tab/java)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die micro soft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) -voor beeld aanroept in MSAL java.

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="python"></a>[Python](#tab/python)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de python- [Webtoepassing die micro soft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) -voor beeld aanroept in MSAL python.

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

---

## <a name="configuration-files"></a>Configuratie bestanden

Webtoepassingen die gebruikers aanmelden met behulp van het micro soft-identiteits platform, worden geconfigureerd via configuratie bestanden. De instellingen die u moet invullen zijn:

- De Cloud instantie ( `Instance` ) als u wilt dat uw app in nationale Clouds wordt uitgevoerd, bijvoorbeeld
- De doel groep in de Tenant-ID ( `TenantId` )
- De client-ID ( `ClientId` ) voor uw toepassing, zoals gekopieerd van de Azure Portal

Soms kunnen toepassingen worden parametrized door `Authority` , dat wil zeggen een samen voeging van `Instance` en `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core bevinden deze instellingen zich in het [appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) in het bestand, in de sectie ' AzureAd '.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

In ASP.NET Core bevat een ander bestand ([properties\launchSettings.jsop](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) de URL ( `applicationUrl` ) en de TLS/SSL-poort ( `sslPort` ) voor uw toepassing en verschillende profielen.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. Voor de twee voorafgaande configuratie bestanden zouden ze zijn `https://localhost:44321/signin-oidc` . De reden hiervoor is `applicationUrl` `http://localhost:3110` , maar `sslPort` is opgegeven (44321). `CallbackPath` is `/signin-oidc` , zoals gedefinieerd in `appsettings.json` .

Op dezelfde manier wordt de afmeldings-URI ingesteld op `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt de toepassing geconfigureerd via het [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) bestand, regels van 12 tot en met 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. Dat wil zeggen dat ze moeten zijn `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

In Java bevindt de configuratie zich in het bestand [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , dat zich onder bevindt `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met de `redirectUri` instanties die de toepassing definieert. Dat wil zeggen dat ze moeten zijn `http://localhost:8080/msal4jsample/secure/aad` en `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Hier is het python-configuratie bestand in [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> In deze Quick Start wordt voorgesteld het client geheim op te slaan in het configuratie bestand voor eenvoud. In uw productie-app wilt u andere manieren gebruiken om uw geheim op te slaan, zoals een sleutel kluis of een omgevings variabele zoals beschreven in [de documentatie van de kolf](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initialisatie code

De initialisatie code verschilt, afhankelijk van het platform. Voor ASP.NET Core en ASP.NET wordt het aanmelden van gebruikers overgedragen aan de middleware OpenID Connect Connect. Met de sjabloon ASP.NET of ASP.NET Core worden webtoepassingen gegenereerd voor het Azure Active Directory (Azure AD) v 1.0-eind punt. Sommige configuratie is vereist om deze aan te passen aan het micro soft Identity-platform. In het geval van Java wordt het afgehandeld met de samen werking van de toepassing.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core web-apps (en Web-Api's) is de toepassing beveiligd omdat u een `[Authorize]` kenmerk op de controllers of de controller acties hebt. Met dit kenmerk wordt gecontroleerd of de gebruiker is geverifieerd. De code die de toepassing initialiseert, bevindt zich in het *Startup.cs* -bestand.

Als u verificatie wilt toevoegen met het micro soft-identiteits platform (voorheen Azure AD v 2.0), moet u de volgende code toevoegen. De opmerkingen in de code moeten zichzelf verklaren.

> [!NOTE]
> Als u rechtstreeks wilt beginnen met de nieuwe ASP.NET Core sjablonen voor micro soft Identity platform, die gebruikmaken van micro soft. Identity. Web, kunt u een preview NuGet-pakket downloaden met Project sjablonen voor .NET Core 3,1 en .NET 5,0. Na de installatie kunt u ASP.NET Core webtoepassingen (MVC of razendsnelle) rechtstreeks instantiëren. Zie de [Project sjablonen voor micro soft. Identity. Web Web-app](https://aka.ms/ms-id-web/webapp-project-templates) voor meer informatie. Dit is de eenvoudigste manier om alle onderstaande stappen voor u uit te voeren.
>
> Als u liever uw project start met het huidige standaard ASP.NET Core webproject in Visual Studio of met `dotnet new mvc --auth SingleAuth` of `dotnet new webapp --auth SingleAuth` , ziet u code zoals in het volgende voor beeld:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Deze code maakt gebruik van het verouderde pakket **micro soft. AspNetCore. Authentication. AzureAD. UI** NuGet, dat wordt gebruikt voor het maken van een Azure AD v 1.0-toepassing. In dit artikel wordt uitgelegd hoe u een micro soft Identity platform (Azure AD v 2.0)-toepassing maakt waarmee die code wordt vervangen.
>

1. Voeg de NuGet-pakketten [micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) en [micro soft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) toe aan uw project. Verwijder het NuGet-pakket micro soft. AspNetCore. Authentication. AzureAD. UI als dit aanwezig is.

2. Werk de code in `ConfigureServices` zodat deze gebruikmaakt van de `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` methoden en.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. Schakel in de `Configure` methode in *Startup.cs* verificatie in met een aanroep naar `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

In de bovenstaande code:
- De `AddMicrosoftIdentityWebAppAuthentication` extensie methode is gedefinieerd in **micro soft. Identity. Web**. ,
  - Hiermee voegt u de verificatie service toe.
  - Hiermee configureert u opties voor het lezen van het configuratie bestand (hier uit de sectie ' AzureAD ')
  - Hiermee configureert u de OpenID Connect Connect-opties, zodat de instantie het micro soft Identity-platform is.
  - Valideert de uitgever van het token.
  - Zorgt ervoor dat de claims die overeenkomen met de naam, zijn toegewezen uit de `preferred_username` claim in het ID-token.

- Naast het configuratie object kunt u de naam van de configuratie sectie opgeven tijdens het aanroepen van `AddMicrosoftIdentityWebAppAuthentication` . Standaard is dit `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` heeft andere para meters voor geavanceerde scenario's. U kunt bijvoorbeeld bij het traceren van middleware-gebeurtenissen voor OpenID Connect verbinden u helpen bij het oplossen van problemen met uw webtoepassing als verificatie niet werkt. Als u de optionele para meter instelt `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` op `true` , ziet u hoe informatie wordt verwerkt door de set ASP.net core middleware, terwijl deze wordt uitgevoerd op basis van de HTTP-reactie op de identiteit van de gebruiker in `HttpContext.User` .

- De `AddMicrosoftIdentityUI` extensie methode is gedefinieerd in **micro soft. Identity. Web. UI**. Het bevat een standaard controller voor het afhandelen van aanmelden en afmelden.

U kunt meer informatie vinden over hoe u met micro soft. Identity. Web Web-apps kunt maken in <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Micro soft. Identity. Web biedt momenteel geen ondersteuning voor het scenario van **afzonderlijke gebruikers accounts** (waarbij gebruikers accounts in-app worden opgeslagen) wanneer u Azure AD als en externe aanmeldings provider gebruikt. Zie voor meer informatie: [AzureAD/micro soft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De code die betrekking heeft op verificatie in een ASP.NET-Web-app en Web-Api's bevindt zich in het bestand [App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Het Java-voor beeld maakt gebruik van het lente-Framework. De toepassing is beveiligd omdat u een filter implementeert, waarmee elke HTTP-reactie wordt onderschept. In de Quick start voor Java-Web-apps bevindt zich dit filter `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Het filter verwerkt de OAuth 2,0-autorisatie code stroom en controleert of de gebruiker is geverifieerd ( `isAuthenticated()` methode). Als de gebruiker niet is geverifieerd, wordt de URL van de Azure AD-autorisatie-eind punten berekend en wordt de browser omgeleid naar deze URI.

Wanneer het antwoord arriveert, met daarin de autorisatie code, wordt het token verkregen met behulp van MSAL java. Wanneer het het token uiteindelijk van het token eindpunt ontvangt (op de omleidings-URI), is de gebruiker aangemeld.

Zie de `doFilter()` methode in [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)voor meer informatie.

> [!NOTE]
> De code van de `doFilter()` is in een iets andere volg orde geschreven, maar de stroom is die wordt beschreven.

Zie de [micro soft Identity platform and OAuth 2,0 Authorization code flow](v2-oauth2-auth-code-flow.md)(Engelstalig) voor meer informatie over de autorisatie code stroom die door deze methode wordt geactiveerd.

# <a name="python"></a>[Python](#tab/python)

Het python-voor beeld maakt gebruik van een fles. De initialisatie van de kolf en MSAL python wordt uitgevoerd in [app. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel leert u hoe u aanmelden en afmelden kunt activeren.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ga naar het volgende artikel in dit scenario, Meld u aan [en](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)af.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ga naar het volgende artikel in dit scenario, Meld u aan [en](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)af.

# <a name="java"></a>[Java](#tab/java)

Ga naar het volgende artikel in dit scenario, Meld u aan [en](./scenario-web-app-sign-user-sign-in.md?tabs=java)af.

# <a name="python"></a>[Python](#tab/python)

Ga naar het volgende artikel in dit scenario, Meld u aan [en](./scenario-web-app-sign-user-sign-in.md?tabs=python)af.

---

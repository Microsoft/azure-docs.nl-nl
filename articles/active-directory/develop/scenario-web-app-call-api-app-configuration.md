---
title: Een web-app configureren die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van de code van een web-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669301"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Een web-app die web-Api's aanroept: code configuratie

Zoals wordt weer gegeven in de [Web-app die het scenario voor gebruikers aanmeldt](scenario-web-app-sign-user-overview.md) , gebruikt de Web-App de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md) om de gebruiker te ondertekenen in. Deze stroom heeft twee stappen:

1. Een autorisatie code aanvragen. Dit onderdeel delegeert een persoonlijke dialoog met de gebruiker naar het micro soft Identity-platform. Tijdens dat dialoog venster wordt de gebruiker aangemeld en wordt er gebruikgemaakt van het gebruik van web-Api's. Wanneer de persoonlijke dialoog is beëindigd, ontvangt de web-app een autorisatie code op de omleidings-URI.
1. Een toegangs token voor de API aanvragen door de autorisatie code in te wisselen.

De [Web-app die zich in gebruikers scenario's aanmeldt](scenario-web-app-sign-user-overview.md) , bedekt alleen de eerste stap. Hier leest u hoe u uw web-app kunt aanpassen, zodat u niet alleen gebruikers in ondertekent, maar ook Web-Api's aanroept.

## <a name="microsoft-libraries-supporting-web-apps"></a>Micro soft-bibliotheken die web-apps ondersteunen

De volgende micro soft-bibliotheken ondersteunen web-apps:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

Selecteer het tabblad voor het platform waarin u bent geïnteresseerd:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Client geheimen of client certificaten

Als uw web-app nu een stroomafwaartse Web-API aanroept, geeft u een client geheim of client certificaat op in het *appsettings.js* bestand. U kunt ook een sectie toevoegen waarin het volgende wordt opgegeven:

- De URL van de stroomafwaartse Web-API
- De bereiken die vereist zijn voor het aanroepen van de API

In het volgende voor beeld `GraphBeta` geeft de sectie deze instellingen op.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

In plaats van een client geheim kunt u een client certificaat opgeven. Het volgende code fragment toont het gebruik van een certificaat dat is opgeslagen in Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Micro soft. Identity. Web* biedt verschillende manieren om certificaten te beschrijven, hetzij per configuratie of per code. Zie voor meer informatie [micro soft. Identity. web-certificaten gebruiken](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) op github.

## <a name="startupcs"></a>Startup.cs

Uw web-app moet een token aanschaffen voor de downstream API. U geeft deze op door de `.EnableTokenAcquisitionToCallDownstreamApi()` regel na te voegen `.AddMicrosoftIdentityWebApi(Configuration)` . Deze regel beschrijft de `ITokenAcquisition` service die u in uw controller en pagina acties kunt gebruiken. Zoals u in de volgende twee opties ziet, kan dit echter meer worden gedaan. U moet ook een implementatie van de token cache kiezen, bijvoorbeeld `.AddInMemoryTokenCaches()` in *Startup. cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

De bereiken die zijn door gegeven aan `EnableTokenAcquisitionToCallDownstreamApi` zijn optioneel en de web-app kunnen de bereiken en de toestemming van de gebruiker voor deze bereiken aanvragen wanneer deze zich aanmeldt. Als u de bereiken niet opgeeft, wordt door *micro soft. Identity. Web* een incrementele toestemming verleend.

Als u het token zelf niet zelf wilt verkrijgen, biedt *micro soft. Identity. Web* twee mechanismen voor het aanroepen van een web-API vanuit een web-app. Welke optie u kiest, is afhankelijk van het feit of u Microsoft Graph of een andere API wilt aanroepen.

### <a name="option-1-call-microsoft-graph"></a>Optie 1: oproep Microsoft Graph

Als u Microsoft Graph wilt aanroepen, kunt u met *micro soft. Identity. Web* rechtstreeks de `GraphServiceClient` (beschikbaar gemaakt door de Microsoft Graph SDK) in uw API-acties gebruiken. Microsoft Graph beschikbaar maken:

1. Voeg het NuGet-pakket [micro soft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) toe aan uw project.
1. Voeg toe `.AddMicrosoftGraph()` na `.EnableTokenAcquisitionToCallDownstreamApi()` in het bestand *Startup. cs* . `.AddMicrosoftGraph()` heeft verschillende onderdrukkingen. Met behulp van de onderdrukking waarvoor een configuratie sectie als para meter wordt gebruikt, wordt de code:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Optie 2: een andere stroomafwaartse Web-API aanroepen dan Microsoft Graph

Voor het aanroepen van een andere web-API dan Microsoft Graph, wordt door *micro soft. Identity. Web* geboden `.AddDownstreamWebApi()` , waarmee tokens worden aangevraagd en de DOWNSTREAM Web-API wordt aangeroepen.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Samenvatting

Net als bij Web-Api's kunt u verschillende token cache-implementaties kiezen. Zie [micro soft. Identity. Web-token cache serialisatie](https://aka.ms/ms-id-web/token-cache-serialization) op github voor meer informatie.

In de volgende afbeelding ziet u de verschillende mogelijkheden van *micro soft. Identity. Web* en de invloed hiervan op het bestand *Startup. cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blok diagram met de service configuratie opties in startup dot C S voor het aanroepen van een web-API en het opgeven van een token cache-implementatie":::

> [!NOTE]
> Als u hier volledig inzicht wilt krijgen in de code voorbeelden, moet u bekend zijn met [ASP.net core fundamentals](/aspnet/core/fundamentals), en met name met [afhankelijkheids injectie](/aspnet/core/fundamentals/dependency-injection) en [Opties](/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Omdat het aanmelden van de gebruiker is gedelegeerd naar de OpenID Connect Connect (OIDC)-middleware, moet u communiceren met het OIDC-proces. Hoe u communiceert, is afhankelijk van het Framework dat u gebruikt.

Voor ASP.NET kunt u zich abonneren op middleware OIDC-gebeurtenissen:

- U kunt ASP.NET Core een autorisatie code aanvragen met behulp van de Open ID Connect middleware. Met ASP.NET of ASP.NET Core kunnen gebruikers zich aanmelden en toestemming geven.
- U bent geabonneerd op de web-app om de autorisatie code te ontvangen. Dit abonnement wordt uitgevoerd met behulp van een C#-gemachtigde.
- Wanneer de autorisatie code wordt ontvangen, gebruikt u MSAL-bibliotheken om deze te inwisselen. De resulterende toegangs tokens en vernieuwings tokens worden opgeslagen in de token cache. De cache kan worden gebruikt in andere onderdelen van de toepassing, zoals controllers, om andere tokens op de achtergrond te verkrijgen.

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Mogelijk wilt u dit voor beeld raadplegen voor volledige implementatie details.

# <a name="java"></a>[Java](#tab/java)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die Microsoft Graph aanroept](https://github.com/Azure-Samples/ms-identity-java-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL voor Java.
Met het voor beeld wordt momenteel MSAL voor Java de autorisatie code-URL geproduceerd en wordt de navigatie naar het verificatie-eind punt voor het micro soft Identity-platform verwerkt. Het is ook mogelijk Sprint beveiliging te gebruiken om de gebruiker te ondertekenen in. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

# <a name="python"></a>[Python](#tab/python)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de python- [webtoepassing Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL. Python.
Met het voor beeld kunt u momenteel MSAL. Python produceert de autorisatie code-URL en verwerkt de navigatie naar het autorisatie-eind punt voor het micro soft Identity-platform. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code die de autorisatie code inwisselt

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Micro soft. Identity. Web vereenvoudigt uw code door de juiste OpenID Connect Connect-instellingen in te stellen, u te abonneren op de gebeurtenis ontvangen code en de code te inwisselen. Er is geen aanvullende code vereist om de autorisatie code te inwisselen. Zie [micro soft. Identity. web source code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) voor meer informatie over hoe dit werkt.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET verwerkt dingen op soort gelijke wijze als ASP.NET Core, behalve dat de configuratie van OpenID Connect Connect en het abonnement op de `OnAuthorizationCodeReceived` gebeurtenis plaatsvindt in het bestand [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . De concepten zijn ook vergelijkbaar met die in ASP.NET Core, behalve dat u in ASP.NET de `RedirectUri` in [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)moet opgeven. Deze configuratie is iets minder krachtig dan het in ASP.NET Core, omdat u het moet wijzigen wanneer u de toepassing implementeert.

Hier volgt de code voor startup. auth. CS:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Bekijk de [Web-app die zich in gebruikers aanmeldt: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) om te begrijpen hoe het Java-voor beeld de autorisatie code ontvangt. Nadat de app de code heeft ontvangen, wordt de [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegeren aan de- `AuthHelper.processAuthenticationCodeRedirect` methode in [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Aanroepen `getAuthResultByAuthCode` .

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

De `getAuthResultByAuthCode` methode is gedefinieerd in [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Er wordt een MSAL gemaakt `ConfidentialClientApplication` en vervolgens aangeroepen `acquireToken()` `AuthorizationCodeParameters` vanuit de autorisatie code.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

De autorisatie code stroom is aangevraagd zoals weer gegeven in [de web-app die zich aanmeldt bij gebruikers: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). De code wordt vervolgens ontvangen op de `authorized` functie, die door de route wordt verzonden vanuit de `/getAToken` URL. Zie [app. py # l30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) voor de volledige context van deze code:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

In plaats van een client geheim kan de vertrouwelijke client toepassing ook de identiteit bewijzen met behulp van een client certificaat of een client bevestiging.
Het gebruik van client verklaringen is een geavanceerd scenario dat wordt beschreven in [client verklaringen](msal-net-client-assertions.md).

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> De implementatie van de token-cache voor web-apps of Web-Api's wijkt af van de implementatie voor desktop toepassingen, die vaak [op bestanden is gebaseerd](scenario-desktop-acquire-token.md#file-based-token-cache).
> Uit veiligheids overwegingen is het belang rijk om ervoor te zorgen dat voor web-apps en Web-Api's één token cache per gebruikers account is. U moet de token cache voor elk account serialiseren.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

De zelf studie ASP.NET core maakt gebruik van afhankelijkheids injectie om u te laten bepalen welke implementatie van de token cache in het bestand startup. CS voor uw toepassing. Micro soft. Identity. web wordt geleverd met vooraf ontwikkelde token-cache-serialisatiefunctie beschreven in de [serialisatie van de token cache](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Een interessante mogelijkheid is om ASP.NET Core [gedistribueerde geheugen caches](/aspnet/core/performance/caching/distributed#distributed-memory-cache)te kiezen:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Zie ook het artikel [cache serialisatie](https://aka.ms/ms-id-web/token-cache-serialization) van micro soft. Identity. Web en zelf studies voor de ASP.net core-web-apps voor meer informatie over de token-cache providers. [ Token caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fase van de zelf studie voor web-apps.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De implementatie van de token-cache voor web-apps of Web-Api's wijkt af van de implementatie voor desktop toepassingen, die vaak [op bestanden is gebaseerd](scenario-desktop-acquire-token.md#file-based-token-cache).

De implementatie van de web-app kan gebruikmaken van de ASP.NET-sessie of het server geheugen. Zie bijvoorbeeld hoe de cache-implementatie is gehookd na het maken van de MSAL.NET-toepassing in [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java biedt methoden voor het serialiseren en deserialiseren van de token cache. In het Java-voor beeld wordt de serialisatie vanuit de sessie verwerkt, zoals wordt weer gegeven in de `getAuthResultBySilentFlow` methode in [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

De details van de- `SessionManagementHelper` klasse zijn opgenomen in het [MSAL-voor beeld voor Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

In het python-voor beeld wordt één cache per account gegarandeerd door het opnieuw maken van een vertrouwelijke client toepassing voor elke aanvraag en deze vervolgens te serialiseren in de kolf sessie cache:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Volgende stappen

Wanneer de gebruiker zich aanmeldt, wordt er op dit punt een token opgeslagen in de token cache. Laten we eens kijken hoe het wordt gebruikt in andere onderdelen van de web-app.

[Accounts verwijderen uit de cache op globale afmelding](scenario-web-app-call-api-sign-in.md)

---
title: Een web-API configureren die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API die web-Api's aanroept (de code configuratie van de app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686577"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Een web-API die web-Api's aanroept: code configuratie

Nadat u uw web-API hebt geregistreerd, kunt u de code voor de toepassing configureren.

De code die u gebruikt om uw web-API te configureren, zodat downstream Web-Api's worden aangeroepen boven op de code die wordt gebruikt om een web-API te beveiligen. Zie [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md)(Engelstalig) voor meer informatie.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Micro soft. Identity. Web

Micro soft raadt u aan het NuGet-pakket [micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) te gebruiken bij het ontwikkelen van een ASP.net core beveiligde API die stroomafwaartse Web-api's aanroept. Zie de [beveiligde web-API: code configuratie | Micro soft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) voor een snelle presentatie van die bibliotheek in de context van een web-API.

## <a name="client-secrets-or-client-certificates"></a>Client geheimen of client certificaten

Als uw web-API nu een stroomafwaartse Web-API aanroept, geeft u een client geheim of client certificaat op in het *appsettings.js* bestand. U kunt ook een sectie toevoegen waarin het volgende wordt opgegeven:

- De URL van de stroomafwaartse Web-API
- De bereiken die vereist zijn voor het aanroepen van de API

In het volgende voor beeld `GraphBeta` geeft de sectie deze instellingen op.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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

Micro soft. Identity. Web biedt verschillende manieren om certificaten te beschrijven, hetzij per configuratie of per code. Zie voor meer informatie [micro soft. Identity. Web wiki-certificaten gebruiken](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) op github.

## <a name="startupcs"></a>Startup.cs

De Web-API moet een token aanschaffen voor de downstream API. U geeft deze op door de `.EnableTokenAcquisitionToCallDownstreamApi()` regel na te voegen `.AddMicrosoftIdentityWebApi(Configuration)` . Deze regel beschrijft de `ITokenAcquisition` service, die u kunt gebruiken in de acties van uw besturing/pagina. Zoals u echter in de volgende twee opsommings punten ziet, kunt u nog eenvoudiger werken. U moet ook een implementatie van de token cache kiezen, bijvoorbeeld `.AddInMemoryTokenCaches()` in *Startup. cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Als u het token zelf niet zelf wilt verkrijgen, biedt *micro soft. Identity. Web* twee mechanismen voor het aanroepen van een stroomafwaartse Web-API vanuit een andere API. Welke optie u kiest, is afhankelijk van het feit of u Microsoft Graph of een andere API wilt aanroepen.

### <a name="option-1-call-microsoft-graph"></a>Optie 1: oproep Microsoft Graph

Als u Microsoft Graph wilt aanroepen, kunt u met micro soft. Identity. web rechtstreeks de `GraphServiceClient` (beschikbaar gemaakt door de Microsoft Graph SDK) in uw API-acties gebruiken. Microsoft Graph beschikbaar maken:

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
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Optie 2: een andere stroomafwaartse Web-API aanroepen dan Microsoft Graph

Voor het aanroepen van een andere stroomafwaartse API dan Microsoft Graph, wordt door *micro soft. Identity. Web* geboden `.AddDownstreamWebApi()` , waarmee tokens worden aangevraagd en de stroomafwaartse Web-API aangeroepen.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Net als bij Web apps, kunt u verschillende implementaties van de token-cache kiezen. Zie [micro soft Identity Web-token cache serialisatie](https://aka.ms/ms-id-web/token-cache-serialization) op github voor meer informatie.

In de volgende afbeelding ziet u de verschillende mogelijkheden van *micro soft. Identity. Web* en de invloed hiervan op het bestand *Startup. cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blok diagram met de service configuratie opties in startup dot C S voor het aanroepen van een web-API en het opgeven van een token cache-implementatie":::

> [!NOTE]
> Als u hier volledig inzicht wilt krijgen in de code voorbeelden, moet u bekend zijn met [ASP.net core fundamentals](/aspnet/core/fundamentals), en met name met [afhankelijkheids injectie](/aspnet/core/fundamentals/dependency-injection) en [Opties](/aspnet/core/fundamentals/configuration/options).

# <a name="java"></a>[Java](#tab/java)

De stroom namens-of (OBO) wordt gebruikt om een token op te halen voor het aanroepen van de stroomafwaartse Web-API. In deze stroom ontvangt uw web-API een Bearer-token met door de gebruiker gedelegeerde machtigingen van de client toepassing. vervolgens wordt dit token door gegeven aan een ander toegangs token om de stroomafwaartse Web-API aan te roepen.

De onderstaande code maakt gebruik van Lente Security Framework `SecurityContextHolder` in de Web-API om het gevalideerde Bearer-token op te halen. Vervolgens wordt de MSAL Java-bibliotheek gebruikt voor het verkrijgen van een token voor de downstream API met behulp van de `acquireToken` aanroep van `OnBehalfOfParameters` . Met MSAL wordt het token in de cache opgeslagen, zodat de volgende aanroepen naar de API kunnen `acquireTokenSilently` worden gebruikt om het token in de cache op te halen.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

De stroom namens-of (OBO) wordt gebruikt om een token op te halen voor het aanroepen van de stroomafwaartse Web-API. In deze stroom ontvangt uw web-API een Bearer-token met door de gebruiker gedelegeerde machtigingen van de client toepassing. vervolgens wordt dit token door gegeven aan een ander toegangs token om de stroomafwaartse Web-API aan te roepen.

Een python-Web-API moet een middleware gebruiken om het Bearer-token te valideren dat van de client is ontvangen. De Web-API kan vervolgens het toegangs token voor downstream API verkrijgen met behulp van de MSAL python-bibliotheek door de methode aan te roepen [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Voor een voor beeld van het gebruik van deze API raadpleegt u de [test code voor micro soft-Authentication-library-for-python op github](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Zie ook de bespreking van [probleem 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) in diezelfde opslag plaats voor een benadering die de nood zaak van een toepassing voor een middelste laag niet meer hoeft te omzeilen.

U kunt ook een voor beeld bekijken van de implementatie van de OBO-stroom in de [MS-Identity-python-voor](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) -voor beeld.

---

U kunt ook een voor beeld van een OBO-stroom implementatie bekijken in [Node.js en Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Voor meer informatie over het OBO-protocol raadpleegt u het [micro soft Identity platform en de OAuth 2,0-stroom](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de app](scenario-web-api-call-api-acquire-token.md).

---
title: Bureau blad-apps configureren die web-Api's aanroepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van de code van een bureau blad-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 372efa217e400ffd4bcecd286f3c3dc7b81f64d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007958"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Bureau blad-app voor het aanroepen van web-Api's: code configuratie

Nu u de toepassing hebt gemaakt, leert u hoe u de code kunt configureren met de coördinaten van de toepassing.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Micro soft-bibliotheken die bureau blad-apps ondersteunen

De volgende micro soft-bibliotheken ondersteunen bureau blad-apps:

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Openbare clienttoepassing

Vanuit een code point van de weer gave zijn desktop toepassingen open bare client toepassingen. De configuratie is een andere bit, afhankelijk van of u interactieve verificatie gebruikt of niet.

# <a name="net"></a>[.NET](#tab/dotnet)

U moet MSAL.NET bouwen en bewerken `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Uitsluitend op code

Met de volgende code wordt een open bare client toepassing geïnstantieerd en worden Microsoft Azure de gebruikers in de open bare Cloud gemeldd met een werk-of school account of een persoonlijke Microsoft-account.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Als u van plan bent interactieve verificatie of de code stroom van het apparaat te gebruiken, zoals eerder weer gegeven, gebruikt u de `.WithRedirectUri` aanpassings functie.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Configuratiebestanden gebruiken

Met de volgende code wordt een open bare client toepassing geïnstantieerd vanuit een configuratie object, die programmatisch kan worden ingevuld of vanuit een configuratie bestand kan worden gelezen.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Meer uitgebreide configuratie

U kunt het bouwen van toepassingen uitwerken door een aantal para meters toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multi tenant-toepassing in een nationale Cloud is, zoals de Amerikaanse overheid die hier wordt weer gegeven, kunt u het volgende schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET bevat ook een aanpassings functie voor Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Ten slotte, als u tokens wilt verkrijgen voor een Azure Active Directory-Tenant (Azure AD) B2C, geeft u uw Tenant op zoals weer gegeven in het volgende code fragment:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Meer informatie

Voor meer informatie over het configureren van een MSAL.NET-bureaublad toepassing:

- `PublicClientApplicationBuilder`Zie de referentie documentatie [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)voor een lijst met alle wijzigingen die beschikbaar zijn op.
- `PublicClientApplicationOptions`Zie [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) in de referentie documentatie voor een beschrijving van alle opties die worden weer gegeven in.

### <a name="complete-example-with-configuration-options"></a>Volledig voor beeld met configuratie opties

Stel dat u een .NET core-console toepassing hebt die het volgende `appsettings.json` configuratie bestand bevat:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

U hebt weinig code om dit bestand te lezen met behulp van de. NET-werkend configuratie raamwerk:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

U kunt nu de volgende code schrijven om uw toepassing te maken:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Voordat `.Build()` u de-methode aanroept, kunt u uw configuratie overschrijven met aanroepen naar `.WithXXX` methoden, zoals eerder is weer gegeven.

# <a name="java"></a>[Java](#tab/java)

Hier is de klasse die wordt gebruikt in MSAL Java-Ontwikkel voorbeelden voor het configureren van de voor beelden: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[macOS](#tab/macOS)

Met de volgende code wordt een open bare client toepassing geïnstantieerd en worden Microsoft Azure de gebruikers in de open bare Cloud gemeldd met een werk-of school account of een persoonlijke Microsoft-account.

### <a name="quick-configuration"></a>Snelle configuratie

Doel-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Meer uitgebreide configuratie

U kunt het bouwen van toepassingen uitwerken door een aantal para meters toe te voegen. Als u bijvoorbeeld wilt dat uw toepassing een multi tenant-toepassing in een nationale Cloud is, zoals de Amerikaanse overheid die hier wordt weer gegeven, kunt u het volgende schrijven:

Doel-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de bureau blad-app](scenario-desktop-acquire-token.md).
---
title: Beveiligde web API-apps configureren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en het configureren van de code van uw toepassing.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9ffb77db4f7bcd5a07e25085eed17e8972aa9a33
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683756"
---
# <a name="protected-web-api-code-configuration"></a>Beveiligde web-API: code configuratie

Als u de code voor de beveiligde web-API wilt configureren, moet u het volgende weten:

- Waarvan Api's als beveiligd worden gedefinieerd.
- Een Bearer-token configureren.
- Het token valideren.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Wat definieert ASP.NET-en ASP.NET Core-Api's als beveiligd?

Net als web-apps zijn de ASP.NET-en ASP.NET Core Web-Api's beschermd, omdat hun controller acties worden voorafgegaan door het kenmerk **[autoriseren]** . De controller acties kunnen alleen worden aangeroepen als de API wordt aangeroepen met een geautoriseerde identiteit.

Denk na over de volgende vragen:

- Alleen een app kan een web-API aanroepen. Hoe kent de API de identiteit van de app waarmee deze wordt aangeroepen?
- Als de app namens een gebruiker de API aanroept, wat is de identiteit van de gebruiker?

## <a name="bearer-token"></a>Bearer-token

Het Bearer-token dat is ingesteld in de header wanneer de app wordt aangeroepen, bevat informatie over de app-identiteit. Het bevat ook informatie over de gebruiker, tenzij de web-app service-to-service-aanroepen accepteert vanuit een daemon-app.

Hier volgt een voor beeld van een C#-code waarin een client wordt weer gegeven die de API aanroept na het ophalen van een token met de micro soft Authentication Library voor .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Een client toepassing vraagt het Bearer-token aan bij het micro soft Identity platform-eind punt *voor de Web-API*. De Web-API is de enige toepassing waarmee het token moet worden geverifieerd en de claims worden weer gegeven. Client-apps moeten nooit proberen de claims in tokens te controleren.
>
> In de toekomst is het mogelijk dat het token moet worden versleuteld met de Web-API. Deze vereiste zou de toegang voor client-apps die toegangs tokens kunnen bekijken, voor komen.

## <a name="jwtbearer-configuration"></a>JwtBearer-configuratie

In deze sectie wordt beschreven hoe u een Bearer-token configureert.

### <a name="config-file"></a>Configuratie bestand

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Als u een aangepaste App-ID-URI hebt gebruikt voor uw web-API

Als u de URI voor de App-ID hebt geaccepteerd die is voorgesteld door de app-registratie Portal, hoeft u de doel groep niet op te geven (Zie [URI en bereiken van de toepassings-id](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). Anders moet u een eigenschap toevoegen `Audience` waarvan de waarde de URI van de app-id voor uw web-API is.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Code-initialisatie

Wanneer een app wordt aangeroepen voor een controller actie met een kenmerk **[autoriseren]** , ASP.NET en ASP.net core het toegangs token uit het Bearer-token van de autorisatie-header ophalen. Het toegangs token wordt vervolgens doorgestuurd naar de JwtBearer-middleware, die micro soft Identity model-extensies aanroept voor .NET.

#### <a name="microsoftidentityweb"></a>Micro soft. Identity. Web

Micro soft raadt u aan het NuGet-pakket [micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) te gebruiken bij het ontwikkelen van een web-API met ASP.net core.

_Micro soft. Identity. Web_ biedt de lijm tussen ASP.net core, de verificatie-middleware en de [micro soft Authentication Library (MSAL)](msal-overview.md) voor .net. Het biedt een duidelijker, robuuster ervaring voor ontwikkel aars en maakt gebruik van de kracht van het micro soft-identiteits platform en de Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Micro soft. Identity. Web sjablonen gebruiken

U kunt een volledig nieuwe web-API maken met behulp van micro soft. Identity. web project-sjablonen. Zie [micro soft. Identity. Web-Web API project Temp late (Engelstalig)](https://aka.ms/ms-id-web/webapi-project-templates)voor meer informatie.

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Beginnen met een bestaande ASP.NET Core 3,1-toepassing

Momenteel gebruikt ASP.NET Core 3,1 de bibliotheek micro soft. AspNetCore. AzureAD. UI. De middleware wordt geïnitialiseerd in het Startup.cs-bestand.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

De middleware wordt toegevoegd aan de Web-API met behulp van deze instructie:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Op dit moment maken de ASP.NET Core sjablonen Azure Active Directory (Azure AD) Web-Api's waarmee gebruikers zich kunnen aanmelden binnen uw organisatie of een organisatie. Gebruikers kunnen zich niet aanmelden met persoonlijke accounts. U kunt de sjablonen echter wijzigen om het micro soft Identity platform-eind punt te gebruiken met behulp van [micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) Vervang de code in *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

u kunt ook het volgende schrijven (dit is gelijk aan)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Als u micro soft. Identity. web gebruikt en de `Audience` in- *appsettings.js* niet hebt ingesteld op, wordt het volgende gebruikt:
> -  `$"{ClientId}"` Als u de [geaccepteerde versie van het toegangs token](scenario-protected-web-api-app-registration.md#accepted-token-version) hebt ingesteld op `2` of voor Azure AD B2C Web-api's.
> - `$"api://{ClientId}` in alle andere gevallen (voor [toegangs tokens](access-tokens.md)van v 1.0).
> Zie micro soft. Identity. web [Source code](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)(Engelstalig) voor meer informatie.

Het vorige code fragment wordt geëxtraheerd uit de [Stapsgewijze zelf studie voor de ASP.net core web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). De details van **AddMicrosoftIdentityWebApiAuthentication** zijn beschikbaar in [micro soft. Identity. Web](microsoft-identity-web.md). Met deze methode wordt [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?preserve-view=true&view=azure-dotnet-preview)aangeroepen, die zelf de middleware geeft over het valideren van het token.

## <a name="token-validation"></a>Token validatie

In het voor gaande fragment valideert de JwtBearer-middleware, zoals de OpenID Connect Connect-koppeling in web apps, het token op basis van de waarde van `TokenValidationParameters` . Het token wordt naar behoefte ontsleuteld, de claims worden geëxtraheerd en de hand tekening wordt gecontroleerd. De middleware valideert vervolgens het token door te controleren op deze gegevens:

- Doel groep: het token is gericht op de Web-API.
- Sub: het certificaat is uitgegeven voor een app die de Web-API mag aanroepen.
- Uitgever: het is uitgegeven door een Trusted Security Token Service (STS).
- Verloop datum: de levens duur ligt binnen het bereik.
- Hand tekening: er is niet mee geknoeid.

Er kunnen ook speciale validaties zijn. Het is bijvoorbeeld mogelijk om te controleren of de ondertekeningssleutels, wanneer deze zijn Inge sloten in een token, worden vertrouwd en dat het token niet opnieuw wordt afgespeeld. Ten slotte vereisen sommige protocollen specifieke validaties.

### <a name="validators"></a>Controles

De validatie stappen worden vastgelegd in validators, die worden weer gegeven in de [micro soft Identity model Extensions voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open-source-bibliotheek. De validatie functies worden gedefinieerd in het bron bestand van de bibliotheek [micro soft. Identity model. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

In deze tabel worden de validatie functies beschreven:

| Validator | Beschrijving |
|---------|---------|
| **ValidateAudience** | Zorgt ervoor dat het token voor de toepassing is die het token voor u valideert. |
| **ValidateIssuer** | Hiermee zorgt u ervoor dat het token is uitgegeven door een vertrouwde STS, wat inhoudt dat u vertrouwt. |
| **ValidateIssuerSigningKey** | Zorgt ervoor dat de toepassing die het token valideert, de sleutel vertrouwt die is gebruikt voor het ondertekenen van het token. Er is een speciaal geval waarin de sleutel is inge sloten in het token. Maar deze situatie doet zich meestal niet voor. |
| **ValidateLifetime** | Zorgt ervoor dat het token nog of al geldig is. Met de validatie functie wordt gecontroleerd of de levens duur van het token zich in het bereik bevindt dat is opgegeven door de **notBefore** en **verlopen** claims. |
| **ValidateSignature** | Hiermee wordt gegarandeerd dat er niet met het token is geknoeid. |
| **ValidateTokenReplay** | Hiermee zorgt u ervoor dat het token niet opnieuw is afgespeeld. Er is een speciaal geval voor sommige eenmalige-protocollen. |

#### <a name="customizing-token-validation"></a>Token validatie aanpassen

De validators zijn gekoppeld aan eigenschappen van de klasse **TokenValidationParameters** . De eigenschappen worden geïnitialiseerd vanuit de configuratie ASP.NET en ASP.NET Core.

In de meeste gevallen hoeft u de para meters niet te wijzigen. Apps die geen enkele Tenant zijn, zijn uitzonde ringen. Deze web-apps accepteren gebruikers van elke organisatie of van persoonlijke micro soft-accounts. Uitgevers in dit geval moeten worden gevalideerd. Micro soft. Identity. Web zorgt voor een goede validatie van de verlener. Zie micro soft. Identity. web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)voor meer informatie.

Als u in ASP.NET Core de para meters voor token validatie wilt aanpassen, gebruikt u het volgende code fragment in uw *Startup.cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Voor ASP.NET MVC laat het volgende code voorbeeld zien hoe u aangepaste token validatie kunt uitvoeren:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Token validatie in Azure Functions

U kunt ook binnenkomende toegangs tokens in Azure Functions valideren. Voor beelden van dergelijke validatie vindt u in de volgende code voorbeelden op GitHub:

- .NET: [Azure-samples/MS-Identity-DotNet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-samples/MS-Identity-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-samples/MS-Identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [Controleer bereiken en app-rollen in uw code](scenario-protected-web-api-verification-scope-app-roles.md).
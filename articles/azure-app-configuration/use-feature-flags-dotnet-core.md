---
title: Zelfstudie voor het gebruik van functievlaggen in een .NET Core-app | Microsoft Docs
description: In deze zelfstudie leert u hoe u functievlaggen kunt implementeren in .NET Core-apps.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 2f141b896ef11fecdf156d062a78252ce6f7ffb3
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734980"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Zelfstudie: Functievlaggen gebruiken in een ASP.NET Core-app

De .NET Core Feature Management-bibliotheken bieden idiomatische ondersteuning voor het implementeren van functievlaggen in een .NET- of ASP.NET Core-toepassing. Met deze bibliotheken kunt u functievlaggen declaratief toevoegen aan uw code, zodat u niet alle `if`-instructies handmatig hoeft te schrijven.

De Feature Management-bibliotheken beheren ook de levenscyclus van functievlaggen achter de schermen. Bijvoorbeeld: de bibliotheken worden vernieuwd en slaan vlagstatussen op in een cache of garanderen dat een vlagstatus onveranderbaar is tijdens het aanroepen van een aanvraag. Daarnaast biedt de ASP.NET Core-bibliotheek kant-en-klare integraties, waaronder acties, weergaven, routes en middleware voor MVC-controllers.

De [quickstart Functievlaggen toevoegen aan een ASP.NET Core-app](./quickstart-feature-flag-aspnet-core.md) bevat verschillende manieren om functievlaggen toe te voegen in een ASP.NET Core-toepassing. In deze zelfstudie worden deze methoden uitgebreid beschreven. Zie de [documentatie over het beheren van ASP.NET Core-functies](/dotnet/api/microsoft.featuremanagement) voor een volledig overzicht.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functievlaggen toe aan de belangrijkste onderdelen van uw toepassing om de beschikbaarheid van functies te bepalen.
> * Integreer met App Configuration wanneer u deze gebruikt voor het beheren van functievlaggen.

## <a name="set-up-feature-management"></a>Functiebeheer instellen

Voeg een verwijzing naar de NuGet-pakketten `Microsoft.FeatureManagement.AspNetCore` en `Microsoft.FeatureManagement` toe om gebruik te maken van de .NET Core-functiebeheerder.
De .NET Core-functiebeheerder `IFeatureManager` krijgt functievlaggen van het systeemeigen configuratiesysteem van het framework. Als gevolg hiervan kunt u de functievlaggen van uw toepassing definiëren met behulp van een configuratiebron die .NET Core ondersteunt, met inbegrip van het lokale *appsettings.json*-bestand of omgevingsvariabelen. `IFeatureManager` maakt gebruik van .NET Core-afhankelijkheidsinjectie. U kunt de functiebeheerservices registreren met behulp van standaard conventies:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Standaard haalt de functiebeheerder functievlaggen op uit de sectie `"FeatureManagement"` van de .NET Core-configuratiegegevens. In het volgende voorbeeld moet de functiebeheerder deze echter lezen vanuit een andere sectie met de naam `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Als u filters in uw functievlaggen gebruikt, moet u een extra bibliotheek toevoegen en registreren. In het volgende voorbeeld ziet u hoe u een ingebouwd functiefilter gebruikt met de naam `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

U wordt aangeraden de functievlaggen buiten de toepassing te houden en deze afzonderlijk te beheren. Als u dit doet, kunt u de status van de vlaggen op elk gewenst moment wijzigen. Deze wijzigingen worden direct doorgevoerd in de toepassing. App Configuration biedt een centrale locatie voor het organiseren en beheren van al uw functievlaggen via een toegewezen portal-gebruikersinterface. App Configuration levert ook rechtstreeks de vlaggen aan uw toepassing via de clientbibliotheken voor .NET Core.

De eenvoudigste manier om uw .NET Core-toepassing te verbinden met App Configuration is via de configuratieprovider `Microsoft.Azure.AppConfiguration.AspNetCore`. Voer deze stappen uit om dit NuGet-pakket te gebruiken.

1. Open het bestand *Program.cs* en voeg de volgende code toe.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Open *Startup.cs* en werk de `Configure` `ConfigureServices` methode en bij om de ingebouwde middleware met de naam toe te voegen `UseAzureAppConfiguration` . Met deze middleware kunnen de waarden van de functievlaggen periodiek worden vernieuwd terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
De waarden van de functievlaggen worden naar verwachting in de loop van de tijd gewijzigd. Standaard worden de waarden van de functievlaggen in de cache opgeslagen gedurende een periode van 30 seconden. Als een vernieuwingsbewerking wordt geactiveerd terwijl de middleware een aanvraag ontvangt, wordt de waarde dus pas bijgewerkt als de waarde in de cache verloopt. De volgende code laat zien hoe u de vervaltijd van de cache of het polling-interval wijzigt in 5 minuten met de aanroep `options.UseFeatureFlags()`.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaratie van functievlaggen

Elke functievlag bestaat uit twee delen: een naam en een lijst met een of meer filters die worden gebruikt om te evalueren of de status van een functie *aan* is (dat wil zeggen, wanneer de waarde ervan `True` is). Een filter definieert een use-case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functievlag meerdere filters heeft, wordt de filterlijst in de juiste volgorde doorgelopen, totdat een van de filters bepaalt dat de functie moet worden ingeschakeld. Op dat moment is de functievlag *aan* en worden alle resterende filterresultaten overgeslagen. Als geen enkel filter aangeeft dat de functie moet worden ingeschakeld, is de functievlag *uit*.

De functiebeheerder ondersteunt *appsettings.json* als een configuratiebron voor functievlaggen. In het volgende voorbeeld ziet u hoe u functievlaggen kunt instellen in een JSON-bestand:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Standaard wordt het gedeelte `FeatureManagement` van dit JSON-document gebruikt voor instellingen voor functievlaggen. Het vorige voorbeeld toont drie functievlaggen waarvoor de filters zijn gedefinieerd in de eigenschap `EnabledFor`:

* `FeatureA` is *aan*.
* `FeatureB` is *uit*.
* Met `FeatureC` geeft u een filter op met de naam `Percentage` met een eigenschap `Parameters`. `Percentage` is een configureerbaar filter. In dit voorbeeld geeft `Percentage` een kans van 50 procent dat de vlag `FeatureC` *aan* is.

## <a name="feature-flag-references"></a>Verwijzingen naar functievlaggen

Om gemakkelijk naar functievlaggen in code te kunnen verwijzen, moet u deze definiëren als `enum`-variabelen:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Controles van functievlaggen

Het basispatroon van functiebeheer is om eerst te controleren of een functievlag is ingesteld op *aan*. Als dit het geval is, voert het functiebeheer de acties uit die de functie bevat. Bijvoorbeeld:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In ASP.NET Core MVC krijgt u toegang tot de functiebeheerder `IFeatureManager` via afhankelijkheidsinjectie:

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Controller-acties

In MVC-controllers gebruikt u het kenmerk `FeatureGate` om te controleren of een hele controllerklasse of een bepaalde actie is ingeschakeld. Voor de volgende `HomeController`-controller moet `FeatureA` worden ingesteld op *aan* voordat een actie in de controllerklasse kan worden uitgevoerd:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Voor de volgende actie `Index` moet `FeatureA` *aan* zijn voordat deze kan worden uitgevoerd:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Wanneer een MVC-controller of -actie wordt geblokkeerd omdat de vlag voor het controleren van de functie *uit* is, wordt een geregistreerde `IDisabledFeaturesHandler`-interface aangeroepen. De standaard `IDisabledFeaturesHandler`-interface retourneert een 404-statuscode naar de client zonder hoofdtekst van de reactie.

## <a name="mvc-views"></a>MVC-weergaven

Open *_ViewImports.cshtml* in de map *Views* en voeg de taghelper voor functiebeheer toe:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

In MVC-weergaven kunt u een `<feature>`-tag gebruiken om inhoud weer te geven op basis van het feit of een functievlag is ingeschakeld:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Om alternatieve inhoud weer te geven als niet aan de vereisten wordt voldaan, kan het kenmerk `negate` worden gebruikt.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

De `<feature>`-tag kan ook worden gebruikt voor het weergeven van inhoud als een of alle functies in een lijst zijn ingeschakeld.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filters

U kunt MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functievlag. Met de volgende code wordt een MVC-filter toegevoegd met de naam `SomeMvcFilter`. Dit filter wordt alleen in de MVC-pijplijn geactiveerd als `FeatureA` is ingeschakeld. Deze mogelijkheid is beperkt tot `IAsyncActionFilter`. 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Middleware

U kunt functievlaggen ook gebruiken om voorwaardelijk toepassingsvertakkingen en middleware toe te voegen. Met de volgende code wordt alleen een middleware-onderdeel in de aanvraagpijplijn ingevoegd wanneer `FeatureA` is ingeschakeld:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Deze code bouwt voort op de meer generieke mogelijkheid om de hele toepassing te vertakken op basis van een functievlag:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u functievlaggen kunt implementeren in een ASP.NET Core-toepassing met behulp van de `Microsoft.FeatureManagement`-bibliotheken. Raadpleeg de volgende bronnen voor meer informatie over de ondersteuning van functiebeheer in ASP.NET Core en App Configuration:

* [Voorbeeldcode voor de ASP.NET Core-functievlag](./quickstart-feature-flag-aspnet-core.md)
* [Documentatie voor Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Functievlaggen beheren](./manage-feature-flags.md)

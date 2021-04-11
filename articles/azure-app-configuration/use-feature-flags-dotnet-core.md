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
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729504"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Zelfstudie: Functievlaggen gebruiken in een ASP.NET Core-app

De .NET Core Feature Management-bibliotheken bieden idiomatische ondersteuning voor het implementeren van functievlaggen in een .NET- of ASP.NET Core-toepassing. Met deze bibliotheken kunt u functie vlaggen declaratief toevoegen aan uw code, zodat u niet hand matig code hoeft te schrijven om functies met instructies in of uit te scha kelen `if` .

De Feature Management-bibliotheken beheren ook de levenscyclus van functievlaggen achter de schermen. Bijvoorbeeld: de bibliotheken worden vernieuwd en slaan vlagstatussen op in een cache of garanderen dat een vlagstatus onveranderbaar is tijdens het aanroepen van een aanvraag. Daarnaast biedt de ASP.NET Core-bibliotheek kant-en-klare integraties, waaronder acties, weergaven, routes en middleware voor MVC-controllers.

De [functie vlaggen toevoegen aan een ASP.net core-app Quick](./quickstart-feature-flag-aspnet-core.md) start toont een eenvoudig voor beeld van het gebruik van functie vlaggen in een ASP.net core toepassing. In deze zelf studie worden aanvullende opties voor het instellen en de mogelijkheden van de beheer bibliotheken voor onderdelen weer gegeven. U kunt de voor beeld-app die u in de Quick Start hebt gemaakt, gebruiken om de voorbeeld code uit te proberen die in deze zelf studie wordt weer gegeven. 

Zie [micro soft. FeatureManagement-naam ruimte](/dotnet/api/microsoft.featuremanagement)voor de API-referentie documentatie voor ASP.net core feature management.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functievlaggen toe aan de belangrijkste onderdelen van uw toepassing om de beschikbaarheid van functies te bepalen.
> * Integreer met App Configuration wanneer u deze gebruikt voor het beheren van functievlaggen.

## <a name="set-up-feature-management"></a>Functiebeheer instellen

Voor toegang tot de .NET core feature manager moet uw app verwijzingen naar het `Microsoft.FeatureManagement.AspNetCore` NuGet-pakket bevatten.

Het .NET Core-functie beheer is geconfigureerd vanuit het systeem eigen configuratiesysteem van het Framework. Als gevolg hiervan kunt u de instellingen voor de functie vlag van uw toepassing definiëren met behulp van een configuratie bron die .NET core ondersteunt, inclusief de lokale *appsettings.jsvoor* bestands-of omgevings variabelen.

Standaard wordt in functie beheer de configuratie van de functie vlag opgehaald uit de `"FeatureManagement"` sectie van de .net core-configuratie gegevens. Als u de standaard configuratie locatie wilt gebruiken, roept u de methode [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) aan van de **IServiceCollection** die is door gegeven aan de methode **ConfigureServices** van de klasse **Startup** .


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

U kunt opgeven dat de configuratie van functie beheer moet worden opgehaald uit een andere configuratie sectie door het aanroepen van [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) en door geven van de naam van de gewenste sectie. In het volgende voorbeeld moet de functiebeheerder deze echter lezen vanuit een andere sectie met de naam `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Als u filters in uw functie vlaggen gebruikt, moet u de naam ruimte [micro soft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) opnemen en een aanroep toevoegen aan [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) die de type naam aangeeft van het filter dat u wilt gebruiken als algemeen type van de methode. Zie voor meer informatie over het gebruik van functie filters voor het dynamisch in-en uitschakelen van de functionaliteit voor het inschakelen [van gefaseerde implementatie van functies voor doel groepen](./howto-targetingfilter-aspnet-core.md).

In het volgende voorbeeld ziet u hoe u een ingebouwd functiefilter gebruikt met de naam `PercentageFilter`:



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

In plaats van uw functie vlaggen in uw toepassing te coderen, wordt u aangeraden de functie vlaggen buiten de toepassing te houden en deze afzonderlijk te beheren. Als u dit doet, kunt u de status van de vlaggen op elk gewenst moment wijzigen. Deze wijzigingen worden direct doorgevoerd in de toepassing. De Azure-app Configuration-service biedt een speciale portal-gebruikers interface voor het beheren van al uw functie vlaggen. De Azure-app Configuration-service levert ook rechtstreeks de functie vlaggen aan uw toepassing via de .NET core-client bibliotheken.

De eenvoudigste manier om uw ASP.NET Core-toepassing te koppelen aan app-configuratie is via de configuratie provider die is opgenomen in het `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-pakket. Nadat u een verwijzing naar het pakket hebt opgenomen, volgt u deze stappen om dit NuGet-pakket te gebruiken.

1. Open het bestand *Program.cs* en voeg de volgende code toe.
    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.x. Selecteer de juiste syntaxis op basis van uw omgeving.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Open *Start. cs* en werk de `Configure` `ConfigureServices` methode en bij om de ingebouwde middleware met de naam toe te voegen `UseAzureAppConfiguration` . Met deze middleware kunnen de waarden van de functievlaggen periodiek worden vernieuwd terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
In een typisch scenario werkt u de waarden van de functie vlag regel matig bij wanneer u de functies van uw toepassing implementeert en inschakelt. Standaard worden de waarden van de functievlaggen in de cache opgeslagen gedurende een periode van 30 seconden. Als een vernieuwingsbewerking wordt geactiveerd terwijl de middleware een aanvraag ontvangt, wordt de waarde dus pas bijgewerkt als de waarde in de cache verloopt. De volgende code laat zien hoe u de verval tijd van de cache of polling-interval wijzigt in 5 minuten door de [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) in te stellen in de aanroep van **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Declaratie van functievlaggen

Elke declaratie van functie vlaggen bestaat uit twee delen: een naam en een lijst met een of meer filters die worden gebruikt om te evalueren of de status van een functie is *ingeschakeld* (dat wil zeggen, wanneer de waarde ervan is `True` ). Een filter definieert een criterium voor wanneer een functie moet worden ingeschakeld.

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
* Met `FeatureC` geeft u een filter op met de naam `Percentage` met een eigenschap `Parameters`. `Percentage` is een configureerbaar filter. In dit voorbeeld geeft `Percentage` een kans van 50 procent dat de vlag `FeatureC` *aan* is. Zie [functie filters gebruiken om voorwaardelijke functie vlaggen in te scha kelen](./howto-feature-filters-aspnet-core.md)voor een hand leiding voor het gebruik van functie filters.




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Afhankelijkheids injectie gebruiken om toegang te krijgen tot IFeatureManager 

Voor sommige bewerkingen, zoals het hand matig controleren van functie vlag waarden, moet u een instantie van [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview)ophalen. In ASP.NET Core MVC kunt u de functie beheer openen `IFeatureManager` via afhankelijkheids injectie. In het volgende voor beeld wordt een argument van `IFeatureManager` het type toegevoegd aan de hand tekening van de constructor voor een controller. De runtime lost de verwijzing automatisch op en biedt een van de interface bij het aanroepen van de constructor. Als u een toepassings sjabloon gebruikt waarbij de controller al een of meer argumenten voor het invoegen van afhankelijkheden heeft in de constructor, bijvoorbeeld `ILogger` , kunt u gewoon toevoegen `IFeatureManager` als een extra argument:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

---

## <a name="feature-flag-references"></a>Verwijzingen naar functievlaggen

Definieer functie vlaggen als teken reeks variabelen om te verwijzen naar de volgende code:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Controles van functievlaggen

Een gemeen schappelijk patroon van functie beheer is om te controleren of een functie vlag is ingesteld op *aan* , en als dit het geval is, voert u een sectie van code uit. Bijvoorbeeld:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Controller-acties

Met MVC-controllers kunt u het `FeatureGate` kenmerk gebruiken om te bepalen of een volledige controller klasse of een specifieke actie is ingeschakeld. Voor de volgende `HomeController`-controller moet `FeatureA` worden ingesteld op *aan* voordat een actie in de controllerklasse kan worden uitgevoerd:

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

Wanneer een MVC-controller of-actie wordt geblokkeerd omdat de vlag voor het bepalen van de functie is *uitgeschakeld*, wordt een geregistreerde [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) -interface aangeroepen. De standaard `IDisabledFeaturesHandler`-interface retourneert een 404-statuscode naar de client zonder hoofdtekst van de reactie.

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

U kunt MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functievlag. Deze mogelijkheid is beperkt tot filters waarmee [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter)wordt geïmplementeerd. Met de volgende code wordt een MVC-filter toegevoegd met de naam `ThirdPartyActionFilter`. Dit filter wordt alleen in de MVC-pijplijn geactiveerd als `FeatureA` is ingeschakeld.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

U kunt functievlaggen ook gebruiken om voorwaardelijk toepassingsvertakkingen en middleware toe te voegen. Met de volgende code wordt alleen een middleware-onderdeel in de aanvraagpijplijn ingevoegd wanneer `FeatureA` is ingeschakeld:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
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
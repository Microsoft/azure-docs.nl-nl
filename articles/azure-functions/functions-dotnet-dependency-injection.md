---
title: Afhankelijkheidsinjectie gebruiken in .NET Azure Functions
description: Meer informatie over het gebruik van afhankelijkheids injectie voor het registreren en gebruiken van services in .NET functions
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: f87ed9b7455bed870cf25a6920cc6295811d94c8
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617065"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Afhankelijkheidsinjectie gebruiken in .NET Azure Functions

Azure Functions ondersteunt het ' Dependency Injection (DI)-ontwerp patroon voor software. Dit is een techniek voor het bezorgen [van een inversie van het besturings element (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tussen klassen en hun afhankelijkheden.

- Het invoegen van afhankelijkheden in Azure Functions is gebaseerd op de functies van de .NET core-invoeg injectie. Het is raadzaam om vertrouwd te raken met het [invoeren van .net core-afhankelijkheid](/aspnet/core/fundamentals/dependency-injection) . Er zijn verschillen in hoe u afhankelijkheden overschrijft en hoe configuratie waarden worden gelezen met Azure Functions op het verbruiks plan.

- Ondersteuning voor het invoegen van afhankelijkheden begint met Azure Functions 2. x.

## <a name="prerequisites"></a>Vereisten

Voordat u afhankelijkheids injectie kunt gebruiken, moet u de volgende NuGet-pakketten installeren:

- [Micro soft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Micro soft. net. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) pakket versie 1.0.28 of hoger

## <a name="register-services"></a>Services registreren

Als u services wilt registreren, maakt u een methode om onderdelen te configureren en toe te voegen aan een `IFunctionsHostBuilder` exemplaar.  Op de Azure Functions-host wordt een exemplaar van gemaakt `IFunctionsHostBuilder` en direct in uw methode door gegeven.

Als u de methode wilt registreren, voegt `FunctionsStartup` u het kenmerk assembly toe dat de type naam specificeert die wordt gebruikt tijdens het opstarten.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

In dit voor beeld wordt het pakket [micro soft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) gebruikt om een bij het opstarten te registreren `HttpClient` .

### <a name="caveats"></a>Waarschuwingen

Een reeks registratie stappen die vóór en na de uitvoering van de runtime worden uitgevoerd, wordt de opstart klasse. Denk daarom aan de volgende punten:

- *De klasse Startup is uitsluitend bedoeld voor installatie en registratie.* Vermijd het gebruik van services die bij het opstarten worden geregistreerd tijdens het opstart proces. Probeer bijvoorbeeld niet een bericht in een logboek te registreren dat tijdens het opstarten wordt geregistreerd. Dit moment van het registratie proces is te vroeg om uw services beschikbaar te maken voor gebruik. Nadat de `Configure` methode is uitgevoerd, blijft de runtime van functions extra afhankelijkheden registreren. Dit kan van invloed zijn op de werking van uw services.

- *De container voor de injectie van afhankelijkheden bevat alleen expliciet geregistreerde typen*. De enige services die beschikbaar zijn als Injecteer bare typen, zijn wat is ingesteld in de- `Configure` methode. Als gevolg hiervan zijn functies-specifieke typen zoals `BindingContext` en `ExecutionContext` niet beschikbaar tijdens de installatie of als Injecteer bare typen.

## <a name="use-injected-dependencies"></a>Geïnjecteerde afhankelijkheden gebruiken

Injectie van constructor wordt gebruikt om uw afhankelijkheden beschikbaar te maken in een functie. Voor het gebruik van de constructor-injectie moet u geen statische klassen gebruiken voor geïnjecteerde Services of voor uw functie klassen.

In het volgende voor beeld ziet u hoe de `IMyService` en- `HttpClient` afhankelijkheden worden geïnjecteerd in een functie die door http wordt geactiveerd.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

In dit voor beeld wordt het pakket [micro soft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) gebruikt om een bij het opstarten te registreren `HttpClient` .

## <a name="service-lifetimes"></a>Levens duur van service

Azure Functions-apps bieden dezelfde levens duur van de service als [ASP.net-afhankelijkheids injectie](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Voor een functions-app gedragen de verschillende levens duur van de service er als volgt uit:

- **Tijdelijk**: tijdelijke services worden bij elke oplossing van de service gemaakt.
- **Bereik**: de levens duur van het bereik van de service komt overeen met de levens duur van een functie. Services met een bereik worden eenmaal per functie-uitvoering gemaakt. Latere aanvragen voor die service tijdens het uitvoeren van het bestaande service-exemplaar opnieuw gebruiken.
- **Singleton**: de levens duur van de singleton-service komt overeen met de levens duur van de host en wordt opnieuw gebruikt voor het uitvoeren van functies voor dat exemplaar. Services voor de singleton-levens duur worden aanbevolen voor verbindingen en clients, bijvoorbeeld `DocumentClient` of `HttpClient` exemplaren.

Bekijk of down load een voor [beeld van de verschillende levens duur](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) van de service op github.

## <a name="logging-services"></a>Services voor logboekregistratie

Als u uw eigen registratie provider nodig hebt, moet u een aangepast type registreren als een exemplaar van [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , dat beschikbaar is via het [micro soft. Extensions. logges. abstracties](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) NuGet-pakket.

Application Insights wordt automatisch toegevoegd door Azure Functions.

> [!WARNING]
> - Voeg geen toevoegen `AddApplicationInsightsTelemetry()` aan de services-verzameling, waarmee services worden geregistreerd die conflicteren met services die worden meegeleverd door de omgeving.
> - Registreer uw eigen `TelemetryConfiguration` functie niet of `TelemetryClient` Als u de ingebouwde Application Insights functionaliteit gebruikt. Als u uw eigen exemplaar moet configureren `TelemetryClient` , maakt u er een via het geïnjecteerde, `TelemetryConfiguration` zoals wordt weer gegeven in de [logboek aangepaste telemetrie in C#-functies](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> en ILoggerFactory

De host injecteert `ILogger<T>` en `ILoggerFactory` Services in constructors.  Deze nieuwe logboek registratie filters zijn echter standaard gefilterd op de functie Logboeken.  U moet het bestand wijzigen `host.json` om u aan te melden voor aanvullende filters en categorieën.

In het volgende voor beeld ziet u hoe u een `ILogger<HttpTrigger>` with-Logboeken kunt toevoegen die aan de host worden blootgesteld.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Het volgende voorbeeld `host.json` bestand voegt het logboek filter toe.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Zie [logboek niveaus configureren](configure-monitoring.md#configure-log-levels)voor meer informatie over logboek niveaus.

## <a name="function-app-provided-services"></a>Services die door de functie-app worden meegeleverd

De functie-host registreert veel services. De volgende services zijn veilig als afhankelijkheid in uw toepassing:

|Servicetype|Dood|Beschrijving|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime configuratie|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwoordelijk voor het opgeven van de ID van het exemplaar van de host|

Als er andere services zijn waarvoor u een afhankelijkheid wilt maken, [maakt u een probleem en stelt u deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Host-Services overschrijven

Het overschrijven van services die door de host worden geboden, wordt momenteel niet ondersteund.  Als er services zijn die u wilt overschrijven, kunt u [een probleem maken en deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Werken met opties en instellingen

De waarden die zijn gedefinieerd in de [app-instellingen](./functions-how-to-use-azure-function-app-settings.md#settings) zijn beschikbaar in een `IConfiguration` exemplaar, waarmee u waarden voor app-instellingen in de klasse Startup kunt lezen.

U kunt waarden uit het exemplaar extra heren `IConfiguration` in een aangepast type. Als u de waarden van de app-instellingen naar een aangepast type kopieert, kunt u uw services eenvoudig testen door deze waarden Injecteer te maken. De instellingen die in het configuratie-exemplaar worden gelezen, moeten eenvoudige sleutel-waardeparen zijn.

Houd rekening met de volgende klasse die een eigenschap bevat met de naam consistent met een app-instelling:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

En een `local.settings.json` bestand dat de aangepaste instelling als volgt kan structureren:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Vanuit de `Startup.Configure` methode kunt u waarden uit het `IConfiguration` exemplaar in uw aangepaste type ophalen met de volgende code:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Het aanroepen `Bind` van kopieën waarden die overeenkomende eigenschapnamen van eigenschappen van de configuratie in het aangepaste exemplaar hebben. Het optie-exemplaar is nu beschikbaar in de IoC-container om in een functie te injecteren.

Het object Options wordt in de functie als een exemplaar van de algemene interface ingevoegd `IOptions` . Gebruik de `Value` eigenschap om toegang te krijgen tot de waarden die in uw configuratie zijn gevonden.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Raadpleeg het [Opties patroon in ASP.net core](/aspnet/core/fundamentals/configuration/options) voor meer informatie over het werken met opties.

## <a name="customizing-configuration-sources"></a>Configuratie bronnen aanpassen

> [!NOTE]
> Het aanpassen van de configuratie bron is beschikbaar vanaf Azure Functions host-versies 2.0.14192.0 en 3.0.14191.0.

Als u aanvullende configuratie bronnen wilt opgeven, overschrijft u de `ConfigureAppConfiguration` methode in de klasse van de functie-app `StartUp` .

In het volgende voor beeld worden configuratie waarden van een basis en een optionele omgeving-specifieke app-instellingen bestanden toegevoegd.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Configuratie providers toevoegen aan de `ConfigurationBuilder` eigenschap van `IFunctionsConfigurationBuilder` . Zie [configuratie in ASP.net core](/aspnet/core/fundamentals/configuration/#configuration-providers)voor meer informatie over het gebruik van configuratie providers.

A `FunctionsHostBuilderContext` wordt verkregen van `IFunctionsConfigurationBuilder.GetContext()` . Gebruik deze context om de huidige omgevings naam op te halen en de locatie van de configuratie bestanden in de map van de functie-app op te lossen.

Standaard worden configuratie bestanden zoals *appsettings.jsop* niet automatisch gekopieerd naar de uitvoermap van de functie-app. Werk het *. csproj* -bestand bij zodat het overeenkomt met het volgende voor beeld om te controleren of de bestanden zijn gekopieerd.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Voor functie-apps die worden uitgevoerd in de verbruiks-of Premium-abonnementen, kunnen wijzigingen in configuratie waarden die worden gebruikt in triggers, schaal fouten veroorzaken. Wijzigingen in deze eigenschappen door de klasse worden veroorzaakt door het `FunctionsStartup` opstarten van een functie-app.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

- [Uw functie-app bewaken](functions-monitoring.md)
- [Aanbevolen procedures voor functies](functions-best-practices.md)

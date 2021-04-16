---
title: .NET-traceerlogboeken verkennen met ILogger - Azure-toepassing Insights
description: Voorbeelden van het gebruik van Azure-toepassing Insights ILogger-provider met ASP.NET Core- en Console-toepassingen.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568587"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider voor Microsoft.Extension.Logging

In dit artikel wordt beschreven hoe u kunt gebruiken `ApplicationInsightsLoggerProvider` om logboeken `ILogger` vast te leggen in de console en ASP.NET Core-toepassingen.
Zie Logboekregistratie in ASP.NET [Core voor meer informatie.](/aspnet/core/fundamentals/logging)

## <a name="aspnet-core-applications"></a>ASP.NET Core-toepassingen

`ApplicationInsightsLoggerProvider`is standaard ingeschakeld voor ASP.NET Core-toepassingen wanneer ApplicationInsights is geconfigureerd met behulp van [code](./asp-net-core.md) of [code-less.](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring)

Alleen *waarschuwings-* `ILogger` en [](/aspnet/core/fundamentals/logging/#log-category)bovenstaande logboeken (uit alle categorieën ) worden standaard Application Insights verzonden. Maar u kunt [dit gedrag aanpassen.](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection) Er zijn aanvullende stappen vereist om ILogger-logboeken van **Program.cs** of **Startup.cs vast te leggen.** (Zie [ILogger-logboeken vastleggen vanuit Startup.cs en Program.cs in ASP.NET Core-toepassingen](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Als u alleen wilt gebruiken zonder andere `ApplicationInsightsLoggerProvider` Application Insights bewaking, gebruikt u de volgende stappen.

1. Installeer het NuGet-pakket:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Wijzig `Program.cs` zoals hier wordt weergegeven:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Met de code in stap 2 wordt `ApplicationInsightsLoggerProvider` geconfigureerd. De volgende code toont een voorbeeld van een Controller-klasse, die gebruikt `ILogger` voor het verzenden van logboeken. De logboeken worden vastgelegd door Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-logboeken van Startup.cs en Program.cs vastleggen in ASP.NET Core-apps

> [!NOTE]
> In ASP.NET Core 3.0 en hoger is het niet meer mogelijk om te injecteren `ILogger` in Startup.cs en Program.cs. Zie https://github.com/aspnet/Announcements/issues/353 voor meer informatie.

`ApplicationInsightsLoggerProvider` kan logboeken van vroeg in het opstarten van de toepassing vastleggen. Hoewel ApplicationInsightsLoggerProvider automatisch wordt ingeschakeld in Application Insights (vanaf versie 2.7.1), is er pas later in de pijplijn een instrumentatiesleutel ingesteld. Dus alleen logboeken van **Controller**/andere klassen worden vastgelegd. Als u elk logboek wilt vastleggen dat begint met **Program.cs** en **Startup.cs** zelf, moet u expliciet een instrumentatiesleutel inschakelen voor ApplicationInsightsLoggerProvider. *TelemetryConfiguration* is ook niet volledig ingesteld wanneer u zich aanmeldt vanuit **Program.cs** of **Startup.cs** zelf. Deze logboeken hebben dus een minimale configuratie die gebruikmaakt van [InMemoryChannel,](./telemetry-channels.md)geen [steekproeven](./sampling.md)en geen standaard [telemetrie-initialisaties of processors.](./api-filtering-sampling.md)

In de volgende voorbeelden wordt deze mogelijkheid **gedemonstreerd met Program.cs** en **Startup.cs.**

#### <a name="example-programcs"></a>Voorbeeld van Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Voorbeeld van Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migreren vanuit de oude ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versies vóór 2.7.1 ondersteunden een logboekregistratieprovider die nu verouderd is. Deze provider is ingeschakeld via de **extensiemethode AddApplicationInsights()** van ILoggerFactory. We raden u aan om te migreren naar de nieuwe provider. Dit omvat twee stappen:

1. Verwijder de *aanroep ILoggerFactory.AddApplicationInsights()* uit de **methodeStartup.Configure()** om dubbele logboekregistratie te voorkomen.
2. Pas eventuele filterregels in code opnieuw toe, omdat deze niet worden gerespecteerd door de nieuwe provider. Overloads of *ILoggerFactory.AddApplicationInsights()* heeft minimale LogLevel- of filterfuncties nodig. Bij de nieuwe provider maakt filteren deel uit van het framework voor logboekregistratie zelf. Dit wordt niet gedaan door de Application Insights provider. Alle filters die worden geleverd via *ILoggerFactory.AddApplicationInsights()* moeten dus worden verwijderd. En filterregels moeten worden opgegeven door de instructies op niveau van logboekregistratie [te](#control-logging-level) volgen. Als uappsettings.jsgebruikt *om* logboekregistratie te filteren, blijft deze werken met de nieuwe provider, omdat beide dezelfde *provideralias, ApplicationInsights, gebruiken.*

U kunt nog steeds de oude provider gebruiken. (Deze wordt alleen verwijderd in een belangrijke versiewijziging naar 3. *xx*.) Maar we raden u aan om de volgende redenen te migreren naar de nieuwe provider:

- De vorige provider biedt geen ondersteuning voor [logboekbereiken.](/aspnet/core/fundamentals/logging#log-scopes) In de nieuwe provider worden eigenschappen van het bereik automatisch als aangepaste eigenschappen toegevoegd aan de verzamelde telemetrie.
- Logboeken kunnen nu veel eerder in de opstartpijplijn van de toepassing worden vastgelegd. Logboeken van **de klassen Program** en **Startup** kunnen nu worden vastgelegd.
- Met de nieuwe provider wordt filteren uitgevoerd op frameworkniveau zelf. U kunt logboeken filteren op Application Insights provider op dezelfde manier als voor andere providers, waaronder ingebouwde providers zoals Console, Foutopsporing, en meer. U kunt dezelfde filters ook toepassen op meerdere providers.
- In ASP.NET Core (2.0 en hoger)  [kunt](https://github.com/aspnet/Announcements/issues/255) u het beste logboekproviders inschakelen door extensiemethoden te gebruiken in ILoggingBuilder in **Program.cs** zelf.

> [!Note]
> De nieuwe provider is beschikbaar voor toepassingen die zijn gericht op NETSTANDARD2.0 of hoger. Vanaf [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.14.0 is er ook een nieuwe provider beschikbaar voor toepassingen die zijn gericht op .NET Framework NET461 of hoger. Als uw toepassing is gericht op oudere .NET Core-versies, zoals .NET Core 1.1, of als deze zijn gericht op de .NET Framework kleiner dan NET46, blijft u de oude provider gebruiken.

## <a name="console-application"></a>Consoletoepassing

> [!NOTE]
> Er is een nieuwe Application Insights-SDK met de naam [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) die kan worden gebruikt om Application Insights (ILogger en andere Application Insights-telemetrie) in te stellen voor consoletoepassingen. Het wordt aanbevolen dit pakket en de bijbehorende instructies hier [te gebruiken.](./worker-service.md)

Als u Alleen ApplicationInsightsLoggerProvider wilt gebruiken zonder andere Application Insights bewaking, gebruikt u de volgende stappen.

Geïnstalleerde pakketten:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

In dit voorbeeld wordt het zelfstandige pakket `Microsoft.Extensions.Logging.ApplicationInsights` gebruikt. Deze configuratie maakt standaard gebruik van de 'bare minimum' TelemetryConfiguration voor het verzenden van gegevens naar Application Insights. Bare minimum betekent dat InMemoryChannel het kanaal is dat wordt gebruikt. Er zijn geen steekproeven en geen standaard TelemetryInitializers. Dit gedrag kan worden overschrijven voor een consoletoepassing, zoals het volgende voorbeeld laat zien.

Installeer dit extra pakket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

In de volgende sectie ziet u hoe u de standaard TelemetryConfiguration overschrijven met behulp van de **services.Configure \<TelemetryConfiguration> ()-methode.** In dit voorbeeld worden `ServerTelemetryChannel` steekproeven en gemaakt. Er wordt een aangepaste ITelemetryInitializer toegevoegd aan de TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Niveau van logboekregistratie bepalen

`ILogger`heeft een ingebouwd mechanisme om [logboekfiltering toe te passen.](/aspnet/core/fundamentals/logging#log-filtering) Hiermee kunt u de logboeken die naar elke geregistreerde provider worden verzonden, met inbegrip van de Application Insights provider. Het filteren kan worden uitgevoerd in de configuratie (meestal met behulp van een *appsettings.jsin* het bestand) of in code.

De volgende voorbeelden laten zien hoe u filterregels kunt toepassen op `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Filterregels maken in de configuratie met appsettings.jsaan

Voor ApplicationInsightsLoggerProvider is de provideralias `ApplicationInsights` . De volgende sectie van *appsettings.jsinstrueert* logboekregistratieproviders over het algemeen om te registreren op niveau *Waarschuwing* en hoger. Vervolgens worden de categorieën voor `ApplicationInsightsLoggerProvider` logboeken overschreven die beginnen met 'Microsoft' op het *niveau Fout* en hoger.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Filterregels in code maken

Het volgende codefragment configureert logboeken voor *Waarschuwing*  en hoger van alle categorieën en voor Fout en hoger van categorieën die beginnen met 'Microsoft' om te worden verzonden naar `ApplicationInsightsLoggerProvider` . Deze configuratie is hetzelfde als in de vorige sectie inappsettings.js *op*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Logboekbereiken

`ApplicationInsightsLoggingProvider` ondersteunt [logboekbereiken](/aspnet/core/fundamentals/logging#log-scopes) en scopes zijn standaard ingeschakeld.

Als het bereik van het type is, wordt elk sleutel-waardepaar in de verzameling als aangepaste eigenschappen toegevoegd aan de `IReadOnlyCollection<KeyValuePair<string,object>>` Application Insights-telemetrie. In het onderstaande voorbeeld worden logboeken vastgelegd als `TraceTelemetry` en hebben ze ('MyKey', 'MyValue') in eigenschappen.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Als een ander type wordt gebruikt als Bereik, worden deze opgeslagen onder de eigenschap 'Bereik' in Application Insights-telemetrie. In het onderstaande voorbeeld `TraceTelemetry` heeft de een eigenschap met de naam 'Bereik' die het bereik bevat.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Wat zijn de oude en nieuwe versies van ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) omvat een ingebouwde ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), die is ingeschakeld via extensiemethoden van **ILoggerFactory.** Deze provider is gemarkeerd als verouderd in versie 2.7.1. Deze wordt volledig verwijderd bij de volgende belangrijke versiewijziging. Het [pakket Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zelf is niet verouderd. Het is vereist om de bewaking van aanvragen, afhankelijkheden, en meer in te kunnenschakelen.

Het voorgestelde alternatief is het nieuwe zelfstandige pakket [Microsoft.Extensions.Logging.ApplicationInsights,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)dat een verbeterde ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) en extensiemethoden bevat op ILoggerBuilder om dit in te stellen.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.7.1 is afhankelijk van het nieuwe pakket en schakelt ILogger Capture automatisch in.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Waarom worden sommige ILogger-logboeken twee keer weergegeven in Application Insights?

Duplicatie kan optreden als u de oudere (nu verouderde) versie van ApplicationInsightsLoggerProvider hebt ingeschakeld door aan te roepen `AddApplicationInsights` op `ILoggerFactory` . Controleer of uw **configureermethode** het volgende heeft en verwijder deze:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Als u dubbele logboekregistratie ervaart wanneer u fouten opsport in Visual Studio, stelt u als volgt in de code in op false Application Insights `EnableDebugLogger` inschakelen.  Deze duplicatie en oplossing is alleen relevant wanneer u fouten in de toepassing opsport.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ik ben [bijgewerkt naar Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.7.1 en logboeken van ILogger worden automatisch vastgelegd. Hoe kan ik functie volledig uitschakelen?

Zie de [sectie Niveau van logboekregistratie](#control-logging-level) bepalen voor meer informatie over het filteren van logboeken in het algemeen. Als u ApplicationInsightsLoggerProvider wilt uitschakelen, gebruikt u `LogLevel.None` :

**In code:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In configuratie:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Waarom hebben sommige ILogger-logboeken niet dezelfde eigenschappen als andere?

Application Insights legt ILogger-logboeken vast en verzendt deze met behulp van dezelfde TelemetryConfiguration die wordt gebruikt voor elke andere telemetrie. Maar er is een uitzondering. TelemetryConfiguration is standaard niet volledig ingesteld wanneer u zich aanmeldt vanuit **Program.cs** of **Startup.cs.** Logboeken van deze locaties hebben niet de standaardconfiguratie, dus worden niet alle TelemetryInitializers en TelemetryProcessors uitgevoerd.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Ik gebruik het zelfstandige pakket Microsoft.Extensions.Logging.ApplicationInsights en ik wil handmatig enkele aanvullende aangepaste telemetrie vastleggen. Hoe doe ik dat?

Wanneer u het zelfstandige pakket gebruikt, wordt niet geïnjecteerd in de di-container, dus moet u een nieuw exemplaar van maken en dezelfde configuratie gebruiken als de `TelemetryClient` loggerprovider gebruikt, zoals de volgende code laat `TelemetryClient` zien. Dit zorgt ervoor dat dezelfde configuratie wordt gebruikt voor alle aangepaste telemetrie en telemetrie van ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Als u het pakket Microsoft.ApplicationInsights.AspNetCore gebruikt om Application Insights, wijzigt u deze code om rechtstreeks in de `TelemetryClient` constructor te komen. Zie deze veelgestelde vragen voor [een voorbeeld.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Welk Application Insights telemetrie wordt geproduceerd uit ILogger-logboeken? Of waar kan ik ILogger-logboeken in Application Insights?

ApplicationInsightsLoggerProvider legt ILogger-logboeken vast en maakt er TraceTelemetry van. Als een uitzonderingsobject wordt doorgegeven aan de methode op , wordt `Log` `ILogger` *ExceptionTelemetry* gemaakt in plaats van TraceTelemetry. Deze telemetrie-items zijn te vinden op dezelfde plaatsen als andere TraceTelemetry of ExceptionTelemetry voor Application Insights, waaronder portal, analyse of Visual Studio lokale debugger.

Als u liever altijd TraceTelemetry verzendt, gebruikt u dit fragment: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Ik heb de SDK niet geïnstalleerd en ik gebruik de Azure Web Apps-extensie om Application Insights in te ASP.NET Core-toepassingen. Hoe kan ik de nieuwe provider gebruiken? 

De Application Insights-extensie in Azure Web Apps maakt gebruik van de nieuwe provider. U kunt de filterregels in deappsettings.js *voor* uw toepassing wijzigen.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Ik gebruik het zelfstandige pakket Microsoft.Extensions.Logging.ApplicationInsights en het inschakelen van Application Insights provider door Builder aan te **roepen. AddApplicationInsights("ikey")**. Is er een optie om een instrumentatiesleutel op te halen uit de configuratie?


Wijzig Program.cs en appsettings.jsals volgt in:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Relevante sectie van `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Deze code is alleen vereist als u een zelfstandige provider voor logboekregistratie gebruikt. Voor normale Application Insights wordt de instrumentatiesleutel automatisch geladen vanuit het configuratiepad *ApplicationInsights: InstrumentationKey.* Appsettings.jsmoet er als volgende uitzien:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Nieuwe **Azure-regio's vereisen** het gebruik van verbindingsreeksen in plaats van instrumentatiesleutels. [Verbindingsreeks](./sdk-connection-string.md?tabs=net) identificeert de resource waar u uw telemetriegegevens aan wilt koppelen. U kunt hiermee ook de eindpunten wijzigen die door uw resource worden gebruikt als bestemming voor uw telemetrie. U moet de gegevens kopiëren connection string toevoegen aan de code van uw toepassing of aan een omgevingsvariabele.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

* [Aanmelden bij ASP.NET Core](/aspnet/core/fundamentals/logging)
* [.NET-traceerlogboeken in Application Insights](./asp-net-trace-logs.md)


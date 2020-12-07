---
title: Azure-toepassing inzichten voor ASP.NET Core toepassingen | Microsoft Docs
description: Bewaak ASP.NET Core webtoepassingen voor Beschik baarheid, prestaties en gebruik.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: 404e820168c64bd47b6e94598ad5bb13faf32a86
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751339"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights voor ASP.NET Core toepassingen

In dit artikel wordt beschreven hoe u Application Insights inschakelt voor een [ASP.net core](/aspnet/core) toepassing. Wanneer u de instructies in dit artikel hebt voltooid, verzamelt Application Insights aanvragen, afhankelijkheden, uitzonde ringen, prestatie meter items, heartbeats en logboeken van uw ASP.NET Core toepassing.

Het voor beeld dat hier wordt gebruikt, is een [MVC-toepassing](/aspnet/core/tutorials/first-mvc-app) die gericht is `netcoreapp3.0` . U kunt deze instructies Toep assen op alle ASP.NET Core-toepassingen. Als u de [Worker-service](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)gebruikt, gebruikt u de instructies [hier](./worker-service.md).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Met de [Application INSIGHTS SDK voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kunt u uw toepassingen bewaken, ongeacht waar of hoe ze worden uitgevoerd. Als uw toepassing wordt uitgevoerd en netwerk verbinding heeft met Azure, kan telemetrie worden verzameld. Application Insights bewaking wordt ondersteund overal wat .NET core ondersteunt. Ondersteunings dekkingen:
* **Besturings systeem**: Windows, Linux of Mac.
* **Hosting methode**: in verwerking of out-of-process.
* **Implementatie methode**: Framework-afhankelijk of zelfstandig.
* **Webserver**: IIS (Internet Information Server) of Kestrel.
* **Hosting platform**: de web apps functie van Azure app service, Azure VM, docker, Azure Kubernetes service (AKS), enzovoort.
* **.Net core-versie**: alle officieel [ondersteunde](https://dotnet.microsoft.com/download/dotnet-core) .net Core-versies.
* **IDE**: Visual Studio, VS code of opdracht regel.

> [!NOTE]
> ASP.NET Core 3. X vereist [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) of hoger.

## <a name="prerequisites"></a>Vereisten

- Een functionerende ASP.NET Core-toepassing. Als u een ASP.NET Core-toepassing moet maken, volgt u deze [ASP.net core zelf studie](/aspnet/core/getting-started/).
- Een geldige Application Insights instrumentatie sleutel. Deze sleutel is vereist voor het verzenden van telemetrie naar Application Insights. Als u een nieuwe Application Insights resource moet maken om een instrumentatie sleutel op te halen, raadpleegt u [een Application Insights resource maken](./create-new-resource.md).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Telemetrie van Application Insights server inschakelen (Visual Studio)

Gebruik de [hand matige richt lijnen](#enable-application-insights-server-side-telemetry-no-visual-studio)voor Visual Studio voor Mac. Deze procedure wordt alleen ondersteund door de Windows-versie van Visual Studio.

1. Open uw project in Visual Studio.

    > [!TIP]
    > Als u wilt, kunt u broncode beheer instellen voor uw project, zodat u alle wijzigingen kunt bijhouden die Application Insights maakt. Als u broncode beheer wilt inschakelen, selecteert u **bestand**  >  **toevoegen aan bron beheer**.

2. Selecteer **project**  >  **toevoegen Application Insights Telemetry**.

3. Selecteer **aan de slag**. De tekst van deze selectie kan variëren, afhankelijk van uw versie van Visual Studio. In een aantal eerdere versies wordt in plaats daarvan een knop **Start Free** gebruikt.

4. Selecteer uw abonnement. Selecteer vervolgens **resource**  >  **register**.

5. Nadat u Application Insights aan uw project hebt toegevoegd, controleert u of u de laatste stabiele versie van de SDK gebruikt. Ga naar **project**  >  **Manage NuGet packages**  >  **micro soft. ApplicationInsights. AspNetCore**. Als dat het geval is, kiest u **bijwerken**.

     ![Scherm opname van het selecteren van het Application Insights pakket dat moet worden bijgewerkt](./media/asp-net-core/update-nuget-package.png)

6. Als u de optionele tip hebt gevolgd en uw project aan broncode beheer hebt toegevoegd, **View** gaat u naar  >  **team Explorer**-  >  **wijzigingen** weer geven. Selecteer vervolgens elk bestand om een diff-weer gave te zien van de wijzigingen die zijn aangebracht door Application Insights telemetrie.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Telemetrie van Application Insights server inschakelen (geen Visual Studio)

1. Installeer het [Application INSIGHTS SDK NuGet-pakket voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). U wordt aangeraden altijd de nieuwste stabiele versie te gebruiken. Zoek volledige release opmerkingen voor de SDK op de [open-source github opslag plaats](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    In het volgende code voorbeeld ziet u de wijzigingen die moeten worden toegevoegd aan het bestand van het project `.csproj` .

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Voeg toe `services.AddApplicationInsightsTelemetry();` aan de `ConfigureServices()` methode in uw `Startup` klasse, zoals in dit voor beeld:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Stel de instrumentatie sleutel in.

    Hoewel u de instrumentatie sleutel als een argument kunt opgeven `AddApplicationInsightsTelemetry` , is het raadzaam om de instrumentatie sleutel in te stellen in de configuratie. In het volgende code voorbeeld ziet u hoe u een instrumentatie sleutel opgeeft in `appsettings.json` . Zorg ervoor dat `appsettings.json` tijdens het publiceren naar de hoofdmap van de toepassing is gekopieerd.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    U kunt ook de instrumentatie sleutel opgeven in een van de volgende omgevings variabelen:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Bijvoorbeeld:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` wordt doorgaans gebruikt in [Azure web apps](./azure-web-apps.md?tabs=net), maar kan ook worden gebruikt op alle plaatsen waar deze SDK wordt ondersteund. (Als u de bewaking van web-apps met code uitvoert, is deze indeling vereist als u geen verbindings reeksen gebruikt.)

    In plaats van het instellen van instrumentatie sleutels kunt u nu ook [verbindings reeksen](./sdk-connection-string.md?tabs=net)gebruiken.

    > [!NOTE]
    > Een instrumentatie sleutel die is opgegeven in code WINS via de omgevings variabele `APPINSIGHTS_INSTRUMENTATIONKEY` , die WINS over andere opties overneemt.

### <a name="user-secrets-and-other-configuration-providers"></a>Gebruikers geheimen en andere configuratie providers

Als u de instrumentatie sleutel in ASP.NET Core gebruikers geheimen wilt opslaan of van een andere configuratie provider wilt ophalen, kunt u de overbelasting met een `Microsoft.Extensions.Configuration.IConfiguration` para meter gebruiken. Bijvoorbeeld `services.AddApplicationInsightsTelemetry(Configuration);`.
Vanaf micro soft. ApplicationInsights. AspNetCore versie [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), `services.AddApplicationInsightsTelemetry()` wordt de instrumentatie sleutel van `Microsoft.Extensions.Configuration.IConfiguration` van de toepassing automatisch gelezen. Het is niet nodig om het te expliciet opgeven `IConfiguration` .

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Voer de toepassing uit en maak er aanvragen aan. Telemetrie moet nu stromen naar Application Insights. De Application Insights SDK verzamelt automatisch inkomende webaanvragen voor uw toepassing, samen met de volgende telemetrie.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](./live-stream.md) kunnen worden gebruikt om snel te controleren of Application Insights bewaking op de juiste wijze is geconfigureerd. Het kan enkele minuten duren voordat telemetrie in de portal en de analyse wordt weer gegeven. met Live metrische gegevens wordt het CPU-gebruik van het actieve proces in bijna realtime weer gegeven. Het kan ook andere telemetrie weer geven, zoals aanvragen, afhankelijkheden, traceringen, enzovoort.

### <a name="ilogger-logs"></a>ILogger-logboeken

In de standaard configuratie worden `ILogger` Logboeken met Ernst `Warning` en bovenstaande gegevens verzameld. Deze configuratie kan worden [aangepast](#how-do-i-customize-ilogger-logs-collection).

### <a name="dependencies"></a>Afhankelijkheden

De afhankelijkheids verzameling is standaard ingeschakeld. In [Dit](asp-net-dependencies.md#automatically-tracked-dependencies) artikel worden de afhankelijkheden beschreven die automatisch worden verzameld en bevatten de stappen voor het hand matig bijhouden.

### <a name="performance-counters"></a>Prestatiemeteritems

De ondersteuning voor [prestatie meter items](./web-monitor-performance.md) in ASP.net Core is beperkt:

* Met SDK-versies 2.4.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in azure Web Apps (Windows).
* Met SDK-versies 2.7.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in Windows en doelen `NETSTANDARD2.0` of hoger.
* Voor toepassingen die zijn gericht op de .NET Framework, worden alle versies van de SDK-prestatie meter items ondersteund.
* SDK-versies 2.8.0 en hoger ondersteunen CPU/geheugen teller in Linux. Er wordt geen ander item ondersteund in Linux. De aanbevolen manier om systeem tellers op te halen in Linux (en andere niet-Windows-omgevingen) is met behulp van [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Event Counter

`EventCounterCollectionModule` is standaard ingeschakeld. De [Event Counter](eventcounters.md) -zelf studie bevat instructies voor het configureren van de lijst met prestatie meter items die moeten worden verzameld.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Telemetrie aan de client zijde inschakelen voor webtoepassingen

De voor gaande stappen zijn voldoende om u te helpen bij het verzamelen van telemetrie aan de server zijde. Als uw toepassing onderdelen aan client zijde heeft, voert u de volgende stappen uit om te beginnen met het verzamelen van [telemetrie](./usage-overview.md)van het gebruik.

1. Voeg in een `_ViewImports.cshtml` injectie toe:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. `_Layout.cshtml`Voeg `HtmlHelper` aan het einde van de sectie in, `<head>` maar vóór elk ander script. Als u een aangepaste Java script-telemetrie wilt rapporteren vanaf de pagina, moet u deze na dit fragment injecteren:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

U kunt de de-oplossing ook gebruiken `FullScript` `ScriptBody` vanaf SDK v 2.14. Gebruik deze instelling als u de tag moet beheren `<script>` om een beveiligings beleid voor inhoud in te stellen:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

De `.cshtml` bestands namen waarnaar eerder wordt verwezen, zijn afkomstig uit een standaard sjabloon voor MVC-toepassingen. Als u bewaking aan client zijde wilt inschakelen voor uw toepassing, moet het Java script-fragment uiteindelijk worden weer gegeven in de `<head>` sectie van elke pagina van de toepassing die u wilt bewaken. U kunt dit doel voor deze toepassings sjabloon bereiken door het Java script-fragment toe te voegen aan `_Layout.cshtml` . 

Als uw project niet bevat `_Layout.cshtml` , kunt u nog steeds [bewaking aan client zijde](./website-monitoring.md)toevoegen. U kunt dit doen door het Java script-fragment toe te voegen aan een gelijkwaardig bestand dat de `<head>` van alle pagina's in uw app beheert. Of u kunt het fragment toevoegen aan meerdere pagina's, maar deze oplossing is lastig te onderhouden. het wordt over het algemeen niet aanbevolen.

## <a name="configure-the-application-insights-sdk"></a>De Application Insights SDK configureren

U kunt de Application Insights SDK voor ASP.NET Core aanpassen om de standaard configuratie te wijzigen. Gebruikers van de Application Insights ASP.NET SDK kunnen bekend zijn met het wijzigen van de configuratie door te gebruiken `ApplicationInsights.config` of door te wijzigen `TelemetryConfiguration.Active` . Voor ASP.NET Core worden bijna alle configuratie wijzigingen aangebracht in de `ConfigureServices()` methode van uw `Startup.cs` klasse, tenzij u anders omgaat. De volgende secties bieden meer informatie.

> [!NOTE]
> In ASP.NET Core toepassingen wordt het wijzigen van de configuratie door wijzigen `TelemetryConfiguration.Active` niet ondersteund.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions gebruiken

U kunt enkele algemene instellingen wijzigen door door `ApplicationInsightsServiceOptions` te geven aan `AddApplicationInsightsTelemetry` , zoals in dit voor beeld:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Volledige lijst met instellingen in `ApplicationInsightsServiceOptions`

|Instelling | Beschrijving | Standaard
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Inschakelen/uitschakelen `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Inschakelen/uitschakelen `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Inschakelen/uitschakelen `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Inschakelen/uitschakelen `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Inschakelen/uitschakelen `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Inschakelen/uitschakelen `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Functie LiveMetrics in-of uitschakelen | true
|EnableAdaptiveSampling | Adaptieve steek proeven in-/uitschakelen | true
|EnableHeartbeat | De functie heartbeats inschakelen/uitschakelen, die periodiek (standaard 15-minuten) een aangepaste metriek met de naam ' HeartbeatState ' verzendt met informatie over de runtime, zoals .NET-versie, informatie over de Azure-omgeving, indien van toepassing, enzovoort. | true
|AddAutoCollectedMetricExtractor | Schakel AutoCollectedMetrics extractor in/uit. Dit is een TelemetryProcessor die vooraf geaggregeerde metrische gegevens over aanvragen/afhankelijkheden verzendt voordat steek proeven worden uitgevoerd. | true
|RequestCollectionOptions.TrackExceptions | Rapportage van niet-verwerkte uitzonderings tracering door de verzamelings module voor aanvragen in-of uitschakelen. | False in netstandard 2.0 (omdat uitzonde ringen worden bijgehouden met ApplicationInsightsLoggerProvider), anders waar.
|EnableDiagnosticsTelemetryModule | Inschakelen/uitschakelen `DiagnosticsTelemetryModule` . Als u dit uitschakelt, worden de volgende instellingen genegeerd. `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Zie de [Configureer bare instellingen in `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) voor de meest recente lijst.

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>Aanbevolen configuratie voor micro soft. ApplicationInsights. AspNetCore SDK 2.15.0 & hierboven

Vanaf micro soft. ApplicationInsights. AspNetCore SDK-versie [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) de aanbeveling is om elke instelling te configureren die beschikbaar is in `ApplicationInsightsServiceOptions` , inclusief instrumentationkey met behulp van toepassings `IConfiguration` exemplaren. De instellingen moeten worden weer gegeven onder de sectie ' ApplicationInsights ', zoals in het onderstaande voor beeld. De volgende sectie van appsettings.jsop de configuratie sleutel configures en schakelt ook verzameling van aanpas bare steek proeven en prestatie meter items uit.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

Als `services.AddApplicationInsightsTelemetry(aiOptions)` wordt gebruikt, overschrijft het de instellingen van `Microsoft.Extensions.Configuration.IConfiguration` .

### <a name="sampling"></a>Steekproeven

De Application Insights SDK voor ASP.NET Core ondersteunt zowel vaste als adaptieve steek proeven. Adaptieve steek proeven zijn standaard ingeschakeld.

Zie [adaptieve steek proeven voor ASP.net core toepassingen configureren](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)voor meer informatie.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers toevoegen

Gebruik de [initialisatie functies voor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) wanneer u telemetrie wilt verrijken met aanvullende informatie.

Voeg nieuwe `TelemetryInitializer` toe aan de `DependencyInjection` container, zoals in de volgende code wordt weer gegeven. De SDK neemt automatisch een selectie `TelemetryInitializer` op die wordt toegevoegd aan de `DependencyInjection` container.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` kan worden gebruikt voor eenvoudige initialisatie functies. Voor anderen is het volgende vereist: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### <a name="removing-telemetryinitializers"></a>TelemetryInitializers verwijderen

De initialisatie functies voor telemetrie zijn standaard aanwezig. Als u alle of specifieke telemetrie-initialisatie functies wilt verwijderen, gebruikt u de volgende voorbeeld code *nadat* u hebt gebeld `AddApplicationInsightsTelemetry()` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### <a name="adding-telemetry-processors"></a>Telemetrie-processors toevoegen

U kunt aangepaste telemetrie-processors toevoegen aan met `TelemetryConfiguration` behulp van de uitbreidings methode `AddApplicationInsightsTelemetryProcessor` op `IServiceCollection` . U kunt telemetrie-processors gebruiken in [Geavanceerde filter scenario's](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Gebruik het volgende voor beeld.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Standaard TelemetryModules configureren of verwijderen

Application Insights maakt gebruik van telemetrie-modules om automatisch nuttige telemetrie te verzamelen over specifieke workloads zonder hand matige tracking door de gebruiker.

De volgende automatische verzamelings modules zijn standaard ingeschakeld. Deze modules zijn verantwoordelijk voor het automatisch verzamelen van telemetrie. U kunt ze uitschakelen of configureren om het standaard gedrag te wijzigen.

* `RequestTrackingTelemetryModule` -Verzamelt RequestTelemetry van inkomende webaanvragen.
* `DependencyTrackingTelemetryModule` -Verzamelt [DependencyTelemetry](./asp-net-dependencies.md) van uitgaande HTTP-aanroepen en SQL-aanroepen.
* `PerformanceCollectorModule` -Verzamelt Windows Performance Counters.
* `QuickPulseTelemetryModule` -Verzamelt telemetrie voor weer gave in de portal voor Live Metrics.
* `AppServicesHeartbeatTelemetryModule` -Hiermee worden kern maten (die worden verzonden als aangepaste metrische gegevens) verzameld over Azure App Service omgeving waarin de toepassing wordt gehost.
* `AzureInstanceMetadataTelemetryModule` -Hiermee worden kern maten (die worden verzonden als aangepaste metrische gegevens) verzameld over de Azure VM-omgeving waarin de toepassing wordt gehost.
* `EventCounterCollectionModule` -Verzamelt [EventCounters.](eventcounters.md) Deze module is een nieuwe functie die beschikbaar is in SDK-versie 2.8.0 en hoger.

Als u een standaard instelling wilt configureren `TelemetryModule` , gebruikt u de uitbreidings methode `ConfigureTelemetryModule<T>` op `IServiceCollection` , zoals wordt weer gegeven in het volgende voor beeld.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Vanaf 2.12.2-versie [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) bevat een eenvoudige optie om een van de standaard modules uit te scha kelen.

### <a name="configuring-a-telemetry-channel"></a>Een telemetrie-kanaal configureren

Het standaard- [telemetrie-kanaal](./telemetry-channels.md) is `ServerTelemetryChannel` . U kunt deze overschrijven zoals in het volgende voor beeld wordt weer gegeven.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetrie dynamisch uitschakelen

Als u telemetrie voorwaardelijk en dynamisch wilt uitschakelen, kunt u het `TelemetryConfiguration` exemplaar met ASP.net core afhankelijkheids container voor injecties overal in uw code omzetten en `DisableTelemetry` er een vlag op instellen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Met de bovenstaande optie wordt niet voor komen dat modules voor automatische verzameling telemetrie verzamelen. Alleen het verzenden van telemetrie naar Application Insights wordt uitgeschakeld met de bovenstaande methode. Als een bepaalde module voor automatische verzameling niet gewenst is, kunt u [de telemetrie-module het beste verwijderen](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-application-insights-support-aspnet-core-3x"></a>Ondersteunt Application Insights ASP.NET Core 3. X?

Ja. Werk bij naar [Application INSIGHTS SDK voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.8.0 of hoger. Oudere versies van de SDK bieden geen ondersteuning voor ASP.NET Core 3. X.

Als u [hier](#enable-application-insights-server-side-telemetry-visual-studio)op basis van Visual Studio-instructies gebruikt, werkt u ook bij naar de nieuwste versie van visual studio 2019 (16.3.0) voor onboarding. Eerdere versies van Visual Studio bieden geen ondersteuning voor automatische onboarding voor ASP.NET Core 3. X-apps.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hoe kan ik telemetrie traceren die niet automatisch wordt verzameld?

Haal een exemplaar op van met `TelemetryClient` behulp van constructor-injectie en roep de vereiste `TrackXXX()` methode aan. Het is niet raadzaam om nieuwe `TelemetryClient` of exemplaren te maken `TelemetryConfiguration` in een ASP.net core-toepassing. Een singleton-exemplaar van `TelemetryClient` is al geregistreerd in de `DependencyInjection` container, die `TelemetryConfiguration` met de rest van de telemetrie deelt. Het maken van een nieuw `TelemetryClient` exemplaar wordt alleen aanbevolen als er een configuratie nodig is die gescheiden is van de rest van de telemetrie.

In het volgende voor beeld ziet u hoe u extra telemetrie van een controller kunt bijhouden.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Voor meer informatie over aangepaste gegevens rapportage in Application Insights raadpleegt u Application Insights data API-naslag informatie voor [aangepaste metrieken](./api-custom-events-metrics.md). Een soort gelijke benadering kan worden gebruikt voor het verzenden van aangepaste metrische gegevens naar Application Insights met behulp van de [GetMetric-API](./get-metric.md).

### <a name="how-do-i-customize-ilogger-logs-collection"></a>Hoe kan ik de ILogger-logboek verzameling aanpassen?

Standaard worden alleen logboeken met Ernst `Warning` en bovenstaande vermeldingen automatisch vastgelegd. Als u dit gedrag wilt wijzigen, moet u de configuratie van de logboek registratie voor de provider expliciet overschrijven, `ApplicationInsights` zoals hieronder wordt weer gegeven.
Met de volgende configuratie kunnen ApplicationInsights alle logboeken met Ernst `Information` en bovenstaande vermeldingen vastleggen.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

Het is belang rijk te weten dat de volgende reden is dat de ApplicationInsights-provider geen `Information` Logboeken vastlegt. De reden hiervoor is dat de SDK een standaard logboek registratie filter toevoegt, waarmee wordt aangegeven `ApplicationInsights` dat ze alleen en boven moeten worden vastgelegd `Warning` . Daarom is er een expliciete onderdrukking vereist voor ApplicationInsights.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Meer informatie over [ILogger-configuratie](ilogger.md#control-logging-level).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Sommige Visual Studio-sjablonen hebben de uitbreidings methode UseApplicationInsights () gebruikt op IWebHostBuilder om Application Insights in te scha kelen. Is dit gebruik nog geldig?

Hoewel de uitbreidings methode `UseApplicationInsights()` nog steeds wordt ondersteund, is deze verouderd gemarkeerd in Application INSIGHTS SDK-versie 2.8.0. Deze wordt verwijderd uit de volgende primaire versie van de SDK. De aanbevolen manier om Application Insights telemetrie in te scha kelen, is door gebruik te maken van `AddApplicationInsightsTelemetry()` overbelasting voor het beheren van een bepaalde configuratie. Daarnaast is in ASP.NET Core 3. X-apps `services.AddApplicationInsightsTelemetry()` de enige manier om Application Insights in te scha kelen.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Ik implementeer mijn ASP.NET Core-toepassing naar Web Apps. Moet ik de uitbrei ding van de Application Insights nog steeds inschakelen vanuit Web Apps?

Als de SDK tijdens het bouwen is geïnstalleerd, zoals wordt weer gegeven in dit artikel, hoeft u de [uitbrei ding Application Insights](./azure-web-apps.md) niet in te scha kelen vanuit de app Service Portal. Zelfs als de uitbrei ding is geïnstalleerd, wordt deze weer uitgeschakeld wanneer wordt gedetecteerd dat de SDK al aan de toepassing is toegevoegd. Als u Application Insights van de uitbrei ding inschakelt, hoeft u de SDK niet te installeren en bij te werken. Maar als u Application Insights inschakelt door de volg instructies in dit artikel, hebt u meer flexibiliteit omdat:

   * Application Insights telemetrie blijft werken in:
       * Alle besturings systemen, waaronder Windows, Linux en Mac.
       * Alle publicatie modi, inclusief zelfstandig of Framework afhankelijk.
       * Alle doel raamwerken, met inbegrip van de volledige .NET Framework.
       * Alle hosting opties, waaronder Web Apps, Vm's, Linux, containers, Azure Kubernetes-service en niet-Azure-hosting.
       * Alle .NET Core-versies inclusief Preview-versies.
   * U kunt telemetrie lokaal zien wanneer u fouten opspoort vanuit Visual Studio.
   * U kunt aanvullende aangepaste telemetrie bijhouden met behulp van de `TrackXXX()` API.
   * U hebt volledige controle over de configuratie.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan ik Application Insights bewaking inschakelen met behulp van hulpprogram ma's zoals Status Monitor?

Nee. [Status monitor](./monitor-performance-live-website-now.md) en [status monitor v2](./status-monitor-v2-overview.md) ondersteunen momenteel alleen ASP.net 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Als ik mijn toepassing in Linux uitvoer, worden alle functies ondersteund?

Ja. De functie ondersteuning voor de SDK is op alle platforms hetzelfde, met de volgende uitzonde ringen:

* De SDK verzamelt [gebeurtenis tellers](./eventcounters.md) voor Linux omdat [prestatie meter items](./performance-counters.md) alleen worden ondersteund in Windows. De meeste meet waarden zijn hetzelfde.
* Hoewel `ServerTelemetryChannel` de toepassing standaard is ingeschakeld in Linux of macOS, maakt het kanaal niet automatisch een lokale opslagmap om telemetrie tijdelijk te blijven als er netwerk problemen zijn. Vanwege deze beperking gaat telemetrie verloren als er tijdelijke problemen zijn met het netwerk of de server. U kunt dit probleem omzeilen door een lokale map voor het kanaal te configureren:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

Deze beperking is niet van toepassing op [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) en nieuwere versies.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Wordt deze SDK ondersteund voor de nieuwe .NET Core 3. X Worker-service sjabloon toepassingen?

Deze SDK vereist `HttpContext` en werkt daarom niet in niet-HTTP-toepassingen, waaronder de .net Core 3. X Worker-service toepassingen. Raadpleeg [Dit](worker-service.md) document voor het inschakelen van Application Insights in dergelijke toepassingen, met behulp van de zojuist gepubliceerde micro soft. ApplicationInsights. WorkerService SDK.

## <a name="open-source-sdk"></a>Open-Source-SDK

* [Lees en bijdragen aan de code](https://github.com/microsoft/ApplicationInsights-dotnet).

[Raadpleeg de opmerkingen bij de release](./release-notes.md)voor de nieuwste updates en oplossingen voor problemen.

## <a name="next-steps"></a>Volgende stappen

* [Verken de gebruikers stromen](./usage-flows.md) om te begrijpen hoe gebruikers door uw app navigeren.
* [Configureer een momentopname verzameling](./snapshot-debugger.md) om de status van de bron code en variabelen weer te geven op het moment dat een uitzonde ring wordt gegenereerd.
* [Gebruik de API](./api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een gedetailleerde weer gave van de prestaties en het gebruik van uw app.
* Gebruik [beschikbaarheids tests](./monitor-web-app-availability.md) om uw app voortdurend van over de hele wereld te controleren.
* [Afhankelijkheidsinjectie in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)

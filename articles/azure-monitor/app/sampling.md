---
title: Telemetriesampling in Azure-toepassing Insights | Microsoft Docs
description: Het volume aan telemetrie onder controle houden.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834746"
---
# <a name="sampling-in-application-insights"></a>Steekproeven in Application Insights

Sampling is een functie in [Azure-toepassing Insights](./app-insights-overview.md). Het is de aanbevolen manier om telemetrieverkeer, gegevenskosten en opslagkosten te verminderen, terwijl een statistisch juiste analyse van toepassingsgegevens behouden blijven. Met steekproeven kunt u ook Application Insights telemetrie beperken. Met het samplingfilter worden gerelateerde items geselecteerd, zodat u tussen items kunt navigeren wanneer u diagnostische onderzoeken doet.

Wanneer metrische tellingen worden weergegeven in de portal, worden ze opnieuw genormaliseerd om rekening te houden met steekproeven. Als u dit doet, wordt elk effect op de statistieken geminimaliseerd.

## <a name="brief-summary"></a>Korte samenvatting

* Er zijn drie verschillende soorten steekproeven: adaptieve steekproeven, steekproeven met een vaste snelheid en opnamesampling.
* Adaptieve steekproeven zijn standaard ingeschakeld in alle nieuwste versies van de Application Insights ASP.NET en ASP.NET Core Software Development Kits (SDK's). Deze wordt ook gebruikt door [Azure Functions](../../azure-functions/functions-overview.md).
* Sampling met vaste snelheid is beschikbaar in recente versies van de Application Insights-SDK's voor ASP.NET, ASP.NET Core, Java (zowel de agent als de SDK) en Python.
* Opnamesampling werkt op het Application Insights service-eindpunt. Dit geldt alleen wanneer er geen andere steekproeven van kracht zijn. Als de SDK uw telemetrievoorbeelden heeft, is opnamesampling uitgeschakeld.
* Als u voor webtoepassingen aangepaste gebeurtenissen vastlegt en ervoor wilt zorgen dat een set gebeurtenissen samen wordt bewaard of verwijderd, moeten de gebeurtenissen dezelfde waarde `OperationId` hebben.
* Als u Analytics-query's schrijft, moet u [rekening houden met steekproeven.](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations) In plaats van alleen records te tellen, moet u met name `summarize sum(itemCount)` gebruiken.
* Sommige telemetrietypen, waaronder metrische prestatiegegevens en aangepaste metrische gegevens, worden altijd bewaard, ongeacht of steekproeven al dan niet zijn ingeschakeld.

De volgende tabel bevat een overzicht van de samplingtypen die beschikbaar zijn voor elke SDK en elk type toepassing:

| Application Insights SDK | Ondersteunde adaptieve steekproeven | Ondersteunde steekproeven met vaste snelheid | Ondersteunde opnamesampling |
|-|-|-|-|
| ASP.NET | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Alleen als er geen andere steekproeven van kracht zijn |
| ASP.NET Core | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Alleen als er geen andere steekproeven van kracht zijn |
| Azure Functions | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-azure-functions) | No | Alleen als er geen andere steekproeven van kracht zijn |
| Java | Nee | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Alleen als er geen andere steekproeven van kracht zijn |
| Node.JS | Nee | [Ja](./nodejs.md#sampling) | Alleen als er geen andere steekproeven van kracht zijn
| Python | Nee | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Alleen als er geen andere steekproeven van kracht zijn |
| Alle overige | Nee | Nee | [Ja](#ingestion-sampling) |

> [!NOTE]
> De informatie op de meeste van deze pagina is van toepassing op de huidige versies van de Application Insights SDK's. Zie de sectie hieronder voor meer informatie over oudere versies [van de SDK's.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Typen steekproeven

Er zijn drie verschillende samplingmethoden:

* **Adaptieve steekproeven** passen automatisch het volume aan van de telemetrie die vanuit de SDK in uw ASP.NET/ASP.NET Core-app wordt verzonden en van Azure Functions. Dit is de standaardsampling wanneer u de ASP.NET of ASP.NET Core SDK gebruikt. Adaptieve steekproeven zijn momenteel alleen beschikbaar ASP.NET telemetrie aan de serverzijde en voor Azure Functions.

* **Sampling met vaste** snelheid vermindert de hoeveelheid telemetrie die wordt verzonden vanuit zowel uw ASP.NET- of ASP.NET Core- of Java-server als vanuit de browsers van uw gebruikers. U stelt de snelheid in. De client en server synchroniseren hun steekproef, zodat u in Zoeken kunt navigeren tussen gerelateerde paginaweergaven en aanvragen.

* **Opnamesampling** vindt plaats op het Application Insights service-eindpunt. Een deel van de telemetrie die vanuit uw app binnenkomt, wordt verwijderd met een samplingfrequentie die u hebt ingesteld. Het vermindert niet het telemetrieverkeer dat vanuit uw app wordt verzonden, maar helpt u om binnen uw maandelijkse quotum te blijven. Het belangrijkste voordeel van opnamesampling is dat u de samplingfrequentie kunt instellen zonder uw app opnieuw te moeten uitvoeren. Opnamesampling werkt op uniforme wijze voor alle servers en clients, maar is niet van toepassing wanneer er andere typen steekproeven worden uitgevoerd.

> [!IMPORTANT]
> Als er adaptieve samplingmethoden of steekproeven met vaste snelheid zijn ingeschakeld voor een telemetrietype, wordt opnamesampling uitgeschakeld voor die telemetrie. Telemetrietypen die worden uitgesloten van steekproeven op SDK-niveau, zijn echter nog steeds onderworpen aan opnamesampling met de snelheid die in de portal is ingesteld.

## <a name="adaptive-sampling"></a>Adaptieve steekproeven

Adaptieve steekproeven zijn van invloed op de hoeveelheid telemetrie die vanuit uw webserver-app naar het Application Insights service-eindpunt wordt verzonden.

> [!TIP]
> Adaptieve steekproeven zijn standaard ingeschakeld wanneer u de ASP.NET SDK of de ASP.NET Core SDK gebruikt en is standaard ook ingeschakeld voor Azure Functions.

Het volume wordt automatisch aangepast om binnen een opgegeven maximale verkeerssnelheid te blijven en wordt beheerd via de instelling `MaxTelemetryItemsPerSecond` . Als de toepassing een lage hoeveelheid telemetrie produceert, zoals bij het debuggen of vanwege een laag gebruik, worden items niet verwijderd door de samplingprocessor zolang het volume lager is `MaxTelemetryItemsPerSecond` dan . Naarmate het volume van de telemetrie toeneemt, wordt de steekproeffrequentie aangepast om het doelvolume te bereiken. De aanpassing wordt regelmatig opnieuw berekend en is gebaseerd op een bewegend gemiddelde van de uitgaande overdrachtssnelheid.

Om het doelvolume te bereiken, wordt een deel van de gegenereerde telemetrie verwijderd. Maar net als andere typen steekproeven behoudt het algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie inspecteert in Zoeken, kunt u de aanvraag vinden die betrekking heeft op een bepaalde uitzondering.

Metrische gegevens zoals aanvraagsnelheid en uitzonderingsfrequentie worden aangepast om de steekproeffrequentie te compenseren, zodat ze ongeveer de juiste waarden weergeven in Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptieve steekproeven configureren voor ASP.NET toepassingen

> [!NOTE]
> Deze sectie is van toepassing ASP.NET toepassingen, niet op ASP.NET Core-toepassingen. [Meer informatie over het configureren van adaptieve steekproeven voor ASP.NET Core-toepassingen verder in dit document.](#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) kunt u verschillende parameters in het `AdaptiveSamplingTelemetryProcessor` knooppunt aanpassen. De weergegeven cijfers zijn de standaardwaarden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    De doelsnelheid van [logische bewerkingen die](./correlation.md#data-model-for-telemetry-correlation) het adaptieve algoritme op elke **serverhost wil verzamelen.** Als uw web-app op veel hosts wordt uitgevoerd, vermindert u deze waarde zodat deze binnen de doelsnelheid van het verkeer in de Application Insights portal blijft.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Het interval waarmee de huidige telemetriesnelheid opnieuw wordt geëvalueerd. Evaluatie wordt uitgevoerd als een bewegend gemiddelde. Mogelijk wilt u dit interval verkorten als uw telemetrie aansprakelijk is voor plotselinge bursts.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Wanneer de waarde van het steekproefpercentage verandert, hoe snel daarna mogen we het steekproefpercentage opnieuw verlagen om minder gegevens vast te leggen?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Wanneer de waarde van het steekproefpercentage verandert, hoe snel daarna mogen we het steekproefpercentage opnieuw verhogen om meer gegevens vast te leggen?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Als het steekproefpercentage varieert, wat is de minimumwaarde die we mogen instellen?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Als het steekproefpercentage varieert, wat is de maximale waarde die we mogen instellen?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    In de berekening van het bewegende gemiddelde geeft dit het gewicht aan dat moet worden toegewezen aan de meest recente waarde. Gebruik een waarde die gelijk is aan of kleiner is dan 1. Kleinere waarden zorgen ervoor dat het algoritme minder reactief is op plotselinge wijzigingen.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    De hoeveelheid telemetrie die moet worden genomen wanneer de app net is gestart. Verminder deze waarde niet tijdens het debuggen.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Een door punt dubbele punt met scheidingstekens lijst met typen die u niet wilt worden onderworpen aan steekproeven. Herkende typen zijn: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` . `Trace` Alle telemetrie van de opgegeven typen wordt verzonden; De typen die niet zijn opgegeven, worden als steekproef genomen.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Een door punt dubbele punt met scheidingstekens lijst met typen die u wilt onderwerpen aan steekproeven. Herkende typen zijn: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` . `Trace` Er wordt een steekproef genomen van de opgegeven typen; alle telemetrie van de andere typen wordt altijd verzonden.

**Als u adaptieve** steekproeven wilt uitschakelen, verwijdert u `AdaptiveSamplingTelemetryProcessor` de knooppunten uit `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatief: Adaptieve steekproeven in code configureren

In plaats van de steekproefparameter in het bestand in `.config` te stellen, kunt u deze waarden programmatisch instellen.

1. Verwijder alle `AdaptiveSamplingTelemetryProcessor` knooppunt(en) uit het `.config` bestand.
2. Gebruik het volgende codefragment om adaptieve steekproeven te configureren:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Meer informatie over telemetrieprocessors](./api-filtering-sampling.md#filtering).)

U kunt ook de steekproeffrequentie voor elk telemetrietype afzonderlijk aanpassen of zelfs bepaalde typen uitsluiten van het nemen van steekproeven:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptieve sampling configureren voor ASP.NET Core-toepassingen

Er is geen `ApplicationInsights.config` voor ASP.NET Core-toepassingen, dus alle configuratie wordt uitgevoerd via code.
Adaptieve steekproeven zijn standaard ingeschakeld voor alle ASP.NET Core-toepassingen. U kunt het steekproefgedrag uitschakelen of aanpassen.

#### <a name="turning-off-adaptive-sampling"></a>Adaptieve steekproeven uitschakelen

De standaardsamplingfunctie kan worden uitgeschakeld tijdens het toevoegen Application Insights-service, in de methode `ConfigureServices` , met behulp van in het `ApplicationInsightsServiceOptions` `Startup.cs` bestand:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Met de bovenstaande code wordt adaptieve steekproeven uitgeschakeld. Volg de onderstaande stappen om steekproeven toe te voegen met meer aanpassingsopties.

#### <a name="configure-sampling-settings"></a>Steekproefinstellingen configureren

Gebruik uitbreidingsmethoden van `TelemetryProcessorChainBuilder` zoals hieronder wordt weergegeven om het steekproefgedrag aan te passen.

> [!IMPORTANT]
> Als u deze methode gebruikt om steekproeven te configureren, moet u de eigenschap `aiOptions.EnableAdaptiveSampling` instellen op bij het `false` aanroepen van `AddApplicationInsightsTelemetry()` . Nadat u deze wijziging hebt gemaakt, moet u  de instructies in het onderstaande codeblok exact volgen om adaptieve steekproeven opnieuw in te stellen met uw aanpassingen. Als u dit niet doet, kan dit leiden tot overmatige gegevens opname. Test altijd na het wijzigen van de steekproefinstellingen en stel een geschikte [dagelijkse gegevenslimiet in om](pricing.md#set-the-daily-cap) uw kosten te beheersen.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Adaptieve steekproeven configureren voor Azure Functions

Volg de instructies op [deze pagina om](../../azure-functions/configure-monitoring.md#configure-sampling) adaptieve steekproeven te configureren voor apps die worden uitgevoerd in Azure Functions.

## <a name="fixed-rate-sampling"></a>Steekproeven met vaste snelheid

Steekproeven met vaste snelheid verminderen het verkeer dat vanaf uw webserver en webbrowsers wordt verzonden. In tegenstelling tot adaptieve steekproeven, vermindert dit de telemetrie met een vaste snelheid die door u is bepaald. Sampling met vaste snelheid is beschikbaar voor ASP.NET, ASP.NET Core-, Java- en Python-toepassingen.

Net als bij andere samplingtechnieken blijven hier ook gerelateerde items behouden. Ook synchroniseert de client- en serversampling, zodat gerelateerde items behouden blijven. Wanneer u bijvoorbeeld naar een paginaweergave in Zoeken kijkt, kunt u de gerelateerde serveraanvragen vinden. 

In Metrics Explorer worden percentages zoals het aantal aanvragen en uitzonderingen vermenigvuldigd met een factor om de steekproeffrequentie te compenseren, zodat ze ongeveer juist zijn.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Sampling met vaste snelheid configureren voor ASP.NET toepassingen

1. **Adaptieve steekproeven uitschakelen:** in [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) verwijdert u het knooppunt of maakt u er commentaar `AdaptiveSamplingTelemetryProcessor` van.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Schakel de module steekproeven met vaste snelheid in.** Voeg dit fragment toe aan [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      U kunt de volgende waarden ook programmatisch instellen in plaats van de steekproefparameter in `ApplicationInsights.config` te stellen in het bestand:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Meer informatie over telemetrieprocessors](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Sampling met vaste snelheid configureren voor ASP.NET Core-toepassingen

1. **Adaptieve steekproeven uitschakelen:** er kunnen wijzigingen worden aangebracht in de `ConfigureServices` methode met behulp van `ApplicationInsightsServiceOptions` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Schakel de module steekproeven met vaste snelheid in.** Wijzigingen kunnen worden aangebracht in de `Configure` methode , zoals wordt weergegeven in het onderstaande fragment:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Sampling met vaste snelheid configureren voor Java-toepassingen

Standaard is er geen sampling ingeschakeld in de Java-agent en SDK. Op dit moment ondersteunt het alleen steekproeven met een vaste snelheid. Adaptieve steekproeven worden niet ondersteund in Java.

#### <a name="configuring-java-agent"></a>Java-agent configureren

1. Download [applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Als u steekproeven wilt inschakelen, voegt u het volgende toe aan uw `applicationinsights.json` bestand:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Java SDK configureren

1. Download en configureer uw webtoepassing met de nieuwste [Application Insights Java SDK.](./java-get-started.md)

2. **Schakel de module sampling met vaste snelheid in** door het volgende fragment toe te voegen aan het `ApplicationInsights.xml` bestand:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. U kunt specifieke typen telemetrie opnemen of uitsluiten van steekproeven met behulp van de volgende tags in de `Processor` van de tag `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De telemetrietypen die kunnen worden opgenomen of uitgesloten van steekproeven zijn: `Dependency` , , , , en `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> Kies voor het steekproefpercentage een percentage dat dicht bij 100/N ligt, waarbij N een geheel getal is.  Steekproeven bieden momenteel geen ondersteuning voor andere waarden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Sampling met vaste snelheid configureren voor OpenCensus Python-toepassingen

Instrumenteren van uw toepassing met de meest recente [OpenCensus Azure Monitor export.](./opencensus-python.md)

> [!NOTE]
> Steekproeven met vaste snelheid zijn niet beschikbaar voor de export van metrische gegevens. Dit betekent dat aangepaste metrische gegevens de enige typen telemetrie zijn waarbij steekproeven NIET kunnen worden geconfigureerd. De export van metrische gegevens verzendt alle telemetriegegevens die het bij houdt.

#### <a name="fixed-rate-sampling-for-tracing"></a>Steekproeven met vaste snelheid voor tracering ####
U kunt een `sampler` opgeven als onderdeel van uw `Tracer`-configuratie. Als er geen expliciete sampler is opgegeven, `ProbabilitySampler` wordt standaard de gebruikt. De gebruikt standaard een snelheid van 1/10000, wat betekent dat één van elke 10000 aanvragen wordt verzonden `ProbabilitySampler` naar Application Insights. Zie hieronder als u zelf een samplefrequentie wilt opgeven.

Als u de steekproeffrequentie wilt opgeven, moet u ervoor zorgen dat u een sampler opgeeft met een steekproeffrequentie tussen `Tracer` 0,0 en 1,0. Een steekproeffrequentie van 1,0 vertegenwoordigt 100%, wat betekent dat al uw aanvragen als telemetrie worden verzonden naar Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Steekproeven met vaste snelheid voor logboeken ####
U kunt steekproeven met vaste snelheid voor `AzureLogHandler` configureren door het optionele `logging_sampling_rate` argument te wijzigen. Als er geen argument wordt opgegeven, wordt een steekproeffrequentie van 1,0 gebruikt. Een steekproeffrequentie van 1,0 vertegenwoordigt 100%, wat betekent dat al uw aanvragen als telemetrie worden verzonden naar Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Steekproeven met vaste snelheid configureren voor webpagina's met JavaScript

JavaScript-webpagina's kunnen worden geconfigureerd voor het gebruik van Application Insights. Telemetrie wordt verzonden vanuit de clienttoepassing die wordt uitgevoerd in de browser van de gebruiker en de pagina's kunnen vanaf elke server worden gehost.

Wanneer u [uw JavaScript-webpagina's](javascript.md)configureert voor Application Insights , wijzigt u het JavaScript-fragment dat u uit de Application Insights portal.

> [!TIP]
> In ASP.NET apps waarin JavaScript is opgenomen, gaat het fragment meestal naar `_Layout.cshtml` .

Voeg een regel in zoals `samplingPercentage: 10,` vóór de instrumentatiesleutel:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Kies voor het steekproefpercentage een percentage dat dicht bij 100/N ligt, waarbij N een geheel getal is. Steekproeven bieden momenteel geen ondersteuning voor andere waarden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Steekproeven aan serverzijde en clientzijde coördineren

De JavaScript SDK aan de clientzijde neemt deel aan steekproeven met vaste snelheid in combinatie met de SDK aan de serverzijde. De instrumentatiepagina's verzenden alleen telemetrie aan de clientzijde van dezelfde gebruiker waarvoor de SDK aan de serverzijde heeft besloten om op te nemen in de steekproef. Deze logica is ontworpen om de integriteit van gebruikerssessies in client- en servertoepassingen te behouden. Als gevolg hiervan kunt u vanuit een bepaald telemetrie-item in Application Insights alle andere telemetrie-items voor deze gebruiker of sessie vinden en kunt u in Zoeken navigeren tussen gerelateerde paginaweergaven en aanvragen.

Als uw client- en server-side telemetrie geen gecoördineerde voorbeelden tonen:

* Controleer of u steekproeven zowel op de server als op de client hebt ingeschakeld.
* Controleer of u hetzelfde steekproefpercentage in zowel de client als de server hebt ingesteld.
* Zorg ervoor dat de SDK-versie 2.0 of hoger is.

## <a name="ingestion-sampling"></a>Opnamesampling

Opnamesampling werkt op het punt waarop de telemetrie van uw webserver, browsers en apparaten het Application Insights service-eindpunt bereikt. Hoewel het telemetrieverkeer dat vanuit uw app wordt verzonden niet wordt beperkt, wordt de hoeveelheid verwerkte en bewaard (en in rekening gebrachte hoeveelheid) door de Application Insights.

Gebruik dit type steekproeven als uw app vaak het maandelijkse quotum overschrijdt en u niet de mogelijkheid hebt om een van de SDK-typen steekproeven te gebruiken. 

Stel de steekproeffrequentie in op de pagina Gebruik en geschatte kosten:

![Klik op de blade Overzicht van de toepassing op Instellingen, Quotum, Voorbeelden, selecteer een steekproeffrequentie en klik op Bijwerken.](./media/sampling/data-sampling.png)

Net als andere typen steekproeven behoudt het algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie inspecteert in Zoeken, kunt u de aanvraag vinden die betrekking heeft op een bepaalde uitzondering. Metrische gegevens zoals aanvraagsnelheid en uitzonderingsfrequentie worden correct bewaard.

Gegevenspunten die door steekproeven worden verwijderd, zijn niet beschikbaar in Application Insights functie, zoals [Continue export.](./export-telemetry.md)

Opnamesampling werkt niet terwijl adaptieve steekproeven of steekproeven met vaste snelheid worden uitgevoerd. Adaptieve steekproeven worden standaard ingeschakeld wanneer de ASP.NET SDK of de ASP.NET Core SDK wordt gebruikt, of wanneer Application Insights is ingeschakeld in [Azure App Service of ](azure-web-apps.md) met behulp van Status Monitor. Wanneer telemetrie wordt ontvangen door het Application Insights-service-eindpunt, wordt de telemetrie onderzocht en als wordt gerapporteerd dat de steekproeffrequentie minder dan 100% is (wat aangeeft dat er een steekproef wordt genomen van telemetrie), wordt de opnamesamplingsfrequentie genegeerd die u hebt ingesteld.

> [!WARNING]
> De waarde die wordt weergegeven op de portaltegel geeft de waarde aan die u hebt ingesteld voor opnamesampling. Het vertegenwoordigt niet de werkelijke steekproeffrequentie als er een soort SDK-sampling (adaptieve steekproeven of steekproeven met vaste snelheid) wordt uitgevoerd.

## <a name="when-to-use-sampling"></a>Wanneer steekproeven gebruiken

Over het algemeen hebt u voor de meeste kleine en middelgrote toepassingen geen steekproeven nodig. De meest nuttige diagnostische gegevens en de meest nauwkeurige statistieken worden verkregen door gegevens over al uw gebruikersactiviteiten te verzamelen. 

De belangrijkste voordelen van steekproeven zijn:

* Application Insights service daalt ('beperkt') gegevenspunten wanneer uw app een zeer hoge snelheid van telemetrie verzendt in een kort tijdsinterval. Sampling vermindert de kans dat er een beperking optreedt in uw toepassing.
* Om binnen het [quotum van](pricing.md) gegevenspunten voor uw prijscategorie te blijven. 
* Om netwerkverkeer van het verzamelen van telemetrie te verminderen. 

### <a name="which-type-of-sampling-should-i-use"></a>Welk type steekproeven moet ik gebruiken?

**Gebruik opnamesampling als:**

* Vaak gebruikt u uw maandelijkse quotum voor telemetrie.
* U krijgt te veel telemetrie van de webbrowsers van uw gebruikers.
* U gebruikt een versie van de SDK die geen ondersteuning biedt voor steekproeven, bijvoorbeeld ASP.NET oudere versies dan 2.

**Gebruik steekproeven met vaste snelheid als:**

* U wilt gesynchroniseerde steekproeven tussen client en server, zodat u, wanneer u gebeurtenissen [in](./diagnostic-search.md)Zoeken onderzoekt, kunt navigeren tussen gerelateerde gebeurtenissen op de client en server, zoals paginaweergaven en HTTP-aanvragen.
* U hebt vertrouwen in het juiste steekproefpercentage voor uw app. Deze moet hoog genoeg zijn om nauwkeurige metrische gegevens te krijgen, maar onder het tarief dat uw prijsquotum en de beperkingslimieten overschrijdt.

**Adaptieve steekproeven gebruiken:**

Als de voorwaarden voor het gebruik van de andere vormen van steekproeven niet van toepassing zijn, raden we adaptieve steekproeven aan. Deze instelling is standaard ingeschakeld in de ASP.NET/ASP.NET Core SDK. Het vermindert het verkeer pas als een bepaalde minimumsnelheid is bereikt, waardoor er waarschijnlijk helemaal geen steekproef wordt genomen van sites voor weinig gebruik.

## <a name="knowing-whether-sampling-is-in-operation"></a>Weten wanneer voorbeelden actief zijn

Als u de werkelijke samplingfrequentie wilt ontdekken, ongeacht waar deze is toegepast, gebruikt u een [Analytics-query](../logs/log-query-overview.md) zoals deze:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Als u ziet dat voor elk type kleiner is dan 100, wordt er een steekproef genomen van dat `RetainedPercentage` type telemetrie.

> [!IMPORTANT]
> Application Insights geen voorbeelden van sessie-, metrische gegevens (inclusief aangepaste metrische gegevens) of telemetrietypen van prestatiemeters in een van de steekproeftechnieken. Deze typen worden altijd uitgesloten van steekproeven, omdat een vermindering van de precisie zeer ongewenst kan zijn voor deze telemetrietypen.

## <a name="how-sampling-works"></a>Hoe steekproeven werken

Het samplingalgoritme bepaalt welke telemetrie-items moeten worden wegvallen en welke moeten worden behouden. Dit geldt voor steekproeven door de SDK of in de Application Insights service. De steekproefbeslissing is gebaseerd op verschillende regels die zijn gericht op het intact houden van alle gerelateerde gegevenspunten, met behoud van een diagnostische ervaring in Application Insights die bruikbaar en betrouwbaar is, zelfs bij een gereduceerde gegevensset. Als uw app bijvoorbeeld een mislukte aanvraag heeft opgenomen in een voorbeeld, blijven de aanvullende telemetrie-items (zoals uitzonderingen en traceringen die zijn vastgelegd voor deze aanvraag) behouden. Steekproeven houden ze bij elkaar of vallen ze allemaal samen. Als u de aanvraagdetails in Application Insights bekijkt, ziet u daarom altijd de aanvraag samen met de bijbehorende telemetrie-items.

De steekproefbeslissing is gebaseerd op de bewerkings-id van de aanvraag, wat betekent dat alle telemetrie-items die bij een bepaalde bewerking horen, behouden blijven of worden uitgevallen. Voor de telemetrie-items die geen bewerkings-id-set hebben (zoals telemetrie-items die zijn gerapporteerd vanuit asynchrone threads zonder HTTP-context), wordt met steekproeven gewoon een percentage telemetrie-items van elk type vast leggen.

Wanneer u telemetriegegevens aan u presenteert, past de Application Insights-service de metrische gegevens aan met hetzelfde steekproefpercentage dat is gebruikt op het moment van de verzameling, om de ontbrekende gegevenspunten te compenseren. Daarom zien de gebruikers bij het bekijken van de telemetrie in Application Insights statistisch juiste benaderingen die zeer dicht bij de werkelijke getallen liggen.

De nauwkeurigheid van de benadering is grotendeels afhankelijk van het geconfigureerde steekproefpercentage. Ook neemt de nauwkeurigheid toe voor toepassingen die een groot volume aan in het algemeen vergelijkbare aanvragen van veel gebruikers verwerken. Aan de andere kant is er geen sampling nodig voor toepassingen die niet met een aanzienlijke belasting werken, omdat deze toepassingen meestal al hun telemetrie kunnen verzenden terwijl ze binnen het quotum blijven, zonder dat dit leidt tot gegevensverlies door beperking. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

*Wat is het standaardsamplinggedrag in de ASP.NET en ASP.NET Core SDK's?*

* Als u een van de nieuwste versies van de bovenstaande SDK gebruikt, wordt adaptieve steekproeven standaard ingeschakeld met vijf telemetrie-items per seconde.
  Er zijn standaard twee knooppunten toegevoegd en één bevat het type in steekproeven, terwijl de andere het `AdaptiveSamplingTelemetryProcessor` `Event` type `Event` uitsluit van steekproeven. Deze configuratie betekent dat de SDK probeert telemetrie-items te beperken tot vijf telemetrie-items van de typen en vijf telemetrie-items van alle andere typen gecombineerd, waardoor er een afzonderlijke steekproef wordt genomen van andere `Event` `Events` telemetrietypen. Gebeurtenissen worden doorgaans gebruikt voor zakelijke telemetrie en mogen waarschijnlijk niet worden beïnvloed door diagnostische telemetrievolumes.
  
  Hieronder ziet u het standaardbestand `ApplicationInsights.config` dat is gegenereerd. In ASP.NET Core is hetzelfde standaardgedrag ingeschakeld in code. Gebruik de [voorbeelden in de vorige sectie van deze pagina om](#configuring-adaptive-sampling-for-aspnet-core-applications) dit standaardgedrag te wijzigen.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Kan er meer dan één keer een steekproef worden genomen van telemetrie?*

* Nee. SamplingTelemetryProcessors negeren items uit overwegingen bij steekproeven als het item al in de steekproef is opgenomen. Hetzelfde geldt voor opnamesampling, waarbij geen steekproeven worden toegepast op de items die al zijn opgenomen in de SDK zelf.

*Waarom is het nemen van steekproeven geen eenvoudig 'X procent verzamelen van elk telemetrietype'?*

* Hoewel deze samplingmethode een hoge mate van precisie biedt in metrische benaderingen, zou dit de mogelijkheid om diagnostische gegevens per gebruiker, sessie en aanvraag te correleren, breken, wat essentieel is voor diagnostische gegevens. Daarom werkt sampling beter met beleidsregels zoals 'verzamel alle telemetrie-items voor X procent van de app-gebruikers' of 'verzamel alle telemetrie voor X procent van app-aanvragen'. Voor de telemetrie-items die niet zijn gekoppeld aan de aanvragen (zoals asynchrone verwerking op de achtergrond), is de terugval 'het verzamelen van X procent van alle items voor elk telemetrietype'. 

*Kan het steekproefpercentage na een periode veranderen?*

* Ja, adaptieve steekproeven wijzigen geleidelijk het steekproefpercentage, op basis van het momenteel waargenomen volume van de telemetrie.

*Als ik steekproeven met vaste snelheid gebruik, hoe weet ik dan welk steekproefpercentage het beste werkt voor mijn app?*

* Eén manier is om te beginnen met adaptieve steekproeven, uit te zoeken op welke snelheid deze zich vereffent (zie de bovenstaande vraag) en vervolgens over te schakelen naar steekproeven met een vaste snelheid met behulp van die snelheid. 
  
    Anders moet u raden. Analyseer uw huidige telemetriegebruik in Application Insights, observeer eventuele bandbreedtebeperking en schat het volume van de verzamelde telemetrie. Deze drie invoer, samen met de geselecteerde prijscategorie, stellen voor hoeveel u het volume van de verzamelde telemetrie wilt verminderen. Een toename van het aantal gebruikers of een andere verschuiving in het volume van telemetrie kan uw schatting echter ongeldig maken.

*Wat gebeurt er als ik het steekproefpercentage zo configureren dat dit te laag is?*

* Overmatig lage steekproefpercentages veroorzaken te agressieve steekproeven en verminderen de nauwkeurigheid van de benaderingen wanneer Application Insights probeert de visualisatie van de gegevens te compenseren voor het verminderen van het gegevensvolume. Ook kan uw diagnostische ervaring negatief worden beïnvloed, omdat er een steekproef kan worden genomen van een aantal van de niet-vaak mislukte of trage aanvragen.

*Wat gebeurt er als ik het steekproefpercentage zo configureren dat dit te hoog is?*

* Het configureren van een te hoog steekproefpercentage (niet agressief genoeg) resulteert in een onvoldoende vermindering van het volume van de verzamelde telemetrie. Mogelijk treedt er nog steeds telemetriegegevensverlies op met betrekking tot beperking en kunnen de kosten voor het gebruik van Application Insights hoger zijn dan u had gepland vanwege uitvalkosten.

*Op welke platforms kan ik steekproeven gebruiken?*

* Opnamesampling kan automatisch plaatsvinden voor telemetrie boven een bepaald volume, als de SDK geen steekproeven maakt. Deze configuratie werkt bijvoorbeeld als u een oudere versie van de ASP.NET SDK of Java SDK gebruikt.
* Als u de huidige SDK's voor ASP.NET of ASP.NET Core gebruikt (gehost in Azure of op uw eigen server), krijgt u standaard adaptieve steekproeven, maar u kunt overschakelen naar een vaste snelheid zoals hierboven wordt beschreven. Met steekproeven met een vaste snelheid wordt de browser-SDK automatisch gesynchroniseerd met voorbeeldgerelateerde gebeurtenissen. 
* Als u de huidige Java-agent gebruikt, kunt u `applicationinsights.json` configureren (voor Java SDK configureren) om sampling met vaste snelheid `ApplicationInsights.xml` in te schakelen. Steekproeven zijn standaard uitgeschakeld. Met steekproeven met een vaste snelheid synchroniseren de browser-SDK en de server automatisch met voorbeeldgerelateerde gebeurtenissen.

*Er zijn bepaalde zeldzame gebeurtenissen die ik altijd wil zien. Hoe kom ik voorbij de steekproefmodule?*

* De beste manier om dit te doen, is door een aangepaste [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)te schrijven, waarmee de op 100 wordt weergegeven voor het telemetrie-item dat u wilt behouden, zoals hieronder wordt `SamplingPercentage` weergegeven. Aangezien initialisaties gegarandeerd worden uitgevoerd vóór telemetrieprocessors (inclusief steekproeven), zorgt dit ervoor dat alle steekproeftechnieken dit item negeren bij eventuele overwegingen bij steekproeven. Aangepaste telemetrie-initialisaties zijn beschikbaar in de ASP.NET SDK, de ASP.NET Core SDK, de JavaScript SDK en de Java SDK. U kunt bijvoorbeeld een initialisatie voor telemetrie configureren met behulp van de ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Oudere SDK-versies

Adaptieve steekproeven zijn beschikbaar voor de Application Insights SDK voor ASP.NET v2.0.0-beta3 en hoger, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 en hoger, en is standaard ingeschakeld.

Sampling met vaste snelheid is een functie van de SDK in ASP.NET-versies van 2.0.0 en Java SDK versie 2.0.1 en hoger.

Vóór v2.5.0-beta2 van de ASP.NET SDK en v2.2.0-beta3 van de ASP.NET Core SDK werd de steekproefbeslissing gebaseerd op de hash van de gebruikers-id voor toepassingen die 'gebruiker' definiëren (dat wil zeggen, de meeste typische webtoepassingen). Voor de typen toepassingen die geen gebruikers (zoals webservices) definieerde, werd de steekproefbeslissing gebaseerd op de bewerkings-id van de aanvraag. Recente versies van de ASP.NET en ASP.NET Core SDK's gebruiken de bewerkings-id voor de steekproefbeslissing.

## <a name="next-steps"></a>Volgende stappen

* [Filteren](./api-filtering-sampling.md) kan een striktere controle bieden over wat uw SDK verzendt.
* Lees het artikel [Telemetrie optimaliseren met](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)Application Insights .


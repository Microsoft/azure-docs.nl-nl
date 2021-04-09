---
title: Bemonsterde telemetrie in Azure-toepassing Insights | Microsoft Docs
description: Hoe u het telemetrie-volume onder controle kunt houden.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 7b53b0bc8c7cc3df2123d327bf87a85081f88f50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589555"
---
# <a name="sampling-in-application-insights"></a>Steekproeven in Application Insights

Steek proeven zijn een functie in [Azure-toepassing Insights](./app-insights-overview.md). Het is de aanbevolen manier om het telemetrische verkeer, de gegevens kosten en de opslag kosten te verlagen, terwijl u een statistische, juiste analyse van toepassings gegevens behoudt. Met steek proeven kunt u ook Application Insights beperking van uw telemetrie beperken. Het bemonsterings filter selecteert items die gerelateerd zijn, zodat u kunt navigeren tussen items wanneer u diagnostische tests uitvoert.

Wanneer metrische aantallen worden weer gegeven in de portal, worden ze opnieuw genormaliseerd om rekening te houden met de steek proeven. Zo minimaliseert u het effect op de statistieken.

## <a name="brief-summary"></a>Korte samen vatting

* Er zijn drie verschillende soorten steek proeven: adaptieve steek proeven, steek proeven met een vaste frequentie en steek proeven voor opname.
* Adaptieve steek proeven zijn standaard ingeschakeld in alle nieuwste versies van de Application Insights ASP.NET en ASP.NET Core Sdk's (Software Development Kits). Het wordt ook gebruikt door [Azure functions](../../azure-functions/functions-overview.md).
* Beproefde vaste-rente Beschik baarheid is beschikbaar in recente versies van de Application Insights Sdk's voor ASP.NET, ASP.NET Core, java (zowel de agent als de SDK) en python.
* Opname sampling werkt op het eind punt van de Application Insights service. Dit geldt alleen wanneer er geen andere steek proeven van kracht zijn. Als de SDK voor beelden van uw telemetrie is, wordt opname sampling uitgeschakeld.
* Als u voor webtoepassingen aangepaste gebeurtenissen registreert en er zeker van wilt zijn dat een set gebeurtenissen wordt behouden of verwijderd, moeten de gebeurtenissen dezelfde `OperationId` waarde hebben.
* Als u analyse query's schrijft, moet u [rekening houden met steek proeven](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). Met name in plaats van records eenvoudigweg te tellen, moet u gebruiken `summarize sum(itemCount)` .
* Sommige typen telemetrie, met inbegrip van metrische gegevens over prestaties en aangepaste metrische gegevens, worden altijd bewaard, ongeacht of steek proeven zijn ingeschakeld of niet.

De volgende tabel bevat een overzicht van de beschik bare bemonsterings typen voor elke SDK en type toepassing:

| Application Insights SDK | Adaptieve steek proeven worden ondersteund | Steek proeven met een vast aantal worden ondersteund | Bemonstering van opname wordt ondersteund |
|-|-|-|-|
| ASP.NET | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Alleen als er geen andere steek proeven van kracht zijn |
| ASP.NET Core | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Alleen als er geen andere steek proeven van kracht zijn |
| Azure Functions | [Ja (standaard ingeschakeld)](#configuring-adaptive-sampling-for-azure-functions) | No | Alleen als er geen andere steek proeven van kracht zijn |
| Java | Nee | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Alleen als er geen andere steek proeven van kracht zijn |
| Node.JS | Nee | [Ja](./nodejs.md#sampling) | Alleen als er geen andere steek proeven van kracht zijn
| Python | Nee | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Alleen als er geen andere steek proeven van kracht zijn |
| Alle andere | Nee | Nee | [Ja](#ingestion-sampling) |

> [!NOTE]
> De informatie op de meeste van deze pagina is van toepassing op de huidige versies van de Application Insights Sdk's. [Zie de sectie hieronder](#older-sdk-versions)voor meer informatie over oudere versies van de sdk's.

## <a name="types-of-sampling"></a>Typen steek proeven

Er zijn drie verschillende bemonsterings methoden:

* Met **adaptieve steek proeven** wordt het volume van de telemetrie van de SDK in uw ASP.NET/ASP.net core-app automatisch aangepast en van Azure functions. Dit is de standaard steekproef waarde wanneer u de ASP.NET-of ASP.NET Core SDK gebruikt. Adaptieve steek proeven zijn momenteel alleen beschikbaar voor telemetrie op de ASP.NET-Server en voor Azure Functions.

* Bij **steek proeven met een vaste frequentie** wordt het volume van de telemetrie gereduceerd van uw ASP.net-of ASP.net core-of Java-Server en van de browsers van uw gebruikers. U stelt het aantal in. De steek proeven van de client en de server worden gesynchroniseerd, zodat u kunt navigeren tussen gerelateerde pagina weergaven en-aanvragen.

* **Steek proeven voor opname** gebeurt op het Application Insights service-eind punt. Er wordt een deel van de telemetrie verwijderd uit uw app, met een sampling frequentie die u hebt ingesteld. Het vermindert niet het telemetrie verkeer dat vanuit uw app wordt verzonden, maar helpt u binnen uw maandelijkse quotum te houden. Het belangrijkste voor deel van opname sampling is dat u de sampling frequentie kunt instellen zonder uw app opnieuw te implementeren. De steek proef van opname werkt op uniforme wijze voor alle servers en clients, maar is niet van toepassing wanneer er andere soorten steek proeven worden uitgevoerd.

> [!IMPORTANT]
> Als er voor een type telemetrie methoden voor het bemonsteren van adaptieve of vaste frequentie zijn ingeschakeld, worden de steek proeven voor opname uitgeschakeld voor deze telemetrie. Voor typen telemetrie die zijn uitgesloten van steek proeven op het niveau van de SDK, zullen echter nog steeds steek proeven worden genomen op basis van de snelheid die in de portal is ingesteld.

## <a name="adaptive-sampling"></a>Adaptieve steek proef

Adaptieve steek proeven zijn van invloed op het volume van de telemetrie die vanuit uw webserver-app wordt verzonden naar het Application Insights service-eind punt.

> [!TIP]
> Adaptieve steek proeven zijn standaard ingeschakeld wanneer u de ASP.NET SDK of de ASP.NET Core SDK gebruikt en is ook standaard ingeschakeld voor Azure Functions.

Het volume wordt automatisch aangepast om binnen een opgegeven maximum hoeveelheid verkeer te blijven en wordt beheerd via de instelling `MaxTelemetryItemsPerSecond` . Als de toepassing een geringe hoeveelheid telemetrie produceert, zoals bij het opsporen van fouten of wegens een laag gebruik, worden de items niet verwijderd door de sampling processor, zolang het volume hieronder niet wordt gebruikt `MaxTelemetryItemsPerSecond` . Naarmate het volume van de telemetrie toeneemt, wordt de sampling frequentie aangepast om het doel volume te bereiken. De correctie wordt met regel matige tussen pozen opnieuw berekend en is gebaseerd op een zwevend gemiddelde van de uitgaande verzend snelheid.

Om het doel volume te bereiken, wordt een deel van de gegenereerde telemetrie genegeerd. Maar net als andere typen steek proeven behouden de algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie in de zoek actie inspecteert, kunt u de aanvraag voor een bepaalde uitzonde ring vinden.

Metrische aantallen, zoals de frequentie van aanvragen en uitzonderings snelheden, worden aangepast om de sampling frequentie te compenseren, zodat ze ongeveer de juiste waarden in metrische Explorer weer geven.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptieve steek proeven configureren voor ASP.NET-toepassingen

> [!NOTE]
> Deze sectie is van toepassing op ASP.NET-toepassingen, niet op ASP.NET Core toepassingen. [Meer informatie over het configureren van adaptieve steek proeven voor ASP.NET Core toepassingen verderop in dit document.](#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) kunt u verschillende para meters aanpassen in het `AdaptiveSamplingTelemetryProcessor` knoop punt. De weer gegeven afbeeldingen zijn de standaard waarden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Het doel aantal [logische bewerkingen](./correlation.md#data-model-for-telemetry-correlation) dat het adaptieve algoritme **op elke server host** gericht is. Als uw web-app op veel hosts wordt uitgevoerd, verlaagt u deze waarde zodat deze binnen de doel frequentie van het verkeer op de Application Insights Portal blijft.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Het interval waarmee de huidige frequentie van de telemetrie opnieuw wordt geëvalueerd. De evaluatie wordt uitgevoerd als een zwevend gemiddelde. Mogelijk wilt u dit interval verkorten als uw telemetrie op plotselinge bursts is.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Hoe snel na het berekenen van het percentage van de waarde wordt het steekproef percentage opnieuw ingesteld om minder gegevens vast te leggen?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Hoe snel na het berekenen van het percentage van de waarde wordt het sampling percentage opnieuw toegestaan om meer gegevens vast te leggen?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Wat is de minimale waarde die we mogen instellen als het steekproef percentage varieert?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Wat is de maximum waarde die we mogen instellen, omdat het steekproef percentage varieert?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Bij het berekenen van het zwevend gemiddelde geeft dit het gewicht aan dat moet worden toegewezen aan de meest recente waarde. Gebruik een waarde die gelijk is aan of kleiner is dan 1. Kleinere waarden zorgen ervoor dat het algoritme minder opnieuw wordt geactiveerd tot onverwachte wijzigingen.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    De hoeveelheid telemetrie die moet worden gesampled wanneer de app zojuist is gestart. Verlaag deze waarde niet tijdens het opsporen van fouten.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Een door punt komma's gescheiden lijst met typen waarvan u niet wilt dat ze worden gesampled. De herkende typen zijn: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . Alle telemetrie van de opgegeven typen wordt verzonden. voor de typen die niet zijn opgegeven, wordt een steek proef genomen.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Een door punt komma's gescheiden lijst met typen die u wilt onderhevig aan steek proeven. De herkende typen zijn: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . De opgegeven typen worden gesampled. alle telemetrie van de andere typen wordt altijd verzonden.

**Als u** adaptieve steek proeven wilt uitschakelen, verwijdert u de `AdaptiveSamplingTelemetryProcessor` knoop punten uit `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatief: adaptieve steek proeven in code configureren

In plaats van de para meter sampling in het `.config` bestand in te stellen, kunt u deze waarden programmatisch instellen.

1. Verwijder alle `AdaptiveSamplingTelemetryProcessor` knoop punten uit het `.config` bestand.
2. Gebruik het volgende code fragment om adaptieve steek proeven te configureren:

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

    ([Meer informatie over telemetrie-processors](./api-filtering-sampling.md#filtering).)

U kunt ook de sampling frequentie voor elk type telemetrie aanpassen, of u kunt zelfs bepaalde typen uitsluiten van steek proeven:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptieve steek proeven configureren voor ASP.NET Core toepassingen

Er zijn geen `ApplicationInsights.config` voor ASP.net core toepassingen, zodat alle configuratie via code wordt uitgevoerd.
Adaptieve steek proeven zijn standaard ingeschakeld voor alle ASP.NET Core-toepassingen. U kunt het steekproef gedrag uitschakelen of aanpassen.

#### <a name="turning-off-adaptive-sampling"></a>Adaptieve steek proeven uitschakelen

De standaard steekproef functie kan worden uitgeschakeld tijdens het toevoegen van Application Insights-service, in de `ConfigureServices` -methode met in `ApplicationInsightsServiceOptions` het `Startup.cs` bestand:

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

Met de bovenstaande code worden adaptieve steek proeven uitgeschakeld. Volg de onderstaande stappen om steek proeven toe te voegen met meer aanpassings opties.

#### <a name="configure-sampling-settings"></a>Sampling-instellingen configureren

Gebruik extensie methoden van `TelemetryProcessorChainBuilder` zoals hieronder wordt weer gegeven om het steekproef gedrag aan te passen.

> [!IMPORTANT]
> Als u deze methode gebruikt om steek proeven te configureren, moet u ervoor zorgen dat u de `aiOptions.EnableAdaptiveSampling` eigenschap instelt op `false` Wanneer u aanroept `AddApplicationInsightsTelemetry()` .

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Adaptieve steek proeven configureren voor Azure Functions

Volg de instructies op [Deze pagina](../../azure-functions/configure-monitoring.md#configure-sampling) om adaptieve steek proeven te configureren voor apps die worden uitgevoerd in azure functions.

## <a name="fixed-rate-sampling"></a>Steek proeven met een vaste frequentie

Bij steek proeven met een vaste frequentie wordt het verkeer dat wordt verzonden vanaf uw webserver en webbrowsers verminderd. In tegens telling tot adaptieve steek proeven wordt de telemetrie verminderd met een vast bedrag dat door u wordt bepaald. Steek proeven met een vaste frequentie zijn beschikbaar voor ASP.NET-, ASP.NET Core-, Java-en python-toepassingen.

Net als andere technieken voor steek proeven blijven ook verwante items behouden. Ook worden de client-en server sampling gesynchroniseerd, zodat verwante items worden bewaard. Als u bijvoorbeeld een pagina weergave in de zoek opdracht bekijkt, kunt u de bijbehorende server aanvragen vinden. 

In Metrics Explorer worden tarieven zoals het aantal aanvragen en uitzonde ringen vermenigvuldigd met een factor om de sampling frequentie te compenseren, zodat ze ongeveer correct zijn.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Sampling van vaste frequentie voor ASP.NET-toepassingen configureren

1. **Adaptieve steek proeven uitschakelen**: in [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) kunt u het knoop punt verwijderen of opmerkingen toevoegen `AdaptiveSamplingTelemetryProcessor` .

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Schakel de bemonsterings module met vaste frequentie in.** Voeg dit fragment toe aan [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      U kunt deze waarden ook programmatisch instellen in plaats van de sampling para meter in het `ApplicationInsights.config` bestand in te stellen:

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

    ([Meer informatie over telemetrie-processors](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Sampling van vaste frequentie voor ASP.NET Core toepassingen configureren

1. **Adaptieve bemonstering uitschakelen**: wijzigingen kunnen worden aangebracht in de `ConfigureServices` -methode met behulp van `ApplicationInsightsServiceOptions` :

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

2. **Schakel de bemonsterings module met vaste frequentie in.** U kunt wijzigingen aanbrengen in de- `Configure` methode, zoals wordt weer gegeven in het onderstaande fragment:

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

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Bemonsterde vaste frequentie voor Java-toepassingen configureren

Standaard is geen steek proef ingeschakeld in de Java-Agent en-SDK. Momenteel wordt alleen de sampling van vaste kosten ondersteund. Adaptieve steek proeven worden niet ondersteund in Java.

#### <a name="configuring-java-agent"></a>Java-Agent configureren

1. [Applicationinsights-agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) downloaden

1. Als u steek proeven wilt inschakelen, voegt u het volgende toe aan het `applicationinsights.json` bestand:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Java SDK configureren

1. Down load en configureer uw webtoepassing met de nieuwste [Application Insights Java-SDK](./java-get-started.md).

2. **Schakel de bemonsterings module voor vaste frequentie** in door het volgende fragment toe te voegen aan het `ApplicationInsights.xml` bestand:

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

3. U kunt specifieke soorten telemetrie opnemen of uitsluiten van steek proeven met behulp van de volgende tags in de `Processor` tag `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De typen telemetrie die kunnen worden opgenomen in of uitgesloten van steek proeven zijn: `Dependency` ,, `Event` ,, `Exception` `PageView` `Request` en `Trace` .

> [!NOTE]
> Kies voor het steekproef percentage een percentage dat dicht bij 100/N ligt waarbij N een geheel getal is.  De huidige steek proef biedt geen ondersteuning voor andere waarden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Bemonsterde vaste frequentie configureren voor opentellingen python-toepassingen

Instrumenteer uw toepassing met de meest recente [Opentellings Azure monitor exporteurs](./opencensus-python.md).

> [!NOTE]
> Er zijn geen steek proeven met een vaste rente beschikbaar voor de metrische gegevens Exporter. Dit betekent dat aangepaste metrische gegevens de enige typen telemetrie zijn waar steek proeven niet kunnen worden geconfigureerd. De export functie voor metrische gegevens stuurt alle telemetrie-tracerer.

#### <a name="fixed-rate-sampling-for-tracing"></a>Steek proeven met een vast aantal voor tracering ####
U kunt een `sampler` opgeven als onderdeel van uw `Tracer`-configuratie. Als er geen expliciete Voorbeeldset wordt gegeven, `ProbabilitySampler` wordt de standaard waarde gebruikt. De `ProbabilitySampler` waarde is standaard een rente 1/10000, wat betekent dat één van elke 10000 aanvragen wordt verzonden naar Application Insights. Zie hieronder als u zelf een samplefrequentie wilt opgeven.

Als u de sampling frequentie wilt opgeven, moet u ervoor zorgen dat uw `Tracer` pipet een steek proef met een sampling frequentie tussen 0,0 en 1,0. Een sampling frequentie van 1,0 vertegenwoordigt 100%, wat betekent dat al uw aanvragen worden verzonden als telemetrie naar Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Steek proeven met een vaste frequentie voor logboeken ####
U kunt een vast aantal steek proeven configureren voor `AzureLogHandler` door het `logging_sampling_rate` optionele argument te wijzigen. Als er geen argument wordt opgegeven, wordt er een sampling frequentie van 1,0 gebruikt. Een sampling frequentie van 1,0 vertegenwoordigt 100%, wat betekent dat al uw aanvragen worden verzonden als telemetrie naar Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Bemonsterde vaste frequentie voor webpagina's met Java script configureren

Op Java script gebaseerde webpagina's kunnen worden geconfigureerd voor het gebruik van Application Insights. Telemetrie wordt verzonden vanuit de client toepassing die wordt uitgevoerd in de browser van de gebruiker, en de pagina's kunnen worden gehost vanaf elke server.

Wanneer u [uw op Java script gebaseerde webpagina's voor Application Insights configureert](javascript.md), wijzigt u het Java script-fragment dat u van de Application Insights Portal ontvangt.

> [!TIP]
> In ASP.NET-Apps met Java script bevat het fragment meestal `_Layout.cshtml` .

Voeg een regel toe zoals `samplingPercentage: 10,` vóór de instrumentatie sleutel:

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

Kies voor het steekproef percentage een percentage dat dicht bij 100/N ligt waarbij N een geheel getal is. De huidige steek proef biedt geen ondersteuning voor andere waarden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>De steek proef aan de server zijde en client zijde coördineren

De Java script SDK aan de client zijde maakt deel uit van een vast aantal steek proeven in combi natie met de SDK aan de server zijde. Op de pagina's met een instrumentatie worden alleen telemetrie aan de client zijde verzonden van de gebruiker voor wie de SDK voor de server zijde de beslissing heeft genomen voor het nemen van de steek proef. Deze logica is ontworpen voor het onderhouden van de integriteit van gebruikers sessies in toepassingen op de client en aan de server zijde. Als gevolg hiervan kunt u vanuit een bepaald telemetrie-item in Application Insights alle andere telemetrie-items voor deze gebruiker of sessie vinden en kunt u in de zoek opdracht navigeren tussen gerelateerde pagina weergaven en-aanvragen.

Als uw telemetrie-client en server zijde geen gecoördineerde voor beelden weer geven:

* Controleer of u steek proeven hebt ingeschakeld op de server en de client.
* Controleer of u hetzelfde steekproef percentage hebt ingesteld in zowel de client als de server.
* Zorg ervoor dat de SDK-versie 2,0 of hoger is.

## <a name="ingestion-sampling"></a>Steek proeven voor opname

De steek proef van opname werkt op het punt waar de telemetrie van uw webserver, browsers en apparaten het Application Insights service-eind punt bereikt. Hoewel het telemetrie verkeer dat vanuit uw app wordt verzonden, niet wordt verminderd, wordt de hoeveelheid verwerkte en behouden (en in rekening gebracht voor) door Application Insights verminderd.

Gebruik dit type steek proef als uw app vaak het maandelijkse quotum overschrijdt en u niet de mogelijkheid hebt om een van de op SDK gebaseerde typen steek proeven te gebruiken. 

Stel de sampling frequentie in op de pagina gebruik en geschatte kosten:

![Klik op de Blade overzicht van de toepassing op instellingen, quotum, voor beelden, selecteer een sampling frequentie en klik op bijwerken.](./media/sampling/data-sampling.png)

Net als bij andere soorten steek proeven blijven de verwante telemetrie-items behouden. Wanneer u bijvoorbeeld de telemetrie in de zoek actie inspecteert, kunt u de aanvraag voor een bepaalde uitzonde ring vinden. Metrische aantallen, zoals de frequentie van aanvragen en uitzonderings snelheden, worden correct bewaard.

Gegevens punten die worden verwijderd door middel van steek proeven zijn niet beschikbaar in een Application Insights functie, zoals [continue export](./export-telemetry.md).

De steek proeven voor opname worden niet uitgevoerd terwijl er een adaptieve of vaste sampling bewerking is. Adaptieve steek proeven zijn standaard ingeschakeld wanneer de ASP.NET SDK of de ASP.NET Core SDK wordt gebruikt, of wanneer Application Insights is ingeschakeld in [Azure app service ](azure-web-apps.md) of door status monitor te gebruiken. Wanneer telemetrie wordt ontvangen door het Application Insights service-eind punt, wordt de telemetrie onderzocht en wordt aangegeven dat de sampling frequentie kleiner dan 100% is (wat aangeeft dat de telemetrie wordt gesampled), wordt de door u ingestelde opname frequentie genegeerd.

> [!WARNING]
> De waarde die wordt weer gegeven op de portal tegel geeft de waarde aan die u hebt ingesteld voor steek proeven van opname. Het bevat niet de werkelijke sampling frequentie als er een soort bewerking wordt uitgevoerd voor de controle van de SDK-steek proeven (adaptieve of vaste gemiddelde steek proeven).

## <a name="when-to-use-sampling"></a>Wanneer gebruikt u steek proeven?

Over het algemeen hebt u voor de meeste kleine en middel grote toepassingen geen steek proeven nodig. De meest nuttige diagnostische informatie en de meest nauw keurige statistieken worden verkregen door het verzamelen van gegevens voor al uw gebruikers activiteiten. 

De belangrijkste voor delen van steek proeven zijn:

* De gegevens punten van Application Insights-service worden beperkt (' gashendel ') wanneer uw app een zeer hoog aantal telemetrie verstuurt in een korte periode. Door de steek proef wordt de kans kleiner dat er sprake is van een vertraging van uw toepassing.
* Om binnen het [quotum](pricing.md) van gegevens punten voor uw prijs categorie te blijven. 
* Het netwerk verkeer van de verzameling telemetrie verminderen. 

### <a name="which-type-of-sampling-should-i-use"></a>Welk type steek proef moet ik gebruiken?

**Opname sampling gebruiken als:**

* U gebruikt vaak uw maandelijkse quota van telemetrie.
* U krijgt te veel telemetrie van de webbrowsers van uw gebruikers.
* U gebruikt een versie van de SDK die geen steek proeven ondersteunt, bijvoorbeeld ASP.NET-versies die ouder zijn dan 2.

**Gebruik een bemonsterde vaste frequentie als:**

* U wilt gesynchroniseerde steek proeven tussen client en server zodat, wanneer u gebeurtenissen in de [Zoek opdracht](./diagnostic-search.md)onderzoekt, naar gerelateerde gebeurtenissen op de client en de server, zoals pagina weergaven en HTTP-aanvragen.
* U bent zeker van het juiste sampling-percentage voor uw app. Het moet hoog genoeg zijn om nauw keurige metrische gegevens te krijgen, maar minder dan het tarief dat het quotum voor prijzen en de beperkings limieten overschrijdt.

**Adaptieve steek proeven gebruiken:**

Als de voor waarden voor het gebruik van de andere vormen van steek proeven niet van toepassing zijn, raden we aan om adaptieve steek proeven te nemen. Deze instelling is standaard ingeschakeld in de ASP.NET/ASP.NET core-SDK. Het verkeer wordt pas verminderd als een bepaald minimum aantal is bereikt. Daarom worden de sites met weinig gebruik waarschijnlijk niet bemonsterd.

## <a name="knowing-whether-sampling-is-in-operation"></a>Weten wanneer voorbeelden actief zijn

Als u de werkelijke sampling frequentie wilt detecteren, ongeacht waar deze is toegepast, gebruikt u een [analyse query](../logs/log-query-overview.md) zoals deze:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Als u ziet dat `RetainedPercentage` voor elk type kleiner is dan 100, wordt het type van de telemetrie als steek proef.

> [!IMPORTANT]
> Application Insights geen voorbeeld sessie, metrische gegevens (inclusief aangepaste metrische gegevens) of telemetrie-typen voor prestatie meter items in een van de bemonsterings technieken. Deze typen worden altijd uitgesloten van steek proeven, omdat een minder nauw keurigheid kan worden gereduceerd voor deze typen telemetrie.

## <a name="how-sampling-works"></a>De werking van steek proeven

Het steekproef algoritme bepaalt welke telemetriegegevens moeten worden verwijderd en welke items moeten worden bewaard. Dit is waar of steek proeven worden uitgevoerd door de SDK of in de Application Insights-service. De beoordelings beslissing is gebaseerd op verschillende regels die erop gericht zijn alle gerelateerde gegevens punten intact te houden, waarbij een diagnostische ervaring wordt bijgehouden in Application Insights die actiebaar en betrouwbaar is, zelfs met een gereduceerde gegevensset. Als uw app bijvoorbeeld een mislukte aanvraag bevat die in een voor beeld is opgenomen, blijven de extra telemetriegegevens (zoals uitzonde ringen en traceringen die voor deze aanvraag zijn vastgelegd) behouden. Door de steek proeven worden deze allemaal bijeenhouden of neergezet. Als u de details van de aanvraag in Application Insights bekijkt, kunt u de aanvraag altijd samen met de bijbehorende telemetrie-items weer geven.

De beoordelings beslissing is gebaseerd op de bewerkings-ID van de aanvraag, wat betekent dat alle telemetrie-items die deel uitmaken van een bepaalde bewerking, worden behouden of verwijderd. Voor de telemetrie-items waarvoor geen bewerkings-ID is ingesteld (zoals telemetriegegevens die zijn gerapporteerd vanuit asynchrone threads zonder HTTP-context), wordt een percentage van de telemetrie-items van elk type vastgelegd.

Wanneer u telemetrie naar u presenteert, past de Application Insights-service de metrische gegevens aan op basis van het steekproef percentage dat op het moment van de verzameling is gebruikt, om de ontbrekende data punten te compenseren. Daarom zien de gebruikers bij het bekijken van de telemetrie in Application Insights statistische juiste benaderingen die zeer dicht bij de reële getallen vallen.

De nauw keurigheid van de benadering is grotendeels afhankelijk van het geconfigureerde sampling percentage. De nauw keurigheid neemt ook toe voor toepassingen die een grote hoeveelheid algemeen vergelijk bare aanvragen van veel gebruikers verwerken. Daarentegen, voor toepassingen die niet met een aanzienlijke belasting werken, is er geen steek proef nodig omdat deze toepassingen doorgaans al hun telemetrie kunnen verzenden terwijl ze binnen het quotum blijven, zonder dat er gegevens verloren gaan. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

*Wat is het standaard bemonsterings gedrag in de ASP.NET en ASP.NET Core Sdk's?*

* Als u een van de nieuwste versies van de bovenstaande SDK gebruikt, is adaptieve steek proeven standaard ingeschakeld met vijf telemetriegegevens per seconde.
  Er worden `AdaptiveSamplingTelemetryProcessor` standaard twee knoop punten toegevoegd en één bevat het `Event` type steek proef, terwijl de andere het type uitsluiten `Event` van steek proeven. Deze configuratie houdt in dat de SDK probeert om telemetriegegevens te beperken tot vijf telemetriegegevens van `Event` typen en vijf telemetriegegevens van alle andere typen gecombineerd, waardoor het `Events` niet afzonderlijk van andere typen telemetrie kan worden gesampled. Gebeurtenissen worden meestal gebruikt voor zakelijke telemetrie, en worden waarschijnlijk niet beïnvloed door de diagnostische telemetrie-volumes.
  
  Hieronder ziet u het standaard `ApplicationInsights.config` bestand dat is gegenereerd. In ASP.NET Core is hetzelfde standaard gedrag ingeschakeld in code. Gebruik de [voor beelden in de vorige sectie van deze pagina](#configuring-adaptive-sampling-for-aspnet-core-applications) om dit standaard gedrag te wijzigen.

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

*Kan telemetrie meerdere malen worden bemonsterd?*

* Nee. SamplingTelemetryProcessors negeren items van bemonsterings overwegingen als er al een voor beeld van het item is. Hetzelfde geldt ook voor steek proeven voor opname, waardoor geen steek proeven worden toegepast op die items die al worden bemonsterd in de SDK zelf.

*Waarom wordt niet gesampling van een eenvoudig "Collect X procent van elk type telemetrie"?*

* Hoewel deze bemonsterings benadering een hoge mate van nauw keurigheid biedt in metrische benaderingen, zou deze de mogelijkheid om diagnostische gegevens te correleren per gebruiker, sessie en aanvraag, te verstoren, wat essentieel is voor diagnose. Daarom werkt de steek proef beter met beleids regels zoals ' alle telemetrie-items voor X% van app-gebruikers verzamelen ' of ' alle telemetrie voor X procent van de app-aanvragen verzamelen '. Voor de telemetrie-items die niet aan de aanvragen zijn gekoppeld (zoals asynchrone verwerking op de achtergrond), is de terugval het ' collect X procent van alle items voor elk type telemetrie '. 

*Kan het steekproef percentage worden gewijzigd in de loop van de tijd?*

* Ja, met adaptieve steek proef wordt het steekproef percentage geleidelijk gewijzigd, op basis van het momenteel waargenomen volume van de telemetrie.

*Hoe weet ik welk steekproef percentage het beste werkt voor mijn app als ik steek proeven met een vaste frequentie gebruik?*

* Een manier is om te beginnen met adaptieve steek proeven, erachter te komen wat de rente is (Zie de bovenstaande vraag) en vervolgens over te scha kelen naar een vast aantal steek proeven met dat aantal. 
  
    Anders moet u raden. Analyseer het huidige gebruik van de telemetrie in Application Insights, Bekijk eventuele beperkingen die zich voordoen en schat het volume van de verzamelde telemetrie. Deze drie invoer waarden, samen met de geselecteerde prijs categorie, stellen aan hoeveel u het volume van de verzamelde telemetrie wellicht wilt reduceren. Een toename van het aantal gebruikers of een andere verschuiving in het volume van de telemetrie kan de schatting echter ongeldig maken.

*Wat gebeurt er als het steekproef percentage te laag is?*

* Uitzonderlijk weinig steekproef percentages veroorzaken meer dan agressieve steek proeven en verminderen de nauw keurigheid van de benaderingen wanneer Application Insights probeert de visualisatie van de gegevens te compenseren voor de reductie van het gegevens volume. Het is ook mogelijk dat uw diagnostische ervaring een negatieve invloed heeft op het aantal niet-regel matig mislukte of trage aanvragen.

*Wat gebeurt er als ik het steekproef percentage te hoog Configureer?*

* Het configureren van een te hoge steekproef percentage (niet agressief genoeg) resulteert in een ontoereikende vermindering van het volume van de verzamelde telemetrie. U kunt nog steeds telemetrie-gegevens verlies ondervinden met betrekking tot beperking en de kosten voor het gebruik van Application Insights zijn mogelijk hoger dan gepland wegens overschrijding kosten.

*Op welke platformen kan ik steek proeven gebruiken?*

* Steek proeven voor opname kunnen automatisch worden uitgevoerd voor telemetrie boven een bepaald volume, als de SDK geen steek proeven uitvoert. Deze configuratie werkt bijvoorbeeld als u een oudere versie van de ASP.NET SDK of Java SDK gebruikt.
* Als u de huidige ASP.NET-of ASP.NET Core Sdk's gebruikt (die worden gehost in azure of op uw eigen server), krijgt u standaard adaptieve steek proeven, maar u kunt overschakelen naar een vaste frequentie zoals hierboven wordt beschreven. Met een steek proef van een vaste frequentie synchroniseert de SDK van de browser automatisch met voor beeld van gerelateerde gebeurtenissen. 
* Als u de huidige Java-agent gebruikt, kunt u configureren `applicationinsights.json` (voor Java SDK, configureren `ApplicationInsights.xml` ) om steek proeven met een vaste frequentie in te scha kelen. Steek proeven zijn standaard uitgeschakeld. Met een steek proef van een vaste frequentie worden de browser-SDK en de-server automatisch gesynchroniseerd met voor beeld van gerelateerde gebeurtenissen.

*Er zijn bepaalde zeldzame gebeurtenissen die ik altijd wil zien. Hoe kan ik de bemonsterings module naraken?*

* De beste manier om dit te doen is door een aangepaste [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)te schrijven, waarmee de 100 wordt ingesteld `SamplingPercentage` op het telemetrie-item dat u wilt behouden, zoals hieronder wordt weer gegeven. Als initialisatie functies worden gegarandeerd voordat telemetrie-processors (inclusief steek proeven) worden uitgevoerd, zorgt dit ervoor dat alle bemonsterings technieken dit item negeren van eventuele bemonsterings overwegingen. Aangepaste telemetrie-initialisatie functies zijn beschikbaar in de ASP.NET SDK, de ASP.NET Core SDK, de Java script-SDK en de Java-SDK. U kunt bijvoorbeeld een initialisatie functie voor telemetrie configureren met behulp van de ASP.NET-SDK:

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

Adaptieve steek proeven zijn beschikbaar voor de Application Insights SDK voor ASP.NET v 2.0.0-beta3 en hoger, micro soft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 en hoger, en is standaard ingeschakeld.

Steek proeven met een vaste frequentie zijn een functie van de SDK in ASP.NET-versies van 2.0.0 en Java SDK-versie 2.0.1 en hoger.

Vóór v 2.5.0-beta2 van de ASP.NET SDK en v 2.2.0-beta3 van ASP.NET Core SDK werd de steekproef beslissing gebaseerd op de hash van de gebruikers-ID voor toepassingen die ' gebruiker ' definiëren (dat wil zeggen de meest voorkomende webtoepassingen). Voor de typen toepassingen die geen gebruikers hebben gedefinieerd (zoals webservices), is de bemonsterings beslissing gebaseerd op de bewerkings-ID van de aanvraag. Recente versies van de ASP.NET-en ASP.NET Core Sdk's gebruiken de bewerkings-ID voor de beslissing van de steek proef.

## <a name="next-steps"></a>Volgende stappen

* [Filteren](./api-filtering-sampling.md) kan meer controle bieden over wat uw SDK verzendt.
* Lees het artikel over het ontwikkel netwerk om [telemetrie te optimaliseren met Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).


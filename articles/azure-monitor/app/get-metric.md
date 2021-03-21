---
title: Get-Metric in Azure Monitor Application Insights
description: Meer informatie over het effectief gebruiken van de GetMetric ()-aanroep voor het vastleggen van lokale vooraf samengestelde metrische gegevens voor .NET-en .NET core-toepassingen met Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719777"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Aangepaste metrische verzameling in .NET en .NET core

De Azure Monitor Application Insights .NET en .NET core Sdk's hebben twee verschillende methoden voor het verzamelen van aangepaste metrische gegevens, `TrackMetric()` en `GetMetric()` . Het belangrijkste verschil tussen deze twee methoden is lokale aggregatie. `TrackMetric()` Er ontbreken vooraf aggregatie met `GetMetric()` vooraf aggregatie. De aanbevolen aanpak is het gebruik van aggregatie, dus `TrackMetric()` is niet langer de voorkeurs methode voor het verzamelen van aangepaste metrische gegevens. Dit artikel begeleidt u stapsgewijs door het gebruik van de methode GetMetric () en een deel van de motivering achter de werking ervan.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric versus GetMetric

`TrackMetric()` Hiermee verzendt u een ruwe telemetrie die een metriek aangeeft. Het is niet efficiënt om één telemetrie-item voor elke waarde te verzenden. `TrackMetric()` is ook inefficiënt in termen van prestaties, omdat elke keer `TrackMetric(item)` de volledige SDK-pijp lijn van de telemetrie-initialisatie functies en-processors doorloopt. In tegens telling tot `TrackMetric()` , worden `GetMetric()` lokale vooraf aggregatie voor u verwerkt en wordt vervolgens alleen een statistische samenvattings waarde verzonden met een vast interval van één minuut. Als u dus een aantal aangepaste metrische gegevens wilt bewaken op het tweede of zelfs milliseconde niveau, kunt u dit doen, waarbij alleen de kosten voor opslag en netwerk verkeer van elke minuut worden bewaakt. Dit vermindert ook aanzienlijk het risico van beperking, omdat het totale aantal telemetriegegevens dat voor een geaggregeerde metriek moet worden verzonden, sterk wordt verminderd.

In Application Insights worden aangepaste metrische gegevens verzameld via `TrackMetric()` en `GetMetric()` niet onderhevig aan [steek proeven](./sampling.md). Het bemonsteren van belang rijke metrische gegevens kan leiden tot scenario's waarbij waarschuwingen die u mogelijk hebt gemaakt om deze metrische gegevens onbetrouwbaar te raken. Door nooit uw aangepaste metrische gegevens te bemonsteren, kunt u er in het algemeen zeker van zijn dat wanneer uw waarschuwings drempels worden geschonden, een waarschuwing wordt geactiveerd.  Maar omdat aangepaste metrische gegevens niet worden bemonsterd, zijn er mogelijke problemen.

Als u elke seconde trends moet volgen, of als u een even nauw keuriger interval wilt, kan dit resulteren in:

- Hogere kosten voor gegevens opslag. Er zijn kosten verbonden aan de hoeveelheid gegevens die u naar Azure Monitor stuurt. (Hoe meer gegevens u verstuurt, des te groter is de totale kosten van bewaking.)
- Verbeterde netwerk verkeer/prestatie overhead. (In sommige scenario's kan dit zowel een monetaire als een toepassings prestatie kosten hebben.)
- Risico op opname beperking. (De Azure Monitor-service daalt gegevens punten (' Throttles ') wanneer uw app een zeer hoog aantal telemetrie verstuurt in een korte periode.)

Het beperken van een beperking is met name van belang als de steek proef kan leiden tot gemiste waarschuwingen omdat de voor waarde voor het activeren van een waarschuwing lokaal kan optreden en vervolgens op het opname-eind punt wordt neergezet omdat er te veel gegevens worden verzonden. Daarom wordt het gebruik van .NET en .NET Core niet aanbevolen `TrackMetric()` , tenzij u uw eigen lokale aggregatie logica hebt geïmplementeerd. Als u probeert om elke instantie bij te houden die een gebeurtenis binnen een bepaalde periode vindt, is het mogelijk dat deze [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) beter past. In tegens telling tot aangepaste metrische gegevens worden aangepaste gebeurtenissen onderworpen aan steek proeven. U kunt natuurlijk nog steeds gebruiken `TrackMetric()` zonder uw eigen lokale vooraf-aggregatie te schrijven, maar als u dit wel weet, weet u zeker dat u de Valk uilen kent.

In samen vatting `GetMetric()` is de aanbevolen benadering, omdat deze vooraf aggregatie is, worden waarden van alle track ()-aanroepen verzameld en wordt één keer per minuut een samen vatting/aggregatie verzonden. Dit kan de kosten en de prestatie overhead aanzienlijk verminderen door minder gegevens punten te verzenden, terwijl alle relevante gegevens worden verzameld.

> [!NOTE]
> Alleen de .NET-en .NET core Sdk's hebben een GetMetric ()-methode. Als u Java gebruikt, kunt u [metrische gegevens over micrometer](./micrometer-java.md) of gebruiken `TrackMetric()` . Voor Java script en Node.js u zou blijven gebruiken `TrackMetric()` , maar houd wel wel de voor behoud die in de vorige sectie werden beschreven. Voor python kunt u [Opentellingen. stats](./opencensus-python.md#metrics) gebruiken om aangepaste metrische gegevens te verzenden, maar de implementatie van metrische gegevens verschilt.

## <a name="getting-started-with-getmetric"></a>Aan de slag met GetMetric

Voor onze voor beelden gaan we een Basic .NET Core 3,1 Worker-service toepassing gebruiken. Als u de test omgeving die bij deze voor beelden is gebruikt, precies wilt repliceren, volgt u stap 1-6 van het [artikel Monitoring Worker service](./worker-service.md#net-core-30-worker-service-application) om Application Insights toe te voegen aan een Basic Worker service-project sjabloon. Deze concepten zijn van toepassing op elke algemene toepassing waarin de SDK kan worden gebruikt, met inbegrip van web apps en console-apps.

### <a name="sending-metrics"></a>Metrische gegevens verzenden

Vervang de inhoud van het `worker.cs` bestand door het volgende:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Als u de bovenstaande code uitvoert en de telemetrie bekijkt die wordt verzonden via het Visual Studio-uitvoer venster of een hulp programma zoals Telerik Fiddler, ziet u de lus while herhalen zonder telemetrie die wordt verzonden en vervolgens wordt één telemetrie-item verzonden met ongeveer de 60-tweede markering, wat in het geval van de test er als volgt uitziet: :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Dit enkelvoudige telemetrie-item vertegenwoordigt een samen stelling van 41 afzonderlijke metrische meet waarden. Omdat we dezelfde waarde voor en tot nu toe hebben verzonden, hebben we een *standaard afwijking (stDev)* van 0 met een identieke *maximum waarde (max)* en *minimum (min)* -waarden. De eigenschap *Value* vertegenwoordigt een som van alle afzonderlijke waarden die zijn geaggregeerd.

> [!NOTE]
> GetMetric biedt geen ondersteuning voor het bijhouden van de laatste waarde (bijvoorbeeld ' meter ') of het bijhouden van histogrammen/distributies.

Als we onze Application Insights-resource in de logboeken-ervaring onderzoeken, ziet dit afzonderlijke telemetrie-item er als volgt uit:

![Query weergave Log Analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> Hoewel het onbewerkte telemetriegegevens geen expliciete Sum-eigenschap/-veld bevat, hebben we er een voor u gemaakt. In dit geval vertegenwoordigen de `value` and- `valueSum` eigenschap hetzelfde.

U kunt ook toegang krijgen tot uw aangepaste metrische telemetriegegevens in het gedeelte [_metrische gegevens_](../essentials/metrics-charts.md) van de portal. Als een [op een logboek gebaseerd en aangepaste metrische gegevens](pre-aggregated-metrics-log-metrics.md). (In de onderstaande scherm afbeelding ziet u een voor beeld van op een logboek gebaseerd.) ![Weer gave metrische gegevens Verkenner](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Naslag informatie over caching in cache voor gebruik met hoge door Voer

In sommige gevallen worden metrische waarden zeer vaak geobserveerd. Een service met hoge door Voer die 500 aanvragen per seconde verwerkt, zou bijvoorbeeld 20 telemetriegegevens voor de telemetrie voor elke aanvraag willen verzenden. Dit betekent dat er 10.000 waarden per seconde worden bijgehouden. In dergelijke scenario's met hoge door voer moeten gebruikers mogelijk de SDK helpen door bepaalde zoek acties te vermijden.

In dit geval heeft het bovenstaande voor beeld bijvoorbeeld een zoek opdracht uitgevoerd voor een ingang voor de metrische gegevens ' ComputersSold ' en vervolgens een waargenomen waarde 42 gevolgd. In plaats daarvan kan de ingang in de cache worden opgeslagen voor meerdere tracerings aanroepen:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Naast het in de cache plaatsen van de metrische gegevens verlaagt het bovenstaande voor beeld ook de `Task.Delay` tot 50 milliseconden zodat de lus vaker wordt uitgevoerd, wat resulteert in 772 `TrackValue()` aanroepen.

## <a name="multi-dimensional-metrics"></a>Multi-dimensionale metrische gegevens

In de voor beelden in de vorige sectie worden nulwaarden weer gegeven. Metrische gegevens kunnen ook multidimensionaal zijn. Momenteel ondersteunen we Maxi maal 10 dimensies.

 Hier volgt een voor beeld van het maken van een eendimensionale metriek:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Als u deze code gedurende ten minste 60 seconden uitvoert, worden er drie afzonderlijke telemetriegegevens verzonden naar Azure, die elk de aggregatie van een van de drie vorm factoren vertegenwoordigen. Net als voordat u deze kunt onderzoeken in de weer gave Logboeken (analyse):

![Log Analytics-weer gave van multidimensionale metrische gegevens](./media/get-metric/log-analytics-multi-dimensional.png)

En in de ervaring met metrische gegevens Verkenner:

![Aangepaste metrische gegevens](./media/get-metric/custom-metrics.png)

U zult echter merken dat u de metrische gegevens niet kunt splitsen op basis van uw nieuwe aangepaste dimensie of dat u uw aangepaste dimensie wilt weer geven in de weer gave metrische gegevens:

![Ondersteuning voor splitsen](./media/get-metric/splitting-support.png)

Multidimensionale metrische gegevens in de metrische Explorer-ervaring zijn niet ingeschakeld in Application Insights resources.

### <a name="enable-multi-dimensional-metrics"></a>Multidimensionale metrische gegevens inschakelen

Als u multidimensionale metrische gegevens wilt inschakelen voor een Application Insights resource, selecteert u **gebruik en geschatte kosten**  >  **aangepaste metrische gegevens** Hiermee  >  **kunt u waarschuwingen op aangepaste metrische dimensies maken**  >  . Meer informatie hierover vindt u [hier](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

Zodra u hebt vastgesteld dat er nieuwe multi-dimensionale telemetrie is gewijzigd en verzonden, kunt u **splitsen Toep assen**.

> [!NOTE]
> Alleen recent verzonden metrische gegevens nadat de functie is ingeschakeld in de portal, hebben de dimensies opgeslagen.

![Splitsing Toep assen](./media/get-metric/apply-splitting.png)

En Bekijk uw metrische aggregaties voor elke _FormFactor_ -dimensie:

![Formulier factoren](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>MetricIdentifier gebruiken wanneer er meer dan drie dimensies zijn

Momenteel worden 10 dimensies ondersteund, maar voor meer dan drie dimensies is het gebruik van vereist `MetricIdentifier` :

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Aangepaste metrische configuratie

Als u de metrische configuratie wilt wijzigen, moet u dit doen in de locatie waar de metrische gegevens worden geïnitialiseerd.

### <a name="special-dimension-names"></a>Speciale dimensie namen

Metrische gegevens maken geen gebruik van de telemetrie-context van de `TelemetryClient` gebruikt om ze te openen. speciale dimensie namen die beschikbaar zijn als constanten in `MetricDimensionNames` klasse, zijn de beste oplossing voor deze beperking.

Metrische aggregaties die worden verzonden door de onderstaande ' speciale grootte van bewerkings aanvraag '-metrische gegevens zijn **niet** `Context.Operation.Name` ingesteld op ' speciale bewerking '. Terwijl `TrackMetric()` of een andere TrackXXX () correct is `OperationName` ingesteld op ' speciale bewerking '.

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

In dit geval gebruikt u de speciale dimensie namen in de `MetricDimensionNames` klasse om waarden op te geven `TelemetryContext` .

Als bijvoorbeeld de metrische statistische waarde die voortkomt uit de volgende instructie wordt verzonden naar het Application Insights Cloud-eind punt, `Context.Operation.Name` wordt het gegevens veld ervan ingesteld op ' speciale bewerking ':

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

De waarden van deze speciale dimensie worden gekopieerd naar en worden `TelemetryContext` niet gebruikt als een ' normale ' dimensie. Als u ook een bewerkings dimensie voor normale metrische gegevens wilt gebruiken, moet u hiervoor een afzonderlijke dimensie maken:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Afgetopting van dimensie en tijd Series

 Om te voor komen dat het telemetrie-subsysteem per ongeluk uw resources gebruikt, kunt u het maximum aantal gegevens reeksen per metriek beheren. De standaard limieten zijn niet meer dan 1000 totale gegevens reeksen per metriek en niet meer dan 100 verschillende waarden per dimensie.

 In de context van dimensie-en tijd Series die we gebruiken `Metric.TrackValue(..)` om ervoor te zorgen dat de limieten worden waargenomen. Als de limieten al zijn bereikt, `Metric.TrackValue(..)` retourneert ' false ' en wordt de waarde niet bijgehouden. Als dat niet het geval is, wordt ' True ' geretourneerd. Dit is handig als de gegevens voor een metriek afkomstig zijn van gebruikers invoer.

De `MetricConfiguration` constructor maakt deel uit van een aantal opties voor het beheren van verschillende reeksen binnen de respectieve metriek en een object van een klasse `IMetricSeriesConfiguration` die wordt geïmplementeerd en waarmee aggregatie gedrag wordt opgegeven voor elke afzonderlijke reeks van de metriek:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` is het maximum aantal gegevens tijd reeksen dat een metrieke waarde kan bevatten. Wanneer deze limiet is bereikt, worden aanroepen naar `TrackValue()` niet bijgehouden.
* `valuesPerDimensionLimit` beperkt het aantal afzonderlijke waarden per dimensie op een vergelijk bare manier.
* `restrictToUInt32Values` Hiermee wordt bepaald of alleen niet-negatieve gehele waarden moeten worden bijgehouden.

Hier volgt een voor beeld van hoe u een bericht verzendt om te weten te komen of limieten voor Cap worden overschreden:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie ](./worker-service.md)over het bewaken van Worker-service toepassingen.
* Voor meer informatie over [metrische gegevens op basis van een logboek en vooraf geaggregeerde metrieken](./pre-aggregated-metrics-log-metrics.md).
* [Metrische Explorer](../essentials/metrics-getting-started.md)
* Application Insights voor [ASP.net core-toepassingen](asp-net-core.md) inschakelen
* Application Insights inschakelen voor ASP.NET- [toepassingen](asp-net.md)

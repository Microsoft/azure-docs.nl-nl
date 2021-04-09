---
title: Problemen vaststellen met Live Metrics Stream-Azure-toepassing inzichten
description: Bewaak uw web-app in realtime met aangepaste metrische gegevens en stel problemen vast met een live feed van fouten, traceringen en gebeurtenissen.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679463"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: controleren & diagnose met een latentie van 1 seconde

Bewaak uw Live, in-productie webtoepassing met behulp van Live Metrics Stream (ook wel bekend als QuickPulse) van [Application Insights](./app-insights-overview.md). Selecteer en filter metrische gegevens en prestatie meter items die u in realtime wilt bekijken, zonder dat u uw service hoeft te verstoren. Inspecteer stack traceringen van voor beelden van mislukte aanvragen en uitzonde ringen. In combi natie met [Profiler](./profiler.md) -en [momentopname debugger](./snapshot-debugger.md)biedt live Metrics Stream een krachtig en niet-invasief diagnostisch hulp programma voor uw Live website.

Met Live Metrics Stream kunt u het volgende doen:

* Valideer een oplossing tijdens de release, door prestatie-en fout aantallen te bekijken.
* Bekijk het effect van de test belasting en stel problemen vast.
* Richt u op bepaalde test sessies of filter bekende problemen door de metrische gegevens die u wilt bekijken, te selecteren en te filteren.
* Ontvang uitzonderings traceringen wanneer ze plaatsvinden.
* Experimenteer met filters om de meest relevante Kpi's te vinden.
* Bewaak elk Windows-prestatie meter item Live.
* U kunt eenvoudig een server identificeren die problemen ondervindt en alle KPI/live-feed filteren op alleen die server.

![Het tabblad Live Metrics](./media/live-stream/live-metric.png)

Live metrics worden momenteel ondersteund voor ASP.NET-, ASP.NET Core-, Azure Functions-, Java-en Node.js-apps.

## <a name="get-started"></a>Aan de slag

1. Volg de taalspecifieke richt lijnen om live metrische gegevens in te scha kelen.
   * [ASP.net](./asp-net.md) -Live Metrics is standaard ingeschakeld.
   * [ASP.net core](./asp-net-core.md)-Live Metrics is standaard ingeschakeld.
   * [.Net/.net Core-Console/werk nemer](./worker-service.md)-Live Metrics is standaard ingeschakeld.
   * [.NET-toepassingen: Schakel code](#enable-livemetrics-using-code-for-any-net-application)in.
    * [Java](./java-in-process-agent.md) -Live-metrische gegevens is standaard ingeschakeld.
   * [Node.js](./nodejs.md#live-metrics)

2. Open in de [Azure Portal](https://portal.azure.com)de Application Insights resource voor uw app en open Live Stream.

3. [Beveilig het besturings kanaal](#secure-the-control-channel) als u gevoelige gegevens zoals klant namen in uw filters kunt gebruiken.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>LiveMetrics inschakelen met behulp van code voor elke .NET-toepassing

Hoewel LiveMetrics standaard is ingeschakeld tijdens onboarding met behulp van aanbevolen instructies voor .NET-toepassingen, ziet u hoe u de Live metrieken hand matig kunt instellen.

1. Installeer het NuGet-pakket [micro soft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. In de volgende voorbeeld console-app-code ziet u hoe u live metrieken instelt.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Hoewel het bovenstaande voor beeld voor een console-app is, kan dezelfde code worden gebruikt in alle .NET-toepassingen. Als er andere TelemetryModules zijn ingeschakeld waarmee telemetrie automatisch wordt verzameld, is het belang rijk om ervoor te zorgen dat dezelfde configuratie die wordt gebruikt voor het initialiseren van deze modules ook wordt gebruikt voor de module Live Metrics.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hoe verschilt Live Metrics Stream van Metrics Explorer en Analytics?

| |Live Stream | Metrics Explorer en analyse |
|---|---|---|
|**Latentie**|Gegevens die binnen één seconde worden weer gegeven|Geaggregeerd over minuten|
|**Geen Bewaar periode**|De gegevens blijven behouden in de grafiek en vervolgens verwijderd|[Gegevens die gedurende 90 dagen worden bewaard](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Op aanvraag**|Gegevens worden alleen gestreamd wanneer het deel venster Live Metrics is geopend. |Er worden gegevens verzonden wanneer de SDK is geïnstalleerd en ingeschakeld|
|**Gratis**|Er worden geen kosten in rekening gebracht voor Live Stream gegevens|Onderworpen aan [prijzen](./pricing.md)
|**Steekproeven**|Alle geselecteerde metrische gegevens en tellers worden verzonden. Voor beelden van fouten en stack traceringen zijn. |Gebeurtenissen kunnen worden [bemonsterd](./api-filtering-sampling.md)|
|**Besturings kanaal**|Filter besturings signalen worden naar de SDK verzonden. U wordt aangeraden dit kanaal te beveiligen.|Communicatie is een manier, naar de portal|

## <a name="select-and-filter-your-metrics"></a>Uw metrische gegevens selecteren en filteren

(Beschikbaar met ASP.NET, ASP.NET Core en Azure Functions (v2).)

U kunt een aangepaste KPI Live bewaken door wille keurige filters toe te passen op Application Insights telemetrie vanuit de portal. Klik op het filter besturings element dat laat zien wanneer u met de muis aanwijzer op een van de grafieken klikt. In het volgende diagram wordt een KPI van een aangepast aanvraag aantal getekend met filters voor de kenmerken URL en duration. Valideer uw filters met behulp van de sectie voor beeld van de stream, waarin een live-feed van de telemetrie wordt weer gegeven die overeenkomt met de criteria die u op een bepaald moment hebt opgegeven.

![Frequentie van filter aanvragen](./media/live-stream/filter-request.png)

U kunt een andere waarde dan aantal bewaken. De opties zijn afhankelijk van het type stream. Dit kan elke Application Insights telemetrie zijn: aanvragen, afhankelijkheden, uitzonde ringen, traceringen, gebeurtenissen of metrische gegevens. Dit kan uw eigen [aangepaste meting](./api-custom-events-metrics.md#properties)zijn:

![Opbouw functie voor query's op aanvraag frequentie met aangepaste metrische gegevens](./media/live-stream/query-builder-request.png)

Naast Application Insights telemetrie, kunt u ook elk prestatie meter item van Windows controleren door dat te selecteren in de stroom opties en de naam van het prestatie meter item op te geven.

Live-metrische gegevens worden op twee punten geaggregeerd: lokaal op elke server en vervolgens op alle servers. U kunt de standaard instelling wijzigen door andere opties te selecteren in de vervolg keuzelijsten.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Voor beeld-telemetrie: aangepaste Live diagnostische gebeurtenissen
De live feed van gebeurtenissen toont standaard voor beelden van mislukte aanvragen en afhankelijkheids aanroepen, uitzonde ringen, gebeurtenissen en traceringen. Klik op het filter pictogram om de toegepaste criteria op elk gewenst moment weer te geven.

![Knop filteren](./media/live-stream/filter.png)

Net als bij metrische gegevens kunt u wille keurige criteria opgeven voor elk van de Application Insights typen telemetrie. In dit voor beeld selecteren we specifieke aanvraag fouten en gebeurtenissen.

![Opbouwfunctie voor query's](./media/live-stream/query-builder.png)

> [!NOTE]
> Gebruik op dit moment voor op uitzonderings bericht gebaseerde criteria het buitenste uitzonderings bericht. In het vorige voor beeld kunt u de onschadelijke uitzonde ring filteren met een intern uitzonderings bericht (de ' <--' scheidings teken) ' de client heeft de verbinding verbroken '. Gebruik een bericht dat niet de criteria ' fout bij het lezen van aanvragen van inhoud ' bevat.

Bekijk de details van een item in de live feed door erop te klikken. U kunt de feed onderbreken door te klikken op **onderbreken** of omlaag schuiven of op een item te klikken. Live feed wordt hervat wanneer u weer naar boven schuift of door te klikken op het item van items die worden verzameld terwijl het is onderbroken.

![Scherm afbeelding toont het venster voor beeld-telemetrie waarin een uitzonde ring is geselecteerd en de uitzonderings details die onder aan het venster worden weer gegeven.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filteren op Server exemplaar

Als u een bepaalde serverrol wilt bewaken, kunt u filteren op server. Als u wilt filteren, selecteert u de server naam onder *servers*.

![Voorbeeld Live-fouten](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Het besturings kanaal beveiligen

> [!NOTE]
> Op dit moment kunt u alleen een geverifieerd kanaal instellen met behulp van op code gebaseerde bewaking en kunt u geen servers verifiëren met een koppeling zonder code.

De criteria voor aangepaste filters die u opgeeft in de Live Metrics-Portal worden teruggestuurd naar het onderdeel Live Metrics in de SDK van Application Insights. De filters kunnen mogelijk gevoelige informatie bevatten, zoals customerIDs. U kunt het kanaal veilig maken met een geheime API-sleutel naast de instrumentatie sleutel.

### <a name="create-an-api-key"></a>Een API-sleutel maken

![API-sleutel > een API-sleutel maken API-sleutel maken ](./media/live-stream/api-key.png)
 ![ . Selecteer het besturings kanaal van de SDK verifiëren en vervolgens sleutel genereren](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>API-sleutel aan configuratie toevoegen

### <a name="aspnet"></a>ASP.NET

Voeg in het applicationinsights.config bestand de AuthenticationApiKey toe aan de QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Volg de onderstaande instructies voor [ASP.net core](./asp-net-core.md) toepassingen.

Wijzig `ConfigureServices` het bestand startup. CS als volgt:

Voeg de volgende naam ruimte toe.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Wijzig de `ConfigureServices` methode vervolgens als hieronder.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Meer informatie over het configureren van ASP.NET Core toepassingen vindt u in onze richt lijnen over het [configureren van telemetrie-modules in ASP.net core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

Volg de onderstaande instructies voor [WorkerService](./worker-service.md) -toepassingen.

Voeg de volgende naam ruimte toe.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Voeg vervolgens de volgende regel toe vóór de aanroep `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Meer informatie over het configureren van WorkerService-toepassingen vindt u in onze richt lijnen over het [configureren van telemetrie-modules in WorkerServices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Azure Function-apps

Voor Azure function-apps (v2) kunt u het kanaal beveiligen met een API-sleutel door een omgevings variabele te gebruiken.

Maak een API-sleutel in uw Application Insights-resource en ga naar **instellingen > configuratie** voor uw functie-app. Selecteer **nieuwe toepassings instelling** en voer een naam `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` en een waarde in die overeenkomt met uw API-sleutel.

Als u echter alle verbonden servers herkent en vertrouwt, kunt u de aangepaste filters proberen zonder het geverifieerde kanaal. Deze optie is zes maanden beschikbaar. Deze onderdrukking is vereist na elke nieuwe sessie of wanneer een nieuwe server online is.

![Verificatie opties voor Live Metrics](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>We raden u ten zeerste aan het geverifieerde kanaal in te stellen voordat u mogelijk gevoelige informatie, zoals KlantId, in de filter criteria invoert.
>

## <a name="supported-features-table"></a>Tabel met ondersteunde functies

| Taal                         | Basis gegevens       | Metrische gegevens voor prestaties | Aangepast filteren    | Voor beeld-telemetrie    | CPU-splitsing per proces |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Ondersteund (V 2.7.2 +) | Ondersteund (V 2.7.2 +) | Ondersteund (V 2.7.2 +) | Ondersteund (V 2.7.2 +) | Ondersteund (V 2.7.2 +)  |
| .NET core (target =. NET Framework)| Ondersteund (V 2.4.1 +) | Ondersteund (V 2.4.1 +) | Ondersteund (V 2.4.1 +) | Ondersteund (V 2.4.1 +) | Ondersteund (V 2.4.1 +)  |
| .NET core (target =. NET core)     | Ondersteund (V 2.4.1 +) | Ondersteund*          | Ondersteund (V 2.4.1 +) | Ondersteund (V 2.4.1 +) | **Niet ondersteund**    |
| Azure Functions v2               | Ondersteund           | Ondersteund           | Ondersteund           | Ondersteund           | **Niet ondersteund**    |
| Java                             | Ondersteund (V 2.0.0 +) | Ondersteund (V 2.0.0 +) | **Niet ondersteund**   | **Niet ondersteund**   | **Niet ondersteund**    |
| Node.js                          | Ondersteund (V 1.3.0 +) | Ondersteund (V 1.3.0 +) | **Niet ondersteund**   | Ondersteund (V 1.3.0 +) | **Niet ondersteund**    |

Basis metrieken zijn onder andere aanvraag, afhankelijkheid en uitzonderings frequentie. Prestatie gegevens (prestatie meter items) zijn onder andere geheugen en CPU. Voor beeld-telemetrie toont een stroom van gedetailleerde informatie over mislukte aanvragen en afhankelijkheden, uitzonde ringen, gebeurtenissen en traceringen.

 \* PerfCounters-ondersteuning varieert enigszins per versie van .NET core die niet gericht is op de .NET Framework:

- PerfCounters-metrische gegevens worden ondersteund wanneer in Azure App Service voor Windows wordt uitgevoerd. (AspNetCore SDK-versie 2.4.1 of hoger)
- PerfCounters worden ondersteund wanneer de app wordt uitgevoerd op een Windows-machine (VM of Cloud service of on-premises, enz.) (AspNetCore SDK-versie 2.7.1 of hoger), maar voor apps die zijn gericht op .NET Core 2,0 of hoger.
- PerfCounters worden ondersteund wanneer de app wordt uitgevoerd op een wille keurige locatie (Linux, Windows, app service voor Linux, containers, enzovoort) in de meest recente versies (bijvoorbeeld AspNetCore SDK-versie 2.8.0 of hoger), maar alleen voor apps met .NET Core 2,0 of hoger.

## <a name="troubleshooting"></a>Problemen oplossen

Live Metrics Stream gebruikt andere IP-adressen dan andere Application Insights telemetrie. Zorg ervoor dat [deze IP-adressen](./ip-addresses.md) zijn geopend in uw firewall. Controleer ook of de [uitgaande poorten voor Live Metrics stream](./ip-addresses.md#outgoing-ports) zijn geopend in de firewall van uw servers.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik controleren met Application Insights](./usage-overview.md)
* [Diagnostische Zoek opdrachten gebruiken](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Fout opsporing voor moment opnamen](./snapshot-debugger.md)
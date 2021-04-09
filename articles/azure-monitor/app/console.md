---
title: Azure-toepassing inzichten voor console toepassingen | Microsoft Docs
description: Bewaak webtoepassingen voor Beschik baarheid, prestaties en gebruik.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920600"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights voor .NET-console toepassingen

Met [Application Insights](./app-insights-overview.md) kunt u uw webtoepassing controleren op Beschik baarheid, prestaties en gebruik.

U hebt een abonnement met [Microsoft Azure](https://azure.com)nodig. Meld u aan met een Microsoft-account, dat u mogelijk hebt voor Windows, Xbox Live of andere micro soft-Cloud Services. Uw team heeft mogelijk een organisatie abonnement op Azure: vraag de eigenaar om u toe te voegen met behulp van uw Microsoft-account.

> [!NOTE]
> Het is *raadzaam* om het pakket [micro soft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) en de bijbehorende instructies van [hier](./worker-service.md) te gebruiken voor alle console toepassingen. Deze pakket doelen [`NetStandard2.0`](/dotnet/standard/net-standard) en kunnen daarom worden gebruikt in .net Core 2,1 of hoger, en .NET Framework 4.7.2 of hoger.

## <a name="getting-started"></a>Aan de slag

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=net) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

* Maak in de [Azure Portal](https://portal.azure.com)[een Application Insights-resource](./create-new-resource.md). Voor toepassings type kiest u **Algemeen**.
* Kopieer de instrumentatiesleutel. Zoek de sleutel in de vervolg keuzelijst **Essentials** van de nieuwe resource die u hebt gemaakt.
* Installeer het meest recente pakket [micro soft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Stel de instrumentatie sleutel in uw code in voordat u een telemetrie bijhoudt (of stel APPINSIGHTS_INSTRUMENTATIONKEY omgevings variabele in). Daarna kunt u de telemetrie hand matig bijhouden en weer geven op de Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetrie wordt niet direct verzonden. Telemetriegegevens worden batches en verzonden door de ApplicationInsights-SDK. In console-apps die direct na het aanroepen van `Track()` methoden verlaten, mag telemetrie niet worden verzonden, tenzij `Flush()` en `Sleep` / `Delay` wordt uitgevoerd voordat de app wordt [](#full-example) afgesloten, zoals verderop in dit artikel wordt weer gegeven. `Sleep` is niet vereist als u gebruikt `InMemoryChannel` . Er is een actief probleem met betrekking tot de nood zaak `Sleep` die hier wordt bijgehouden: [ApplicationInsights-DotNet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Installeer de nieuwste versie van het pakket [micro soft. ApplicationInsights. DependencyCollector.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) het traceert automatisch http, SQL of andere externe afhankelijkheids aanroepen.

U kunt Application Insights initialiseren en configureren vanuit de code of met behulp van het `ApplicationInsights.config` bestand. Zorg ervoor dat de initialisatie zo snel mogelijk gebeurt. 

> [!NOTE]
> Instructies die verwijzen naar **ApplicationInsights.config** zijn alleen van toepassing op apps die zijn gericht op de .NET Framework en zijn niet van toepassing op .net core-toepassingen.

### <a name="using-config-file"></a>Configuratie bestand gebruiken

Application Insights SDK zoekt standaard naar `ApplicationInsights.config` een bestand in de werkmap wanneer het `TelemetryConfiguration` wordt gemaakt

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

U kunt ook het pad naar het configuratie bestand opgeven.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Zie voor meer informatie [configuratie bestand verwijzing](configuration-with-applicationinsights-config.md).

U kunt een volledig voor beeld van het configuratie bestand krijgen door de nieuwste versie van het pakket [micro soft. ApplicationInsights. Windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) te installeren. Dit is de **minimale** configuratie voor afhankelijkheids verzameling die gelijk is aan het code voorbeeld.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Telemetrie verzameling configureren vanuit code
> [!NOTE]
> Het lezen van het configuratie bestand wordt niet ondersteund in .NET core. U kunt overwegen [Application INSIGHTS SDK te gebruiken voor ASP.net core](./asp-net-core.md)

* Tijdens het opstarten van de toepassing wordt instance gemaakt en geconfigureerd. `DependencyTrackingTelemetryModule` Dit moet een singleton zijn en moet worden bewaard voor de levens duur van de toepassing.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Veelgebruikte telemetrie-initialisatie functies toevoegen

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Als u een configuratie met een gewone `TelemetryConfiguration()` constructor hebt gemaakt, moet u ook ondersteuning voor correlatie inschakelen. **Het is niet nodig** als u de configuratie van bestand, gebruikt `TelemetryConfiguration.CreateDefault()` of maakt `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* U kunt ook de Collector-module voor prestatie meter items installeren en initialiseren, zoals [hier](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/) wordt beschreven


#### <a name="full-example"></a>Volledig voor beeld

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Volgende stappen
* [Bewaak afhankelijkheden](./asp-net-dependencies.md) om te zien of rest, SQL of andere externe bronnen worden vertraagd.
* [Gebruik de API](./api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een meer gedetailleerde weer gave van de prestaties en het gebruik van uw app.


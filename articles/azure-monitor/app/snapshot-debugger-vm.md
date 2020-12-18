---
title: Snapshot Debugger voor .NET-Apps inschakelen in azure Service Fabric, Cloud service en Virtual Machines | Microsoft Docs
description: Snapshot Debugger voor .NET-apps in azure Service Fabric, Cloud service en Virtual Machines inschakelen
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 4bccc2922cf20262149ef54fbe2a1a821d9551ab
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673498"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Snapshot Debugger voor .NET-apps in azure Service Fabric, Cloud service en Virtual Machines inschakelen

Als uw kern toepassing voor ASP.NET of ASP.NET wordt uitgevoerd in Azure App Service, wordt het ten zeerste aanbevolen Snapshot Debugger in te [scha kelen via de pagina Application Insights Portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Als uw toepassing echter een aangepaste Snapshot Debugger configuratie of een preview-versie van .NET core vereist, moet deze instructie ***naast*** de instructies voor [het inschakelen van de Application Insights portal-pagina](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)worden gevolgd.

Als uw toepassing wordt uitgevoerd in azure Service Fabric, Cloud service, Virtual Machines of on-premises machines, moet u de volgende instructies gebruiken. 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Momentopname verzameling configureren voor ASP.NET-toepassingen

1. [Schakel Application Insights in uw web-app in](./asp-net.md)als u deze nog niet hebt gedaan.

2. Neem het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket op in uw app.

3. Als dat nodig is, is de Snapshot Debugger configuratie aan [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)aangepast. De standaard configuratie van Snapshot Debugger is grotendeels leeg en alle instellingen zijn optioneel. Hier volgt een voor beeld van een configuratie equivalent met de standaard configuratie:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Moment opnamen worden alleen verzameld voor uitzonde ringen die worden gerapporteerd aan Application Insights. In sommige gevallen (bijvoorbeeld oudere versies van het .NET-platform) moet u mogelijk een [uitzonderings verzameling configureren](./asp-net-exceptions.md#exceptions) om uitzonde ringen te bekijken met moment opnamen in de portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Momentopname verzameling configureren voor toepassingen die gebruikmaken van ASP.NET Core 2,0 of hoger

1. [Schakel Application Insights in uw ASP.net core web-app in](./asp-net-core.md)als u deze nog niet hebt gedaan.

    > [!NOTE]
    > Zorg ervoor dat uw toepassing verwijst naar versie 2.1.1 of nieuwer van het pakket micro soft. ApplicationInsights. AspNetCore.

2. Neem het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket op in uw app.

3. Wijzig de klasse van uw toepassing `Startup` om de telemetrie-processor van Snapshot Collector toe te voegen en te configureren.
    1. Als [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket versie 1.3.5 of hoger wordt gebruikt, voegt u de volgende using-instructies toe aan `Startup.cs` .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Voeg het volgende toe aan het einde van de ConfigureServices-methode in de- `Startup` klasse in `Startup.cs` .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Als [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket versie 1.3.4 of hieronder wordt gebruikt, voegt u de volgende using-instructies toe aan `Startup.cs` .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       Voeg de volgende `SnapshotCollectorTelemetryProcessorFactory` klasse toe aan de `Startup` klasse.

       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;

               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;

               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Voeg de `SnapshotCollectorConfiguration` Services en toe `SnapshotCollectorTelemetryProcessorFactory` aan de opstart pijplijn:

        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Als dat nodig is, kunt u de Snapshot Debugger configuratie aanpassen door een sectie SnapshotCollectorConfiguration toe te voegen aan appsettings.jsop. Alle instellingen in de Snapshot Debugger configuratie zijn optioneel. Hier volgt een voor beeld van een configuratie equivalent met de standaard configuratie:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Momentopname verzameling configureren voor andere .NET-toepassingen

1. Als uw toepassing niet al is voorzien van Application Insights, gaat u aan de slag door [Application Insights in te scha kelen en de instrumentatie sleutel in te stellen](./windows-desktop.md).

2. Voeg het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket toe aan uw app.

3. Moment opnamen worden alleen verzameld voor uitzonde ringen die worden gerapporteerd aan Application Insights. Mogelijk moet u de code wijzigen om deze te rapporteren. De uitzonderings code is afhankelijk van de structuur van uw toepassing, maar hieronder vindt u een voor beeld:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren voor uw toepassing die een uitzonde ring kan veroorzaken. Wacht vervolgens 10 tot 15 minuten totdat moment opnamen naar het Application Insights-exemplaar worden verzonden.
- Zie [moment opnamen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure Portal.
- Zie [Snapshot debugger Troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)(Engelstalig) voor hulp bij het oplossen van problemen met snapshot debugger.


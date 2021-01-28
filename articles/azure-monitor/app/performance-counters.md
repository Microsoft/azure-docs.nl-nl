---
title: Prestatie meter items in Application Insights | Microsoft Docs
description: Systeem-en aangepaste .NET-prestatie meter items bewaken in Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f772b129e20fbaaade7305d84064a713dd734199
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937387"
---
# <a name="system-performance-counters-in-application-insights"></a>Systeem prestatie meter items in Application Insights

Windows biedt een grote verscheidenheid aan [prestatiemeteritems](/windows/desktop/perfctrs/about-performance-counters), bijvoorbeeld voor CPU-bezetting, geheugen, schijfruimte en netwerkgebruik. U kunt ook uw eigen prestatie meter items definiëren. Verzameling van prestatie meter items wordt ondersteund zolang uw toepassing wordt uitgevoerd onder IIS op een on-premises host of virtuele machine waartoe u beheerders toegang hebt. Hoewel toepassingen die als Azure worden uitgevoerd Web Apps geen directe toegang tot prestatie meter items hebben, wordt een subset van de beschik bare items verzameld door Application Insights.

## <a name="view-counters"></a>Items weer geven

In het deel venster metrische gegevens ziet u de standaard set prestatie meter items.

![Prestatie meter items die zijn gerapporteerd in Application Insights](./media/performance-counters/performance-counters.png)

De huidige standaard prestatie meter items die zijn geconfigureerd om te worden verzameld voor ASP.NET/ASP.NET core-webtoepassingen zijn:
- Percentage \\ processor tijd van proces
- % Process \\ processor tijd genormaliseerd
- Geheugen \\ beschik bare bytes
- ASP.NET aanvragen per seconde
- .NET CLR-uitzonde ringen per seconde
- Uitvoerings tijd van ASP.NET ApplicationsRequest
- \\Privé-bytes verwerken
- \\I/o-gegevens bytes verwerken per seconde
- Aanvragen voor ASP.NET-toepassingen \\ in de toepassings wachtrij
- Processor (_Total) \\ % processor tijd

## <a name="add-counters"></a>Items toevoegen

Als het gewenste prestatie meter item niet is opgenomen in de lijst met metrische gegevens, kunt u dit toevoegen.

1. Meer informatie over welke items beschikbaar zijn op uw server met behulp van deze Power shell-opdracht op de lokale server:

    `Get-Counter -ListSet *`

    (Zie [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter) .)
2. Open ApplicationInsights.config.

   * Als u tijdens de ontwikkeling Application Insights aan uw app hebt toegevoegd, bewerkt u ApplicationInsights.config in uw project en implementeert u het vervolgens opnieuw op uw servers.
3. Bewerk de instructie van de prestatie Collector:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core-toepassingen hebben niet `ApplicationInsights.config` en daarom is de bovenstaande methode niet geldig voor ASP.net core toepassingen.

U kunt zowel standaard tellers vastleggen als de items die u zelf hebt geïmplementeerd. `\Objects\Processes` is een voor beeld van een standaard teller die beschikbaar is op alle Windows-systemen. `\Sales(photo)\# Items Sold` is een voor beeld van een aangepaste teller die kan worden geïmplementeerd in een webservice.

De indeling is `\Category(instance)\Counter"` of voor categorieën die geen exemplaren hebben, alleen `\Category\Counter` .

`ReportAs` is vereist voor item namen die niet overeenkomen `[a-zA-Z()/-_ \.]+` -dat wil zeggen dat ze tekens bevatten die zich niet in de volgende sets bevinden: letters, ronde haken, slash, afbreek streepje, onderstrepings teken, spatie, punt.

Als u een exemplaar opgeeft, wordt deze verzameld als een dimensie ' CounterInstanceName ' van de gerapporteerde metriek.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Verzamelen van prestatie meter items in code voor ASP.NET Web Applications of .NET/.NET Core Console-toepassingen
Als u prestatie meter items voor het systeem wilt verzamelen en deze naar Application Insights wilt verzenden, kunt u het volgende fragment aanpassen:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

U kunt ook hetzelfde doen met aangepaste metrische gegevens die u hebt gemaakt:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Prestatie meter items verzamelen in code voor ASP.NET Core-webtoepassingen

Wijzig `ConfigureServices` de methode in uw `Startup.cs` klasse zoals hieronder.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Prestatie meter items in Analytics
U kunt rapporten over prestatie meter items zoeken en weer geven in [Analytics](../log-query/log-query-overview.md).

Het **Performance Counters** -schema stelt de `category` , `counter` naam en `instance` naam van elk prestatie meter item in.  In de telemetrie voor elke toepassing ziet u alleen de items voor die toepassing. Als u bijvoorbeeld wilt zien welke items beschikbaar zijn: 

![Prestatie meter items in Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(' Instance ' komt hier naar het exemplaar van het prestatie meter item, niet de rol of server machine-exemplaar. De exemplaar naam van het prestatie meter item telt doorgaans tellers zoals processor tijd op naam van het proces of de toepassing.)

Een grafiek van het beschik bare geheugen voor de recente periode ophalen: 

![Geheugen timechart in Application Insights Analytics](./media/performance-counters/analytics-available-memory.png)

Net als bij andere telemetrie heeft **Performance Counters** ook een kolom `cloud_RoleInstance` die de identiteit aangeeft van het exemplaar van de hostserver waarop uw app wordt uitgevoerd. Als u bijvoorbeeld de prestaties van uw app op de verschillende computers wilt vergelijken: 

![Prestaties gesegmenteerd op rolinstantie in Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET en aantal Application Insights

*Wat is het verschil tussen het uitzonderings tempo en de metrische gegevens van uitzonde ringen?*

* *Uitzonderings snelheid* is een systeem prestatie meter item. De CLR telt alle verwerkte en onverwerkte uitzonde ringen die worden gegenereerd en deelt het totaal in een steekproef interval met de lengte van het interval. De Application Insights SDK verzamelt dit resultaat en verzendt het naar de portal.

* *Uitzonde ringen* is een telling van de TrackException-rapporten die worden ontvangen door de portal in het steekproef interval van de grafiek. Het bevat alleen de verwerkte uitzonde ringen waarin u TrackException-aanroepen hebt geschreven in uw code en bevat geen niet-verwerkte [uitzonde ringen](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Prestatie meter items voor toepassingen die worden uitgevoerd in azure Web Apps

Zowel ASP.NET-als ASP.NET Core-toepassingen die zijn geïmplementeerd op Azure Web Apps in een speciale sandbox-omgeving worden uitgevoerd. Deze omgeving staat geen directe toegang tot systeem prestatie meter items toe. Een beperkte subset van tellers wordt echter weer gegeven als omgevings variabelen, zoals [hier](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)wordt beschreven. Application Insights SDK voor ASP.NET en ASP.NET Core verzamelt prestatie meter items van Azure Web Apps vanuit deze speciale omgevings variabelen. Er is slechts een subset van de prestatie meter items beschikbaar in deze omgeving en de volledige lijst vindt u [hier.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestatie meter items in ASP.NET Core toepassingen

De ondersteuning voor prestatie meter items in ASP.NET Core is beperkt:

* Met [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -versies 2.4.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in azure web apps (Windows).
* Met SDK-versies 2.7.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in Windows en doelen `NETSTANDARD2.0` of hoger.
* Voor toepassingen die zijn gericht op de .NET Framework, worden alle versies van de SDK-prestatie meter items ondersteund.
* SDK-versies 2.8.0 en hoger ondersteunen CPU/geheugen teller in Linux. Er wordt geen ander item ondersteund in Linux. De aanbevolen manier om systeem tellers op te halen in Linux (en andere niet-Windows-omgevingen) is met behulp van [EventCounters](eventcounters.md)

## <a name="alerts"></a>Waarschuwingen
Net als bij andere metrische gegevens kunt u [een waarschuwing instellen](../platform/alerts-log.md) om u te waarschuwen als een prestatie meter item buiten een limiet valt die u opgeeft. Open het deel venster waarschuwingen en klik op waarschuwing toevoegen.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

* [Afhankelijkheden bijhouden](./asp-net-dependencies.md)
* [Uitzonde ringen bijhouden](./asp-net-exceptions.md)


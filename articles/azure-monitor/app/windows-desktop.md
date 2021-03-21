---
title: Gebruik en prestaties bewaken voor Windows-bureaublad-apps
description: Analyseer het gebruik en de prestaties van uw Windows-bureaublad-app met Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583319"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Gebruik en prestaties bewaken in Klassieke Windows-bureaublad-apps

Toepassingen die on-premises worden gehost, in Azure, en in andere clouds, kunnen allemaal profiteren van Application Insights. De enige beperking is de noodzaak om [communicatie toe te staan](./ip-addresses.md) met de Application Insights-service. Voor het bewaken van UWP-toepassingen (Universal Windows Platform), raden we [Visual Studio App Center](../app/mobile-center-quickstart.md) aan.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Telemetrie verzenden naar Application Insights vanuit een Klassieke Windows-toepassing
1. Maak in de [Azure Portal](https://portal.azure.com)[een Application Insights-resource](./create-new-resource.md). 
2. Kopieer de instrumentatiesleutel.
3. Bewerk in Visual Studio de NuGet-pakketten van uw app-project en voeg Microsoft.ApplicationInsights.WindowsServer toe. (Of kies micro soft. ApplicationInsights als u alleen de basis-API wilt, zonder de standaard telemetrie-verzamelings modules.)
4. Stel de instrumentatiesleutel in uw code in:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*uw sleutel*`";`
   
    of in ApplicationInsights.config (als u een van de standaardtelemetriepakketten hebt geïnstalleerd):
   
    `<InstrumentationKey>`*uw sleutel*`</InstrumentationKey>` 
   
    Als u ApplicationInsights.config gebruikt, moet u ervoor zorgen dat de bijbehorende eigenschappen in Solution Explorer zijn ingesteld op **Buildactie = Inhoud, Naar uitvoermap kopiëren = Kopiëren**.
5. [Gebruik de API](./api-custom-events-metrics.md) om telemetrie te verzenden.
6. Voer uw app uit en Bekijk de telemetrie in de resource die u hebt gemaakt in de Azure Portal.

## <a name="example-code"></a><a name="telemetry"></a>Voorbeeldcode

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Opslag van computer naam overschrijven

Standaard verzamelt deze SDK de computer naam van het systeem dat telemetrie verzendt en opslaat.

De computer naam wordt gebruikt door de [prijs categorie Application Insights verouderde onderneming (per knoop punt)](./pricing.md#legacy-enterprise-per-node-pricing-tier) voor interne facturerings doeleinden. Als u een initialisatie functie voor telemetrie gebruikt om te overschrijven `telemetry.Context.Cloud.RoleInstance` , wordt er standaard een afzonderlijke eigenschap `ai.internal.nodeName` verzonden, waarin de waarde van de computer naam nog steeds wordt opgenomen. Deze waarde wordt niet opgeslagen met uw Application Insights telemetrie, maar wordt intern gebruikt bij opname om achterwaartse compatibiliteit met het op verouderde op knoop punt gebaseerde facturerings model mogelijk te maken.

Als u zich in de [prijs categorie verouderde onderneming (per knoop punt)](./pricing.md#legacy-enterprise-per-node-pricing-tier) bevindt en simpelweg de opslag van de computer naam moet overschrijven, gebruikt u de initialisatie functie voor telemetrie:

**Schrijf aangepaste TelemetryInitializer zoals hieronder.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Exemplaar de initialisatie functie in de `Program.cs` `Main()` onderstaande methode, waarbij u de instrumentatie sleutel instelt:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Verzen ding van computer naam overschrijven

Als u zich niet in de [prijs categorie verouderde onderneming (per knoop punt)](./pricing.md#legacy-enterprise-per-node-pricing-tier) bevindt en wilt voor komen dat een telemetrie met de computer naam wordt verzonden, moet u een telemetrie-processor gebruiken.

### <a name="telemetry-processor"></a>Telemetrie-processor

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Maak een exemplaar van de telemetrie-processor in de `Program.cs` `Main()` onderstaande methode, waarbij u de instrumentatie sleutel instelt:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Hoewel u technisch gebruik kunt maken van een telemetrie-processor zoals hierboven wordt beschreven, zelfs als u zich in de [prijs categorie verouderde onderneming (per knoop punt)](./pricing.md#legacy-enterprise-per-node-pricing-tier)bevindt, leidt dit ertoe dat het mogelijk is om te voor komen dat knoop punten goed kunnen worden onderscheiden voor prijzen per knoop punt.

## <a name="next-steps"></a>Volgende stappen
* [Een dashboard maken](./overview-dashboard.md)
* [Diagnostische gegevens doorzoeken](./diagnostic-search.md)
* [Metrische gegevens verkennen](../essentials/metrics-charts.md)
* [Analytics-query's schrijven](../logs/log-query-overview.md)


---
title: .NET-traceer Logboeken in Application Insights verkennen
description: Zoek logboeken die zijn gegenereerd door Trace, NLog of Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937550"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>.NET/.NET core-en python-traceer Logboeken in Application Insights verkennen

Verzend Diagnostische logboeken voor uw ASP.NET/ASP.NET-kern toepassing vanuit ILogger, NLog, log4Net of System. Diagnostics. trace naar [Azure-toepassing Insights][start]. Voor python-toepassingen verzendt u diagnostische tracerings logboeken met behulp van AzureLogHandler in opentellingen python voor Azure Monitor. U kunt ze verkennen en doorzoeken. Deze logboeken worden samengevoegd met de andere logboek bestanden van uw toepassing, zodat u traceringen kunt identificeren die zijn gekoppeld aan elke gebruikers aanvraag en ze kunnen correleren met andere gebeurtenissen en uitzonderings rapporten.

> [!NOTE]
> Hebt u de module logboek registratie nodig? Het is een handige adapter voor logboeken van derden. Als u NLog, log4Net of System. Diagnostics. trace niet al gebruikt, kunt u het beste direct [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) aanroepen.
>
>
## <a name="install-logging-on-your-app"></a>Logboek registratie voor uw app installeren
Installeer uw gekozen Framework voor logboek registratie in uw project. dit moet resulteren in een vermelding in app.config of web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Application Insights configureren voor het verzamelen van Logboeken
[Voeg Application Insights toe aan uw project](./asp-net.md) als u dat nog niet hebt gedaan. U ziet een optie voor het toevoegen van de logboek verzamelaar.

Of klik met de rechter muisknop op uw project in Solution Explorer om **Application Insights te configureren**. Selecteer de optie **tracerings verzameling configureren** .

> [!NOTE]
> Geen Application Insights menu of logboek verzamelaar-optie? Probeer het probleem op te [lossen](#troubleshooting).

## <a name="manual-installation"></a>Handmatige installatie
Gebruik deze methode als uw project type niet wordt ondersteund door het Application Insights-installatie programma (bijvoorbeeld een Windows Desktop-project).

1. Als u van plan bent log4Net of NLog te gebruiken, installeert u deze in uw project.
2. Klik in Solution Explorer met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren**.
3. Zoek naar ' Application Insights '.
4. Selecteer een van de volgende pakketten:

   - Voor ILogger: [micro soft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Voor NLog: [micro soft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Voor log4net: [micro soft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet log4net banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Voor System. Diagnostics: [micro soft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System. diagnose banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Micro soft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Span doek voor NuGet diagnostische bron](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Micro soft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ Banner voor NuGet etw-Collector](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Micro soft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Span doek voor NuGet-gebeurtenis bron](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Het NuGet-pakket installeert de benodigde assembly's en wijzigt web.config of app.config als dit van toepassing is.

## <a name="ilogger"></a>ILogger

Zie [ApplicationInsightsLoggerProvider voor .net core ILogger-logboeken](ilogger.md)voor voor beelden van het gebruik van de Application Insights ILogger-implementatie met console toepassingen en ASP.net core.

## <a name="insert-diagnostic-log-calls"></a>Diagnostische logboek aanroepen invoegen
Als u System. Diagnostics. trace gebruikt, zou een typische oproep het volgende zijn:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Als u liever log4net of NLog, gebruikt u:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Event source-gebeurtenissen gebruiken
U kunt [System. Diagnostics. tracing. Event source](/dotnet/api/system.diagnostics.tracing.eventsource) -gebeurtenissen configureren die moeten worden verzonden naar Application Insights als traceringen. Installeer eerst het `Microsoft.ApplicationInsights.EventSourceListener` pakket NuGet. Bewerk vervolgens de `TelemetryModules` sectie van het [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) -bestand.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Voor elke bron kunt u de volgende para meters instellen:
 * **Naam** geeft de naam aan van de Event source die moet worden verzameld.
 * **Niveau** geeft het logboek registratie niveau aan dat moet worden verzameld: *kritiek*, *fout*, *informatief*, *LogAlways*, *uitgebreid* of *waarschuwing*.
 * **Tref woorden** (optioneel) Geef de integere waarde op van trefwoord combinaties die moeten worden gebruikt.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource-gebeurtenissen gebruiken
U kunt [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) -gebeurtenissen configureren die moeten worden verzonden naar Application Insights als traceringen. Installeer eerst het [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakket NuGet. Bewerk vervolgens de sectie ' TelemetryModules ' van het [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) -bestand.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Voor elke DiagnosticSource die u wilt traceren, voegt u een vermelding toe met het kenmerk **name** ingesteld op de naam van uw DiagnosticSource.

## <a name="use-etw-events"></a>ETW-gebeurtenissen gebruiken
U kunt Event Tracing for Windows (ETW)-gebeurtenissen configureren die naar Application Insights worden verzonden als traceringen. Installeer eerst het `Microsoft.ApplicationInsights.EtwCollector` pakket NuGet. Bewerk vervolgens de sectie ' TelemetryModules ' van het [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) -bestand.

> [!NOTE] 
> ETW-gebeurtenissen kunnen alleen worden verzameld als het proces dat als host fungeert voor de SDK wordt uitgevoerd onder een identiteit die lid is van prestatie logboek gebruikers of beheerders.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Voor elke bron kunt u de volgende para meters instellen:
 * **ProviderName** is de naam van de etw-provider die moet worden verzameld.
 * **ProviderGuid** Hiermee geeft u de GUID op van de etw-provider die u wilt verzamelen. Het kan worden gebruikt in plaats van `ProviderName` .
 * **Niveau** stelt het logboek registratie niveau in dat moet worden verzameld. Dit kan *kritiek*, *fout*, *informatief*, *LogAlways*, *uitgebreid* of *waarschuwing* zijn.
 * **Tref woorden** (optioneel) Stel de gehele waarde van trefwoord combinaties in op gebruik.

## <a name="use-the-trace-api-directly"></a>De tracerings-API rechtstreeks gebruiken
U kunt de Application Insights Trace-API rechtstreeks aanroepen. De logboek registratie adapters gebruiken deze API.

Bijvoorbeeld:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

Een voor deel van TrackTrace is dat u relatief lange gegevens in het bericht kunt plaatsen. U kunt bijvoorbeeld POST-gegevens coderen.

U kunt ook een Ernst niveau aan uw bericht toevoegen. En, net als bij andere telemetrie, kunt u eigenschaps waarden toevoegen om te helpen bij het filteren of zoeken naar verschillende sets traceringen. Bijvoorbeeld:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Zo kunt u eenvoudig filteren op het [zoeken][diagnostic] in alle berichten van een bepaald Ernst niveau die betrekking hebben op een bepaalde data base.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler voor opentellingen python
Met de handler voor Azure Monitor-Logboeken kunt u python-logboeken exporteren naar Azure Monitor.

Instrumenteer uw toepassing met de [Opentellingen PYTHON SDK](./opencensus-python.md) voor Azure monitor.

In dit voor beeld ziet u hoe u een logboek op waarschuwings niveau verzendt naar Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Uw logboeken verkennen
Voer uw app uit in de foutopsporingsmodus of implementeer deze live.

Selecteer in het deel venster Overzicht van de app in [de Application Insights Portal][portal] [zoeken][diagnostic].

U kunt bijvoorbeeld het volgende doen:

* Filter op logboek traceringen of op items met specifieke eigenschappen.
* Inspecteer een specifiek item in detail.
* Andere systeem logboek gegevens zoeken die betrekking hebben op dezelfde gebruikers aanvraag (dezelfde OperationId hebben).
* Sla de configuratie van een pagina op als favoriet.

> [!NOTE]
>Als uw toepassing veel gegevens verzendt en u de Application Insights SDK voor ASP.NET versie 2.0.0-beta3 of hoger gebruikt, kan de *adaptieve bemonsterings* functie werken en slechts een deel van uw telemetrie verzenden. [Meer informatie over steekproeven.](./sampling.md)
>

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="how-do-i-do-this-for-java"></a>Hoe kan ik doe dit voor Java?
In Java-instrumentatie zonder code (aanbevolen) de logboeken worden verzameld uit het vak. Gebruik de [java 3,0-agent](./java-in-process-agent.md).

Als u de Java-SDK gebruikt, gebruikt u de [Java-logboek adapters](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Er is geen optie Application Insights in het context menu van het project
* Zorg ervoor dat Developer Analytics-Hulpprogram Ma's op de ontwikkel computer zijn geïnstalleerd. Zoek in de   >  **extensies en updates** van Visual Studio-hulpprogram ma's naar **Developer Analytics-hulpprogram ma's**. Als dit **niet het geval** is, opent u het tabblad **online** en installeert u het.
* Dit kan een project type zijn dat niet wordt ondersteund door Developer Analytics tools. [Hand matige installatie](#manual-installation)gebruiken.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Er is geen optie voor de logboek adapter in het configuratie hulpprogramma
* Installeer eerst het Framework voor logboek registratie.
* Als u System. Diagnostics. trace gebruikt, zorg er dan voor dat u deze [in *web.config* hebt geconfigureerd](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Zorg ervoor dat u beschikt over de nieuwste versie van Application Insights. Ga in Visual Studio naar **hulpprogram ma's** voor  >  **uitbrei dingen en updates** en open het tabblad **updates** . Als **Developer Analytics-Hulpprogram ma's** er is, selecteert u deze om het bij te werken.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Fout bericht ' de instrumentatie sleutel mag niet leeg zijn '
Waarschijnlijk hebt u het NuGet-pakket voor de logboek registratie adapter geïnstalleerd zonder Application Insights te installeren. Klik in Solution Explorer met de rechter muisknop op *ApplicationInsights.config* en selecteer **Update Application Insights**. U wordt gevraagd om u aan te melden bij Azure en een Application Insights resource te maken of een bestaande te hergebruiken. Dat het probleem moet oplossen.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Ik zie traceringen maar geen andere gebeurtenissen in diagnostische Zoek opdrachten
Het kan even duren voordat alle gebeurtenissen en aanvragen worden ontvangen via de pijp lijn.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hoeveel gegevens worden er bewaard?
Verschillende factoren zijn van invloed op de hoeveelheid gegevens die wordt bewaard. Zie de sectie [limieten](./api-custom-events-metrics.md#limits) van de pagina metrische gegevens van klant gebeurtenis voor meer informatie.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Ik zie enkele logboek vermeldingen die ik had verwacht
Als uw toepassing Voluminous hoeveel heden gegevens verzendt en u de Application Insights SDK voor ASP.NET-versie 2.0.0-beta3 of hoger gebruikt, kan de adaptieve bemonsterings functie werken en slechts een deel van uw telemetrie verzenden. [Meer informatie over steekproeven.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Volgende stappen

* [Fouten en uitzonde ringen in ASP.NET diagnosticeren][exceptions]
* [Meer informatie over zoeken][diagnostic]
* [Testen van Beschik baarheid en reactie snelheid instellen][availability]
* [Problemen oplossen][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md


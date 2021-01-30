---
title: Problemen met ontbrekende gegevens oplossen - Application Insights voor .NET
description: Ziet u geen gegevens in Azure-toepassing Insights? Probeer het hier.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: e41b0a9ce1ff86bc6010e12fdf5d3320f303fd87
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092448"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Problemen met geen gegevens Application Insights voor .NET/.NET core oplossen

## <a name="some-of-my-telemetry-is-missing"></a>Er ontbreekt een deel van de telemetrie
*In Application Insights ziet u alleen een fractie van de gebeurtenissen die door mijn app worden gegenereerd.*

* Als u op consistente wijze dezelfde fractie ziet, is dit waarschijnlijk het gevolg van adaptieve [steek proeven](./sampling.md). Om dit te bevestigen, opent u zoeken (op de Blade overzicht) en bekijkt u een exemplaar van een aanvraag of een andere gebeurtenis. Klik onder aan het gedeelte Eigenschappen op '... ' Details van de volledige eigenschap ophalen. Als het aantal aanvragen > 1, dan wordt de steek proef van de bewerking uitgevoerd.
* Anders is het mogelijk dat u een limiet hebt bereikt voor de [gegevens frequentie](./pricing.md#limits-summary) voor uw prijs plan. Deze limieten worden per minuut toegepast.

*Er is een wille keurig gegevens verlies opgetreden.*

* Controleren of gegevens verlies optreedt op een [telemetrie-kanaal](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Controleren op bekende problemen in [github opslag plaats](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) van het telemetrie-kanaal

*Er treden gegevens verloren in de console-app of op Web-apps wanneer de app wordt gestopt.*

* Het SDK-kanaal houdt telemetrie in de buffer en verzendt deze in batches. Als de toepassing wordt afgesloten, moet u mogelijk expliciet [Flush ()](api-custom-events-metrics.md#flushing-data)aanroepen. Gedrag van `Flush()` hangt af van het gebruikte [kanaal](telemetry-channels.md#built-in-telemetry-channels) .

## <a name="no-data-from-my-server"></a>Geen gegevens van mijn server
*Ik heb mijn app op mijn webserver geïnstalleerd en nu zie ik geen telemetrie. De werk ervaring op mijn dev machine is OK.*

* Waarschijnlijk een probleem met de firewall. [Firewall-uitzonde ringen instellen voor Application Insights om gegevens te verzenden](./ip-addresses.md).
* Er ontbreken mogelijk enkele vereisten voor de IIS-server: .NET Extensibility 4,5 en ASP.NET 4,5.

*Ik heb Status Monitor op mijn webserver [geïnstalleerd](./monitor-performance-live-website-now.md) om bestaande apps te controleren. Ik zie geen resultaten.*

* Zie [probleem oplossing status monitor](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=net) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: kan het bestand of de assembly micro soft. AspNet TelemetryCorrelation niet laden

Zie [GitHub issue 1610] (voor meer informatie over deze fout https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

Wanneer u een upgrade uitvoert van Sdk's die ouder zijn dan (2,4), moet u ervoor zorgen dat de volgende wijzigingen worden toegepast op `web.config` en `ApplicationInsights.config` :

1. Twee HTTP-modules in plaats van één. In `web.config` moet u twee HTTP-modules hebben. De volg orde is belang rijk voor bepaalde scenario's:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Daarnaast `RequestTrackingTelemetryModule` moet u de volgende telemetrie-module hebben:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

**_Fout bij het uitvoeren van een upgrade kan leiden tot onverwachte uitzonde ringen of telemetrie niet worden verzameld._* _


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Geen optie voor het toevoegen van Application Insights in Visual Studio
_When ik met de rechter muisknop op een bestaand project in Solution Explorer, zie ik geen Application Insights opties. *

* Niet alle typen .NET-projecten worden ondersteund door de hulpprogram ma's. Web-en WCF-projecten worden ondersteund. Voor andere project typen, zoals bureau blad-of service toepassingen, kunt u nog steeds [een Application INSIGHTS SDK hand matig toevoegen aan uw project](./windows-desktop.md).
* Zorg ervoor dat u [Visual Studio 2013 update 3 of hoger](/visualstudio/releasenotes/vs2013-update3-rtm-vs)hebt. Dit is vooraf geïnstalleerd met Developer Analytics-hulpprogram ma's, die de Application Insights SDK bieden.
* Selecteer **extra**, **uitbrei dingen en updates** en controleer of **Developer Analytics-hulpprogram ma's** zijn geïnstalleerd en ingeschakeld. Als dit het geval is, klikt u op **updates** om te zien of er een update beschikbaar is.
* Open het dialoog venster New project en kies ASP.NET Web Application. Als u de optie Application Insights ziet, worden de hulpprogram ma's geïnstalleerd. Als dat niet het geval is, kunt u proberen de Hulpprogram Ma's voor ontwikkel aars te verwijderen en vervolgens opnieuw te installeren.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Application Insights toevoegen is mislukt
*Wanneer ik Application Insights aan een bestaand project probeer toe te voegen, wordt er een fout bericht weer gegeven.*

Mogelijke oorzaken:

* Communicatie met de Application Insights Portal is mislukt; of
* Er is een probleem met uw Azure-account.
* U hebt alleen [Lees toegang tot het abonnement of de groep waar u de nieuwe resource probeerde te maken](./resources-roles-access-control.md).

Holpen

* Controleer of u aanmeldings referenties hebt ingesteld voor het juiste Azure-account.
* Controleer of u toegang hebt tot de [Azure Portal](https://portal.azure.com)in uw browser. Open instellingen en controleer of er beperkingen gelden.
* [Application Insights toevoegen aan uw bestaande project](./asp-net.md): klik in Solution Explorer met de rechter muisknop op het project en kies Application Insights toevoegen.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Er wordt een fout bericht weer geven dat de instrumentatie sleutel mag niet leeg zijn
Er is een fout opgetreden tijdens de installatie van Application Insights of mogelijk een logboek registratie adapter.

Klik in Solution Explorer met de rechter muisknop op het project en kies **Application Insights > Application Insights configureren**. U krijgt een dialoog venster waarin u wordt gevraagd om u aan te melden bij Azure, een Application Insights resource te maken of een bestaand item opnieuw te gebruiken.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> Er ontbreken een of meer NuGet-pakketten op mijn build-server
*Alles bouwt op OK wanneer ik fout opsporing op mijn ontwikkel computer, maar er wordt een NuGet-fout op de build-server weer geven.*

Zie [NuGet package Restore](https://docs.nuget.org/Consume/Package-Restore) and [Automatic Package Restore](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Menu opdracht ontbreekt om Application Insights te openen vanuit Visual Studio
*Als ik met de rechter muisknop op mijn project Solution Explorer, zie ik geen Application Insights opdrachten of ik zie geen open Application Insights-opdracht.*

Mogelijke oorzaken:

* Als u de Application Insights resource hand matig hebt gemaakt of als het project van een type is dat niet wordt ondersteund door de Application Insights-hulpprogram ma's.
* De hulpprogram ma's voor de analyse van ontwikkel aars zijn uitgeschakeld in uw Visual Studio.
* Uw Visual Studio is ouder dan 2013 update 3.

Holpen

* Zorg ervoor dat uw versie van Visual Studio 2013 update 3 of hoger is.
* Selecteer **extra**, **uitbrei dingen en updates** en controleer of **Developer Analytics-hulpprogram ma's** zijn geïnstalleerd en ingeschakeld. Als dit het geval is, klikt u op **updates** om te zien of er een update beschikbaar is.
* Klik met de rechtermuisknop op uw project in Solution Explorer. Als u de opdracht **Application Insights > Application Insights configureren** ziet, gebruikt u deze om uw project te koppelen aan de resource in de Application Insights-service.

Anders wordt het project type niet rechtstreeks ondersteund door de Developer Analytics-hulpprogram ma's. Als u uw telemetrie wilt zien, meldt u zich aan bij de [Azure Portal](https://portal.azure.com), kiest u Application Insights op de linkernavigatiebalk en selecteert u uw toepassing.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Toegang geweigerd bij het openen van Application Insights van Visual Studio
*Met de menu opdracht openen Application Insights gaat u naar de Azure Portal, maar krijgt u de fout toegang geweigerd.*

De micro soft-aanmelding die u voor het laatst hebt gebruikt in uw standaard browser heeft geen toegang tot [de resource die is gemaakt toen Application Insights werd toegevoegd aan deze app](./asp-net.md). Er zijn twee mogelijke redenen:

* U hebt meer dan een Microsoft-account-misschien een werk en een persoonlijke Microsoft-account? De aanmelding die u voor het laatst hebt gebruikt in uw standaard browser, was voor een ander account dan de versie die toegang heeft om [Application Insights toe te voegen aan het project](./asp-net.md).
  * Herstellen: Klik in de rechter bovenhoek van het browser venster op uw naam en meld u af. Meld u vervolgens aan met het account dat toegang heeft. Klik in de linkernavigatiebalk op Application Insights en selecteer uw app.
* Iemand anders heeft Application Insights toegevoegd aan het project en is verg eten om [toegang te krijgen tot de resource groep](./resources-roles-access-control.md) waarin het is gemaakt.
  * Oplossen: als ze een organisatie-account hebben gebruikt, kunnen ze aan het team worden toegevoegd. of ze kunnen u afzonderlijke toegang verlenen tot de resource groep.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>' Activum niet gevonden ' bij het openen van Application Insights vanuit Visual Studio
*Met de menu opdracht ' openen Application Insights ' gaat u naar de Azure Portal, maar krijg ik de fout ' kan het activum niet vinden '.*

Mogelijke oorzaken:

* De Application Insights resource voor uw toepassing is verwijderd. of
* De instrumentatie sleutel is ingesteld of gewijzigd in ApplicationInsights.config door deze rechtstreeks te bewerken, zonder het project bestand bij te werken.

De instrumentatie sleutel in ApplicationInsights.config bepaalt waar de telemetrie wordt verzonden. Een regel in het project bestand bepaalt welke resource wordt geopend wanneer u de opdracht in Visual Studio gebruikt.

Holpen

* Klik in Solution Explorer met de rechter muisknop op het project en kies Application Insights, Application Insights configureren. In het dialoog venster kunt u ervoor kiezen om telemetrie te verzenden naar een bestaande resource of een nieuw item te maken. Of
* Open de resource rechtstreeks. Meld u aan bij [de Azure Portal](https://portal.azure.com), klik op Application Insights in de linker navigatie balk en selecteer vervolgens uw app.

## <a name="where-do-i-find-my-telemetry"></a>Waar vind ik mijn telemetrie?
*Ik heb me aangemeld bij de [Microsoft Azure-Portal](https://portal.azure.com)en ik Bekijk het Azure Home-dash board. Waar vind ik mijn Application Insights gegevens?*

* Klik in de linkernavigatiebalk op Application Insights en vervolgens op de naam van uw app. Als u nog geen projecten hebt, moet u [Application Insights toevoegen aan of configureren in uw webproject](./asp-net.md).  
  Hier ziet u een aantal samenvattings grafieken. U kunt hier op klikken om meer details weer te geven.
* Tijdens het opsporen van fouten in uw app, klikt u in Visual Studio op de knop Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Geen server gegevens (of geen gegevens)
*Ik heb mijn app uitgevoerd en ik heb de Application Insights-service in Microsoft Azure geopend, maar in alle grafieken wordt uitgelegd hoe ik het verzamelen... of is niet geconfigureerd.* Of, *alleen pagina weergave en gebruikers gegevens, maar geen server gegevens.*

* Voer uw toepassing uit in de foutopsporingsmodus in Visual Studio (F5). Gebruik de toepassing om een telemetrie te genereren. Controleer of de gebeurtenissen in het venster Visual Studio uitvoer worden weer gegeven.  
  ![Scherm opname van de uitvoering van uw toepassing in de foutopsporingsmodus in Visual Studio.](./media/asp-net-troubleshoot-no-data/output-window.png)
* Open in de Application Insights-Portal [Diagnostische gegevens zoeken](./diagnostic-search.md). Gegevens worden doorgaans als eerste weer gegeven.
* Klik op de knop Vernieuwen. De Blade wordt regel matig vernieuwd, maar u kunt dit ook hand matig doen. Het Vernieuwings interval is langer voor grotere Peri Oden.
* Controleer of de instrumentatie sleutels overeenkomen. Bekijk op de hoofd Blade voor uw app in de Application Insights-Portal in de vervolg keuzelijst **Essentials** de **instrumentatie sleutel**. Open vervolgens in het project in Visual Studio ApplicationInsights.config en zoek de `<instrumentationkey>` . Controleer of de twee sleutels gelijk zijn. Zo niet:  
  * Klik in de portal op Application Insights en zoek de app-resource met de juiste sleutel. of
  * Klik in Visual Studio Solution Explorer met de rechter muisknop op het project en kies Application Insights, configureren. Stel de app opnieuw in om telemetrie naar de juiste resource te verzenden.
  * Als u de overeenkomende sleutels niet kunt vinden, controleert u of u dezelfde aanmeldings referenties in Visual Studio gebruikt als in voor de portal.
* Ga in het [Microsoft Azure start-dash board](https://portal.azure.com)naar de service Health kaart. Als er enkele waarschuwings vermeldingen zijn, wacht u totdat ze zijn teruggekeerd naar OK. Sluit vervolgens de Blade Application Insights toepassing en open deze opnieuw.
* Controleer ook [onze status blog](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog).
* Hebt u code geschreven voor de SDK aan de [server zijde](./api-custom-events-metrics.md) die de instrumentatie sleutel in `TelemetryClient` instanties of in kan wijzigen `TelemetryContext` ? Of hebt u een [filter of sampling configuratie](./api-filtering-sampling.md) geschreven die te veel kan worden gefilterd?
* Als u ApplicationInsights.config hebt bewerkt, controleert u zorgvuldig de configuratie van [TelemetryInitializers en TelemetryProcessors](./api-filtering-sampling.md). Een type of para meter met een onjuiste naam kan ertoe leiden dat de SDK geen gegevens verzendt.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Geen gegevens op pagina weergaven, browsers, gebruik
*Ik zie gegevens in de grafieken server reactietijd en server aanvragen, maar geen gegevens in de pagina weergave, of op de Blades browser of gebruik.*

De gegevens zijn afkomstig van scripts in de webpagina's. 

* Als u Application Insights hebt toegevoegd aan een bestaand webproject, moet [u de scripts hand matig toevoegen](./javascript.md).
* Zorg ervoor dat uw site niet wordt weer gegeven in de compatibiliteits modus van Internet Explorer.
* Gebruik de functie fout opsporing van de browser (F12 op sommige browsers en kies vervolgens netwerk) om te controleren of de gegevens naar worden verzonden `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Geen gegevens over afhankelijkheid of uitzonde ring
Zie [afhankelijkheids telemetrie](./asp-net-dependencies.md) en [uitzonde ring telemetrie](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Geen prestatie gegevens
Prestatie gegevens (CPU, i/o-snelheid, enzovoort) zijn beschikbaar voor [Java-webservices](./java-collectd.md), [Windows-bureau blad-apps](./windows-desktop.md), IIS-web- [apps en-services als u status monitor](./monitor-performance-live-website-now.md)en [Azure Cloud Services](./app-insights-overview.md)installeert. u vindt deze onder instellingen, servers.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Geen (Server) gegevens sinds ik de app heb gepubliceerd op mijn server
* Controleer of u alle micro soft hebt gekopieerd. ApplicationInsights dll-bestanden naar de server, samen met Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* In uw firewall moet u mogelijk [enkele TCP-poorten openen](./ip-addresses.md).
* Als u een proxy moet gebruiken om uw bedrijfs netwerk te verzenden, stelt u [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) in Web.config
* Windows Server 2008: Zorg ervoor dat u de volgende updates hebt geïnstalleerd: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Ik heb de gegevens weer gegeven, maar deze zijn gestopt
* Hebt u uw maandelijkse quotum van gegevens punten bereikt? Open de instellingen/het quotum en de prijzen voor meer informatie. Als dat het geval is, kunt u uw abonnement upgraden of extra capaciteit betalen. Zie het [prijs schema](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Ik zie niet alle gegevens die ik verwacht
Als uw toepassing veel gegevens verzendt en u de Application Insights SDK voor ASP.NET versie 2.0.0-beta3 of hoger gebruikt, kan de [adaptieve bemonsterings](./sampling.md) functie werken en alleen een percentage van uw telemetrie verzenden.

U kunt deze uitschakelen, maar dit wordt niet aanbevolen. Steek proeven zijn zodanig ontworpen dat gerelateerde telemetrie correct wordt verzonden voor diagnostische doel einden.

## <a name="client-ip-address-is-0000"></a>IP-adres van client is 0.0.0.0

Op 5 2018 februari hebben we gemeld dat de registratie van het client-IP-adres is verwijderd. Dit heeft geen invloed op de geografische locatie.

> [!NOTE]
> Als u de eerste 3 octetten van het IP-adres nodig hebt, kunt u een [initialisatie functie voor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) gebruiken om een aangepast kenmerk toe te voegen.
> Dit heeft geen invloed op gegevens die zijn verzameld vóór 5 februari 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Verkeerde geografische gegevens in de telemetrie van de gebruiker
De dimensies plaats, regio en land worden afgeleid van IP-adressen en zijn niet altijd nauw keurig. Deze IP-adressen worden eerst verwerkt voor de locatie en vervolgens gewijzigd in 0.0.0.0 om te worden opgeslagen.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Uitzondering Methode niet gevonden bij het uitvoeren van uw app in Azure Cloud Services
Hebt u uw app ontwikkeld voor .NET 4.6? 4.6 wordt niet automatisch ondersteund in Azure Cloud Services-rollen. [Installeer 4.6 voor elke rol](../../cloud-services/cloud-services-dotnet-install-dotnet.md) voordat u uw app uitvoert.

## <a name="troubleshooting-logs"></a>Probleemoplossings logboeken

Volg deze instructies om logboeken voor het oplossen van problemen vast te leggen voor uw Framework.

### <a name="net-framework"></a>.NET Framework

1. Installeer het pakket [micro soft. AspNet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) van NuGet. De versie die u installeert, moet overeenkomen met de huidige geïnstalleerde versie van `Microsoft.ApplicationInsighs`

2. Wijzig het applicationinsights.config-bestand zodat het het volgende bevat:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Uw toepassing moet schrijf machtigingen hebben voor de geconfigureerde locatie

3. Het proces opnieuw starten zodat deze nieuwe instellingen door SDK worden opgehaald

4. Als u klaar bent, kunt u deze wijzigingen ongedaan maken.

### <a name="net-core"></a>.NET Core

1. Installeer het [Application INSIGHTS SDK NuGet-pakket voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pakket van NuGet. De versie die u installeert, moet overeenkomen met de huidige geïnstalleerde versie van `Microsoft.ApplicationInsights` .

   De nieuwste versie van micro soft. ApplicationInsights. AspNetCore is 2.14.0 en verwijst naar micro soft. ApplicationInsights versie 2.14.0. De versie van micro soft. ApplicationInsights. AspNetCore die moet worden geïnstalleerd, moet daarom 2.14.0 zijn.

2. Wijzig `ConfigureServices` de methode in uw `Startup.cs` klasse.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Uw toepassing moet schrijf machtigingen hebben voor de geconfigureerde locatie

3. Het proces opnieuw starten zodat deze nieuwe instellingen door SDK worden opgehaald

4. Als u klaar bent, kunt u deze wijzigingen ongedaan maken.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Logboeken verzamelen met PerfView
[PerfView](https://github.com/Microsoft/perfview) is een gratis hulp programma voor diagnostische gegevens en prestaties waarmee u de CPU, het geheugen en andere problemen kunt isoleren door Diagnostische gegevens uit een groot aantal bronnen te verzamelen en te visualiseren.

De logboeken van de logboek gebeurtenissen van de Application Insights-SDK registreren die door PerfView kunnen worden vastgelegd.

Als u logboeken wilt verzamelen, downloadt u PerfView en voert u de volgende opdracht uit:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

U kunt deze para meters naar behoefte wijzigen:
- **MaxCollectSec**. Stel deze para meter in om te voor komen dat PerfView voor onbepaalde tijd worden uitgevoerd en de prestaties van uw server worden beïnvloed.
- **OnlyProviders**. Stel deze para meter in op alleen logboeken van de SDK verzamelen. U kunt deze lijst aanpassen op basis van uw specifieke onderzoek. 
- **NoGui**. Stel deze para meter in op het verzamelen van Logboeken zonder de GUI.


Meer informatie
- [Prestatie traceringen vastleggen met PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights gebeurtenis bronnen](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Logboeken verzamelen met DotNet-Trace

Een alternatieve methode voor het verzamelen van Logboeken voor het oplossen van problemen die bijzonder handig kunnen zijn voor Linux-omgevingen is [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace)

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Application Insights verwijderen

Meer informatie over het verwijderen van Application Insights in Visual Studio door de stappen te volgen in het [artikel](./remove-application-insights.md)over het verwijderen.

## <a name="still-not-working"></a>Nog steeds niet werken...
* [Micro soft Q&een vraag pagina voor Application Insights](/answers/topics/azure-monitor.html)


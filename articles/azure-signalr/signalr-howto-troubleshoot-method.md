---
title: Probleemoplossings procedure voor de Azure signalerings service
description: Meer informatie over het oplossen van problemen met connectiviteit en het afleveren van berichten
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707653"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Problemen met connectiviteit en aflevering van berichten oplossen

In deze richt lijnen worden verschillende manieren beschreven om zelf te diagnosticeren om de hoofd oorzaak direct te vinden of het probleem op te lossen. Het resultaat zelf diagnose is ook nuttig wanneer het aan ons meldt voor verdere onderzoek.

Eerst moet u controleren vanaf de Azure Portal welke [ServiceMode](./concept-service-mode.md) de Azure signalerings service (ook wel bekend als **ASRS**) is geconfigureerd.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* `Default`Raadpleeg voor modus de [standaard modus problemen oplossen](#default_mode_tsg)

* `Serverless`Raadpleeg voor modus problemen met de [server modus oplossen](#serverless_mode_tsg)

* `Classic`Raadpleeg voor modus de [klassieke modus problemen oplossen](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Problemen met de standaard modus oplossen

Wanneer **ASRS** in de *standaard* modus is, zijn er **drie** rollen: *client*, *Server* en *service*:

* *Client*: *client* staat voor de clients die zijn verbonden met **ASRS**. De permanente verbindingen die client en **ASRS** maken, worden in deze richt lijnen *client verbindingen* genoemd.

* *Server*: *Server* staat voor de server die client onderhandeling verzendt en host de logica van de signalen `Hub` . En de permanente verbindingen tussen de *Server* en **ASRS** worden in deze richt lijnen *Server verbindingen* genoemd.

* *Service*: de korte naam voor **ASRS** *vindt u* in deze richt lijnen.

Raadpleeg de [interne Azure signalerings service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) voor een gedetailleerde inleiding van de hele architectuur en werk stroom.

Er zijn verschillende manieren om het probleem te verhelpen. 

* Als het probleem direct op de juiste manier optreedt of reproduceren is, is het mogelijk om het doorgestuurde verkeer weer te geven. 

* Als het probleem moeilijk te reproduceren is, kunnen traceringen en logboeken u helpen.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Het verkeer weer geven en het probleem beperken

Het vastleggen van het doorlopende verkeer is de meest rechtse manier om het probleem te verfijnen. U kunt de [netwerk traceringen](/aspnet/core/signalr/diagnostics#network-traces) vastleggen met behulp van de opties die hieronder worden beschreven:

* [Een netwerk tracering met Fiddler verzamelen](/aspnet/core/signalr/diagnostics#network-traces)

* [Een netwerk tracering met tcpdump verzamelen](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Een netwerk tracering in de browser verzamelen](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Client aanvragen

Voor een permanente verbinding met een signaal sterkte wordt het eerst door gegeven `/negotiate` aan uw gehoste app-server en vervolgens omgeleid naar de Azure signalerings service. vervolgens wordt de werkelijke permanente verbinding met de Azure signalerings service tot stand gebracht. Raadpleeg de [interne Azure signalerings service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) voor de gedetailleerde stappen.

Controleer aan de hand van de netwerk tracering aan de client zijde of de aanvraag is mislukt met de status code en welk antwoord en zoek naar oplossingen in de [gids voor probleem oplossing](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Server aanvragen

De signaal *Server* onderhoudt de *Server verbinding* tussen de *Server* en de *service*. Wanneer de app-server wordt gestart, wordt de **WebSocket** -verbinding met de Azure signalerings service gestart. Alle client verkeer wordt doorgestuurd via de Azure signalerings service naar deze *Server verbinding* s en vervolgens verzonden naar de `Hub` . Wanneer een *Server verbinding* wordt verbroken, worden de clients die zijn doorgestuurd naar deze *Server verbinding* , beïnvloed. Onze Azure Signalr SDK heeft de logica altijd opnieuw proberen om de *Server verbinding* opnieuw te verbinden met Maxi maal één minuut vertraging om de impact te minimaliseren.

*Server verbinding* s kan worden verwijderd vanwege netwerk instabiliteit of regel matig onderhoud van de Azure signalerings service of de updates/onderhoud van uw gehoste app-server. Zolang de client-side het mechanisme voor ontkoppelen/opnieuw verbinden heeft, is de impact mini maal, net zoals elke client de verbinding verbreken heeft veroorzaakt.

Bekijk de netwerk tracering aan de server zijde om de status code en fout details te achterhalen waarom de *Server verbinding* wordt verbroken of geweigerd door de *service* en zoek naar de hoofd oorzaak in de [probleemoplossings handleiding](./signalr-howto-troubleshoot-guide.md).

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Logboeken toevoegen

Logboeken kunnen nuttig zijn bij het vaststellen van problemen en het bewaken van de uitvoerings status.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Logboek aan client zijde inschakelen

Logboek registratie aan de client zijde is precies hetzelfde als wanneer u een zelf-hostende Signa lering gebruikt.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Logboek registratie aan client zijde inschakelen voor `ASP.NET Core SignalR`

* [Java script-client logboek registratie](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET-client logboek registratie](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Logboek registratie aan client zijde inschakelen voor `ASP.NET SignalR`

* [.NET-client](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Tracering inschakelen in Windows Phone 8 clients](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Tracering inschakelen in de Java script-client](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Logboek aan de server zijde inschakelen

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Logboek registratie aan de server zijde inschakelen voor `ASP.NET Core SignalR`

Logboek registratie aan de server zijde voor `ASP.NET Core SignalR` integreert met de `ILogger` [logboek registratie](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) op basis van het `ASP.NET Core` Framework. U kunt logboek registratie aan de server zijde inschakelen door gebruik te maken van `ConfigureLogging` een voorbeeld gebruik:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Logger-categorieën voor Azure signalering worden altijd gestart met `Microsoft.Azure.SignalR` . Als u gedetailleerde logboeken van Azure-Signa lering wilt inschakelen, configureert u de voor gaande voor voegsels op `Information` niveau in uw **appsettings.jsin** het volgende bestand:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Controleer of er abnormale waarschuwings-en fout logboeken zijn geregistreerd. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Traceringen aan server zijde inschakelen voor `ASP.NET SignalR`

Wanneer u SDK-versie >= gebruikt `1.0.0` , kunt u traceringen inschakelen door het volgende toe te voegen aan `web.config` : ([Details](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

Controleer of er abnormale waarschuwings-en fout logboeken zijn geregistreerd. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Logboeken inschakelen in de Azure signalerings service

U kunt ook [Diagnostische logboeken inschakelen](./signalr-howto-diagnostic-logs.md) voor de Azure signalerings service. deze logboeken bevatten gedetailleerde informatie over elke verbinding die is verbonden met de Azure signalerings service.

<a name="serverless_mode_tsg"></a>

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Problemen met serverloze modus oplossen

Wanneer **ASRS** in de *serverloze* modus is, ondersteunt alleen **ASP.net core signalerings** `Serverless` modus en wordt de **ASP.net-Signa lering** deze modus **niet** ondersteund.

Om problemen met de connectiviteit in `Serverless` de modus te diagnosticeren, is de meest directe voorwaartse manier om verkeer aan de [client zijde weer te geven](#view_traffic_client). Schakel [Logboeken aan de client zijde](#add_logs_client) en [Logboeken aan de service zijde](#add_logs_server) kunnen ook handig zijn.

<a name="classic_mode_tsg"></a>

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Problemen met de klassieke modus oplossen

`Classic` modus is verouderd en wordt niet aanbevolen om te gebruiken. In deze modus gebruikt de Azure signalerings service de verbonden *Server verbindingen* om te bepalen of de huidige service zich in de `default` modus of modus bevindt `serverless` . Dit kan leiden tot een aantal tussenliggende client verbindings problemen omdat, wanneer er een plotselinge overschakeling van de verbinding van de verbonden *Server* is, bijvoorbeeld door een netwerk instabiliteit, de Azure-Signa lering vermeent naar `serverless` de modus en clients die zijn verbonden tijdens deze periode, nooit naar de gehoste app-server worden doorgestuurd. Schakel [Logboeken aan de service zijde](#add_logs_server) in en controleer of er clients zijn geregistreerd alsof `ServerlessModeEntered` u een host hebt van de app server, maar sommige clients nooit de kant van de app-server hebben bereikt. Als dat het geval is, kunt u [deze client verbindingen afbreken](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) en de clients opnieuw opstarten kunnen helpen.

Problemen met de `classic` modus voor probleem oplossing en het afleveren van berichten zijn vergelijkbaar met het [oplossen van problemen met de standaard modus](#default_mode_tsg).

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Status van service

U kunt de status-API voor de service status controleren.

* Aanvraag: ophalen `https://{instance_name}.service.signalr.net/api/v1/health`

* Status code van antwoord:
  * 200: in orde.
  * 503: uw service is niet in orde. U kunt:
    * Wacht enkele minuten op auto herstel.
    * Controleer of het IP-adres overeenkomt met de IP-adressen uit de portal.
    * Of start het exemplaar opnieuw.
    * Als alle bovenstaande opties niet werken, neemt u contact met ons op door een nieuwe ondersteunings aanvraag toe te voegen in Azure Portal.

Meer informatie over [herstel na nood gevallen](./signalr-concept-disaster-recovery.md).

[Ondervindt u problemen of feedback over het oplossen van problemen? Laat het ons weten.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd over het oplossen van problemen met connectiviteit en het afleveren van berichten. U kunt ook leren hoe u de veelvoorkomende problemen kunt afhandelen. 

> [!div class="nextstepaction"]
> [Handleiding voor het oplossen van problemen](./signalr-howto-troubleshoot-guide.md)
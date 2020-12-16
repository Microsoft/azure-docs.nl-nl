---
title: Gids voor probleemoplossing voor Azure SignalR Service
description: Meer informatie over het oplossen van veelvoorkomende problemen
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 55ad9c90129a5d732f377ac1b6c905c14de319dc
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607420"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Probleemoplossings gids voor veelvoorkomende problemen met de Azure signalerings service

In deze richt lijnen vindt u nuttige richt lijnen voor probleem oplossing op basis van de veelvoorkomende problemen die klanten in de afgelopen jaren hebben behaald en opgelost.

## <a name="access-token-too-long"></a>Toegangs token te lang

### <a name="possible-errors"></a>Mogelijke fouten:

* Aan de client zijde `ERR_CONNECTION_`
* 414-URI te lang
* 413 Payload is te groot
* Het toegangs token mag niet langer zijn dan 4 KB. de 413-aanvraag entiteit is te groot

### <a name="root-cause"></a>Hoofd oorzaak:

Voor HTTP/2 is de maximale lengte voor één header **4 K**, dus als u een browser gebruikt om toegang te krijgen tot de Azure-service, wordt er een fout `ERR_CONNECTION_` voor deze beperking weer geven.

Voor HTTP/1.1-of C#-clients is de maximale URI-lengte **12 k**, de maximale header lengte is **16 k**.

Met SDK-versie **1.0.6** of hoger `/negotiate` wordt `413 Payload Too Large` de gegenereerde toegangs token groter dan **4 K**.

### <a name="solution"></a>Oplossing:

Standaard worden claims van `context.User.Claims` gebruikt bij het genereren van **een** JWT-toegangs token naar **ASRS**(een zure **S** ignal **R** **s** erviceniveaudoelstelling), zodat de claims behouden blijven en kunnen worden door gegeven vanuit **ASRS** aan de `Hub` wanneer de client verbinding maakt met de `Hub` .

In sommige gevallen wordt `context.User.Claims` gebruikgemaakt van het opslaan van veel informatie voor de app-server, waarvan de meeste worden gebruikt door `Hub` s, maar door andere onderdelen.

Het gegenereerde toegangs token wordt door gegeven via het netwerk en voor WebSocket/SSE-verbindingen worden toegangs tokens door gegeven via query reeksen. Als best practice, raden we u aan om de **benodigde** claims van de client via **ASRS** aan uw app-server door te geven wanneer de hub nodig heeft.

Er is een `ClaimsProvider` voor u om de claims aan te passen die worden door gegeven aan **ASRS** binnen het toegangs token.

Voor ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Voor ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>TLS 1,2 vereist

### <a name="possible-errors"></a>Mogelijke fouten:

* ASP.NET "geen server beschikbaar"-fout [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "de verbinding is niet actief, gegevens kunnen niet naar de service worden verzonden." fout [#324](https://github.com/Azure/azure-signalr/issues/324)
* ' Er is een fout opgetreden tijdens het maken van de HTTP-aanvraag voor https:// <API endpoint> . Deze fout kan worden veroorzaakt door het feit dat het server certificaat niet correct is geconfigureerd met HTTP.SYS in het HTTPS-geval. Deze fout kan ook worden veroorzaakt doordat de beveiligings binding tussen de client en de server niet overeenkomt.

### <a name="root-cause"></a>Hoofd oorzaak:

Azure service ondersteunt alleen TLS 1.2 voor beveiligings problemen. Met .NET Framework is het mogelijk dat TLS 1.2 niet het standaard protocol is. Als gevolg hiervan kunnen de server verbindingen met ASRS niet tot stand worden gebracht.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

1. Als deze fout lokaal kan worden gereproduceerd, schakelt u *Just my code* uit en genereert u alle CLR-uitzonde ringen en kunt u de app-server lokaal debuggen om te zien welke uitzonde ring wordt gegenereerd.
    * *Just my code* uitschakelen

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Just My Code uitschakelen":::

    * CLR-uitzonde ringen genereren

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR-uitzonde ringen genereren":::

    * Zie uitzonde ringen bij het opsporen van fouten in de code van de app server:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Uitzonde ring wordt gegenereerd":::

2. Voor ASP.NET kunt u ook de volgende code toevoegen aan uw `Startup.cs` om gedetailleerde tracering in te scha kelen en de fouten in het logboek te bekijken.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Oplossing:

Voeg de volgende code toe aan het opstarten:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 ongeldige aanvraag geretourneerd voor client aanvragen

### <a name="root-cause"></a>Hoofdoorzaak

Controleer of uw client aanvraag meerdere `hub` query reeksen heeft. `hub` is een verduurzaamde query parameter en 400 wordt gegenereerd als de service meer dan een `hub` in de query detecteert.

## <a name="401-unauthorized-returned-for-client-requests"></a>401 - Niet gemachtigd geretourneerd voor clientaanvragen

### <a name="root-cause"></a>Hoofdoorzaak

Momenteel is de standaard waarde van de levens duur van het JWT-token 1 uur.

Voor ASP.NET Core-Signa lering, wanneer deze gebruikmaakt van het transport type WebSocket, is het OK.

ASP.NET Core voor het andere transport type van de signaal sterkte, SSE en lange polling, betekent dit dat de verbinding standaard Maxi maal één uur kan worden gehandhaafd.

Voor ASP.NET Signaler verzendt de client `/ping` van tijd tot tijd een keepalive-aanvraag naar de service, wanneer het `/ping` mislukt, de client de verbinding **afbreekt** en nooit opnieuw verbinding maakt. Dit betekent dat de ASP.NET-Signa lering, de standaard levens duur van het token, de verbinding gedurende het grootste uur voor het Trans Port-Type voor het **meest** is.

### <a name="solution"></a>Oplossing

Uit veiligheids overwegingen wordt het uitbreiden van TTL niet aanbevolen. Het toevoegen van de logica voor het opnieuw verbinden van de client wordt voorgesteld om de verbinding opnieuw te starten wanneer dit 401 plaatsvindt. Wanneer de client de verbinding opnieuw start, onderhandelt deze met app server om de JWT-token opnieuw op te halen en wordt een vernieuwings token opgehaald.

[Hier](#restart_connection) kunt u controleren hoe client verbindingen opnieuw moeten worden gestart.

## <a name="404-returned-for-client-requests"></a>404 geretourneerd voor clientaanvragen

Voor een permanente verbinding met een signaal sterkte maakt het eerst `/negotiate` gebruik van de Azure signalerings service en wordt vervolgens de echte verbinding met de Azure signalerings service tot stand gebracht.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

* U kunt als volgt [uitgaande aanvragen weer geven](#view_request) om de aanvraag van de client bij de service op te halen.
* Controleer de URL van de aanvraag wanneer 404 optreedt. Als de URL is gericht op uw web-app en vergelijkbaar met `{your_web_app}/hubs/{hubName}` , controleert u of de client `SkipNegotiation` is `true` . Wanneer u Azure Signalr gebruikt, ontvangt de client omleidings-URL wanneer deze de app server voor het eerst onderhandelt. De client mag **geen** onderhandeling overs Laan bij gebruik van Azure-Signa lering.
* Een andere 404 kan zich voordoen wanneer de verbindings aanvraag meer dan **5** seconden na `/negotiate` wordt aangeroepen. Controleer de tijds tempel van de client aanvraag en open een probleem met ons als de aanvraag voor de service een trage reactie heeft.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 geretourneerd voor aanvraag voor reconnect van ASP.NET-signaal sterkte

Wanneer de [client verbinding](#client_connection_drop)wordt verbroken, maakt de ASP.net-Signa lering een verbinding met hetzelfde `connectionId` aantal drie keer voor het stoppen van de verbinding. `/reconnect` kan helpen als de verbinding wordt verbroken door problemen met het netwerk, waardoor `/reconnect` de permanente verbinding kan worden hersteld. Onder andere omstandigheden wordt de client verbinding verbroken als gevolg van de gerouteerde server verbinding, of de signaal service heeft een aantal interne fouten, zoals het opnieuw opstarten/failover/implementatie, bestaat niet meer, waardoor de verbinding `/reconnect` wordt geretourneerd `404` . Het is het verwachte gedrag voor `/reconnect` en na drie keer opnieuw proberen van de verbinding wordt gestopt. De logica voor het [opnieuw starten](#restart_connection) van de verbinding wordt voorgesteld wanneer de verbinding wordt gestopt.

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (te veel aanvragen) geretourneerd voor client aanvragen

Er zijn twee mogelijke situaties.

### <a name="concurrent-connection-count-exceeds-limit"></a>Aantal **gelijktijdige** verbindingen overschrijdt de limiet.

Voor **gratis** instanties is de limiet voor het aantal **gelijktijdige** verbindingen voor **standaard** instanties 20. de limiet voor het aantal **gelijktijdige** verbindingen **per eenheid** is 1 K, wat betekent dat Unit100 100-K gelijktijdige verbindingen toestaat.

De verbindingen zijn zowel client-als server verbindingen. [hier](./signalr-concept-messages-and-connections.md#how-connections-are-counted) kunt u controleren hoe de verbindingen worden geteld.

### <a name="too-many-negotiate-requests-at-the-same-time"></a>Er zijn te veel onderhandelen over aanvragen op hetzelfde moment.

Er wordt een wille keurige vertraging weer gegeven voordat u opnieuw verbinding kunt maken. Controleer [hier](#restart_connection) de voor beelden voor nieuwe pogingen.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500-fout tijdens de onderhandeling: de Azure signalerings service is nog niet verbonden. Probeer het later opnieuw.

### <a name="root-cause"></a>Hoofdoorzaak

Deze fout wordt gerapporteerd wanneer er geen server verbinding is met de Azure signalerings service.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Schakel tracering aan de server zijde in om de fout gegevens te achterhalen wanneer de server verbinding probeert te maken met de Azure signalerings service.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Logboek registratie aan de server zijde inschakelen voor ASP.NET Core Signaler

Logboek registratie aan de server zijde voor ASP.NET Core Signalr integreert met de `ILogger` [logboek registratie](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) op basis van het ASP.net core Framework. U kunt logboek registratie aan de server zijde inschakelen door gebruik te maken van `ConfigureLogging` een voorbeeld gebruik:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Logger-categorieën voor Azure signalering worden altijd gestart met `Microsoft.Azure.SignalR` . Als u gedetailleerde logboeken van Azure-Signa lering wilt inschakelen, configureert u de voor gaande voor voegsels op `Debug` niveau in uw **appsettings.jsin** het volgende bestand:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Traceringen aan server zijde inschakelen voor ASP.NET-Signa lering

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

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Client verbinding verbroken

Wanneer de client is verbonden met de Azure-Signa lering, kan de permanente verbinding tussen de client en de Azure-Signa lering soms om verschillende redenen worden verbroken. In deze sectie worden verschillende mogelijkheden beschreven die een dergelijke verbinding veroorzaken, en vindt u richt lijnen voor het identificeren van de hoofd oorzaak.

### <a name="possible-errors-seen-from-the-client-side"></a>Mogelijke fouten die worden weer gegeven aan de client zijde

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Hoofd oorzaak:

Client verbindingen kunnen onder verschillende omstandigheden worden verwijderd:
* Bij `Hub` het genereren van uitzonde ringen met de inkomende aanvraag.
* Zie hieronder voor meer informatie over de [Server verbinding,](#server_connection_drop)wanneer de server verbinding, waarnaar de-client wordt doorgestuurd, wordt neergezet.
* Wanneer er een probleem met de netwerk verbinding tussen de client en de signaal service optreedt.
* Wanneer de signaal service een aantal interne fouten heeft, zoals het opnieuw opstarten van een exemplaar, failover, implementatie, enzovoort.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

1. Logboek van de app-server openen om te zien of er iets abnormaal is gebeurd
2. Controleer het gebeurtenis logboek van de app-server om te controleren of de app-server opnieuw is opgestart
3. Maak een probleem aan ons dat het tijds bestek levert en e-mail adres van de resource naar ons verzendt.


## <a name="client-connection-increases-constantly"></a>Client verbinding wordt voortdurend verhoogd

Dit wordt mogelijk veroorzaakt door een onjuist gebruik van de client verbinding. Als iemand vergeet om de signaal-client te stoppen en te verwijderen, blijft de verbinding open.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Mogelijke fouten die worden weer gegeven uit de metrische gegevens van de signaal sterkte in de sectie bewaking van Azure Portal resource menu

Client verbindingen lopen voortdurend lange tijd in de metrische gegevens van Azure signalering.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Client verbinding wordt voortdurend verhoogd":::

### <a name="root-cause"></a>Hoofd oorzaak:

De verbinding van de seingevings client `DisposeAsync` wordt nooit aangeroepen. de verbinding blijft geopend.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

1. Controleer of de seingevings client **nooit** is gesloten.

### <a name="solution"></a>Oplossing

Controleer of de verbinding wordt gesloten. Roep hand matig `HubConnection.DisposeAsync()` aan om de verbinding te stoppen nadat u deze hebt gebruikt.

Bijvoorbeeld:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Algemeen onjuist gebruik van client verbinding

#### <a name="azure-function-example"></a>Azure function-voor beeld 

Dit probleem treedt vaak op wanneer iemand een signaal/client verbinding maakt in de Azure function-methode in plaats van het een statisch lid te maken van uw functie klasse. U kunt verwachten dat er slechts één client verbinding tot stand is gebracht, maar u ziet dat het aantal client verbindingen voortdurend toeneemt in de sectie bewaking van Azure Portal resource menu. alle deze verbindingen worden pas weer gegeven nadat de Azure function-service of de Azure-signaal functie opnieuw is opgestart. Dit komt doordat de Azure-functie voor **elke** aanvraag **één** client verbinding maakt, als u client verbinding niet stopt in functie methode, de client de verbindingen met de Azure signalerings service houdt.

#### <a name="solution"></a>Oplossing

* Vergeet niet om de client verbinding te sluiten als u de seingevings clients in azure function gebruikt of als u de seingevings client als Singleton gebruikt.
* In plaats van seingevings clients in azure Function te gebruiken, kunt u op elke wille keurige manier signalerings clients maken en [Azure functions bindingen voor de Azure signalerings service](https://github.com/Azure/azure-functions-signalrservice-extension) gebruiken om te [onderhandelen over](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) de client naar Azure signalering. Daarnaast kunt u de binding gebruiken om berichten te [verzenden](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). [Hier](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)vindt u voor beelden om te onderhandelen over client-en verzend berichten. Meer informatie vindt u [hier](https://github.com/Azure/azure-functions-signalrservice-extension).
* Wanneer u seingevings clients in azure function gebruikt, is er mogelijk een betere architectuur voor uw scenario. Controleer of u een juiste serverloze architectuur ontwerpt. U kunt verwijzen naar [realtime serverloze toepassingen met de signalerings service bindingen in azure functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Server verbinding verbroken

Wanneer de app-server wordt gestart, wordt op de achtergrond de Azure SDK gestart met het initiëren van server verbindingen met de externe Azure-Signa lering. Zoals beschreven in de [interne werking van de Azure signalerings service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md), stuurt Azure signalering binnenkomende client verkeer naar deze server verbindingen. Zodra een server verbinding is verbroken, worden alle client verbindingen die worden gebruikt, ook gesloten.

Als de verbindingen tussen de app-server en de signaal service permanente verbindingen zijn, kunnen er problemen met de netwerk verbinding optreden. In de server-SDK is de strategie **altijd opnieuw verbonden** met server verbindingen. Als best practice, moedigen we gebruikers aan om continu reconnect logica toe te voegen aan de clients met een wille keurige vertragings tijd om enorme gelijktijdige aanvragen naar de server te voor komen.

Op regel matige basis zijn er nieuwe versie releases voor de Azure signalerings service, en soms de patches voor het besturings systeem op het hele Azure-besturingssysteem, of upgrades, of soms onderbrekingen van onze afhankelijke services. Deze kunnen een korte periode van onderbreking van de service in beslag nemen, maar zolang de client-zijde het mechanisme voor ontkoppelen/opnieuw verbinden heeft, is de impact mini maal, omdat client zijde de verbinding verbreken verbreekt.

In deze sectie worden verschillende mogelijkheden voor Server verbinding verwijderen beschreven en vindt u enkele richt lijnen voor het identificeren van de hoofd oorzaak.

### <a name="possible-errors-seen-from-server-side"></a>Mogelijke fouten die worden weer gegeven aan de server zijde:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Hoofd oorzaak:

Verbinding met Server-service is gesloten door **ASRS**(**een** zure **S** ignal **R** **s** erviceniveaudoelstelling).

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

1. Logboek van de app-server openen om te zien of er iets abnormaal is gebeurd
2. Controleer het gebeurtenis logboek van de app-server om te controleren of de app-server opnieuw is opgestart
3. Maak een probleem aan ons dat het tijds bestek levert en e-mail adres van de resource naar ons verzendt.

## <a name="tips"></a>Tips

<a name="view_request"></a>

* Hoe wordt de uitgaande aanvraag van de client weer gegeven?
Neem ASP.NET Core een voor beeld (ASP.NET één is vergelijkbaar):
    * Van browser:

        Wees als voor beeld: u kunt **F12** gebruiken om het console venster te openen en over te scha kelen naar het tabblad **netwerk** . Mogelijk moet u de pagina vernieuwen met **F5** om het netwerk vanaf het begin vast te leggen.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome Network weer geven":::

    * Van C#-client:

        U kunt lokale webverkeer weer geven met behulp van [Fiddler](https://www.telerik.com/fiddler). WebSocket-verkeer wordt ondersteund sinds Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler netwerk weer geven" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Hoe moet de client verbinding opnieuw worden opgestart?
    
    Hier volgen de [voorbeeld codes](https://github.com/Azure/azure-signalr/tree/dev/samples) die de verbindings logica opnieuw starten met een strategie voor een *nieuwe poging* :

    * [ASP.NET Core C#-client](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core java script-client](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET C#-client](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET java script-client](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u de veelvoorkomende problemen kunt afhandelen. U kunt ook meer algemene probleemoplossings methoden leren. 

> [!div class="nextstepaction"]
> [Problemen met connectiviteit en aflevering van berichten oplossen](./signalr-howto-troubleshoot-method.md)
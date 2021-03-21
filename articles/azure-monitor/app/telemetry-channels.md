---
title: Telemetrie-kanalen in Azure-toepassing Insights | Microsoft Docs
description: Telemetrie-kanalen aanpassen in Azure-toepassing Insights-Sdk's voor .NET en .NET core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937357"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetrie-kanalen in Application Insights

Telemetrie-kanalen vormen een integraal onderdeel van de [Azure-toepassing Insights-sdk's](./app-insights-overview.md). Ze beheren het bufferen en verzenden van telemetrie naar de Application Insights-service. De .NET-en .NET Core-versies van de Sdk's hebben twee ingebouwde telemetrie-kanalen: `InMemoryChannel` en `ServerTelemetryChannel` . In dit artikel wordt elk kanaal in detail beschreven, met inbegrip van het gedrag van het kanaal aan te passen.

## <a name="what-are-telemetry-channels"></a>Wat zijn telemetrie-kanalen?

Telemetrie-kanalen zijn verantwoordelijk voor het bufferen van telemetriegegevens en het verzenden ervan naar de Application Insights-service, waar ze worden opgeslagen voor query's en analyses. Een telemetrie-kanaal is een klasse die de [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) interface implementeert.

De `Send(ITelemetry item)` methode van een telemetrie-kanaal wordt aangeroepen na het aanroepen van alle telemetrie-initialisatie functies en telemetrie-processors. Daarom bereiken items die worden verwijderd door een telemetrie-processor het kanaal niet. `Send()` de items worden meestal niet direct naar de back-end verzonden. Normaal gesp roken worden ze in het geheugen gebufferd en worden ze in batches verzonden voor efficiënte verzen ding.

[Live Metrics stream](live-stream.md) heeft ook een aangepast kanaal dat de live streamen van telemetrie aanstuurt. Dit kanaal is onafhankelijk van het normale telemetrie-kanaal, en dit document is hier niet van toepassing.

## <a name="built-in-telemetry-channels"></a>Ingebouwde telemetrie-kanalen

De Application Insights .NET en .NET core Sdk's worden geleverd met twee ingebouwde kanalen:

* `InMemoryChannel`: Een Lightweight Channel waarmee items in het geheugen worden gebufferd tot ze worden verzonden. Items worden gebufferd in het geheugen en eenmaal per 30 seconden leeg gemaakt, of wanneer 500 items worden gebufferd. Dit kanaal biedt minimale betrouwbaarheids garanties omdat er niet opnieuw een poging wordt gedaan om een telemetrie te verzenden na een storing. Dit kanaal houdt ook geen items op schijf bij, zodat niet-verzonden items permanent verloren gaan bij het afsluiten van de toepassing (zonder problemen). Dit kanaal implementeert een `Flush()` methode die kan worden gebruikt om in-Memory-telemetriegegevens synchroon af te dwingen. Dit kanaal is goed geschikt voor toepassingen met korte uitvoering, waarbij synchroon leegmaken ideaal is.

    Dit kanaal maakt deel uit van het grotere micro soft. ApplicationInsights NuGet-pakket en is het standaard kanaal dat door de SDK wordt gebruikt wanneer niets anders is geconfigureerd.

* `ServerTelemetryChannel`: Een geavanceerdere kanaal met beleid voor opnieuw proberen en de mogelijkheid om gegevens op te slaan op een lokale schijf. Dit kanaal probeert nieuwe telemetrie te verzenden als er tijdelijke fouten optreden. Dit kanaal gebruikt ook lokale schijf opslag om items op schijf te bewaren tijdens netwerk storingen of hoge telemetriegegevens. Als gevolg van deze methoden voor opnieuw proberen en lokale schijf opslag wordt dit kanaal als betrouwbaarder beschouwd en wordt het aanbevolen voor alle productie scenario's. Dit kanaal is de standaard waarde voor [ASP.net](./asp-net.md) -en [ASP.net core](./asp-net-core.md) -toepassingen die zijn geconfigureerd volgens de officiële documentatie. Dit kanaal is geoptimaliseerd voor Server scenario's met langlopende processen. De [`Flush()`](#which-channel-should-i-use) methode die door dit kanaal wordt geïmplementeerd, is niet synchroon.

    Dit kanaal wordt verzonden als het pakket micro soft. ApplicationInsights. WindowsServer. TelemetryChannel NuGet en wordt automatisch aangeschaft wanneer u het micro soft. ApplicationInsights. Web of het micro soft. ApplicationInsights. AspNetCore NuGet-pakket gebruikt.

## <a name="configure-a-telemetry-channel"></a>Een telemetrie-kanaal configureren

U configureert een telemetrie-kanaal door het in te stellen op de actieve telemetrie-configuratie. Voor ASP.NET-toepassingen moet de configuratie het telemetrische kanaal exemplaar instellen op `TelemetryConfiguration.Active` of door te wijzigen `ApplicationInsights.config` . Voor ASP.NET Core toepassingen moet de configuratie het kanaal toevoegen aan de container voor injectie van afhankelijkheid.

In de volgende secties ziet u voor beelden van het configureren van de `StorageFolder` instelling voor het kanaal in verschillende toepassings typen. `StorageFolder` is slechts een van de Configureer bare instellingen. Zie [de sectie instellingen](#configurable-settings-in-channels) verderop in dit artikel voor een volledige lijst met configuratie-instellingen.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuratie met behulp van ApplicationInsights.config voor ASP.NET-toepassingen

In het volgende gedeelte van [ApplicationInsights.config](configuration-with-applicationinsights-config.md) ziet `ServerTelemetryChannel` u het kanaal dat is geconfigureerd met `StorageFolder` ingesteld op een aangepaste locatie:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuratie in code voor ASP.NET-toepassingen

Met de volgende code wordt een ServerTelemetryChannel-exemplaar ingesteld met `StorageFolder` ingesteld op een aangepaste locatie. Voeg deze code toe aan het begin van de toepassing, meestal in `Application_Start()` Global. aspx. cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuratie in code voor ASP.NET Core-toepassingen

Wijzig de `ConfigureServices` methode van de `Startup.cs` klasse zoals hier wordt weer gegeven:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Het configureren van het kanaal met behulp van `TelemetryConfiguration.Active` wordt niet aanbevolen voor ASP.net core toepassingen.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuratie in code voor .NET/.NET Core-Console toepassingen

Voor console-apps is de code hetzelfde voor .NET en .NET core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Operationele details van ServerTelemetryChannel

`ServerTelemetryChannel` slaat binnenkomende items op in een geheugen buffer. De items worden eenmaal per 30 seconden geserialiseerd, gecomprimeerd en opgeslagen in een `Transmission` exemplaar, of wanneer 500 items zijn gebufferd. Eén `Transmission` exemplaar bevat Maxi maal 500 items en vertegenwoordigt een batch van telemetrie die wordt verzonden via één https-aanroep naar de Application Insights-service.

Standaard kunnen Maxi maal 10 `Transmission` instanties parallel worden verzonden. Als telemetrie op snellere tarieven aankomt, of als het netwerk of de back-end van de Application Insights langzaam is, `Transmission` worden de exemplaren in het geheugen opgeslagen. De standaard capaciteit van deze in-Memory `Transmission` buffer is 5 MB. Wanneer de capaciteit in het geheugen is overschreden, `Transmission` worden de exemplaren op de lokale schijf opgeslagen tot een limiet van 50 MB. `Transmission` instanties worden op een lokale schijf opgeslagen, ook als er netwerk problemen zijn. Alleen items die zijn opgeslagen op een lokale schijf, kunnen de toepassing vastlopen. Ze worden verzonden wanneer de toepassing opnieuw wordt gestart.

## <a name="configurable-settings-in-channels"></a>Configureer bare instellingen in kanalen

Zie voor een volledige lijst met Configureer bare instellingen voor elk kanaal:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Hier vindt u de meest gebruikte instellingen voor `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: De maximale hoeveelheid geheugen (in bytes) die door het kanaal wordt gebruikt voor het bufferen van de verzen ding in het geheugen. Wanneer deze capaciteit is bereikt, worden nieuwe items rechtstreeks op de lokale schijf opgeslagen. De standaard waarde is 5 MB. Het instellen van een hogere waarde leidt tot minder schijf gebruik, maar houd er rekening mee dat items in het geheugen verloren gaan als de toepassing vastloopt.

1. `MaxTransmissionSenderCapacity`: Het maximum aantal `Transmission` exemplaren dat tegelijkertijd naar Application Insights wordt verzonden. De standaardwaarde is 10. Deze instelling kan worden geconfigureerd voor een hoger nummer, dat wordt aanbevolen wanneer een enorme hoeveelheid telemetrie wordt gegenereerd. Hoog volume gebeurt meestal tijdens het testen van de belasting of wanneer steek proeven zijn uitgeschakeld.

1. `StorageFolder`: De map die wordt gebruikt door het kanaal om items naar behoefte op schijf op te slaan. In Windows wordt% LOCALAPPDATA% of% TEMP% gebruikt als er geen ander pad expliciet is opgegeven. In andere omgevingen dan Windows, moet u een geldige locatie of telemetrie opgeven die niet op de lokale schijf wordt opgeslagen.

## <a name="which-channel-should-i-use"></a>Welk kanaal moet ik gebruiken?

`ServerTelemetryChannel` wordt aanbevolen voor de meeste productie scenario's waarbij langlopende toepassingen worden gebruikt. De `Flush()` methode die wordt geïmplementeerd door `ServerTelemetryChannel` is niet synchroon en garandeert ook niet dat alle in behandeling zijnde items uit het geheugen of de schijf worden verzonden. Als u dit kanaal gebruikt in scenario's waarin de toepassing wordt afgesloten, raden we u aan enige vertraging na het aanroepen in te voeren `Flush()` . De exacte hoeveelheid vertraging die u mogelijk nodig hebt, kan niet voorspelbaar zijn. Dit is afhankelijk van factoren zoals hoeveel items of `Transmission` instanties in het geheugen zijn, hoeveel er op schijf staan, hoeveel er worden verzonden naar de back-end en of het kanaal zich in het midden van de exponentiële back-ups bevindt.

Als u een synchrone leegmaak bewerking moet uitvoeren, raden we u aan om te gebruiken `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garandeert het Application Insights kanaal dat telemetrie wordt geleverd? Als dat niet het geval is, wat zijn dan de scenario's waarin telemetrie kan worden verwijderd?

Het korte antwoord is dat geen van de ingebouwde kanalen een transactie type garantie bieden voor telemetrie-levering aan de back-end. `ServerTelemetryChannel` is geavanceerder vergeleken met `InMemoryChannel` voor betrouw bare levering, maar het maakt ook alleen een poging om telemetrie te verzenden. Telemetrie kan nog steeds in verschillende situaties verloren gaan, met inbegrip van deze algemene scenario's:

1. Items in het geheugen gaan verloren wanneer de toepassing vastloopt.

1. Telemetrie gaat verloren tijdens een lange periode van netwerk problemen. Telemetrie wordt op de lokale schijf opgeslagen tijdens netwerk storingen of wanneer er problemen optreden met de Application Insights back-end. Items die ouder zijn dan 48 uur, worden echter verwijderd.

1. De standaard schijf locaties voor het opslaan van telemetrie in Windows zijn% LOCALAPPDATA% of% TEMP%. Deze locaties zijn doorgaans lokaal voor de computer. Als de toepassing fysiek van de ene locatie naar de andere migreert, gaan alle telemetrie die op de oorspronkelijke locatie zijn opgeslagen verloren.

1. In Web Apps in Windows is de standaard schijf-opslag locatie D:\local\LocalAppData. Deze locatie is niet persistent. Het wordt gewist uit het opnieuw opstarten van de app, schalen en andere dergelijke bewerkingen, waardoor er telemetrie die daar is opgeslagen, verloren gaat. U kunt de standaard waarde onderdrukken en opslag opgeven op een blijvende locatie, zoals D:\home. Dergelijke persistente locaties worden echter geleverd door externe opslag en kunnen daarom traag zijn.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Werkt ServerTelemetryChannel op andere systemen dan Windows?

Hoewel de naam van het pakket en de naam ruimte ' WindowsServer ' bevat, wordt dit kanaal ondersteund op andere systemen dan Windows, met de volgende uitzonde ring. Op systemen met uitzonde ring van Windows maakt het kanaal standaard geen lokale opslagmap. U moet een lokale opslagmap maken en het kanaal configureren om het te gebruiken. Nadat de lokale opslag is geconfigureerd, werkt het kanaal op dezelfde manier op alle systemen.

> [!NOTE]
> Met de release 2.15.0-beta3 en meer lokale opslag wordt nu automatisch gemaakt voor Linux, Mac en Windows. Voor niet-Windows-systemen wordt door de SDK automatisch een lokale opslagmap gemaakt op basis van de volgende logica:
> - `${TMPDIR}` -Als `${TMPDIR}` de omgevings variabele is ingesteld, wordt deze locatie gebruikt.
> - `/var/tmp` -Als de vorige locatie niet bestaat, probeert u het opnieuw `/var/tmp` .
> - `/tmp` -Als de vorige locaties niet bestaan, kunt u proberen `tmp` . 
> - Als geen van deze locaties bestaat, wordt er geen lokale opslag gemaakt en is hand matige configuratie nog steeds vereist. [Voor details over de volledige implementatie](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Maakt de SDK tijdelijke lokale opslag? Worden de gegevens versleuteld op opslag?

De SDK slaat telemetrie-items in lokale opslag op tijdens netwerk problemen of tijdens het beperken. Deze gegevens zijn niet lokaal versleuteld.

Voor Windows-systemen maakt de SDK automatisch een tijdelijke lokale map in de map% TEMP% of% LOCALAPPDATA% en beperkt de toegang tot beheerders en alleen de huidige gebruiker.

Voor systemen met uitzonde ring van Windows wordt er geen lokale opslag automatisch gemaakt door de SDK, waardoor er standaard geen gegevens lokaal worden opgeslagen.

> [!NOTE]
> Met de release 2.15.0-beta3 en meer lokale opslag wordt nu automatisch gemaakt voor Linux, Mac en Windows. 

 U kunt zelf een opslag directory maken en het kanaal configureren om het te gebruiken. In dit geval bent u verantwoordelijk voor het beveiligen van de map.
Lees meer over [gegevens bescherming en privacy](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-Source-SDK
Net als elke SDK voor Application Insights zijn kanalen open source. Lees en bijdragen aan de code of rapport problemen op [het officiële github opslag plaats](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Volgende stappen

* [Steekproeven](./sampling.md)
* [SDK-probleem oplossing](./asp-net-troubleshoot-no-data.md)


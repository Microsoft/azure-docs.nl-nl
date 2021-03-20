---
title: Logboek gebeurtenissen genereren op basis van een .NET-app
description: Meer informatie over het toevoegen van logboek registratie aan uw .NET Service Fabric-toepassing die wordt gehost op een Azure-cluster of een zelfstandig cluster.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a36425acf42a469c7f48b2e954bdacfdfcce1b10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011477"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Logboekregistratie toevoegen aan uw Service Fabric-toepassing

Uw toepassing moet voldoende informatie geven om forensically te kunnen opsporen wanneer er problemen optreden. Logboek registratie is een van de belangrijkste dingen die u aan uw Service Fabric-toepassing kunt toevoegen. Als er een fout optreedt, kan een goede logboek registratie u de mogelijkheid geven om fouten te onderzoeken. Door logboek patronen te analyseren, kunt u de prestaties of het ontwerp van uw toepassing verbeteren. In dit document ziet u een aantal verschillende opties voor logboek registratie.

## <a name="eventflow"></a>Event flow

Met de [Event flow-bibliotheek](https://github.com/Azure/diagnostics-eventflow) Suite kunnen toepassingen bepalen welke diagnostische gegevens moeten worden verzameld en waar ze moeten worden gegenereerd. Diagnostische gegevens kunnen van invloed zijn op prestatie meter items voor toepassings traceringen. Het wordt uitgevoerd in hetzelfde proces als de toepassing, waardoor de communicatie overhead is geminimaliseerd. Zie [Azure service Fabric Event Aggregation with Event flow](service-fabric-diagnostics-event-aggregation-eventflow.md)(Engelstalig) voor meer informatie over event flow en service Fabric.

### <a name="using-structured-eventsource-events"></a>Gestructureerde Event source-gebeurtenissen gebruiken

Als u bericht gebeurtenissen per use-case definieert, kunt u gegevens over de gebeurtenis verpakken in de context van de gebeurtenis. U kunt gemakkelijker zoeken en filteren op basis van de namen of waarden van de opgegeven eigenschappen van de gebeurtenis. Door het samen stellen van de instrumentatie-uitvoer is het eenvoudiger te lezen, maar is er meer tijd nodig om een gebeurtenis voor elke use-case te definiëren. 

Sommige gebeurtenis definities kunnen worden gedeeld in de hele toepassing. Zo zou de gebeurtenis start of stop van de methode opnieuw worden gebruikt voor veel services in een toepassing. Een computerspecifieke service, zoals een bestel systeem, kan een **CreateOrder** -gebeurtenis hebben die een eigen unieke gebeurtenis heeft. Deze aanpak kan veel gebeurtenissen genereren en er is mogelijk een coördinatie van id's tussen project teams vereist. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Source Event algemeen gebruiken

Omdat het definiëren van specifieke gebeurtenissen lastig kan zijn, definiëren veel mensen een aantal gebeurtenissen met een gemeen schappelijke set para meters die over het algemeen hun informatie als een teken reeks uitvoeren. Veel van het gestructureerde aspect gaat verloren en het is moeilijker om de resultaten te doorzoeken en te filteren. In deze benadering worden enkele gebeurtenissen gedefinieerd die meestal overeenkomen met de registratie niveaus. In het volgende code fragment wordt een debug-en fout bericht gedefinieerd:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Het gebruik van een Hybrid of Structured en generic instrumentatie kan ook goed werken. Gestructureerd instrumentatie wordt gebruikt voor het rapporteren van fouten en metrische gegevens. Algemene gebeurtenissen kunnen worden gebruikt voor gedetailleerde logboek registratie die door technici wordt verbruikt voor het oplossen van problemen.

## <a name="microsoftextensionslogging"></a>Micro soft. Extensions. Logging

Het ASP.NET Core logboek registratie ([micro soft. Extensions. logging NuGet-pakket](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) is een logboek registratie raamwerk dat een standaard logboek registratie-API biedt voor uw toepassing. Ondersteuning voor andere back-endservers voor logboek registratie kan worden aangesloten op ASP.NET Core logboek registratie. Dit geeft u een breed scala aan ondersteuning voor logboek registratie in uw toepassing, zonder dat u veel code hoeft te wijzigen.

1. Voeg het NuGet-pakket **micro soft. Extensions. logging** toe aan het project dat u wilt instrumenteren. Voeg ook alle provider pakketten toe. Zie [Logging in ASP.net core](/aspnet/core/fundamentals/logging)voor meer informatie.
2. Voeg een **using** -instructie toe voor **micro soft. Extensions. log** in uw service bestand.
3. Definieer een persoonlijke variabele binnen uw service klasse.

   ```csharp
   private ILogger _logger = null;
   ```

4. Voeg deze code toe aan de constructor van uw service klasse:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Begin met het instrumenteren van uw code in uw methoden. Hier volgen enkele voor beelden:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Andere logboek registratie providers gebruiken

Sommige providers van derden gebruiken de benadering die wordt beschreven in de voor gaande sectie, waaronder [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)en [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). U kunt deze koppelen aan ASP.NET Core logboek registratie of u kunt ze afzonderlijk gebruiken. Serilog heeft een functie die alle berichten verrijkt die vanuit een logboek worden verzonden. Deze functie kan nuttig zijn bij het uitvoeren van de service naam, het type en de partitie-informatie. Ga als volgt te werk om deze mogelijkheid te gebruiken in de ASP.NET Core-infra structuur:

1. Voeg de **Serilog**, **Serilog. Extensions. logging**, **Serilog. Sinks. transcrib** en **Serilog. sinks** toe aan het project. 
2. Maak een `LoggerConfiguration` en het logboek exemplaar.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Voeg een `Serilog.ILogger` argument aan de service-constructor toe en geef de zojuist gemaakte logger door.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. In de service-constructor maakt eigenschaps verrijkers voor **ServiceTypeName**, **ServiceName**, **PartitionId** en **InstanceId**.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumenteer de code op dezelfde wijze als wanneer u ASP.NET Core zonder Serilog gebruikt.

   >[!NOTE]
   >Het is raadzaam om  de statische `Log.Logger` met het vorige voor beeld niet te gebruiken. Service Fabric kunnen in één proces meerdere exemplaren van hetzelfde service type hosten. Als u de statische gebruikt `Log.Logger` , worden in de laatste schrijver van de eigenschaps verrijkingen waarden weer gegeven voor alle exemplaren die worden uitgevoerd. Dit is een reden waarom de variabele _logger een persoonlijk lidvariabele van de service klasse is. U moet ook de `_logger` beschik bare algemene code maken, die in meerdere services kan worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [toepassings bewaking vindt u in service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Meer informatie over logboek registratie met [Event flow](service-fabric-diagnostics-event-aggregation-eventflow.md) en [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

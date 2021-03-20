---
title: End-to-end-tracering en diagnostische gegevens Azure Service Bus | Microsoft Docs
description: Overzicht van Service Bus client diagnoses en end-to-end tracering (client via alle services die bij de verwerking betrokken zijn).
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539210"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Gedistribueerde tracering en correlatie via Service Bus berichten

Een van de veelvoorkomende problemen bij micro Services-ontwikkeling is de mogelijkheid om de werking van een client te traceren via alle services die bij de verwerking betrokken zijn. Het is handig voor fout opsporing, prestatie analyse, A/B testen en andere typische diagnose scenario's.
Een deel van dit probleem is het bijhouden van logische stukjes werk. Het omvat bericht verwerkings resultaten en latentie en externe afhankelijkheids aanroepen. Een ander deel is de correlatie van deze diagnostische gebeurtenissen buiten de proces grenzen.

Wanneer een producent een bericht via een wachtrij verzendt, gebeurt dit doorgaans in het bereik van een andere logische bewerking, geïnitieerd door een andere client of service. Dezelfde bewerking wordt voortgezet door de Consumer zodra de gebruiker een bericht ontvangt. Zowel producent als consument (en andere services die de bewerking verwerken) vermoeden dat er waarschijnlijk telemetrie-gebeurtenissen worden getraceerd om de bewerkings stroom en het resultaat te traceren. Om dergelijke gebeurtenissen en tracerings bewerkingen end-to-end te correleren, moet elke service die telemetrie rapporteert elke gebeurtenis met een tracerings context afschrijven.

Microsoft Azure Service Bus Messa ging heeft eigenschappen van Payload gedefinieerd die door producenten en consumenten moeten worden gebruikt om deze tracerings context door te geven.
Het protocol is gebaseerd op het [http-correlatie protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure. Messa ging. ServiceBus SDK (nieuwste)](#tab/net-standard-sdk-2)
| Eigenschapsnaam        | Beschrijving                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | De unieke id van een externe aanroep van de producent naar de wachtrij. Raadpleeg de [aanvraag-id in het HTTP-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) voor de motivering, overwegingen en indeling |

## <a name="service-bus-net-client-autotracing"></a>Autotracering van .NET-Client Service Bus
De `ServiceBusProcessor` klasse van de [Azure Messa ging-service bus client voor .net](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) biedt tracerings instrumentatie punten die kunnen worden aangesloten door traceer systemen of client code. Met de instrumentatie kunt u alle aanroepen van de Service Bus Messa ging-service van aan de client zijde bijhouden. Als de bericht verwerking wordt uitgevoerd met behulp [ `ProcessMessageAsync` van `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (het bericht afhandelingsprocedure-patroon), wordt de bericht verwerking ook instrumentatie gebruikt.

### <a name="tracking-with-azure-application-insights"></a>Bijhouden met Azure-toepassing Insights

[Micro soft Application Insights](https://azure.microsoft.com/services/application-insights/) biedt uitgebreide mogelijkheden voor het controleren van prestaties, waaronder Automagic-aanvraag en het bijhouden van afhankelijkheden.

Installeer Application Insights SDK, afhankelijk van het project type:
- [ASP.net](../azure-monitor/app/asp-net.md) -versie 2,5-Beta2 of hoger installeren
- [ASP.net core](../azure-monitor/app/asp-net-core.md) -install versie 2.2.0-beta2 of hoger.
Deze koppelingen bieden Details over het installeren van SDK, het maken van resources en het configureren van SDK (indien nodig). Raadpleeg het artikel [Azure-toepassing Insights voor console toepassingen](../azure-monitor/app/console.md) voor non-ASP.NET-toepassingen.

Als u gebruikmaakt [ `ProcessMessageAsync` van `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (bericht-handler-patroon) om berichten te verwerken, wordt de bericht verwerking ook instrumentatie gebruikt. Alle Service Bus-aanroepen die door uw service worden uitgevoerd, worden automatisch getraceerd en gecorreleerd met andere telemetrie-items. Raadpleeg anders het volgende voor beeld voor het bijhouden van hand matige bericht verwerking.

#### <a name="trace-message-processing"></a>Verwerking van tracerings berichten

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

In dit voor beeld wordt de aanvraag telemetrie voor elk verwerkte bericht gerapporteerd, met een tijds tempel, duur en resultaat (geslaagd). De telemetrie heeft ook een set correlatie-eigenschappen. Geneste traceringen en uitzonde ringen die worden gerapporteerd tijdens de bericht verwerking, worden ook gestempeld met correlatie-eigenschappen die ze vertegenwoordigen als ' kinderen ' van de `RequestTelemetry` .

Als u tijdens de verwerking van berichten aanroepen naar ondersteunde externe onderdelen maakt, worden deze ook automatisch gevolgd en gecorreleerd. Raadpleeg [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) voor hand matig bijhouden en correlatie.

Als u naast de Application Insights SDK een externe code uitvoert, moet u de langere **duur** bekijken bij het weer geven van Application Insights-Logboeken. 

![Langere duur in Application Insights logboek](./media/service-bus-end-to-end-tracing/longer-duration.png)

Dit betekent niet dat er een vertraging is opgetreden bij het ontvangen van het bericht. In dit scenario is het bericht al ontvangen omdat het bericht is door gegeven als een para meter voor de SDK-code. En de label **name** in de app Insights-Logboeken (**proces**) geeft aan dat het bericht nu wordt verwerkt door de code voor externe gebeurtenis verwerking. Dit probleem is niet gerelateerd aan Azure. In plaats daarvan verwijzen deze metrische gegevens naar de efficiëntie van uw externe code, omdat het bericht al is ontvangen van Service Bus. 

### <a name="tracking-without-tracing-system"></a>Bijhouden zonder tracering systeem
Als uw tracerings systeem geen automatische Service Bus traceringen ondersteunt, is het mogelijk om dergelijke ondersteuning toe te voegen aan een tracerings systeem of in uw toepassing. In deze sectie worden de diagnostische gebeurtenissen beschreven die worden verzonden door Service Bus .NET-client.  

Service Bus .NET-client is instrumentatie met behulp van .NET-tracering primitieven [System. Diagnostics. activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) en [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungeert als tracerings context, maar `DiagnosticSource` is een meldings mechanisme. 

Als er geen listener is voor de DiagnosticSource-gebeurtenissen, is instrumentatie uitgeschakeld, waardoor de kosten voor de instrumentatie gelijk blijven. DiagnosticSource biedt alle controle over de listener:
- Listener bepaalt naar welke bronnen en gebeurtenissen moet worden geluisterd
- de controle van de gebeurtenis frequentie en de steek proef van de listener
- gebeurtenissen worden verzonden met een nettolading die volledige context biedt zodat u tijdens de gebeurtenis toegang krijgt tot het bericht object en deze kunt wijzigen

Raadpleeg de [Gebruikers handleiding voor DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) voordat u doorgaat met de implementatie.

We gaan een listener maken voor Service Bus gebeurtenissen in ASP.NET Core app die logboeken schrijft met micro soft. extension. logger.
Er wordt gebruikgemaakt van [System. Reactive. core](https://www.nuget.org/packages/System.Reactive.Core) -bibliotheek om u te abonneren op DiagnosticSource (u kunt zich ook eenvoudig abonneren op DiagnosticSource zonder IT)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In dit voor beeld worden de duur, het resultaat, de unieke id en de start tijd van de listener voor elke Service Bus bewerking geregistreerd.

### <a name="events"></a>Gebeurtenissen
Alle gebeurtenissen hebben de volgende eigenschappen die voldoen aan de specificatie van de open telemetrie: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` -wachtrij/onderwerp/pad naar abonnement
- `peer.address` – volledig gekwalificeerde naam ruimte
- `kind` – producent, consument of client. De producent wordt gebruikt bij het verzenden van berichten, de consument wanneer deze wordt ontvangen en de client bij het vereffenen.
- `component` – `servicebus`

Alle gebeurtenissen hebben ook de eigenschappen entity en endpoint, die in de onderstaande tabel worden wegge laten
  * `string Entity` --De naam van de entiteit (wachtrij, onderwerp, enzovoort.)
  * `Uri Endpoint` -Service Bus eind punt-URL

### <a name="instrumented-operations"></a>Instrumentele bewerkingen
Dit is de volledige lijst met bewerkingen met een instrument:

| Naam van bewerking | Bijgehouden API |
| -------------- | ----------- | 
| ServiceBusSender. Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender. Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender. Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver. receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver. Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver. Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver. complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. defer |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | De processor-call back is ingesteld op ServiceBusProcessor. Eigenschap ProcessMessageAsync |
| ServiceBusSessionProcessor.ProcessSessionMessage | De processor-call back is ingesteld op ServiceBusSessionProcessor. Eigenschap ProcessMessageAsync |

### <a name="filtering-and-sampling"></a>Filters en steek proeven

In sommige gevallen is het wenselijk om slechts een deel van de gebeurtenissen te registreren om de prestaties van de overhead of het opslag verbruik te verminderen. U kunt stop gebeurtenissen alleen registreren (zoals in het voor gaande voor beeld) of het voorbeeld percentage van de gebeurtenissen. 
`DiagnosticSource` bieden een manier om deze te krijgen met een `IsEnabled` predikaat. Zie [context-based filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)voor meer informatie.

`IsEnabled` kan meerdere keren worden aangeroepen voor één bewerking om de prestaties te verminderen.

`IsEnabled` wordt in de volgende volg orde aangeroepen:

1. `IsEnabled(<OperationName>, string entity, null)` bijvoorbeeld `IsEnabled("ServiceBusSender.Send", "MyQueue1")` . Houd er rekening mee dat er aan het einde geen ' Start ' of ' Stop ' is. Gebruik het om bepaalde bewerkingen of wacht rijen te filteren. Als de call back-methode retourneert `false` , worden de gebeurtenissen voor de bewerking niet verzonden.

   * Voor de bewerkingen ' process ' en ' ProcessSession ' ontvangt u ook `IsEnabled(<OperationName>, string entity, Activity activity)` terugbellen. Gebruik deze functie om gebeurtenissen te filteren op basis van `activity.Id` of label eigenschappen.
  
2. `IsEnabled(<OperationName>.Start)` bijvoorbeeld `IsEnabled("ServiceBusSender.Send.Start")` . Hiermee wordt gecontroleerd of de gebeurtenis start moet worden gestart. Het resultaat is alleen van invloed op de gebeurtenis start, maar verdere instrumentatie is niet afhankelijk van het item.

Er is geen `IsEnabled` gebeurtenis voor ' Stop '.

Als een bepaalde bewerkings resultaat uitzonde ring is, `IsEnabled("ServiceBusSender.Send.Exception")` wordt aangeroepen. U kunt zich alleen abonneren op uitzonderings gebeurtenissen en de rest van de instrumentatie verhinderen. In dit geval moet u dergelijke uitzonde ringen nog steeds afhandelen. Omdat andere instrumentatie is uitgeschakeld, mag u niet verwachten dat de tracerings context met de berichten van de gebruiker naar de producent loopt.

U kunt `IsEnabled` ook sampling strategieën implementeren. Steek proeven op basis van de `Activity.Id` of zorgen voor een `Activity.RootId` consistente steek proef op alle banden (zolang deze door tracering systeem of door uw eigen code worden door gegeven).

Als er meerdere `DiagnosticSource` listeners voor dezelfde bron aanwezig zijn, is het voldoende voor slechts één listener om de gebeurtenis te accepteren. Daarom is er geen garantie die `IsEnabled` wordt aangeroepen.



# <a name="microsoftazureservicebus-sdk"></a>[Micro soft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

| Eigenschapsnaam        | Beschrijving                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | De unieke id van een externe aanroep van de producent naar de wachtrij. Raadpleeg de [aanvraag-id in het HTTP-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) voor de motivering, overwegingen en indeling |
|  Correlation-Context | Bewerkings context, die wordt door gegeven voor alle services die bij de verwerking van de bewerking betrokken zijn. Zie [correlatie-context in http-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) voor meer informatie. |

## <a name="service-bus-net-client-autotracing"></a>Autotracering van .NET-Client Service Bus

Met ingang van versie 3.0.0 [Microsoft Azure ServiceBus-client voor .net](/dotnet/api/microsoft.azure.servicebus.queueclient) kunt u tracerings instrumentatie punten aanwijzen die kunnen worden aangesloten door traceer systemen of client code.
Met de instrumentatie kunt u alle aanroepen van de Service Bus Messa ging-service van aan de client zijde bijhouden. Als bericht verwerking wordt uitgevoerd met het [patroon van de bericht afhandelingsprocedure](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), wordt de bericht verwerking ook instrumentatie

### <a name="tracking-with-azure-application-insights"></a>Bijhouden met Azure-toepassing Insights

[Micro soft Application Insights](https://azure.microsoft.com/services/application-insights/) biedt uitgebreide mogelijkheden voor het controleren van prestaties, waaronder Automagic-aanvraag en het bijhouden van afhankelijkheden.

Installeer Application Insights SDK, afhankelijk van het project type:
- [ASP.net](../azure-monitor/app/asp-net.md) -versie 2,5-Beta2 of hoger installeren
- [ASP.net core](../azure-monitor/app/asp-net-core.md) -install versie 2.2.0-beta2 of hoger.
Deze koppelingen bieden Details over het installeren van SDK, het maken van resources en het configureren van SDK (indien nodig). Raadpleeg het artikel [Azure-toepassing Insights voor console toepassingen](../azure-monitor/app/console.md) voor non-ASP.NET-toepassingen.

Als u het [patroon voor bericht afhandeling](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) gebruikt om berichten te verwerken, bent u klaar: alle Service Bus-aanroepen die door uw service worden uitgevoerd, worden automatisch bijgehouden en gecorreleerd met andere telemetrie-items. Raadpleeg anders het volgende voor beeld voor het bijhouden van hand matige bericht verwerking.

#### <a name="trace-message-processing"></a>Verwerking van tracerings berichten

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

In dit voor beeld `RequestTelemetry` wordt voor elk verwerkte bericht gerapporteerd, met een tijds tempel, duur en resultaat (geslaagd). De telemetrie heeft ook een set correlatie-eigenschappen.
Geneste traceringen en uitzonde ringen die worden gerapporteerd tijdens de bericht verwerking, worden ook gestempeld met correlatie-eigenschappen die ze vertegenwoordigen als ' kinderen ' van de `RequestTelemetry` .

Als u tijdens de verwerking van berichten aanroepen naar ondersteunde externe onderdelen maakt, worden deze ook automatisch gevolgd en gecorreleerd. Raadpleeg [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) voor hand matig bijhouden en correlatie.

Als u naast de Application Insights SDK een externe code uitvoert, moet u de langere **duur** bekijken bij het weer geven van Application Insights-Logboeken. 

![Langere duur in Application Insights logboek](./media/service-bus-end-to-end-tracing/longer-duration.png)

Dit betekent niet dat er een vertraging is opgetreden bij het ontvangen van het bericht. In dit scenario is het bericht al ontvangen omdat het bericht is door gegeven als een para meter voor de SDK-code. En de label **name** in de app Insights-Logboeken (**proces**) geeft aan dat het bericht nu wordt verwerkt door de code voor externe gebeurtenis verwerking. Dit probleem is niet gerelateerd aan Azure. In plaats daarvan verwijzen deze metrische gegevens naar de efficiëntie van uw externe code, omdat het bericht al is ontvangen van Service Bus. Zie [dit bestand op github](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) om te zien waar de **proces** label wordt gegenereerd en toegewezen zodra het bericht is ontvangen van service bus. 

### <a name="tracking-without-tracing-system"></a>Bijhouden zonder tracering systeem
Als uw tracerings systeem geen automatische Service Bus traceringen ondersteunt, is het mogelijk om dergelijke ondersteuning toe te voegen aan een tracerings systeem of in uw toepassing. In deze sectie worden de diagnostische gebeurtenissen beschreven die worden verzonden door Service Bus .NET-client.  

Service Bus .NET-client is instrumentatie met behulp van .NET-tracering primitieven [System. Diagnostics. activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) en [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungeert als tracerings context, maar `DiagnosticSource` is een meldings mechanisme. 

Als er geen listener is voor de DiagnosticSource-gebeurtenissen, is instrumentatie uitgeschakeld, waardoor de kosten voor de instrumentatie gelijk blijven. DiagnosticSource biedt alle controle over de listener:
- Listener bepaalt naar welke bronnen en gebeurtenissen moet worden geluisterd
- de controle van de gebeurtenis frequentie en de steek proef van de listener
- gebeurtenissen worden verzonden met een nettolading die volledige context biedt zodat u tijdens de gebeurtenis toegang krijgt tot het bericht object en deze kunt wijzigen

Raadpleeg de [Gebruikers handleiding voor DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) voordat u doorgaat met de implementatie.

We gaan een listener maken voor Service Bus gebeurtenissen in ASP.NET Core app die logboeken schrijft met micro soft. extension. logger.
Er wordt gebruikgemaakt van [System. Reactive. core](https://www.nuget.org/packages/System.Reactive.Core) -bibliotheek om u te abonneren op DiagnosticSource (u kunt zich ook eenvoudig abonneren op DiagnosticSource zonder IT)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In dit voor beeld worden de duur, het resultaat, de unieke id en de start tijd van de listener voor elke Service Bus bewerking geregistreerd.

#### <a name="events"></a>Gebeurtenissen

Voor elke bewerking worden twee gebeurtenissen verzonden: Start en stop. Waarschijnlijk bent u alleen geïnteresseerd in ' Stop '-gebeurtenissen. Ze bieden het resultaat van de bewerking en de start tijd en duur als eigenschappen van de activiteit.

De nettolading van de gebeurtenis biedt een listener met de context van de bewerking, repliceert de API-binnenkomende para meters en retour waarden. De nettolading van de gebeurtenis stop heeft alle eigenschappen van de nettolading van de gebeurtenis, dus u kunt de gebeurtenis start volledig negeren.

Alle gebeurtenissen hebben ook de eigenschappen entity en endpoint, die in de onderstaande tabel worden wegge laten
  * `string Entity` --De naam van de entiteit (wachtrij, onderwerp, enzovoort)
  * `Uri Endpoint` -Service Bus eind punt-URL

Voor elke gebeurtenis ' Stop ' is een `Status` eigenschap met `TaskStatus` asynchrone bewerking voltooid, die ook in de volgende tabel wordt wegge laten voor eenvoud.

Dit is de volledige lijst met bewerkingen met een instrument:

| Naam van bewerking | Bijgehouden API | Eigenschappen van specifieke nettolading|
|----------------|-------------|---------|
| Micro soft. Azure. ServiceBus. Send | [MessageSender. SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lijst met berichten die worden verzonden |
| Micro soft. Azure. ServiceBus. ScheduleMessage | [MessageSender. ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Bericht wordt verwerkt<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Offset van gepland bericht<br/>`long SequenceNumber` -Volg nummer van het geplande bericht (' Stop ' gebeurtenis lading) |
| Micro soft. Azure. ServiceBus. Cancel | [MessageSender. CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Volg nummer van te annuleren bericht | 
| Micro soft. Azure. ServiceBus. receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` -Het maximum aantal berichten dat kan worden ontvangen.<br/>`IList<Message> Messages` -Lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Micro soft. Azure. ServiceBus. Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` : Het begin punt van waaruit een batch berichten moet worden gebladerd.<br/>`int RequestedMessageCount` -Het aantal berichten dat moet worden opgehaald.<br/>`IList<Message> Messages` -Lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Micro soft. Azure. ServiceBus. ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` -De lijst met de reeks nummers die moeten worden ontvangen.<br/>`IList<Message> Messages` -Lijst met ontvangen berichten (' Stop ' gebeurtenis lading) |
| Micro soft. Azure. ServiceBus. complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` -De lijst met de vergrendelings tokens van de bijbehorende berichten die moeten worden voltooid.|
| Micro soft. Azure. ServiceBus. Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` -Het vergrendelings token van het bijbehorende bericht dat moet worden afgebroken. |
| Micro soft. Azure. ServiceBus. defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` -Het vergrendelings token van het bijbehorende bericht dat moet worden uitgesteld. | 
| Micro soft. Azure. ServiceBus. DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` -Het vergrendelings token van het bijbehorende bericht naar een onbestelbare letter. | 
| Micro soft. Azure. ServiceBus. RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` -Het vergrendelings token van het bijbehorende bericht waarop de vergren deling moet worden vernieuwd.<br/>`DateTime LockedUntilUtc` -Nieuwe eind datum en-tijd van het vergrendelings token in UTC-indeling. (' Stop ' gebeurtenis lading)|
| Micro soft. Azure. ServiceBus. process | Lambda-functie Message Handler in [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Het bericht wordt verwerkt. |
| Micro soft. Azure. ServiceBus. ProcessSession | De Lambda-functie Message session handler in [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Het bericht wordt verwerkt.<br/>`IMessageSession Session` -Sessie wordt verwerkt |
| Micro soft. Azure. ServiceBus. AddRule | [SubscriptionClient. AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` -De regel beschrijving die de toe te voegen regel levert. |
| Micro soft. Azure. ServiceBus. RemoveRule | [SubscriptionClient. RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` -Naam van de regel die u wilt verwijderen. |
| Micro soft. Azure. ServiceBus. GetRules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` -Alle regels die zijn gekoppeld aan het abonnement. (Alleen Payload stoppen) |
| Micro soft. Azure. ServiceBus. AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` -De sessie-id aanwezig in de berichten. |
| Micro soft. Azure. ServiceBus. GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` -De sessie-id aanwezig in de berichten.<br/>`byte [] State` -Sessie status (' Stop ' gebeurtenis lading) |
| Micro soft. Azure. ServiceBus. SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` -De sessie-id aanwezig in de berichten.<br/>`byte [] State` -Sessie status |
| Micro soft. Azure. ServiceBus. RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` -De sessie-id aanwezig in de berichten. |
| Micro soft. Azure. ServiceBus. Exception | een instrumentele API| `Exception Exception` -Uitzonderings exemplaar |

In elke gebeurtenis kunt u toegang krijgen tot `Activity.Current` de huidige bewerkings context.

#### <a name="logging-more-properties"></a>Logboek registratie meer eigenschappen

`Activity.Current` geeft gedetailleerde context van de huidige bewerking en de bovenliggende bewerkingen. Zie [activiteiten documentatie](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)voor meer informatie.
Service Bus instrumentatie biedt meer informatie in het `Activity.Current.Tags` -blok `MessageId` en `SessionId` wanneer deze beschikbaar zijn.

Activiteiten die de gebeurtenis ' Receive ', ' Peek ' en ' ReceiveDeferred ' volgen, kunnen ook `RelatedTo` tag hebben. Het bevat een afzonderlijke lijst met `Diagnostic-Id` berichten die als resultaat zijn ontvangen.
Deze bewerking kan ertoe leiden dat er verschillende niet-gerelateerde berichten worden ontvangen. Het is ook `Diagnostic-Id` niet bekend wanneer de bewerking wordt gestart. Daarom kunnen receive-bewerkingen alleen worden gecorreleerd aan proces bewerkingen met deze tag. Het is handig bij het analyseren van prestatie problemen om te controleren hoe lang het heeft geduurd om het bericht te ontvangen.

Een efficiënte manier om tags te registreren, is om ze te herhalen. het toevoegen van labels aan het voor gaande voor beeld ziet eruit als 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filters en steek proeven

In sommige gevallen is het wenselijk om slechts een deel van de gebeurtenissen te registreren om de prestaties van de overhead of het opslag verbruik te verminderen. U kunt stop gebeurtenissen alleen registreren (zoals in het voor gaande voor beeld) of het voorbeeld percentage van de gebeurtenissen. 
`DiagnosticSource` bieden een manier om deze te krijgen met een `IsEnabled` predikaat. Zie [context-based filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)voor meer informatie.

`IsEnabled` kan meerdere keren worden aangeroepen voor één bewerking om de prestaties te verminderen.

`IsEnabled` wordt in de volgende volg orde aangeroepen:

1. `IsEnabled(<OperationName>, string entity, null)` bijvoorbeeld `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` . Houd er rekening mee dat er aan het einde geen ' Start ' of ' Stop ' is. Gebruik het om bepaalde bewerkingen of wacht rijen te filteren. Als de call back-methode retourneert `false` , worden gebeurtenissen voor de bewerking niet verzonden

   * Voor de bewerkingen ' process ' en ' ProcessSession ' ontvangt u ook `IsEnabled(<OperationName>, string entity, Activity activity)` terugbellen. Gebruik deze functie om gebeurtenissen te filteren op basis van `activity.Id` of label eigenschappen.
  
2. `IsEnabled(<OperationName>.Start)` bijvoorbeeld `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` . Hiermee wordt gecontroleerd of de gebeurtenis start moet worden gestart. Het resultaat is alleen van invloed op de gebeurtenis start, maar verdere instrumentatie is niet afhankelijk van het item.

Er is geen `IsEnabled` gebeurtenis voor ' Stop '.

Als een bepaalde bewerkings resultaat uitzonde ring is, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` wordt aangeroepen. U kunt zich alleen abonneren op uitzonderings gebeurtenissen en de rest van de instrumentatie verhinderen. In dit geval moet u dergelijke uitzonde ringen nog steeds afhandelen. Omdat andere instrumentatie is uitgeschakeld, mag u niet verwachten dat de tracerings context met de berichten van de gebruiker naar de producent loopt.

U kunt `IsEnabled` ook sampling strategieën implementeren. Steek proeven op basis van de `Activity.Id` of zorgen voor een `Activity.RootId` consistente steek proef op alle banden (zolang deze door tracering systeem of door uw eigen code worden door gegeven).

Als er meerdere `DiagnosticSource` listeners voor dezelfde bron aanwezig zijn, is het voldoende voor slechts één listener om de gebeurtenis te accepteren. Daarom is er geen garantie die `IsEnabled` wordt aangeroepen.

---

## <a name="next-steps"></a>Volgende stappen

* [Application Insights correlatie](../azure-monitor/app/correlation.md)
* [Application Insights controle afhankelijkheden](../azure-monitor/app/asp-net-dependencies.md) om te zien of rest, SQL of andere externe bronnen worden vertraagd.
* [Aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)


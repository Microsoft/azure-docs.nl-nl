---
title: Aangepaste bewerkingen bijhouden met Azure-toepassing Insights .NET SDK
description: Aangepaste bewerkingen bijhouden met Azure-toepassing Insights .NET SDK
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 42a5318325f9961483465357403089755feb130d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933304"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Aangepaste bewerkingen bijhouden met Application Insights .NET SDK

Azure-toepassing Insights-Sdk's traceren automatisch binnenkomende HTTP-aanvragen en aanroepen naar afhankelijke services, zoals HTTP-aanvragen en SQL-query's. Het bijhouden en correleren van aanvragen en afhankelijkheden geven u inzicht in de reactie snelheid en betrouw baarheid van de hele toepassing in alle micro services die deze toepassing combi neren. 

Er is een klasse toepassings patronen die niet algemeen kunnen worden ondersteund. Voor een goede controle van dergelijke patronen is hand matige code instrumentatie vereist. Dit artikel heeft betrekking op enkele patronen waarvoor hand matige instrumentatie vereist is, zoals aangepaste wachtrij verwerking en het uitvoeren van langlopende achtergrond taken.

Dit document bevat richt lijnen voor het bijhouden van aangepaste bewerkingen met de SDK van Application Insights. Deze documentatie is van belang voor:

- Application Insights voor .NET (ook bekend als basis-SDK) versie 2.4 +.
- Application Insights voor webtoepassingen (met ASP.NET) versie 2.4 +.
- Application Insights voor ASP.NET Core versie 2.1 +.

## <a name="overview"></a>Overzicht
Een bewerking is een logisch gedeelte van het werk dat door een toepassing wordt uitgevoerd. Het heeft een naam, start tijd, duur, resultaat en een context van uitvoering zoals gebruikers naam, eigenschappen en resultaat. Als bewerking A werd geïnitieerd door bewerking B, wordt bewerking B ingesteld als een bovenliggend item voor een. Een bewerking kan slechts één bovenliggend element hebben, maar kan veel onderliggende bewerkingen hebben. Zie [Azure-toepassing Insights-correlatie voor telemetrie](correlation.md)voor meer informatie over bewerkingen en de correlatie tussen de telemetrie.

In de Application Insights .NET SDK wordt de bewerking beschreven door de abstracte klasse [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) en de bijbehorende descendanten [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) en [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Tracering van binnenkomende bewerkingen 
De Application Insights Web-SDK verzamelt automatisch HTTP-aanvragen voor ASP.NET-toepassingen die worden uitgevoerd in een IIS-pijp lijn en alle ASP.NET Core toepassingen. Er zijn oplossingen die door de community worden ondersteund voor andere platforms en frameworks. Als de toepassing echter niet wordt ondersteund door een van de Standard-of door de Community ondersteunde oplossingen, kunt u deze hand matig instrumenteren.

Een ander voor beeld waarvoor aangepaste tracking vereist is, is de werk nemer die items van de wachtrij ontvangt. Voor sommige wacht rijen wordt de aanroep om een bericht toe te voegen aan deze wachtrij, bijgehouden als een afhankelijkheid. De bewerking op hoog niveau waarmee bericht verwerking wordt beschreven, wordt echter niet automatisch verzameld.

Laten we eens kijken hoe dergelijke bewerkingen kunnen worden gevolgd.

Op hoog niveau is de taak het maken `RequestTelemetry` en instellen van bekende eigenschappen. Wanneer de bewerking is voltooid, kunt u de telemetrie bijhouden. In het volgende voor beeld wordt deze taak gedemonstreerd.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-aanvraag in Owin zelf-hostende app
In dit voor beeld wordt de tracerings context door gegeven volgens het [http-protocol voor correlatie](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). U verwacht headers te ontvangen die daar worden beschreven.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Het HTTP-protocol voor correlatie declareert ook de `Correlation-Context` header. Dit wordt hier echter voor eenvoud wegge laten.

## <a name="queue-instrumentation"></a>Instrumentatie in wachtrij plaatsen
Hoewel er sprake is van een [W3C-tracerings context](https://www.w3.org/TR/trace-context/) en een [http-protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) om correlatie Details met een HTTP-aanvraag door te geven, moet elk wachtrij protocol definiëren hoe dezelfde details worden door gegeven aan het wachtrij bericht. In sommige wachtrij protocollen (zoals AMQP) is het door geven van aanvullende meta gegevens en enkele andere (Azure Storage wachtrij) mogelijk, moet de context worden gecodeerd in de bericht lading.

> [!NOTE]
> * **Tracering van meerdere onderdelen wordt nog niet ondersteund voor wacht rijen** Als uw producent en consument telemetrie verzenden naar verschillende Application Insights resources, worden de trans acties voor de diagnose van de werk wijze en de toepassings toewijzing weer gegeven en worden de end-to-end-berichten toegewezen. In het geval van wacht rijen, wordt dit nog niet ondersteund. 

### <a name="service-bus-queue"></a>Service Bus-wachtrij
Application Insights traceert Service Bus Messa ging-aanroepen met de nieuwe [Microsoft Azure ServiceBus-client voor .net](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versie 3.0.0 en hoger.
Als u een [bericht afhandelingsprocedure](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) gebruikt voor het verwerken van berichten, bent u klaar: alle Service Bus-aanroepen die door uw service worden uitgevoerd, worden automatisch getraceerd en gecorreleerd met andere telemetrie-items. Raadpleeg de [Service Bus client tracering met micro soft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) als u berichten hand matig verwerkt.

Als u het [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -pakket gebruikt, lees dan de volgende voor beelden laten zien hoe u aanroepen (en correleert) aan de Service Bus als service bus wachtrij gebruikmaakt van het AMQP-protocol en Application Insights niet automatisch wachtrij bewerkingen traceert.
Correlatie-id's worden door gegeven in de bericht eigenschappen.

#### <a name="enqueue"></a>Schedul

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proces
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage wachtrij
In het volgende voor beeld ziet u hoe u de [Azure Storage wachtrij](../../storage/queues/storage-dotnet-how-to-use-queues.md) bewerkingen kunt volgen en telemetrie kunt correleren tussen de producent, de consument en Azure Storage. 

De opslag wachtrij heeft een HTTP-API. Alle aanroepen naar de wachtrij worden bijgehouden door de Application Insights dependency collector voor HTTP-aanvragen.
Het is standaard geconfigureerd voor ASP.NET-en ASP.NET Core-toepassingen, met andere soorten toepassingen. Raadpleeg ook de [documentatie van console toepassingen](./console.md)

Het is ook mogelijk dat u de Application Insights bewerkings-ID met de ID van de opslag aanvraag wilt correleren. Zie [Azure Storage controleren, diagnosticeren en problemen oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)voor meer informatie over het instellen en ophalen van een aanvraag-client voor aanvragen en een server aanvraag-id.

#### <a name="enqueue"></a>Schedul
Omdat opslag wachtrijen de HTTP-API ondersteunen, worden alle bewerkingen met de wachtrij automatisch bijgehouden door Application Insights. In veel gevallen moet deze instrumentatie voldoende zijn. Voor het correleren van traceringen aan de gebruiker met producenten traceringen moet u echter een correlatie context door geven aan de hand van de manier waarop we dit doen in het HTTP-protocol voor correlatie. 

In dit voor beeld ziet u hoe u de bewerking kunt volgen `Enqueue` . U kunt:

 - **Nieuwe pogingen (indien van toepassing)**: ze hebben allemaal een gemeen schappelijk bovenliggend item dat de `Enqueue` bewerking is. Anders worden ze bijgehouden als onderliggende items van de inkomende aanvraag. Als er meerdere logische aanvragen naar de wachtrij zijn, kan het lastig zijn om te ontdekken welke aanroep een nieuwe poging heeft gedaan.
 - **Correlatie van opslag Logboeken (als en wanneer nodig)**: ze zijn gerelateerd aan Application Insights telemetrie.

De `Enqueue` bewerking is het onderliggende element van een bovenliggende bewerking (bijvoorbeeld een binnenkomende HTTP-aanvraag). De HTTP-afhankelijkheids aanroep is het onderliggende element van de `Enqueue` bewerking en de grandchild van de inkomende aanvraag:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Als u de hoeveelheid telemetrie van uw toepassings rapporten wilt beperken of als u de `Enqueue` bewerking om andere redenen niet wilt bijhouden, gebruikt u de `Activity` API direct:

- Maak een nieuw (en start) `Activity` in plaats van de Application Insights bewerking te starten. U hoeft *geen* eigenschappen toe te wijzen, behalve de naam van de bewerking.
- Serialisatie `yourActivity.Id` in de bericht lading in plaats van `operation.Telemetry.Id` . U kunt ook gebruiken `Activity.Current.Id` .


#### <a name="dequeue"></a>Wachtrij verwijderen
Op dezelfde manier `Enqueue` wordt een daad werkelijke HTTP-aanvraag voor de opslag wachtrij automatisch bijgehouden door Application Insights. De `Enqueue` bewerking wordt echter vermoedelijk uitgevoerd in de bovenliggende context, zoals een binnenkomende aanvraag context. Application Insights Sdk's correleren een dergelijke bewerking (en het bijbehorende HTTP-deel) automatisch met de bovenliggende aanvraag en andere telemetrie die in hetzelfde bereik zijn gerapporteerd.

De `Dequeue` bewerking is lastig. De Application Insights SDK registreert automatisch HTTP-aanvragen. Het kent echter niet de correlatie context tot het bericht is geparseerd. Het is niet mogelijk om de HTTP-aanvraag voor het ophalen van het bericht met de rest van de telemetrie te correleren, vooral wanneer er meer dan één bericht wordt ontvangen.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Proces

In het volgende voor beeld wordt een inkomend bericht op een manier bijgehouden, op dezelfde wijze als bij een binnenkomende HTTP-aanvraag:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Op dezelfde manier kunnen andere wachtrij bewerkingen worden uitgevoerd. Een Peek-bewerking moet op een vergelijk bare manier worden geinstrumenteerd als een bewerking voor het verwijderen van een wachtrij. Beheer bewerkingen voor de wachtrij voor instrumentatie zijn niet nodig. Met Application Insights worden bewerkingen zoals HTTP bijgehouden, en in de meeste gevallen is dit voldoende.

Wanneer u het verwijderen van een bericht instrumenteert, moet u ervoor zorgen dat u de bewerkings-id's (correlatie) hebt ingesteld. U kunt ook de `Activity` API gebruiken. U hoeft geen bewerkings-id's in te stellen op de telemetriegegevens, omdat Application Insights SDK dit voor u doet:

- Een nieuwe maken `Activity` nadat u een item uit de wachtrij hebt ontvangen.
- Gebruiken `Activity.SetParentId(message.ParentId)` voor het correleren van consumenten-en producer-Logboeken.
- Start de `Activity` .
- Volg de bewerkings-, proces-en verwijder bewerkingen met behulp van `Start/StopOperation` helpers. Doe dit vanuit dezelfde asynchrone controle stroom (uitvoerings context). Op deze manier worden ze op de juiste wijze gecorreleerd.
- Stop de `Activity` .
- Gebruik `Start/StopOperation` of `Track` telemetrie hand matig aan te roepen.

### <a name="dependency-types"></a>Afhankelijkheids typen

Application Insights maakt gebruik van afhankelijkheids type om UI-ervaringen aan te passen. Voor wacht rijen herkent het de volgende typen `DependencyTelemetry` waarmee de ervaring voor het [diagnosticeren van trans acties](./transaction-diagnostics.md)wordt verbeterd:
- `Azure queue` voor Azure Storage wachtrijen
- `Azure Event Hubs` voor Azure Event Hubs
- `Azure Service Bus` voor Azure Service Bus

### <a name="batch-processing"></a>Batchverwerking
Met sommige wacht rijen kunt u meerdere berichten met één aanvraag uit de wachtrij verwijderen. Het verwerken van dergelijke berichten is waarschijnlijk een onafhankelijke onafhankelijk en maakt deel uit van de verschillende logische bewerkingen. Het is niet mogelijk om de `Dequeue` bewerking te correleren aan een bepaald bericht dat wordt verwerkt.

Elk bericht moet worden verwerkt in een eigen asynchrone controle stroom. Zie de sectie [Tracking van uitgaande afhankelijkheden](#outgoing-dependencies-tracking) voor meer informatie.

## <a name="long-running-background-tasks"></a>Langlopende achtergrond taken

Sommige toepassingen voeren langlopende bewerkingen uit die mogelijk worden veroorzaakt door gebruikers aanvragen. Vanuit het perspectief voor tracering/instrumentatie is het niet anders dan aanvragen of afhankelijkheids instrumentatie: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In dit voor beeld `telemetryClient.StartOperation` wordt `DependencyTelemetry` de correlatie context gemaakt en gevuld. Stel dat u een bovenliggende bewerking hebt die is gemaakt door inkomende aanvragen die de bewerking hebben gepland. Zolang als `BackgroundTask` een binnenkomende aanvraag wordt gestart in dezelfde asynchrone controle stroom, wordt deze met de bovenliggende bewerking gecorreleerd. `BackgroundTask` en alle geneste telemetriegegevens worden automatisch gecorreleerd met de aanvraag waardoor de items zijn ontstaan, zelfs nadat de aanvraag is beëindigd.

Wanneer de taak wordt gestart vanaf de achtergrond thread waaraan geen enkele bewerking ( `Activity` ) is gekoppeld, `BackgroundTask` heeft geen bovenliggend element. Het kan echter geneste bewerkingen bevatten. Alle telemetrie-items die van de taak zijn gerapporteerd, worden gecorreleerd aan de `DependencyTelemetry` gemaakt in `BackgroundTask` .

## <a name="outgoing-dependencies-tracking"></a>Bijhouden van uitgaande afhankelijkheden
U kunt uw eigen afhankelijkheids soort volgen of een bewerking die niet wordt ondersteund door Application Insights.

De `Enqueue` methode in de service bus wachtrij of de opslag wachtrij kan dienen als voor beelden voor dergelijke aangepaste tracking.

De algemene aanpak voor het bijhouden van aangepaste afhankelijkheden is:

- Roep de `TelemetryClient.StartOperation` methode (Extension) aan waarmee de `DependencyTelemetry` eigenschappen worden gevuld die nodig zijn voor correlatie en enkele andere eigenschappen (begin tijd stempel, duur).
- Stel andere aangepaste eigenschappen in op de `DependencyTelemetry` , zoals de naam en andere context die u nodig hebt.
- Maak een afhankelijkheids aanroep en wacht erop.
- Stop de bewerking met `StopOperation` wanneer deze is voltooid.
- Uitzonde ringen verwerken.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Als u de bewerking ongedaan maakt, wordt de bewerking gestopt, dus u kunt dit doen in plaats van aan te roepen `StopOperation` .

*Waarschuwing*: in sommige [gevallen kan een](/dotnet/csharp/language-reference/keywords/try-finally) niet-beschik bare uitzonde ring `finally` worden opgeroepen zodat bewerkingen mogelijk niet worden bijgehouden.

### <a name="parallel-operations-processing-and-tracking"></a>Parallelle bewerkingen verwerken en bijhouden

`StopOperation` Hiermee wordt alleen de bewerking gestopt die is gestart. Als de huidige actieve bewerking niet overeenkomt met het account dat u wilt stoppen, gebeurt `StopOperation` er niets. Deze situatie kan zich voordoen als u meerdere bewerkingen parallel in dezelfde uitvoerings context start:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Zorg ervoor dat u altijd met `StartOperation` dezelfde **async** -methode aanroept en verwerkt om bewerkingen die parallel worden uitgevoerd, te isoleren. Als de bewerking synchroon is (of niet asynchroon), omloop proces en volgen `Task.Run` :

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-bewerkingen versus System. Diagnostics. activity
`System.Diagnostics.Activity` vertegenwoordigt de gedistribueerde tracerings context en wordt gebruikt door frameworks en bibliotheken om context binnen en buiten het proces te maken en door te geven en telemetrie-items te correleren. Activiteit werkt samen met `System.Diagnostics.DiagnosticSource` -het meldings mechanisme tussen het Framework/de bibliotheek voor het melden van interessante gebeurtenissen (binnenkomende of uitgaande aanvragen, uitzonde ringen, enzovoort).

Activiteiten zijn eersteklas burgers in Application Insights en automatische afhankelijkheid en verzameling van aanvragen zijn in hoge mate afhankelijk van de `DiagnosticSource` gebeurtenissen. Als u activiteit in uw toepassing maakt, zou dit niet leiden tot Application Insights telemetrie wordt gemaakt. Application Insights moet DiagnosticSource-gebeurtenissen ontvangen en de namen en nettoladingen van de gebeurtenissen weten om de activiteit te vertalen naar telemetrie.

Elke Application Insights bewerking (aanvraag of afhankelijkheid) omvat `Activity` `StartOperation` het aanroepen van-when, waarbij activiteit onder wordt gemaakt. `StartOperation` is de aanbevolen manier om de telerichtingen voor aanvragen of afhankelijkheden hand matig te traceren en ervoor te zorgen dat alles wordt gecorreleerd.

## <a name="next-steps"></a>Volgende stappen

- Leer de basis beginselen van de correlatie tussen de [telemetrie](correlation.md) in Application Insights.
- Bekijk hoe gecorreleerde gegevens over de werking van [trans acties](./transaction-diagnostics.md) en [toepassings overzicht](./app-map.md).
- Zie het [gegevens model](./data-model.md) voor Application Insights typen en het gegevens model.
- Aangepaste [gebeurtenissen en metrische gegevens](./api-custom-events-metrics.md) rapporteren aan Application Insights.
- Bekijk de standaard [configuratie](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) voor de verzameling context eigenschappen.
- Raadpleeg de [Gebruikers handleiding van System. Diagnostics. activity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) om te zien hoe we telemetrie correleren.


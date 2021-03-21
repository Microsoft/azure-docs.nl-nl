---
title: Azure Event Grid uitvoer binding voor Azure Functions
description: Meer informatie over het verzenden van een Event Grid gebeurtenis in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97094673"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid uitvoer binding voor Azure Functions

Gebruik de Event Grid uitvoer binding om gebeurtenissen te schrijven naar een aangepast onderwerp. U moet een geldige [toegangs sleutel voor het aangepaste onderwerp](../event-grid/security-authenticate-publishing-clients.md)hebben.

Zie het [overzicht](./functions-bindings-event-grid.md)voor meer informatie over de installatie-en configuratie details.

> [!NOTE]
> De Event Grid uitvoer binding biedt geen ondersteuning voor hand tekeningen voor gedeelde toegang (SAS-tokens). U moet de toegangs sleutel van het onderwerp gebruiken.

> [!IMPORTANT]
> De Event Grid uitvoer binding is alleen beschikbaar voor functions 2. x en hoger.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een bericht naar een event grid aangepast onderwerp schrijft, met behulp van de retour waarde van de methode als uitvoer:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

In het volgende voorbeeld ziet u hoe u de `IAsyncCollector`-interface gebruikt om een batch berichten te verzenden.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in de *function.jsvoor* het bestand.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is de C#-script code voor het maken van één gebeurtenis:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Dit is de C#-script code waarmee meerdere gebeurtenissen worden gemaakt:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in de *function.jsvoor* het bestand.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is de Java script-code waarmee één gebeurtenis wordt gemaakt:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Dit is de Java script-code waarmee meerdere gebeurtenissen worden gemaakt:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u een functie kunt configureren om een Event Grid gebeurtenis bericht uit te voeren. De sectie waar `type` is ingesteld voor `eventGrid` het configureren van de waarden die nodig zijn om een event grid uitvoer binding te maken.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Gebruik in uw functie de `Push-OutputBinding` om een gebeurtenis naar een aangepast onderwerp te verzenden via de Event grid uitvoer binding.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een trigger binding in een *function.jsin* het bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. Vervolgens wordt er in een gebeurtenis naar het aangepaste onderwerp gezonden, zoals opgegeven door de `topicEndpointUri` .

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Hier volgt een voor beeld van python voor het verzenden van een gebeurtenis naar een aangepast onderwerp door het volgende in te stellen `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik voor [C# class-bibliotheken](functions-dotnet-class-library.md)het kenmerk [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

De constructor van het kenmerk maakt de naam van een app-instelling die de naam van het aangepaste onderwerp bevat en de naam van een app-instelling die de sleutel van het onderwerp bevat. Zie [Uitvoer: configuratie](#configuration) voor meer informatie over deze instellingen. Hier volgt een voorbeeld van een `EventGrid`-kenmerk:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie voor een volledig [voor beeld.](#example)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `EventGrid`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' eventGrid '. |
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze parameter wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die in functiecode wordt gebruikt, vertegenwoordigt de gebeurtenis. |
|**topicEndpointUri** |**TopicEndpointUri** | De naam van een app-instelling die de URI voor het aangepaste onderwerp bevat, zoals `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | De naam van een app-instelling die een toegangs sleutel voor het aangepaste onderwerp bevat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Zorg ervoor dat u de waarde van de `TopicEndpointUri` configuratie-eigenschap instelt op de naam van een app-instelling die de URI van het aangepaste onderwerp bevat. Geef de URI van het aangepaste onderwerp niet rechtstreeks op in deze eigenschap.

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Verzend berichten met behulp van een methodeparameter, zoals `out EventGridEvent paramName`. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Verzend berichten met behulp van een methodeparameter, zoals `out EventGridEvent paramName`. In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="java"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Open de uitvoergebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven voor de eigenschap `name` van *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Open de gebeurtenis output met behulp van de- `Push-OutputBinding` commandlet om een gebeurtenis te verzenden naar de Event grid uitvoer binding.

# <a name="python"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

---

## <a name="next-steps"></a>Volgende stappen

* [Een Event Grid gebeurtenis verzenden](./functions-bindings-event-grid-trigger.md)

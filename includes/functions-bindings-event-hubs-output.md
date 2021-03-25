---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: cdd8b42f3ef7530809c50925c631b7aa90ee213c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105101"
---
Gebruik de Event Hubs-uitvoerbinding om gebeurtenissen naar een gebeurtenisstroom te schrijven. U moet een verzendmachtiging voor een Event Hub hebben om er gebeurtenissen naar te kunnen schrijven.

Zorg ervoor dat de vereiste pakketverwijzingen aanwezig zijn voordat u een uitvoerbinding implementeert.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voorbeeld ziet u een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) die een bericht naar een Event Hub schrijft, met behulp van de retourwaarde van de methode als uitvoer:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

In het volgende voorbeeld ziet u hoe u de `IAsyncCollector`-interface gebruikt om een batch berichten te verzenden. Dit scenario is gebruikelijk bij het verwerken van berichten die afkomstig zijn van een Event Hub en het verzenden van het resultaat naar een andere Event Hub.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [C# Script-functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*. Het eerste voorbeeld is voor Functions 2.x en hoger en de tweede is voor Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de C#-scriptcode voor het maken van één bericht:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Dit is de C#-scriptcode voor het maken van meerdere berichten:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [JavaScript-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*. Het eerste voorbeeld is voor Functions 2.x en hoger en de tweede is voor Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de JavaScript-code voor het verzenden van één bericht:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Dit is de JavaScript-code voor het verzenden van meerdere berichten:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [Python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de Python-code voor het verzenden van één bericht:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld ziet u een Java-functie die een bericht met de huidige tijd schrijft naar een Event Hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Gebruik in de [Java-functies-runtimebibliotheek](/java/api/overview/azure/functions/runtime) de `@EventHubOutput`-aantekening voor parameters waarvan de waarde gepubliceerd zou worden naar Event Hub.  Deze parameter zou van het type `OutputBinding<T>` moeten zijn, waarbij T een POJO of een systeemeigen Java-type is.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik voor [C#-klassebibliotheken](../articles/azure-functions/functions-dotnet-class-library.md) het kenmerk [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

De constructor van het kenmerk neemt de naam van de Event Hub en de naam van een app-instelling die de verbindingsreeks bevat. Zie [Uitvoer: configuratie](#configuration) voor meer informatie over deze instellingen. Hier volgt een voorbeeld van een `EventHub`-kenmerk:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie [Uitvoer: C#-voorbeeld](#example) voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [Java-functies-runtimebibliotheek](/java/api/overview/azure/functions/runtime) de aantekening [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) voor parameters waarvan de waarde gepubliceerd zou worden naar Event Hub. Deze parameter zou van het type `OutputBinding<T>` moeten zijn, waarbij `T` een POJO of een systeemeigen Java-type is.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `EventHub`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op 'eventHub'. |
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze parameter wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die in functiecode wordt gebruikt, vertegenwoordigt de gebeurtenis. |
|**path** |**EventHubName** | Alleen Functions 1.x. De naam van de event hub. Wanneer de event hub-naam ook aanwezig is in de verbindingsreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2.x en hoger. De naam van de event hub. Wanneer de event hub-naam ook aanwezig is in de verbindingsreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**connection** |**Verbinding** | De naam van een app-instelling die de verbindingsreeks naar de naamruimte van de event hub bevat. Kopieer deze verbindingsreeks door op de knop **Verbindingsgegevens** voor de [naamruimte](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) te klikken, en niet op de event hub zelf. Deze verbindingsreeks moet verzendrechten hebben om het bericht naar de gebeurtenisstroom te verzenden. <br><br>Als u [versie 5. x of hoger van de extensie](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)gebruikt in plaats van een Connection String, kunt u een verwijzing naar een configuratie sectie opgeven waarmee de verbinding wordt gedefinieerd. Zie [verbindingen](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Standaard

U kunt de volgende parameter typen gebruiken voor de uitvoer binding van Event hub:

* `string`
* `byte[]`
* `POCO`
* `EventData` -De standaard eigenschappen van Event Data zijn opgenomen in de voor de [micro soft. Azure. Event hubs-naam ruimte](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Verzend berichten met behulp van een methodeparameter, zoals `out string paramName`. In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<string>` of `IAsyncCollector<string>` gebruiken in plaats van `out string`.

### <a name="additional-types"></a>Aanvullende typen 
Apps die gebruikmaken van de 5.0.0 of een hogere versie van de Event hub-uitbrei ding gebruiken het `EventData` type in [Azure. Messa ging. Event hubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) in plaats van de extensie in de [naam ruimte micro soft. Azure. Event hubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Deze versie biedt geen ondersteuning voor het oudere `Body` type voor de volgende typen:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>Standaard

U kunt de volgende parameter typen gebruiken voor de uitvoer binding van Event hub:

* `string`
* `byte[]`
* `POCO`
* `EventData` -De standaard eigenschappen van Event Data zijn opgenomen in de voor de [micro soft. Azure. Event hubs-naam ruimte](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Verzend berichten met behulp van een methodeparameter, zoals `out string paramName`. In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<string>` of `IAsyncCollector<string>` gebruiken in plaats van `out string`.

### <a name="additional-types"></a>Aanvullende typen 
Apps die gebruikmaken van de 5.0.0 of een hogere versie van de Event hub-uitbrei ding gebruiken het `EventData` type in [Azure. Messa ging. Event hubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) in plaats van de extensie in de [naam ruimte micro soft. Azure. Event hubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Deze versie biedt geen ondersteuning voor het oudere `Body` type voor de volgende typen:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Open de uitvoergebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven voor de eigenschap `name` van *function.json*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een Event Hub-bericht van een functie:

- **Retourwaarde**: stel de eigenschap `name` in *function. json* in op `$return`. Met deze configuratie wordt de retourwaarde van de functie persistent gemaakt als een Event Hub-bericht.

- **Imperatief**: geef een waarde door aan de methode [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) voor de parameter die is gedeclareerd als een type [Out](/python/api/azure-functions/azure.functions.out). De waarde die aan `set` is doorgegeven, wordt persistent gemaakt als een Event Hub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een Event Hub-bericht van een functie met behulp van de aantekening [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput):

- **Retourwaarde**: Door de aantekening toe te passen op de functie zelf, wordt de retourwaarde van de functie persistent gemaakt als een Event Hub-bericht.

- **Imperatief**: Als u de berichtwaarde expliciet wilt instellen, past u de aantekening toe op een specifieke parameter van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding), waarbij `T` een POJO of een systeemeigen Java-type is. Met deze configuratie wordt door het doorgeven van een waarde aan de methode `setValue` de waarde persistent gemaakt als een Event Hub-bericht.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Event Hub | [Operations Guide](/rest/api/eventhub/publisher-policy-operations) (Bedieningshandleiding) |
---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 145db7693db126d4e114e8c8a885ea7fd7809e69
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608904"
---
Gebruik de functietrigger om te reageren op een gebeurtenis die naar een event hub-gebeurtenisstroom wordt verzonden. U moet leestoegang hebben tot de onderliggende event hub om de trigger in te stellen. Wanneer de functie wordt geactiveerd, wordt het aan de functie doorgegeven bericht getypeerd als een tekenreeks.

## <a name="scaling"></a>Schalen

Elk exemplaar van een door een gebeurtenis geactiveerde functie wordt ondersteund door één [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-exemplaar. De trigger (verzorgd door Event Hubs) zorgt ervoor dat slechts één [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-exemplaar een lease kan krijgen op een bepaalde partitie.

Denk bijvoorbeeld aan de volgende Event Hub:

* 10 partities
* 1\.000 gebeurtenissen, gelijkmatig verdeeld over alle partities, met 100 berichten in elke partitie

Wanneer uw functie voor het eerst wordt ingeschakeld, is er slechts één exemplaar van de functie. We noemen het eerste functie-exemplaar `Function_0`. De `Function_0`-functie heeft één exemplaar van [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) dat een lease heeft op alle tien partities. Dit exemplaar leest gebeurtenissen van partities 0-9. Vanaf dit punt gebeurt een van de volgende dingen:

* **Er zijn geen nieuwe functie-exemplaren nodig**: `Function_0` kan alle 1.000 gebeurtenissen verwerken voordat de schaallogica van Functions in werking treedt. In dit geval worden alle 1.000 berichten verwerkt door `Function_0`.

* **Er wordt een extra functie-exemplaar toegevoegd**: Als de schaallogica van Functions vaststelt dat `Function_0` meer berichten heeft dan deze kan verwerken, wordt er een nieuw functie-app-exemplaar (`Function_1`) gemaakt. Deze nieuwe functie heeft ook een gekoppeld exemplaar van [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Als de onderliggende Event Hubs detecteren dat een nieuw host-exemplaar berichten probeert te lezen, worden de partities over de host-exemplaren verdeeld. Partities 0-4 kunnen bijvoorbeeld worden toegewezen aan `Function_0`, en partities 5-9 aan `Function_1`.

* **Er worden nog N functie-exemplaren toegevoegd**: Als de schaallogica van Functions vaststelt dat zowel `Function_0` als `Function_1` meer berichten hebben dan ze kunnen verwerken, worden er nieuwe `Functions_N` functie-app-exemplaren gemaakt.  Er worden apps gemaakt tot het punt waarop `N` groter is dan het aantal event hub-partities. In ons voorbeeld verdeelt Event Hubs de partities opnieuw, in dit geval over de exemplaren `Function_0`...`Functions_9`.

Wanneer er wordt geschaald, is `N` exemplaren een getal dat groter is dan het aantal event hub-partities. Dit patroon wordt gebruikt om ervoor te zorgen dat er [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)-exemplaren beschikbaar zijn voor het verkrijgen van vergrendelingen op partities wanneer deze beschikbaar worden vanuit andere exemplaren. Er worden alleen kosten in rekening gebracht voor de resources die worden gebruikt wanneer het functie-exemplaar wordt uitgevoerd. Met andere woorden, er worden geen kosten in rekening gebracht voor deze overinrichting.

Wanneer de uitvoering van alle functies is voltooid (met of zonder fouten), worden controlepunten toegevoegd aan het gekoppelde opslagaccount. Wanneer het toevoegen van controlepunten slaagt, worden alle 1.000 berichten nooit meer opgehaald.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voorbeeld ziet u een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) die de berichttekst van de event hub-trigger registreert.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [gebeurtenismetagegevens](#event-metadata) in functiecode, bindt u aan een [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-object (hiervoor is een using-instructie vereist voor `Microsoft.Azure.EventHubs`). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindingsexpressies in de methodehandtekening.  In het volgende voorbeeld ziet u beide manieren om dezelfde gegevens op te halen:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Als u gebeurtenissen in een batch wilt ontvangen, maakt u `string` of `EventData` een matrix.  

> [!NOTE]
> Wanneer u een batch ontvangt, kunt u niet binden aan methodeparameters zoals in het bovenstaande voorbeeld met `DateTime enqueuedTimeUtc` en moet u deze ontvangen van elk `EventData`-object  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [C# Script-functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie registreert de berichttekst van de event hub-trigger.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*.

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de C# Script-code:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [gebeurtenismetagegevens](#event-metadata) in functiecode, bindt u aan een [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-object (hiervoor is een using-instructie vereist voor `Microsoft.Azure.EventHubs`). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindingsexpressies in de methodehandtekening.  In het volgende voorbeeld ziet u beide manieren om dezelfde gegevens op te halen:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Als u gebeurtenissen in een batch wilt ontvangen, maakt u `string` of `EventData` een matrix:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [JavaScript-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. De functie leest [gebeurtenismetagegevens](#event-metadata) en registreert het bericht.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*.

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Als u gebeurtenissen in een batch wilt ontvangen, stelt u `cardinality` in op `many` in het bestand *function.json*, zoals in de volgende voorbeelden.

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld ziet u een event hub-trigger-binding in een *function.json*-bestand en een [Python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. De functie leest [gebeurtenismetagegevens](#event-metadata) en registreert het bericht.

In de volgende voorbeelden ziet u Event Hubs die gegevens binden in het bestand *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de Python-code:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld ziet u een Event Hub-trigger-binding die de berichttekst van de Event Hub-trigger registreert.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Gebruik in de [Java-functies-runtimebibliotheek](/java/api/overview/azure/functions/runtime) de `EventHubTrigger`-aantekening voor parameters waarvan de waarde afkomstig is van Event Hub. Door parameters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis optreedt.  Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

 ---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C#-klassebibliotheken](../articles/azure-functions/functions-dotnet-class-library.md) het kenmerk [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

De constructor van het kenmerk neemt de naam van de event hub, de naam van de consumentengroep en de naam van een app-instelling die de verbindingsreeks bevat. Zie de [triggerconfiguratiesectie](#configuration) voor meer informatie over deze instellingen. Hier volgt een voorbeeld van een `EventHubTriggerAttribute`-kenmerk:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Zie [Trigger: C#-voorbeeld](#example)voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik vanuit de Java-[functies-runtimebibliotheek](/java/api/overview/azure/functions/runtime) de [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger)-aantekening voor parameters waarvan de waarde afkomstig is van Event Hub. Door parameters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis optreedt. Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `EventHubTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `eventHubTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die het gebeurtenisitem in functiecode vertegenwoordigt. |
|**path** |**EventHubName** | Alleen Functions 1.x. De naam van de event hub. Wanneer de event hub-naam ook aanwezig is in de verbindingsreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2.x en hoger. De naam van de event hub. Wanneer de event hub-naam ook aanwezig is in de verbindingsreeks, overschrijft die waarde deze eigenschap tijdens runtime. Hiernaar kan worden verwezen via [app-instellingen](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) `%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | Een optionele eigenschap waarmee de [consumentengroep](../articles/event-hubs/event-hubs-features.md#event-consumers) wordt ingesteld die wordt gebruikt om abonnementen te nemen op gebeurtenissen in de hub. Als deze wordt weggelaten, wordt de `$Default`-consumentengroep gebruikt. |
|**cardinality** | N.v.t. | Wordt gebruikt voor alle niet- C#-talen. Stel in op `many` om batchverwerking mogelijk te maken.  Als deze eigenschap wordt weggelaten of ingesteld op `one`, wordt er één bericht doorgegeven aan de functie.<br><br>In C# wordt deze eigenschap automatisch toegewezen wanneer de trigger een matrix voor het type heeft.|
|**connection** |**Verbinding** | De naam van een app-instelling die de verbindingsreeks naar de naamruimte van de event hub bevat. Kopieer deze verbindingsreeks door op de knop **Verbindingsgegevens** voor de [naamruimte](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) te klikken, en niet op de event hub zelf. Deze verbindingsreeks moet minimaal leesmachtigingen hebben om de trigger te activeren.<br><br>Als u [versie 5. x of hoger van de extensie](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)gebruikt in plaats van een Connection String, kunt u een verwijzing naar een configuratie sectie opgeven waarmee de verbinding wordt gedefinieerd. Zie [verbindingen](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Standaard

U kunt de volgende parameter typen gebruiken voor de trigger Event hub:

* `string`
* `byte[]`
* `POCO`
* `EventData` -De standaard eigenschappen van Event Data zijn opgenomen in de voor de [micro soft. Azure. Event hubs-naam ruimte](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Aanvullende typen 
Apps die gebruikmaken van de 5.0.0 of een hogere versie van de Event hub-uitbrei ding gebruiken het `EventData` type in [Azure. Messa ging. Event hubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) in plaats van de extensie in de [naam ruimte micro soft. Azure. Event hubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Deze versie biedt geen ondersteuning voor het oudere `Body` type voor de volgende typen:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>Standaard

U kunt de volgende parameter typen gebruiken voor de trigger Event hub:

* `string`
* `byte[]`
* `POCO`
* `EventData` -De standaard eigenschappen van Event Data zijn opgenomen in de voor de [micro soft. Azure. Event hubs-naam ruimte](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Aanvullende typen 
Apps die gebruikmaken van de 5.0.0 of een hogere versie van de Event hub-uitbrei ding gebruiken het `EventData` type in [Azure. Messa ging. Event hubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) in plaats van de extensie in de [naam ruimte micro soft. Azure. Event hubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Deze versie biedt geen ondersteuning voor het oudere `Body` type voor de volgende typen:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

Raadpleeg het [voor beeld](#example) van Java-trigger voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Raadpleeg het [voor beeld](#example) van Java script-trigger voor meer informatie.

# <a name="python"></a>[Python](#tab/python)

Raadpleeg het [voor beeld](#example) van python-trigger voor meer informatie.


---


## <a name="event-metadata"></a>Gebeurtenismetagegevens

De Event Hubs-trigger biedt verschillende [metagegevenseigenschappen](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Metagegevenseigenschappen kunnen worden gebruikt als onderdeel van bindingsexpressies in andere bindingen of als parameters in uw code. De eigenschappen zijn afkomstig van de [Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata)-klasse.

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Het `PartitionContext`-exemplaar.|
|`EnqueuedTimeUtc`|`DateTime`|De wachtrijtijd in UTC.|
|`Offset`|`string`|De offset van de gegevens ten opzichte van de Event Hub-partitiestroom. De offset is een markering of id voor een gebeurtenis binnen de Event Hubs-stroom. De id is uniek binnen een partitie van de Event Hubs-stroom.|
|`PartitionKey`|`string`|De partitie waarnaar gebeurtenisgegevens moeten worden verzonden.|
|`Properties`|`IDictionary<String,Object>`|De gebruikerseigenschappen van de gebeurtenisgegevens.|
|`SequenceNumber`|`Int64`|Het logische volgnummer van de gebeurtenis.|
|`SystemProperties`|`IDictionary<String,Object>`|De systeemeigenschappen, inclusief de gebeurtenisgegevens.|

Zie [codevoorbeelden](#example) die gebruikmaken van deze eigenschappen eerder in dit artikel.
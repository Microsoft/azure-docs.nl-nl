---
title: Azure Service Bus uitvoer bindingen voor Azure Functions
description: Meer informatie over het verzenden van Azure Service Bus berichten vanuit Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 12e57361b9e275fc441df27a3a1381989d48751c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788567"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus uitvoer binding voor Azure Functions

Gebruik Azure Service Bus uitvoer binding om berichten uit de wachtrij of het onderwerp te verzenden.

Zie het [overzicht](functions-bindings-service-bus.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een service bus wachtrij bericht verzendt:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Dit is de C#-script code waarmee één bericht wordt gemaakt:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Dit is de C#-scriptcode voor het maken van meerdere berichten:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die een bericht naar een Service Bus wachtrij verzendt wanneer het wordt `myqueue` geactiveerd door een HTTP-aanvraag.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@QueueOutput` annotatie voor functie parameters waarvan de waarde zou worden geschreven naar een service bus wachtrij.  Het parameter type moet zijn `OutputBinding<T>` , waarbij T een systeem eigen Java-type is van een POJO.

Java-functies kunnen ook worden geschreven naar een Service Bus onderwerp. In het volgende voor beeld wordt met behulp `@ServiceBusTopicOutput` van de aantekening de configuratie voor de uitvoer binding beschreven. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Hier volgt een Java script-script code waarmee één bericht wordt gemaakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Dit is de Java script-script code waarmee meerdere berichten worden gemaakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *function.jsin* een bestand en een [Power shell-functie](functions-reference-powershell.md) die gebruikmaakt van de binding. 

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Hier ziet u de Power shell die een bericht maakt als uitvoer van de functie.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u kunt schrijven naar een Service Bus wachtrij in python.

Een definitie van een Service Bus binding wordt gedefinieerd in *function.jsop* waar *type* is ingesteld op `serviceBus` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

In *_\_ init_ \_ . py* kunt u een bericht naar de wachtrij schrijven door een waarde door te geven aan de- `set` methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en het abonnement. U kunt ook de toegangs rechten van de verbinding opgeven. Het kiezen van de instelling voor toegangs rechten wordt uitgelegd in het gedeelte [output-Configuration](#configuration) . Hier volgt een voor beeld waarin het kenmerk wordt weer gegeven dat wordt toegepast op de geretourneerde waarde van de functie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om de naam op te geven van een app-instelling die het Service Bus Connection String bevat dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer-voor beeld](#example).

U kunt het- `ServiceBusAccount` kenmerk gebruiken om het service bus-account op te geven dat moet worden gebruikt bij klasse, methode of parameter niveau.  Zie [trigger-Attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

De `ServiceBusQueueOutput` `ServiceBusTopicOutput` aantekeningen en zijn beschikbaar om een bericht te schrijven als een functie-uitvoer. De para meter die is gedecoreerd met deze annotaties moet worden gedeclareerd als een `OutputBinding<T>` WHERE `T` is het type dat overeenkomt met het type van het bericht.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `ServiceBus`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' serviceBus '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die de wachtrij of het onderwerp van het bericht in de functie code vertegenwoordigt. Ingesteld op ' $return ' om te verwijzen naar de retour waarde van de functie. |
|**queueName**|**QueueName**|De naam van de wachtrij.  Stel deze waarde alleen in als u wachtrij berichten verzendt, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het onderwerp. Stel deze waarde alleen in als er onderwerp-berichten worden verzonden, niet voor een wachtrij.|
|**connection**|**Verbinding**|De naam van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met "AzureWebJobs", kunt u alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' MyServiceBus ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus Connection String in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De connection string moet voor een Service Bus naam ruimte zijn, niet beperkt tot een specifieke wachtrij of een specifiek onderwerp.|
|**accessRights** (alleen v1)|**Toegang**|Toegangs rechten voor de connection string. Beschik bare waarden zijn `manage` en `listen` . De standaard waarde is `manage` , waarmee wordt aangegeven dat de de `connection` machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders kan het uitvoeren van de functions-runtime geen bewerkingen uitvoeren waarvoor het beheer van rechten nodig is. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

In Azure Functions 1. x wordt de wachtrij door de runtime gemaakt als deze nog niet bestaat en is ingesteld `accessRights` op `manage` . In functies versie 2. x en hoger moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

# <a name="c"></a>[C#](#tab/csharp)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` Dit kan elk type JSON-serialiseerbaar zijn. Als de waarde van de para meter null is wanneer de functie wordt afgesloten, worden met functies het bericht met een null-object gemaakt.
* `out string` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 1. x)
* `out Message` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>` (voor asynchrone methoden): voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de-methode aanroept `Add` .

Wanneer u werkt met C#-functies:

* Asynchrone functies hebben een retour waarde nodig of `IAsyncCollector` in plaats van een `out` para meter.

* Als u toegang wilt krijgen tot de sessie-ID, maakt u een binding met een [`Message`](/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de `sessionId` eigenschap.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` Dit kan elk type JSON-serialiseerbaar zijn. Als de waarde van de para meter null is wanneer de functie wordt afgesloten, worden met functies het bericht met een null-object gemaakt.
* `out string` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 1. x)
* `out Message` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>` -voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de-methode aanroept `Add` .

Wanneer u werkt met C#-functies:

* Asynchrone functies hebben een retour waarde nodig of `IAsyncCollector` in plaats van een `out` para meter.

* Als u toegang wilt krijgen tot de sessie-ID, maakt u een binding met een [`Message`](/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de `sessionId` eigenschap.

# <a name="java"></a>[Java](#tab/java)

Gebruik de [Azure service bus SDK](../service-bus-messaging/index.yml) in plaats van de ingebouwde uitvoer binding.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp met behulp van `context.bindings.<name from function.json>` . U kunt een teken reeks, een byte matrix of een Java Script-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Uitvoer naar de Service Bus is beschikbaar via de `Push-OutputBinding` cmdlet waarbij u argumenten doorgeeft die overeenkomen met de naam die is opgegeven door de naam parameter van de binding in de *function.jsvoor* het bestand.

# <a name="python"></a>[Python](#tab/python)

Gebruik de [Azure service bus SDK](../service-bus-messaging/index.yml) in plaats van de ingebouwde uitvoer binding.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Service Bus | [Service Bus fout codes](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het onderstaande voor beeld host.jsin het onderstaande bestand worden alleen de instellingen voor deze binding opgenomen. Zie [host.jsop referentie voor Azure functions versie](functions-host-json.md)voor meer informatie over globale configuratie-instellingen.

> [!NOTE]
> Zie [host.jsbij verwijzing voor Azure functions 1. x](functions-host-json-v1.md)voor een referentie van host.jsin in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Als u hebt `isSessionsEnabled` ingesteld op `true` , `sessionHandlerOptions` wordt de gehonoreerd.  Als u hebt `isSessionsEnabled` ingesteld op `false` , `messageHandlerOptions` wordt de gehonoreerd.

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|prefetchCount|0|Hiermee wordt het aantal berichten opgehaald of ingesteld dat de ontvanger van het bericht tegelijk kan aanvragen.|
|maxAutoRenewDuration|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.|
|Automatisch aanvullen|true|Hiermee wordt aangegeven of de trigger automatisch moet worden voltooid na de verwerking, of dat de functie code hand matig wordt aangeroepen.<br><br>De instelling in `false` wordt alleen ondersteund in C#.<br><br>Als deze `true` optie is ingesteld op, wordt het bericht automatisch door de trigger voltooid als de uitvoering van de functie is voltooid en wordt het bericht anders afgebroken.<br><br>Wanneer dit is ingesteld op `false` , bent u verantwoordelijk voor het aanroepen van [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet&preserve-view=true) -methoden om het bericht te volt ooien, te annuleren of deadletter. Als er een uitzonde ring wordt gegenereerd (en geen van de `MessageReceiver` methoden worden genoemd), blijft de vergren deling. Zodra de vergren deling is verlopen, wordt het bericht opnieuw in de wachtrij geplaatst `DeliveryCount` en wordt de vergren deling automatisch verlengd.<br><br>In functies zonder C # worden uitzonde ringen in de functie uitgevoerd in de runtime-aanroepen `abandonAsync` op de achtergrond. Als er geen uitzonde ring optreedt, `completeAsync` wordt deze op de achtergrond aangeroepen. |
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de call back dat de bericht pomp moet initiëren per geschaald exemplaar. De functie runtime verwerkt standaard meerdere berichten tegelijk.|
|maxConcurrentSessions|2000|Het maximum aantal sessies dat gelijktijdig kan worden verwerkt per geschaald exemplaar.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Service Bus wachtrij of onderwerp bericht wordt gemaakt (trigger)](./functions-bindings-service-bus-trigger.md)

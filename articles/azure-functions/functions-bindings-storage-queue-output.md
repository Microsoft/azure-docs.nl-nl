---
title: Azure Queue Storage-uitvoer binding voor Azure Functions
description: Meer informatie over het maken van Azure Queue-opslag berichten in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 087073437fe9d6159422799c04ce095c0aae5eca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001249"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Queue Storage-uitvoer bindingen voor Azure Functions

Azure Functions kunt nieuwe Azure Queue-opslag berichten maken door een uitvoer binding in te stellen.

Zie het [overzicht](./functions-bindings-storage-queue.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een wachtrij bericht wordt gemaakt voor elke ontvangen HTTP-aanvraag.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een HTTP-trigger in een *function.jsvoor* de code van het bestand en [C#-script (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt een wachtrij-item met een **CustomQueueMessage** -object lading voor elke ontvangen HTTP-aanvraag.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Hier volgt een C#-script code voor het maken van één wachtrij bericht:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

U kunt meerdere berichten tegelijk verzenden met behulp van een `ICollector` or- `IAsyncCollector` para meter. Dit is het C#-script code dat meerdere berichten verzendt, een met de HTTP-aanvraag gegevens en een met in code vastgelegde waarden:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 In het volgende voor beeld ziet u een Java-functie waarmee een wachtrij bericht wordt gemaakt voor wanneer deze wordt geactiveerd door een HTTP-aanvraag.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@QueueOutput` aantekening voor para meters waarvan de waarde wordt geschreven naar de wachtrij opslag.  Het parameter type moet zijn `OutputBinding<T>` , waarbij `T` een systeem eigen Java-type is van een POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een binding van een HTTP-trigger in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt een wachtrij-item voor elke ontvangen HTTP-aanvraag.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

U kunt meerdere berichten tegelijk verzenden door een bericht matrix te definiëren voor de `myQueueItem` uitvoer binding. De volgende Java script-code verzendt twee wachtrij berichten met in code vastgelegde waarden voor elke ontvangen HTTP-aanvraag.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

De volgende code voorbeelden laten zien hoe u een wachtrij bericht van een door HTTP geactiveerde functie uitvoert. De configuratie sectie met de `type` van `queue` definieert de uitvoer binding.

```json
{
  "bindings": [
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Met behulp van deze bindings configuratie kan een Power shell-functie een wachtrij bericht maken met `Push-OutputBinding` . In dit voor beeld wordt een bericht gemaakt op basis van een query reeks of hoofd code.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Als u meerdere berichten tegelijk wilt verzenden, definieert u een bericht matrix en gebruikt `Push-OutputBinding` u voor het verzenden van berichten naar de wachtrij-uitvoer binding.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u één en meerdere waarden naar opslag wachtrijen kunt uitvoeren. De configuratie die nodig is voor *function.jsop is op* dezelfde manier.

Er is een opslag wachtrij binding gedefinieerd in *function.jsop* waar *type* is ingesteld op `queue` .

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Als u een afzonderlijk bericht in de wachtrij wilt instellen, geeft u één waarde door aan de- `set` methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Als u meerdere berichten in de wachtrij wilt maken, declareert u een para meter als het juiste lijst Type en geeft u een matrix met waarden (die overeenkomen met het lijst Type) door aan de- `set` methode.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Het kenmerk is van toepassing op een `out` para meter of de retour waarde van de functie. De constructor van het kenmerk maakt de naam van de wachtrij, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Zie [uitvoer voorbeeld](#example)voor een volledig voor beeld.

U kunt het- `StorageAccount` kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie trigger-Attributes (Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`Met de aantekening kunt u een bericht schrijven als de uitvoer van een functie. In het volgende voor beeld ziet u een door HTTP geactiveerde functie waarmee een wachtrij bericht wordt gemaakt.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Eigenschap    | Beschrijving |
|-------------|-----------------------------|
|`name`       | Declareert de parameter naam in de functie handtekening. Wanneer de functie wordt geactiveerd, heeft de waarde van deze para meter de inhoud van het wachtrij bericht. |
|`queueName`  | Declareert de wachtrij naam in het opslag account. |
|`connection` | Verwijst naar het opslag account connection string. |

De para meter die aan de `QueueOutput` aantekening is gekoppeld, wordt getypt als een [OutputBinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) -exemplaar.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `Queue`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `queue`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `out`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die de wachtrij in functie code vertegenwoordigt. Instellen op `$return` om te verwijzen naar de functie retour waarde.|
|**queueName** |**QueueName** | De naam van de wachtrij. |
|**connection** | **Verbinding** |De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Een enkel wachtrij bericht schrijven met behulp van een methode parameter, zoals `out T paramName` . U kunt het retour type van de methode gebruiken in plaats van een `out` para meter, en dit `T` kan een van de volgende typen zijn:

* Een object dat als JSON kan worden geserialiseerd
* `string`
* `byte[]`
* [CloudQueueMessage] 

Als u probeert verbinding te maken met `CloudQueueMessage` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)hebt.

Schrijf in C#-en C#-script meerdere wachtrij berichten met behulp van een van de volgende typen: 

* `ICollector<T>` of `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Een enkel wachtrij bericht schrijven met behulp van een methode parameter, zoals `out T paramName` . De `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. U kunt het retour type van de methode gebruiken in plaats van een `out` para meter, en dit `T` kan een van de volgende typen zijn:

* Een object dat als JSON kan worden geserialiseerd
* `string`
* `byte[]`
* [CloudQueueMessage] 

Als u probeert verbinding te maken met `CloudQueueMessage` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)hebt.

Schrijf in C#-en C#-script meerdere wachtrij berichten met behulp van een van de volgende typen: 

* `ICollector<T>` of `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een wachtrij bericht van een functie met behulp van de [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) -aantekening:

- **Retour waarde**: door de aantekening toe te passen op de functie zelf, wordt de geretourneerde waarde van de functie persistent gemaakt als een wachtrij bericht.

- **Imperatief**: Als u de berichtwaarde expliciet wilt instellen, past u de aantekening toe op een specifieke parameter van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), waarbij `T` een POJO of een systeemeigen Java-type is. Met deze configuratie wordt door het door geven van een waarde aan de `setValue` methode de waarde opgeslagen als een wachtrij bericht.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het uitvoer wachtrij-item is beschikbaar via `context.bindings.<NAME>` waar `<NAME>` overeenkomt met de naam die is gedefinieerd in *function.jsop*. U kunt een teken reeks of een JSON-serialiseerbaar object gebruiken voor de nettolading van het wachtrij-item.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Uitvoer naar het wachtrij bericht is beschikbaar via `Push-OutputBinding` waar u argumenten doorgeeft die overeenkomen met de naam die is opgegeven door de bindings `name` parameter in de *function.jsvoor* het bestand.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een wachtrij bericht van een functie:

- **Retourwaarde**: stel de eigenschap `name` in *function. json* in op `$return`. Met deze configuratie wordt de retour waarde van de functie persistent gemaakt als een wachtrij-opslag bericht.

- **Imperatief**: geef een waarde door aan de methode [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) voor de parameter die is gedeclareerd als een type [Out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true). De waarde die is door gegeven aan, `set` wordt persistent gemaakt als een wachtrij-opslag bericht.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Referentie |
|---|---|
| Wachtrij | [Fout codes voor de wachtrij](/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij |  [Problemen oplossen](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het volgende voor beeld host.jsin het onderstaande bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x en verder [host.jsop referentie voor Azure functions](functions-host-json.md).

> [!NOTE]
> Zie [host.jsbij verwijzing voor Azure functions 1. x](functions-host-json-v1.md)voor een referentie van host.jsin in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Het maximum interval tussen de polls van de wachtrij. Minimum is 00:00:00.100 (100 MS) en wordt verhoogd naar 00:01:00 (1 min.).  In 1. x is het gegevens type milliseconden en in 2. x en hoger het een time span.|
|visibilityTimeout|00:00:00|Het tijds interval tussen nieuwe pogingen wanneer het verwerken van een bericht mislukt. |
|batchSize|16|Het aantal wachtrij berichten dat door de functions-runtime gelijktijdig wordt opgehaald en processen parallel. Wanneer het nummer dat wordt verwerkt, naar de wordt verwerk `newBatchThreshold` , wordt er door de runtime een andere batch opgehaald en worden deze berichten verwerkt. Het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, is `batchSize` plus `newBatchThreshold` . Deze limiet geldt afzonderlijk voor elke door de wachtrij geactiveerde functie. <br><br>Als u een parallelle uitvoering wilt voor komen voor berichten die worden ontvangen op één wachtrij, kunt u instellen `batchSize` op 1. Met deze instelling elimineert u echter alleen gelijktijdigheid, zolang uw functie-app wordt uitgevoerd op één virtuele machine (VM). Als de functie-app wordt geschaald naar meerdere Vm's, kan elke virtuele machine één exemplaar van elke door de wachtrij geactiveerde functie uitvoeren.<br><br>De maximum waarde `batchSize` is 32. |
|maxDequeueCount|5|Het aantal keren dat een bericht moet worden verwerkt voordat het naar de verontreinigde wachtrij wordt verplaatst.|
|newBatchThreshold|batchSize/2|Telkens wanneer het aantal berichten dat gelijktijdig wordt verwerkt, naar dit nummer wordt opgehaald, haalt de runtime een andere batch op.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren als gegevens wijzigingen in de wachtrij-opslag (trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage

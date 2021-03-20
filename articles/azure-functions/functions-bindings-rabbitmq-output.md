---
title: RabbitMQ uitvoer bindingen voor Azure Functions
description: Meer informatie over het verzenden van RabbitMQ-berichten van Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097310"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ-uitvoer binding voor Azure Functions-overzicht

> [!NOTE]
> De RabbitMQ-bindingen worden alleen volledig ondersteund voor **Premium-en speciale** abonnementen. Verbruik wordt niet ondersteund.

Gebruik de RabbitMQ-uitvoer binding om berichten te verzenden naar een RabbitMQ-wachtrij.

Zie het [overzicht](functions-bindings-rabbitmq-output.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een RabbitMQ-bericht wordt verzonden wanneer een timer trigger om de 5 minuten wordt geactiveerd met de retour waarde van de methode als uitvoer:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

In het volgende voor beeld ziet u hoe u de IAsyncCollector-interface gebruikt voor het verzenden van berichten.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

In het volgende voor beeld ziet u hoe u de berichten verzendt als POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een RabbitMQ-uitvoer binding in eenfunction.jsin een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt *van* de binding. De functie leest in het bericht van een HTTP-trigger en voert deze uit naar de RabbitMQ-wachtrij.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Dit is de C# Script-code:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een RabbitMQ-uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie leest in het bericht van een HTTP-trigger en voert deze uit naar de RabbitMQ-wachtrij.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Dit is de Java script-code:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een RabbitMQ-uitvoer binding in een *function.jsin* het bestand en een python-functie die gebruikmaakt van de binding. De functie leest in het bericht van een HTTP-trigger en voert deze uit naar de RabbitMQ-wachtrij.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

In *_\_ init_ \_ . py*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

De volgende Java-functie maakt gebruik `@RabbitMQOutput` van de annotatie van de [Java RabbitMQ-typen](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) om de configuratie voor een RabbitMQ-wachtrij-uitvoer binding te beschrijven. De functie verzendt een bericht naar de RabbitMQ-wachtrij wanneer deze elke 5 minuten wordt geactiveerd door een timer trigger.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Dit is een `RabbitMQAttribute` kenmerk in een methode handtekening:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Zie C#-voor [beeld](#example)voor een volledig voor beeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput`Met de aantekening kunt u een functie maken die wordt uitgevoerd wanneer een RabbitMQ-bericht wordt verzonden. Beschik bare configuratie opties zijn wachtrij naam en connection string naam. Ga naar de [RabbitMQOutput Java-aantekeningen](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java)voor meer informatie over de para meters.

Zie het [voor beeld](#example) van een uitvoer binding voor meer details.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `RabbitMQ`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' RabbitMQ '.|
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. |
|**name** | N.v.t. | De naam van de variabele die de wachtrij in functie code vertegenwoordigt. |
|**queueName**|**QueueName**| De naam van de wachtrij waarnaar berichten moeten worden verzonden. |
|**Hostnaam**|**Hostnaam**|(wordt genegeerd als ConnectStringSetting wordt gebruikt) <br>De hostnaam van de wachtrij (bijvoorbeeld: 10.26.45.210)|
|**userName**|**Gebruikers**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) <br>De naam van de app-instelling die de gebruikers naam bevat voor toegang tot de wachtrij. Bijvoorbeeld UserNameSetting: "< UserNameFromSettings >"|
|**password**|**Wachtwoord**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) <br>De naam van de app-instelling die het wacht woord bevat voor toegang tot de wachtrij. Bijvoorbeeld UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|De naam van de app-instelling die de RabbitMQ-berichten wachtrij connection string bevat. Houd er rekening mee dat als u de connection string rechtstreeks opgeeft, en niet via een app-instelling in local.settings.jsop, de trigger niet werkt. (Bijvoorbeeld: in *function.jsop*: connectionStringSetting: "rabbitMQConnection" <br> In *local.settings.jsop*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**Importeer**|**Poort**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) Hiermee wordt de gebruikte poort opgehaald of ingesteld. De standaard waarde is 0 die verwijst naar de standaard poort instelling van de client rabbitmq: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `byte[]` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `string` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `POCO` -Als de waarde van de para meter niet is opgemaakt als een C#-object, wordt er een fout melding ontvangen. Zie C#-voor [beeld](#example)voor een volledig voor beeld.

Wanneer u werkt met C#-functies:

* Asynchrone functies hebben een retour waarde nodig of `IAsyncCollector` in plaats van een `out` para meter.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `byte[]` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `string` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `POCO` -Als de waarde van de para meter niet is opgemaakt als een C#-object, wordt er een fout melding ontvangen. Zie voor een volledig voor beeld C# script- [voor beeld](#example).

Bij het werken met C#-script functies:

* Asynchrone functies hebben een retour waarde nodig of `IAsyncCollector` in plaats van een `out` para meter.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het bericht in de wachtrij is beschikbaar via context. bindingen.<NAME> waar <NAME> komt overeen met de naam die is gedefinieerd in function.jsop. Als de payload JSON is, wordt de waarde in een object gedeserialiseerd.

# <a name="python"></a>[Python](#tab/python)

Raadpleeg het python- [voor beeld](#example).

# <a name="java"></a>[Java](#tab/java)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `byte[]` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `string` -Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `POJO` -Als de waarde van de para meter niet is opgemaakt als een Java-object, wordt er een fout melding ontvangen.

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een RabbitMQ-bericht wordt gemaakt (trigger)](./functions-bindings-rabbitmq-trigger.md)

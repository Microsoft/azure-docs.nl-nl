---
title: RabbitMQ-trigger voor Azure Functions
description: Meer informatie over het uitvoeren van een Azure-functie wanneer een RabbitMQ-bericht wordt gemaakt.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 4ba19fdf700790d89fe04867985fb803c3b0a2fc
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760398"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>RabbitMQ-trigger voor Azure Functions-overzicht

> [!NOTE]
> De RabbitMQ-bindingen worden alleen volledig ondersteund voor **Premium-en speciale** abonnementen. Verbruik wordt niet ondersteund.

Gebruik de trigger RabbitMQ om te reageren op berichten van een RabbitMQ-wachtrij.

Zie het [overzicht](functions-bindings-rabbitmq.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee het RabbitMQ-bericht wordt gelezen en geregistreerd als een [RabbitMQ gebeurtenis](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

In het volgende voor beeld ziet u hoe u het bericht als een POCO leest.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

Net als bij JSON-objecten treedt er een fout op als het bericht niet de juiste indeling heeft als een C#-object. Als dit het geval is, wordt het vervolgens gekoppeld aan de variabele pocObj, die kan worden gebruikt voor de gewenste waarde voor.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een RabbitMQ-trigger binding in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie leest en registreert het RabbitMQ-bericht.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Dit is de C# Script-code:

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een RabbitMQ-trigger binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie wordt een RabbitMQ-bericht gelezen en geregistreerd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Hier volgt de Java script-script code:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een RabbitMQ-wachtrij bericht via een trigger kunt lezen.

Er wordt een RabbitMQ-binding gedefinieerd in *function.js* waarvoor *type* is ingesteld op `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

De volgende Java-functie maakt gebruik `@RabbitMQTrigger` van de annotatie van de [Java RabbitMQ-typen](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) om de configuratie voor een RabbitMQ-wachtrij trigger te beschrijven. De functie trekt het bericht dat in de wachtrij is geplaatst en voegt het toe aan de logboeken.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) .

Dit is een `RabbitMQTrigger` kenmerk in een methode handtekening:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
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

`RabbitMQTrigger`Met de aantekening kunt u een functie maken die wordt uitgevoerd wanneer een RabbitMQ-bericht wordt gemaakt. Beschik bare configuratie opties zijn wachtrij naam en connection string naam. Ga naar de [RabbitMQTrigger Java-aantekeningen](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)voor meer informatie over de para meters.

Zie het [voor beeld](#example) van de trigger voor meer details.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `RabbitMQTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' RabbitMQTrigger '.|
|**direction** | N.v.t. | Moet worden ingesteld op in.|
|**name** | N.v.t. | De naam van de variabele die de wachtrij in functie code vertegenwoordigt. |
|**queueName**|**QueueName**| De naam van de wachtrij waaruit berichten moeten worden ontvangen. |
|**Hostnaam**|**Hostnaam**|(wordt genegeerd als ConnectStringSetting wordt gebruikt) <br>De hostnaam van de wachtrij (bijvoorbeeld: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) <br>De naam van de app-instelling die de gebruikers naam bevat voor toegang tot de wachtrij. Bijvoorbeeld UserNameSetting:% < UserNameFromSettings >%|
|**passwordSetting**|**PasswordSetting**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) <br>De naam van de app-instelling die het wacht woord bevat voor toegang tot de wachtrij. Bijvoorbeeld PasswordSetting:% < PasswordFromSettings >%|
|**connectionStringSetting**|**ConnectionStringSetting**|De naam van de app-instelling die de RabbitMQ-berichten wachtrij connection string bevat. Houd er rekening mee dat als u de connection string rechtstreeks opgeeft, en niet via een app-instelling in local.settings.jsop, de trigger niet werkt. (Bijvoorbeeld: in *function.jsop*: connectionStringSetting: "rabbitMQConnection" <br> In *local.settings.jsop*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**Importeer**|**Poort**|(wordt genegeerd als ConnectionStringSetting wordt gebruikt) Hiermee wordt de gebruikte poort opgehaald of ingesteld. De standaard waarde is 0 die verwijst naar de standaard poort instelling van de client rabbitmq: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Het standaard bericht type is [RabbitMQ gebeurtenis](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)en de `Body` eigenschap van de gebeurtenis RabbitMQ kan worden gelezen als de onderstaande typen:

* `An object serializable as JSON` -Het bericht wordt geleverd als een geldige JSON-teken reeks.
* `string`
* `byte[]`
* `POCO` -Het bericht wordt opgemaakt als een C#-object. Zie C#-voor [beeld](#example)voor een volledig voor beeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Het standaard bericht type is [RabbitMQ gebeurtenis](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)en de `Body` eigenschap van de gebeurtenis RabbitMQ kan worden gelezen als de onderstaande typen:

* `An object serializable as JSON` -Het bericht wordt geleverd als een geldige JSON-teken reeks.
* `string`
* `byte[]`
* `POCO` -Het bericht wordt opgemaakt als een C#-object. Zie voor een volledig voor beeld C# script- [voor beeld](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het bericht in de wachtrij is beschikbaar via context. bindingen.<NAME> waar <NAME> komt overeen met de naam die is gedefinieerd in function.jsop. Als de payload JSON is, wordt de waarde in een object gedeserialiseerd.

# <a name="python"></a>[Python](#tab/python)

Raadpleeg het python- [voor beeld](#example).

# <a name="java"></a>[Java](#tab/java)

Raadpleeg Java- [kenmerken en annotaties](#attributes-and-annotations).

---

## <a name="dead-letter-queues"></a>Wacht rijen voor onbestelbare berichten
Wacht rijen voor onbestelbare berichten en uitwisselingen kunnen niet worden beheerd of geconfigureerd vanuit de RabbitMQ-trigger.  Als u wacht rijen voor onbestelbare berichten wilt gebruiken, configureert u de wachtrij vooraf die wordt gebruikt door de trigger in RabbitMQ. Raadpleeg de RabbitMQ- [documentatie](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het onderstaande voor beeld *host.jsin* het onderstaande bestand worden alleen de instellingen voor deze binding opgenomen. Zie [host.jsop referentie voor Azure functions versie](functions-host-json.md)voor meer informatie over globale configuratie-instellingen.

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|prefetchCount|30|Hiermee wordt het aantal berichten opgehaald of ingesteld dat de ontvanger van het bericht tegelijk kan aanvragen en in de cache wordt geplaatst.|
|queueName|n.v.t.| De naam van de wachtrij waaruit berichten moeten worden ontvangen.|
|connectionString|n.v.t.|De RabbitMQ-berichten wachtrij connection string. Houd er rekening mee dat de connection string hier direct wordt opgegeven en niet via een app-instelling.|
|poort|0|(wordt genegeerd als u connections Tring gebruikt) Hiermee wordt de gebruikte poort opgehaald of ingesteld. De standaard waarde is 0 die verwijst naar de standaard poort instelling van de client rabbitmq: 5672.|

## <a name="local-testing"></a>Lokaal testen

> [!NOTE]
> De Connections Tring heeft voor rang op ' hostName ', ' userName ' en ' password '. Als deze allemaal zijn ingesteld, overschrijven de Connections Tring de andere twee.

Als u lokaal test zonder een connection string, moet u de instelling hostName en "userName" en "Password" instellen, indien van toepassing in de sectie "rabbitMQ" van *host.jsop*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|Hostnaam|n.v.t.|(wordt genegeerd als u connections Tring gebruikt) <br>De hostnaam van de wachtrij (bijvoorbeeld: 10.26.45.210)|
|userName|n.v.t.|(wordt genegeerd als u connections Tring gebruikt) <br>Naam voor toegang tot de wachtrij |
|wachtwoord|n.v.t.|(wordt genegeerd als u connections Tring gebruikt) <br>Wacht woord voor toegang tot de wachtrij|


## <a name="enable-runtime-scaling"></a>Het schalen van de runtime inschakelen

De RabbitMQ-trigger kan alleen naar meerdere instanties worden uitgeschaald als de instelling voor **bewaking van runtime schaal** is ingeschakeld. 

In de portal kunt u deze instelling vinden onder   >  **runtime-instellingen** voor de configuratie-functie voor uw functie-app.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

In de CLI kunt u **runtime Scale-bewaking** inschakelen met behulp van de volgende opdracht:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>RabbitMQ-eind punt bewaken
Uw wacht rijen en uitwisselingen voor een bepaald RabbitMQ-eind punt controleren:

* De [RabbitMQ Management-invoeg toepassing](https://www.rabbitmq.com/management.html) inschakelen
* Blader naar http://{node-hostname}: 15672 en meld u aan met uw gebruikers naam en wacht woord.

## <a name="next-steps"></a>Volgende stappen

- [RabbitMQ-berichten verzenden vanuit Azure Functions (uitvoer binding)](./functions-bindings-rabbitmq-output.md)

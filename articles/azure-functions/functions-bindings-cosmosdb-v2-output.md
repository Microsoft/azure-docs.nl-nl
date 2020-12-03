---
title: Azure Cosmos DB uitvoer binding voor functies 2. x en hoger
description: Meer informatie over het gebruik van de Azure Cosmos DB uitvoer binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 454ac9a377800bd11a53250569c3e7b65bac713a
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558790"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB uitvoer binding voor Azure Functions 2. x en hoger

Met de Azure Cosmos DB uitvoer binding kunt u een nieuw document naar een Azure Cosmos DB-Data Base schrijven met behulp van de SQL-API.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor meer informatie over de installatie-en configuratie details.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Deze sectie bevat de volgende voor beelden:

* [Wachtrij trigger, een document schrijven](#queue-trigger-write-one-doc-c)
* [Wachtrij trigger, documenten schrijven met IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Wachtrij trigger, een document schrijven

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een document wordt toegevoegd aan een Data Base, met behulp van gegevens die zijn opgenomen in het bericht uit de wachtrij opslag.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrij trigger, documenten schrijven met IAsyncCollector

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een verzameling documenten wordt toegevoegd aan een Data Base, met behulp van gegevens die zijn opgenomen in een JSON van een wachtrij bericht.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voor beelden:

* [Wachtrij trigger, een document schrijven](#queue-trigger-write-one-doc-c-script)
* [Wachtrij trigger, documenten schrijven met IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Wachtrij trigger, een document schrijven

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-invoer binding voor een wachtrij die JSON ontvangt in de volgende indeling:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrij trigger, documenten schrijven met IAsyncCollector

Als u meerdere documenten wilt maken, kunt u `ICollector<T>` een binding met of `IAsyncCollector<T>` `T` een van de ondersteunde typen.

Dit voor beeld verwijst naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Dit is de function.jsvoor het volgende bestand:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Dit is de C# Script-code:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-invoer binding voor een wachtrij die JSON ontvangt in de volgende indeling:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

Voor bulksgewijs invoegen vormen de objecten eerst en voert u vervolgens de functie stringify uit. Dit is de JavaScript-code:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een document naar een Azure CosmosDB-data base schrijft als uitvoer van een functie.

De definitie van de binding wordt gedefinieerd in *function.jsvan* waar *type* is ingesteld op `cosmosDB` .

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
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Als u naar de Data Base wilt schrijven, geeft u een document object door aan de `set` methode van de data base-para meter.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Wachtrij trigger, bericht opslaan in data base via retour waarde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, sla het ene document op in de data base via de retour waarde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, sla één document op in de data base via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, meerdere documenten opslaan in de data base via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wachtrij trigger, bericht opslaan in data base via retour waarde

In het volgende voor beeld ziet u een Java-functie waarmee een document wordt toegevoegd aan een Data Base met gegevens uit een bericht in de wachtrij opslag.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-trigger, sla het ene document op in de data base via de retour waarde

In het volgende voor beeld ziet u een Java-functie waarvan de hand tekening aantekeningen bevat ```@CosmosDBOutput``` en de retour waarde van het type heeft ```String``` . Het JSON-document dat door de functie wordt geretourneerd, wordt automatisch naar de bijbehorende CosmosDB-verzameling geschreven.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-trigger, sla één document op in de data base via OutputBinding

In het volgende voor beeld ziet u een Java-functie die een document schrijft naar CosmosDB via een ```OutputBinding<T>``` uitvoer parameter. In dit voor beeld ```outputItem``` moet de para meter worden voorzien van een annotatie ```@CosmosDBOutput``` , niet de functie handtekening. Met ```OutputBinding<T>``` kunt u gebruikmaken van de binding om het document naar CosmosDB te schrijven en tegelijkertijd een andere waarde te retour neren naar de functie aanroeper, zoals een JSON-of XML-document.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-trigger, meerdere documenten opslaan in de data base via OutputBinding

In het volgende voor beeld ziet u een Java-functie die meerdere documenten naar CosmosDB schrijft via een ```OutputBinding<T>``` uitvoer parameter. In dit voor beeld ```outputItem``` wordt er met de para meter een annotatie ```@CosmosDBOutput``` en niet de functie handtekening gebruikt. De output-para meter ```outputItem``` heeft een lijst met ```ToDoItem``` objecten als het parameter type van de sjabloon. Met ```OutputBinding<T>``` kunt u uw functie gebruiken om te profiteren van de binding om de documenten te schrijven naar CosmosDB, terwijl u ook een andere waarde kunt retour neren naar de functie aanroeper, zoals een JSON-of XML-document.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBOutput` aantekening voor para meters die worden geschreven naar Cosmos db.  Het parameter type van de aantekening moet zijn ```OutputBinding<T>``` , waarbij T een systeem eigen Java-type of een POJO is.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [uitvoer-configuratie](#configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een `CosmosDB` voor beeld van een kenmerk in een methode handtekening:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De `CosmosDBOutput` aantekening is beschikbaar voor het schrijven van gegevens naar Cosmos db. U kunt de aantekening Toep assen op de functie of op een afzonderlijke functie parameter. Wanneer u gebruikt voor de functie methode, is de geretourneerde waarde van de functie wat naar Cosmos DB wordt geschreven. Als u de aantekening met een para meter gebruikt, moet het parameter type worden gedeclareerd als een `OutputBinding<T>` locatie van `T` een systeem eigen Java-type of een POJO.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `CosmosDB`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     | N.v.t. | Moet worden ingesteld op `cosmosDB`.        |
|**direction**     | N.v.t. | Moet worden ingesteld op `out`.         |
|**name**     | N.v.t. | De naam van de bindings parameter die het document in de functie vertegenwoordigt.  |
|**databaseName** | **DatabaseName**|De data base met de verzameling waarin het document is gemaakt.     |
|**collectionName** |**NaamVerzameling**  | De naam van de verzameling waar het document wordt gemaakt. |
|**createIfNotExists**  |**CreateIfNotExists**    | Een Booleaanse waarde die aangeeft of de verzameling wordt gemaakt wanneer deze niet bestaat. De standaard waarde is *False* omdat nieuwe verzamelingen worden gemaakt met gereserveerde door Voer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Wanneer `CreateIfNotExists` is ingesteld op True, wordt het pad van de partitie sleutel voor de gemaakte verzameling gedefinieerd.|
|**collectionThroughput**|**CollectionThroughput**| Wanneer `CreateIfNotExists` is True, wordt de [door Voer](../cosmos-db/set-throughput.md) van de gemaakte verzameling gedefinieerd.|
|**connectionStringSetting**    |**ConnectionStringSetting** |De naam van de app-instelling met uw Azure Cosmos DB connection string.        |
|**preferredLocations**| **PreferredLocations**| Beschrijving Hiermee worden voorkeurs locaties (regio's) gedefinieerd voor accounts met geo-gerepliceerde data bases in de Azure Cosmos DB-service. De waarden moeten worden gescheiden door komma's. Bijvoorbeeld ' vs-Oost, VS Zuid-Centraal, Europa-noord '. |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Beschrijving Als `true` `PreferredLocations` deze optie is ingesteld op, kan het gebruikmaken van [schrijf bewerkingen in meerdere regio's](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) in de Azure Cosmos DB-service. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

Wanneer u in uw functie naar de uitvoer parameter schrijft, wordt er standaard een document gemaakt in uw data base. Dit document heeft een automatisch gegenereerde GUID als document-ID. U kunt de document-ID van het uitvoer document opgeven door de `id` eigenschap op te geven in het JSON-object dat is door gegeven aan de uitvoer parameter.

> [!Note]
> Wanneer u de ID van een bestaand document opgeeft, wordt dit overschreven door het nieuwe uitvoer document.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Cosmos DB | [CosmosDB-fout codes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x. Zie [host.jsvoor Azure functions versie 2. x](functions-host-json.md)voor meer informatie over algemene configuratie-instellingen in versie 2. x.

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|GatewayMode|Gateway|De verbindings modus die wordt gebruikt door de functie bij het maken van verbinding met de Azure Cosmos DB-service. Opties zijn `Direct` en `Gateway`|
|Protocol|Https|Het verbindings protocol dat door de functie wordt gebruikt bij het verbinden met de Azure Cosmos DB-service.  Lees [hier voor een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n.v.t.|Het lease voorvoegsel dat moet worden gebruikt voor alle functies in een app.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Een Azure Cosmos DB document lezen (invoer binding)](./functions-bindings-cosmosdb-v2-input.md)

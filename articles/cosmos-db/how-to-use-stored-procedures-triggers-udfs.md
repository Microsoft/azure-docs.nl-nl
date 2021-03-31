---
title: Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies registreren en gebruiken in Azure Cosmos DB Sdk's
description: Informatie over het registeren en aanroepen van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies met behulp van de Azure Cosmos DB-SDK's
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 022a45199cfc2d467b1d0d408e86cb5d621070d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339844"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies registreren en gebruiken in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De SQL-API in Azure Cosmos DB biedt ondersteuning voor het registreren en aanroepen van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies (UDF's) die zijn geschreven in JavaScript. U kunt de SQL API [.net](sql-api-sdk-dotnet.md)-, [.net core](sql-api-sdk-dotnet-core.md)-, [Java](sql-api-sdk-java.md)-, [Java script](sql-api-sdk-node.md)-, [Node.js](sql-api-sdk-node.md)-of [python](sql-api-sdk-python.md) -sdk's gebruiken om de opgeslagen procedures te registreren en aan te roepen. Zodra u een of meer opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies hebt gedefinieerd, kunt u deze in de [Azure Portal](https://portal.azure.com/) laden en weer geven met behulp van Data Explorer.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>Opgeslagen procedures uitvoeren

Opgeslagen procedures worden geschreven met behulp van JavaScript. U kunt er items in een Azure Cosmos-container mee maken, bijwerken, lezen en verwijderen en query's op items uitvoeren. Zie [opgeslagen procedures schrijven in azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) artikel voor meer informatie over het schrijven van opgeslagen procedures in azure Cosmos db.

De volgende voorbeelden laten zien hoe u een opgeslagen procedure registreert en aanroept met behulp van de Azure Cosmos DB-SDK's. Raadpleeg [Een document maken](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) als de bron voor deze opgeslagen procedure is opgeslagen als `spCreateToDoItem.js`.

> [!NOTE]
> Bij het uitvoeren van een opgeslagen procedure voor gepartitioneerde containers moet een waarde voor de partitiesleutel worden opgegeven in de aanvraagopties. Opgeslagen procedures zijn altijd gerelateerd aan een partitiesleutel. Items met een andere partitiesleutelwaarde zijn niet zichtbaar voor de opgeslagen procedure. Dit geldt ook voor triggers.

### <a name="stored-procedures---net-sdk-v2"></a>Opgeslagen procedures-.NET SDK v2

In het volgende voor beeld ziet u hoe u een opgeslagen procedure kunt registreren met behulp van de .NET SDK v2:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

De volgende code laat zien hoe u een opgeslagen procedure aanroept met behulp van de .NET SDK v2:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>Opgeslagen procedures-.NET SDK v3

In het volgende voor beeld ziet u hoe u een opgeslagen procedure kunt registreren met behulp van de .NET SDK V3:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

De volgende code laat zien hoe u een opgeslagen procedure aanroept met behulp van de .NET SDK V3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>Opgeslagen procedures - Java SDK

Het volgende voorbeeld laat zien hoe u een opgeslagen procedure registreert met behulp van de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

De volgende code laat zien hoe u een opgeslagen procedure aanroept met behulp van de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Opgeslagen procedures - JavaScript SDK

Het volgende voorbeeld laat zien hoe u een opgeslagen procedure registreert met behulp van de JavaScript SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

De volgende code laat zien hoe u een opgeslagen procedure aanroept met behulp van de JavaScript SDK:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Opgeslagen procedures - Python SDK

In het volgende voor beeld ziet u hoe u een opgeslagen procedure kunt registreren met behulp van de python-SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()

sproc = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
created_sproc = container.scripts.create_stored_procedure(body=sproc) 
```

De volgende code laat zien hoe u een opgeslagen procedure aanroept met behulp van de python-SDK:

```python
import uuid

new_id= str(uuid.uuid4())

# Creating a document for a container with "id" as a partition key.

new_item =   {
      "id": new_id, 
      "category":"Personal",
      "name":"Groceries",
      "description":"Pick up strawberries",
      "isComplete":False
   }
result = container.scripts.execute_stored_procedure(sproc=created_sproc,params=[[new_item]], partition_key=new_id) 
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Voorafgaande triggers uitvoeren

De volgende voorbeelden laten zien hoe u een voorafgaande trigger registreert en aanroept met behulp van de Azure Cosmos DB-SDK's. Raadpleeg [Voorbeeld van voorafgaande trigger](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) als de bron voor deze voorafgaande trigger is opgeslagen als `trgPreValidateToDoItemTimestamp.js`.

Bij het uitvoeren worden voorafgaande triggers doorgegeven in het object RequestOptions (aanvraagopties) door `PreTriggerInclude` op te geven en vervolgens de naam van de trigger door te geven in een List-object (lijstobject).

> [!NOTE]
> Ook al wordt de naam van de trigger doorgegeven als een lijst, u kunt slechts één trigger per bewerking uitvoeren.

### <a name="pre-triggers---net-sdk-v2"></a>Pretriggers-.NET SDK v2

De volgende code laat zien hoe u een pre-trigger kunt registreren met behulp van de .NET SDK v2:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

De volgende code laat zien hoe u een pre-trigger aanroept met behulp van de .NET SDK v2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Pretriggers-.NET SDK v3

De volgende code laat zien hoe u een pre-trigger kunt registreren met behulp van de .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

De volgende code laat zien hoe u een pre-trigger aanroept met behulp van de .NET SDK V3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Voorafgaande triggers - Java SDK

De volgende code laat zien hoe u een voorafgaande trigger registreert met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

De volgende code laat zien hoe u een voorafgaande trigger aanroept met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Voorafgaande triggers - JavaScript SDK

De volgende code laat zien hoe u een voorafgaande trigger registreert met de JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

De volgende code laat zien hoe u een voorafgaande trigger aanroept met de JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Voorafgaande triggers - Python SDK

De volgende code laat zien hoe u een voorafgaande trigger registreert met de Python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

De volgende code laat zien hoe u een voorafgaande trigger aanroept met de Python SDK:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Navolgende triggers uitvoeren

De volgende voorbeelden laten zien hoe u een navolgende trigger registreert met behulp van de Azure Cosmos DB-SDK's. Raadpleeg [Voorbeeld van navolgende trigger](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) als de bron voor deze navolgende trigger is opgeslagen als `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk-v2"></a>Post-triggers-.NET SDK v2

De volgende code laat zien hoe u een post-trigger kunt registreren met behulp van de .NET SDK v2:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

De volgende code laat zien hoe u een post-trigger aanroept met behulp van de .NET SDK v2:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Post-triggers-.NET SDK v3

De volgende code laat zien hoe u een post-trigger kunt registreren met behulp van de .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

De volgende code laat zien hoe u een post-trigger aanroept met behulp van de .NET SDK V3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Navolgende triggers - Java SDK

De volgende code laat zien hoe u een navolgende trigger registreert met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

De volgende code laat zien hoe u een navolgende trigger aanroept met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Navolgende triggers - JavaScript SDK

De volgende code laat zien hoe u een navolgende trigger registreert met de JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

De volgende code laat zien hoe u een navolgende trigger aanroept met de JavaScript SDK:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Navolgende triggers - Python SDK

De volgende code laat zien hoe u een navolgende trigger registreert met de Python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPostValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPostValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

De volgende code laat zien hoe u een navolgende trigger aanroept met de Python SDK:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'post_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>Werken met door de gebruiker gedefinieerde functies

De volgende voorbeelden laten zien hoe u een door de gebruiker gedefinieerde functie registreert met behulp van de Azure Cosmos DB-SDK's. Raadpleeg [Voorbeeld van door de gebruiker gedefinieerde functie](how-to-write-stored-procedures-triggers-udfs.md#udfs) als de bron voor deze door de gebruiker gedefinieerde functie is opgeslagen als `udfTax.js`.

### <a name="user-defined-functions---net-sdk-v2"></a>Door de gebruiker gedefinieerde functies-.NET SDK v2

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie registreert met behulp van de .NET SDK v2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie aanroept met behulp van de .NET SDK v2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Door de gebruiker gedefinieerde functies-.NET SDK v3

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie registreert met behulp van de .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie aanroept met behulp van de .NET SDK V3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Door de gebruiker gedefinieerde functies - Java SDK

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie registreert met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie aanroept met de Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Door de gebruiker gedefinieerde functies - JavaScript SDK

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie registreert met de JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie aanroept met de JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Door de gebruiker gedefinieerde functies - Python SDK

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie registreert met de Python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/udfTax.js') as file:
    file_contents = file.read()
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.scripts.create_user_defined_function(udf_definition)
```

De volgende code laat zien hoe u een door de gebruiker gedefinieerde functie aanroept met de Python SDK:

```python
results = list(container.query_items(
    'query': 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over concepten en het schrijven of gebruiken van procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB:

- [Werken met opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies registreren en gebruiken in Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Werken met de JavaScript-query-API in Azure Cosmos DB](javascript-query-api.md)
- [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies schrijven in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Opgeslagen procedures en triggers schrijven met de JavaScript-query-API in Azure Cosmos DB](how-to-write-javascript-query-api.md)

---
title: Migreer uw toepassing om de Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos) te gebruiken
description: Leer hoe u uw bestaande Java-toepassing bijwerkt van met de oudere Azure Cosmos DB Java-Sdk's naar het nieuwere Java SDK 4,0 (com. Azure. Cosmos-pakket) voor Core-API (SQL).
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: e537c964d6063b76df63b3d80c5ef72b1ea56c92
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600247"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Uw toepassing migreren om de Azure Cosmos DB Java SDK v4 te gebruiken
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Raadpleeg voor meer informatie over deze SDK de Azure Cosmos DB Java SDK v4- [release opmerkingen](sql-api-sdk-java-v4.md), de [maven-opslag plaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)en Azure Cosmos DB Java SDK v4 [Troubleshooting Guide (Engelstalig](troubleshoot-java-sdk-v4-sql.md)).
>

In dit artikel wordt uitgelegd hoe u een upgrade uitvoert van uw bestaande Java-toepassing die gebruikmaakt van een oudere Azure Cosmos DB Java-SDK naar de nieuwere Azure Cosmos DB Java SDK 4,0 voor Core-API (SQL). Azure Cosmos DB Java SDK v4 correspondeert met het `com.azure.cosmos` pakket. U kunt de instructies in dit document gebruiken als u uw toepassing migreert van een van de volgende Azure Cosmos DB Java-Sdk's: 

* Java SDK 2. x. x synchroniseren
* Asynchrone Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB en pakket toewijzingen van de Java-SDK

De volgende tabel geeft een lijst van verschillende Azure Cosmos DB Java-Sdk's, de pakket naam en de release-informatie:

| Java-SDK| Releasedatum | Gebundelde Api's   | Maven jar  | Naam Java-pakket  |API-referentiemateriaal   | Releaseopmerkingen  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Juni 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Release opmerkingen](sql-api-sdk-async-java.md) |
| Synchronisatie 2. x. x     | Sept 2018    | Synchroniseren   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Release opmerkingen](sql-api-sdk-java.md)  |
| 3. x. x    | Juli 2019    | Async (reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Juni 2020   | Async (reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Wijzigingen in de implementatie van het SDK-niveau

Hier volgen de belangrijkste implementatie verschillen tussen de verschillende Sdk's:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava wordt vervangen door reactor in Azure Cosmos DB Java SDK versie 3. x. x en 4,0

Als u niet bekend bent met asynchrone programmering of reactieve programmering, raadpleegt u de [hand leiding voor het reactor patroon](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) voor een inleiding tot async-programmering en project reactor. Deze hand leiding kan nuttig zijn als u Azure Cosmos DB Sync Java SDK 2. x. x of Azure Cosmos DB Java SDK 3. x. x Sync API in het verleden hebt gebruikt.

Als u Azure Cosmos DB async-SDK 2. x. x hebt gebruikt en u van plan bent om te migreren naar de 4,0 SDK, raadpleegt u de [hand leiding voor reactor en RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) voor richt lijnen voor het converteren van RxJava-code voor het gebruik van reactor.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 de modus directe connectiviteit heeft in zowel async-als synchronisatie-Api's

Als u Azure Cosmos DB Sync Java SDK 2. x. x gebruikt, moet u er rekening mee houden dat de directe verbindings modus op basis van TCP (in tegens telling tot HTTP) is geïmplementeerd in Azure Cosmos DB Java SDK 4,0 voor zowel de async-als de synchronisatie-Api's.

## <a name="api-level-changes"></a>Wijzigingen in het API-niveau

Hieronder vindt u een overzicht van de API-wijzigingen in Azure Cosmos DB Java SDK 4. x. x vergeleken met de vorige Sdk's (Java SDK 3. x. x, async Java SDK 2. x. x en Java SDK 2. x. x synchroniseren):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Naamgevings conventies voor Azure Cosmos DB Java SDK":::

* De Azure Cosmos DB Java SDK 3. x. x en 4,0 verwijzen naar de client resources als `Cosmos<resourceName>` . Bijvoorbeeld,, `CosmosClient` `CosmosDatabase` , `CosmosContainer` . Overwegende dat in versie 2. x. x de Azure Cosmos DB Java-Sdk's geen uniform naam schema hebben.

* Azure Cosmos DB Java SDK 3. x. x en 4,0 bieden synchronisatie-en async-Api's.

  * **Java SDK 4,0** : alle klassen behoren tot de API Sync, tenzij de naam van de klasse wordt toegevoegd `Async` `Cosmos` .

  * **Java SDK 3. x. x**: alle klassen behoren tot de ASYNC-API tenzij de naam van de klasse wordt toegevoegd `Async` `Cosmos` .

  * **Asynchrone Java SDK 2. x. x**: de namen van klassen zijn vergelijkbaar met het synchroniseren van Java SDK 2. x. x, maar de naam begint met *async*.

### <a name="hierarchical-api-structure"></a>Hiërarchische API-structuur

Azure Cosmos DB Java SDK 4,0 en 3. x. x introduceren een hiërarchische API-structuur waarmee de clients, data bases en containers op een geneste manier worden georganiseerd, zoals wordt weer gegeven in het volgende SDK-code fragment van 4,0:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

In versie 2. x. x van de Azure Cosmos DB Java SDK worden alle bewerkingen voor resources en documenten uitgevoerd via het client exemplaar.

### <a name="representing-documents"></a>Documenten vertegenwoordigen

In Azure Cosmos DB Java SDK 4,0 zijn aangepaste POJO en `JsonNodes` de twee opties om de documenten te lezen en te schrijven vanuit Azure Cosmos db.

In de Azure Cosmos DB Java SDK 3. x. x wordt het `CosmosItemProperties` object beschikbaar gemaakt door de open bare API en geleverd als een document weergave. Deze klasse wordt niet meer openbaar weer gegeven in versie 4,0.

### <a name="imports"></a>Rusland

* De Azure Cosmos DB Java SDK 4,0-pakketten beginnen met `com.azure.cosmos`
* Azure Cosmos DB Java SDK 3. x. x-pakketten beginnen met `com.azure.data.cosmos`
* Azure Cosmos DB Java SDK 2. x. x Sync API-pakketten beginnen met `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4,0 worden verschillende klassen in een genest pakket geplaatst `com.azure.cosmos.models` . Enkele van deze pakketten zijn:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * De asynchrone API analoog voor alle bovenstaande pakketten
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... Heap.

### <a name="accessors"></a>Accessors

Azure Cosmos DB Java SDK 4,0 bevat `get` en `set` methoden om toegang te krijgen tot de exemplaar leden. Bijvoorbeeld het `CosmosContainer` exemplaar heeft en- `container.getId()` `container.setId()` methoden.

Dit wijkt af van Azure Cosmos DB Java SDK 3. x. x, waarmee een fluent interface wordt weer gegeven. Bijvoorbeeld: een `CosmosSyncContainer` exemplaar `container.id()` dat is overbelast om de waarde op te halen of in te stellen `id` .

## <a name="code-snippet-comparisons"></a>Vergelijking van code fragmenten

### <a name="create-resources"></a>Resources maken

Het volgende code fragment toont de verschillen in de manier waarop resources worden gemaakt tussen de 4,0, 3. x. x async Api's en 2. x. x Sync Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Item bewerkingen

Het volgende code fragment toont de verschillen in de manier waarop item bewerkingen worden uitgevoerd tussen de 4,0, 3. x. x async Api's en 2. x. x Sync Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indexeren

Het volgende code fragment toont de verschillen in de manier waarop indexering wordt gemaakt tussen de 4,0, 3. x. x async Api's en 2. x. x Sync Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Opgeslagen procedures

Het volgende code fragment toont de verschillen in de manier waarop opgeslagen procedures worden gemaakt tussen de 4,0, 3. x. x async Api's en 2. x. x Sync Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Feed wijzigen

Het volgende code fragment toont de verschillen in de manier waarop wijzigings feed bewerkingen worden uitgevoerd tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

* Deze functie wordt niet ondersteund voor de synchronisatie van Java SDK v2. 
---

### <a name="container-level-time-to-livettl"></a>Time-to-Live (TTL) op container niveau

Het volgende code fragment toont de verschillen in het maken van time-to-Live voor gegevens in de container met behulp van de 4,0-, 3. x. x async-Api's en 2. x. x-synchronisatie-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Item niveau time-to-Live (TTL)

Het volgende code fragment toont de verschillen in het maken van time-to-Live voor een item met de 4,0-, 3. x. x async Api's en 2. x. x Sync-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x Sync API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Volgende stappen

* [Een Java-app bouwen](create-sql-api-java.md) om Azure Cosmos DB SQL-API-gegevens te beheren met de v4-SDK
* Meer informatie over de [op reactor gebaseerde Java-sdk's](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Meer informatie over het converteren van RxJava async-code naar een reactor async-code met de [hand leiding voor reactor tegenover RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)

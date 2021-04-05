---
title: Migreer uw toepassing voor het gebruik van de Azure Cosmos DB .NET SDK 3,0 (com. Azure. Cosmos)
description: Meer informatie over hoe u uw bestaande .NET-toepassing bijwerkt van de v2-SDK naar het nieuwere .NET SDK v3 (com. Azure. Cosmos-pakket) voor Core-API (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334018"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Uw toepassing migreren om de Azure Cosmos DB .NET SDK v3 te gebruiken
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Voor meer informatie over de Azure Cosmos DB .NET SDK v3 raadpleegt u de [release opmerkingen](sql-api-sdk-dotnet-standard.md), de [.net github-opslag plaats](https://github.com/Azure/azure-cosmos-dotnet-v3), de .NET SDK v3- [prestatie tips](performance-tips-dotnet-sdk-v3-sql.md)en de [hand leiding](troubleshoot-dot-net-sdk.md)voor het oplossen van problemen.
>

In dit artikel worden enkele aandachtspunten beschreven voor het upgraden van uw bestaande .NET-toepassing naar de nieuwere Azure Cosmos DB .NET SDK v3 voor Core-API (SQL). Azure Cosmos DB .NET SDK v3 komt overeen met de micro soft. Azure. Cosmos-naam ruimte. U kunt de informatie in dit document gebruiken als u uw toepassing migreert van een van de volgende Azure Cosmos DB .NET-Sdk's:

* Azure Cosmos DB .NET Framework SDK v2 voor SQL-API
* Azure Cosmos DB .NET Core SDK v2 voor SQL-API

De instructies in dit artikel helpen u ook bij het migreren van de volgende externe bibliotheken die nu deel uitmaken van de Azure Cosmos DB .NET SDK v3 for core (SQL) API:

* .NET Change feed-processor bibliotheek 2,0
* .NET-bibliotheek voor bulk-uitvoerder 1,1 of hoger

## <a name="whats-new-in-the-net-v3-sdk"></a>Wat is er nieuw in de .NET v3 SDK

De V3 SDK bevat een aantal verbeteringen op het gebied van bruikbaarheid en prestaties, waaronder:

* Naamgeving van intuïtieve programmeer modellen
* .NET Standard 2,0 * *
* Betere prestaties via stream API-ondersteuning
* Fluent-hiërarchie die de nood zaak van URI-Factory vervangt
* Ingebouwde ondersteuning voor de processor bibliotheek voor wijzigings invoer
* Ingebouwde ondersteuning voor bulk bewerkingen
* Mockabale-Api's voor eenvoudiger eenheids tests
* Transactionele batch en razendsnelle ondersteuning
* Pluggable serialisatiefunctie
* Niet-gepartitioneerde en automatisch schalen containers schalen

* * De SDK streeft naar .NET Standard 2,0, die de bestaande Azure Cosmos DB .NET Framework en .NET core-Sdk's integreert in één .NET-SDK. U kunt de .NET SDK gebruiken in elk platform dat .NET Standard 2,0 implementeert, met inbegrip van uw .NET Framework 4.6.1 +-en .NET Core 2.0 +-toepassingen.

De meeste netwerken, de logica voor nieuwe pogingen en lagere niveaus van de SDK blijven grotendeels ongewijzigd.

**De Azure Cosmos DB .NET SDK v3 is nu open source.** We raden u aan om pull-aanvragen te ontvangen en problemen op te lossen bij het vastleggen van feedback op [github.](https://github.com/Azure/azure-cosmos-dotnet-v3/) We werken aan het gebruik van alle functies waarmee de klant ervaring wordt verbeterd.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Waarom migreren naar de .NET v3 SDK

Naast de vele verbeteringen op het gebied van bruikbaarheid en prestaties, worden nieuwe functie investeringen die zijn gemaakt in de nieuwste SDK, niet teruggezet naar oudere versies.

Hoewel er geen onmiddellijke plannen zijn om [ondersteuning voor de 2,0-sdk's buiten gebruik te stellen](sql-api-sdk-dotnet.md), zullen de sdk's in de toekomst worden vervangen door nieuwere versies. de SDK gaat dan naar de onderhouds modus. Voor de beste ontwikkel ervaring raden we u aan altijd te beginnen met de meest recente ondersteunde versie van SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Belang rijke naam wijzigingen van v2 SDK naar v3 SDK

De volgende naam wijzigingen zijn toegepast in de .NET 3,0 SDK om af te stemmen met de API-naamgevings conventies voor de core-API (SQL):

* `DocumentClient` is gewijzigd in `CosmosClient`
* `Collection` is gewijzigd in `Container`
* `Document` is gewijzigd in `Item`

De naam van alle Resource objecten wordt gewijzigd met aanvullende eigenschappen, die de resource naam voor de duidelijkheid bevat.

Hier volgen enkele van de belangrijkste naam wijzigingen:

| .NET v2 SDK | .NET v3-SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Klassen vervangen op .NET v3 SDK

De volgende klassen zijn vervangen op de 3,0-SDK:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

De Microsoft.Azure.Documents. De klasse UriFactory is vervangen door het Fluent-ontwerp. Dankzij het Fluent-ontwerp worden Url's intern samengesteld en kan één `Container` object worden door gegeven in plaats van een `DocumentClient` , `DatabaseName` en `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Wijzigingen in de item-ID genereren

Item-ID wordt niet meer automatisch ingevuld in de .NET v3-SDK. Daarom moet de item-ID specifiek een gegenereerde ID bevatten. Bekijk het volgende voor beeld:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Standaard gedrag voor verbindings modus is gewijzigd

De SDK v3 is nu standaard ingesteld op direct + TCP-verbindings modi vergeleken met de vorige v2-SDK, die standaard is ingesteld op gateway-en HTTPS-verbindingen. Deze wijziging biedt verbeterde prestaties en schaal baarheid.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Wijzigingen in FeedOptions (QueryRequestOptions in v 3.0 SDK)

De `FeedOptions` naam van de klasse in SDK v2 is nu gewijzigd `QueryRequestOptions` in in de SDK v3 en binnen de klasse hebben verschillende eigenschappen wijzigingen in naam en/of standaard waarde of volledig verwijderd.  

`FeedOptions.MaxDegreeOfParallelism` de naam van is gewijzigd in `QueryRequestOptions.MaxConcurrency` en de standaard waarde en het bijbehorende gedrag blijven hetzelfde, bewerkingen uitvoeren aan client zijde tijdens parallelle query uitvoering wordt serieel zonder parallellisme uitgevoerd.

`FeedOptions.EnableCrossPartitionQuery` is verwijderd en het standaard gedrag in SDK 3,0 is dat kruis partitie query's worden uitgevoerd zonder dat de eigenschap specifiek moet worden ingeschakeld.

`FeedOptions.PopulateQueryMetrics` is standaard ingeschakeld met de resultaten die aanwezig zijn in de eigenschap Diagnostics van het antwoord.

`FeedOptions.RequestContinuation` is nu gepromoveerd tot de query methoden zelf.

De volgende eigenschappen zijn verwijderd:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Een client maken

De .NET SDK V3 biedt een Fluent- `CosmosClientBuilder` klasse die de nood zaak van de SDK v2 URI Factory vervangt.

In het volgende voor beeld wordt een nieuw `CosmosClientBuilder` met een sterke ConsistencyLevel en een lijst met voorkeurs locaties gemaakt:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>De Change feed-processor-Api's rechtstreeks vanuit de V3 SDK gebruiken

De V3 SDK heeft ingebouwde ondersteuning voor de Change feed processor Api's, zodat u dezelfde SDK kunt gebruiken voor het bouwen van uw toepassing en het wijzigen van de implementatie van de feed-processor. Voorheen moest u een afzonderlijke processor bibliotheek voor wijzigings invoer gebruiken.

Zie voor meer informatie [migreren vanuit de bibliotheek voor het wijzigen van de feed-processor naar de Azure Cosmos db .net v3 SDK](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>De bulk-uitvoerder bibliotheek rechtstreeks vanuit de V3 SDK gebruiken

De V3 SDK heeft ingebouwde ondersteuning voor de bibliotheek voor bulk-uitvoerder, zodat u dezelfde SDK kunt gebruiken voor het bouwen van uw toepassing en het uitvoeren van bulk bewerkingen. Voorheen moest u een afzonderlijke bibliotheek voor bulk-uitvoerder gebruiken.

Zie [How to migrate from the bulk Center Library to bulksgewijze support in azure Cosmos db .net v3 SDK](how-to-migrate-from-bulk-executor-library.md) voor meer informatie.

## <a name="code-snippet-comparisons"></a>Vergelijking van code fragmenten

Het volgende code fragment toont de verschillen in de manier waarop resources worden gemaakt tussen de .NET v2-en V3-Sdk's:

## <a name="database-operations"></a>Databasebewerkingen

### <a name="create-a-database"></a>Een database maken

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Een database lezen op id

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Een database verwijderen

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Container bewerkingen

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Een container maken (automatisch schalen + time to Live met verval datum)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Container eigenschappen lezen

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Een container verwijderen

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Item-en query bewerkingen

### <a name="create-an-item"></a>Een item maken

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Alle items in een container lezen

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Query-items

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Een item verwijderen

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Volgende stappen

* [Een console-app maken](sql-api-get-started.md) voor het beheren van Azure Cosmos DB SQL-API-gegevens met behulp van de V3 SDK
* Meer informatie over [wat u kunt doen met de V3 SDK](sql-api-dotnet-v3sdk-samples.md)

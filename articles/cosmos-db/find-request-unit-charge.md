---
title: De kosten van de aanvraag eenheid (RU) zoeken voor een SQL-query in Azure Cosmos DB
description: Meer informatie over het vinden van de kosten voor de aanvraag eenheid (RU) voor SQL-query's die worden uitgevoerd op een Azure Cosmos-container. U kunt de Azure Portal, .NET, Java, python en Node.js talen gebruiken om de RU-kosten te vinden.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201299"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>De toeslag voor aanvraag eenheden zoeken voor bewerkingen die zijn uitgevoerd in Azure Cosmos DB SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en uitgedrukt in aanvraageenheden (kortweg RU's). Kosten voor aanvragen zijn de aanvraag eenheden die worden verbruikt door alle database bewerkingen. U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs. Zie het artikel [aanvraag eenheden en het bijbehorende overwegingen](request-units.md) voor meer informatie.

In dit artikel worden de verschillende manieren beschreven waarop u het gebruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op basis van een container in azure Cosmos DB SQL-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](find-request-unit-charge-mongodb.md), [CASSANDRA-API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)en [Table-API](find-request-unit-charge-table.md) artikelen om de kosten voor de ru/s te vinden.

Op dit moment kunt u dit verbruik alleen meten met behulp van de Azure Portal of door de reactie die via een van de Sdk's wordt teruggestuurd via Azure Cosmos DB te controleren. Als u de SQL-API gebruikt, hebt u meerdere opties voor het vinden van het RU-verbruik voor een bewerking in een Azure Cosmos-container.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account) en voer het in met gegevens of selecteer een bestaand Azure Cosmos-account dat al gegevens bevat.

1. Ga naar het deel venster **Data Explorer** en selecteer vervolgens de container waaraan u wilt werken.

1. Selecteer **New SQL Query**.

1. Voer een geldige query in en selecteer **query uitvoeren**.

1. Selecteer **query statistieken** om de werkelijke aanvraag kosten weer te geven voor de aanvraag die u hebt uitgevoerd.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Scherm opname van de kosten van een SQL-query-aanvraag in de Azure Portal":::

## <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Objecten die worden geretourneerd door de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) , bieden een `RequestCharge` eigenschap:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objecten die worden geretourneerd door de [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) , bieden een `RequestCharge` eigenschap:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Zie [Quick Start: een .net-Web-app bouwen met behulp van een SQL-API-account in azure Cosmos DB](create-sql-api-dotnet.md)voor meer informatie.

---

## <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Objecten die worden geretourneerd door de [Java-SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) , bieden een `getRequestCharge()` methode:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Zie [Quick Start: een Java-toepassing bouwen met behulp van een Azure Cosmos DB SQL-API-account](create-sql-api-java.md)voor meer informatie.

## <a name="use-the-nodejs-sdk"></a>De Node.js SDK gebruiken

Objecten die worden geretourneerd door de [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) geven een `headers` SubObject weer waarmee alle headers worden toegewezen die worden geretourneerd door de onderliggende http API. De aanvraag kosten zijn beschikbaar onder de `x-ms-request-charge` volgende sleutel:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Zie [Quick Start: een Node.js-app bouwen met behulp van een Azure Cosmos DB SQL-API-account](create-sql-api-nodejs.md)voor meer informatie. 

## <a name="use-the-python-sdk"></a>De Python-SDK gebruiken

Het `CosmosClient` object van de [PYTHON-SDK](https://pypi.org/project/azure-cosmos/) geeft een `last_response_headers` woorden lijst weer waarin alle headers worden toegewezen die worden geretourneerd door de onderliggende http API voor de laatste bewerking die is uitgevoerd. De aanvraag kosten zijn beschikbaar onder de `x-ms-request-charge` volgende sleutel:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Zie [Quick Start: een python-app bouwen met een Azure Cosmos DB SQL-API-account](create-sql-api-python.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Wereldwijd schalen van ingerichte doorvoer](./request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Door Voer voor een container inrichten](how-to-provision-container-throughput.md)
* [Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB](use-metrics.md)

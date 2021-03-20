---
title: Metrische gegevens van SQL-query prestaties & uitvoerings metrieken ophalen
description: Meer informatie over het ophalen van metrische gegevens voor SQL-query uitvoering en het profiel SQL-query prestaties van Azure Cosmos DB aanvragen.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097393"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Metrische gegevens over SQL-query uitvoering ophalen en query prestaties analyseren met behulp van .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u de prestaties van SQL-query's in Azure Cosmos DB kunt profileren. Deze profilering kan worden uitgevoerd met `QueryMetrics` opgehaald uit de .NET SDK en wordt hier beschreven. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) is een sterk getypeerd object met informatie over de uitvoering van de back-end-query. Deze metrische gegevens worden uitgebreid beschreven in het artikel [afstemmen op query prestaties](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>De para meter FeedOptions instellen

Alle Overloads voor [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) nemen in een optionele [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) -para meter. Met deze optie kan het uitvoeren van query's worden afgestemd en de para meters worden uitgevoerd. 

Als u de metrische gegevens voor de uitvoering van SQL-query's wilt verzamelen, moet u de para meter [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) in de [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) in te stellen op `true` . Als deze optie is ingesteld op True, wordt de `PopulateQueryMetrics` relevante waarde in het bestand `FeedResponse` opgenomen `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Metrische query gegevens ophalen met AsDocumentQuery ()
In het volgende code voorbeeld ziet u hoe u metrische gegevens kunt ophalen met behulp van de methode [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics samen voegen

In de vorige sectie ziet u dat er meerdere aanroepen naar de methode [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) zijn. Elke aanroep heeft een `FeedResponse` object geretourneerd met een woorden lijst van `QueryMetrics` ; één voor elke voortzetting van de query. In het volgende voor beeld ziet u hoe u deze samenvoegt `QueryMetrics` met behulp van LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Metrische query gegevens groeperen op partitie-ID

U kunt de `QueryMetrics` partitie-id groeperen. Door te groeperen op partitie-ID kunt u zien of een specifieke partitie prestatie problemen veroorzaakt in vergelijking met andere. In het volgende voor beeld ziet u hoe u groepeert `QueryMetrics` met LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ op DocumentQuery

U kunt ook `FeedResponse` een LINQ-query ophalen met behulp van de- `AsDocumentQuery()` methode:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Dure Query's

U kunt de door elke query verbruikte aanvraag eenheden vastleggen voor het onderzoeken van dure query's of query's die hoge door Voer gebruiken. U kunt de aanvraag kosten ophalen met behulp van de eigenschap [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) in `FeedResponse` . Zie het artikel over de [aanvraag eenheid zoeken](find-request-unit-charge.md) voor meer informatie over het verkrijgen van de aanvraag kosten met behulp van de Azure Portal en verschillende sdk's.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>De uitvoerings tijd van de query ophalen

Bij het berekenen van de tijd die nodig is om een query aan client zijde uit te voeren, moet u ervoor zorgen dat u alleen de tijd voor het aanroepen `ExecuteNextAsync` van de methode en niet andere onderdelen van de code basis opneemt. Alleen deze oproepen helpen u bij het berekenen van hoe lang de uitvoering van de query heeft geduurd, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Scan query's (vaak langzaam en duur)

Een scan query verwijst naar een query die niet door de index is geleverd, omdat er veel documenten worden geladen voordat de resultatenset wordt geretourneerd.

Hieronder ziet u een voor beeld van een scan query:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Het filter van deze query maakt gebruik van de systeem functie die niet vanuit de index wordt geleverd. Als deze query wordt uitgevoerd op een grote verzameling, zijn de volgende query gegevens voor de eerste voortzetting gemaakt:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Let op de volgende waarden in de uitvoer van de query gegevens:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Met deze query zijn 60.951 documenten geladen, die het totaal van 399.998.938 bytes hebben. Het laden van dit aantal bytes resulteert in hoge kosten of kosten voor aanvraag eenheden. Het duurt ook lang om de query uit te voeren. Dit is duidelijk met de totale tijd die is besteed aan de eigenschap:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Dit betekent dat de query 4,5 seconden duurde om uit te voeren (en dat er maar één voortzetting was).

Vermijd het gebruik van boven in het filter om deze voorbeeld query te optimaliseren. In plaats daarvan `c.description` moeten de waarden worden ingevoegd in hoofd letters wanneer er documenten worden gemaakt of bijgewerkt. De query wordt vervolgens: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Deze query kan nu vanuit de index worden geleverd.

Zie het artikel [afstemmen op query prestaties](./sql-api-query-metrics.md) voor meer informatie over het afstemmen van query prestaties.

## <a name="references"></a><a id="References"></a>Referenties

- [SQL-specificatie voor Azure Cosmos DB](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Volgende stappen

- [Queryprestaties afstemmen](sql-api-query-metrics.md)
- [Overzicht van indexeren](index-overview.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
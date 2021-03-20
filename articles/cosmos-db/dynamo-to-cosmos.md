---
title: Uw toepassing migreren van Amazon DynamoDB naar Azure Cosmos DB
description: Meer informatie over het migreren van uw .NET-toepassing vanuit de DynamoDB van Amazon naar Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334222"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Uw toepassing migreren van Amazon DynamoDB naar Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB is een schaal bare, wereld wijd gedistribueerde, volledig beheerde data base. Het biedt gegarandeerde lage latentie toegang tot uw gegevens. Zie het artikel [overzicht](introduction.md) voor meer informatie over Azure Cosmos db. In dit artikel wordt beschreven hoe u uw .NET-toepassing migreert van DynamoDB naar Azure Cosmos DB met minimale code wijzigingen.

## <a name="conceptual-differences"></a>Conceptuele verschillen

Hier volgen de belangrijkste conceptuele verschillen tussen Azure Cosmos DB en DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Niet van toepassing|  Database |
|Tabel      |  Verzameling |
|  Item |  Document |
|Kenmerk|Veld|
|Secundaire index|Secundaire index|
|Primaire sleutel-partitie sleutel|Partitiesleutel|
|Primaire sleutel – Sorteer sleutel| Niet vereist |
|Stream|ChangeFeed|
|Reken eenheid schrijven|Aanvraag eenheid (flexibel, kan worden gebruikt voor lees-of schrijf bewerkingen)|
|Reken eenheid lezen    |Aanvraag eenheid (flexibel, kan worden gebruikt voor lees-of schrijf bewerkingen)|
|Globale tabellen| Niet vereist. U kunt de regio rechtstreeks selecteren tijdens het inrichten van het Azure Cosmos-account (u kunt de regio later wijzigen)|

## <a name="structural-differences"></a>Structurele verschillen

Azure Cosmos DB heeft een eenvoudiger JSON-structuur in vergelijking met die van DynamoDB. In het volgende voor beeld ziet u de verschillen

**DynamoDB**:

Het volgende JSON-object vertegenwoordigt de gegevens indeling in DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

Het volgende JSON-object vertegenwoordigt de gegevens indeling in Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Uw gegevens migreren

Er zijn verschillende opties beschikbaar voor het migreren van uw gegevens naar Azure Cosmos DB. Zie de [Opties voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB](cosmosdb-migrationchoices.md) -artikel voor meer informatie.

## <a name="migrate-your-code"></a>Uw code migreren

Dit artikel is een bereik voor het migreren van de code van een toepassing naar Azure Cosmos DB. Dit is het belangrijkste aspect van database migratie. Om het leer proces te verminderen, bevatten de volgende secties een code vergelijking naast elkaar tussen Amazon DynamoDB en het equivalent code fragment van Azure Cosmos DB.

Als u de bron code wilt downloaden, moet u de volgende opslag plaats klonen:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Vereisten

- .NET Framework 4.7.2
- Visual Studio 2019
- Toegang tot Azure Cosmos DB SQL-API-account
- Lokale installatie van Amazon DynamoDB
- Java 8
- De Download bare versie van Amazon DynamoDB uitvoeren op poort 8000 (u kunt de code wijzigen en configureren)

### <a name="set-up-your-code"></a>Uw code instellen

Voeg het volgende ' NuGet-pakket ' toe aan uw project:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Verbinding maken

**DynamoDB**:

In Amazon DynamoDB wordt de volgende code gebruikt om verbinding te maken:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Werk uw code bij om verbinding te maken met Azure Cosmos DB:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optimaliseer de verbinding in Azure Cosmos DB**

Met Azure Cosmos DB kunt u de volgende opties gebruiken om uw verbinding te optimaliseren:

* **ConnectionMode** : gebruik de modus directe verbinding om verbinding te maken met de gegevens knooppunten in de Azure Cosmos DB-service. Gebruik de gateway modus alleen voor het initialiseren en opslaan van de logische adressen en het vernieuwen van updates. Zie het artikel [connectiviteits modi](sql-sdk-connection-modes.md) voor meer informatie.

* **ApplicationRegion** : deze optie wordt gebruikt voor het instellen van de gewenste geo-gerepliceerde regio die wordt gebruikt om te communiceren met Azure Cosmos db. Zie het artikel over [globale distributie](distribute-data-globally.md) voor meer informatie.

* **ConsistencyLevel** : deze optie wordt gebruikt om het standaard consistentie niveau te onderdrukken. Zie het artikel [consistentie niveaus](consistency-levels.md) voor meer informatie.

* **BulkExecutionMode** : deze optie wordt gebruikt voor het uitvoeren van bulk bewerkingen door de eigenschap *AllowBulkExecution* in te stellen op True. Zie het artikel [Bulk Import](tutorial-sql-api-dotnet-bulk-import.md) voor meer informatie.

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>De container inrichten

**DynamoDB**:

Als u de gegevens in Amazon DynamoDB wilt opslaan, moet u eerst de tabel maken. In dit proces definieert u het schema, het sleutel type en de kenmerken, zoals wordt weer gegeven in de volgende code:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

In Amazon DynamoDB moet u de Lees Compute-eenheden inrichten & schrijf Compute-eenheden. Terwijl Azure Cosmos DB u de door Voer opgeeft als [aanvraag eenheden (ru/s)](request-units.md), die voor elke bewerking dynamisch kunnen worden gebruikt. De gegevens zijn ingedeeld als data base--> container--> item. U kunt de door Voer op database niveau of op verzamelings niveau of beide opgeven.

Een Data Base maken:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

De container maken:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>De gegevens laden

**DynamoDB**:

De volgende code laat zien hoe u de gegevens in Amazon DynamoDB kunt laden. De moviesArray bestaat uit een lijst met JSON-documenten. vervolgens moet u het JSON-document door lopen en in Amazon DynamoDB laden:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

In Azure Cosmos DB kunt u kiezen voor Stream en write with `moviesContainer.CreateItemStreamAsync()` . In dit voor beeld wordt de JSON gedeserialiseerd in het type *MovieModel* om de functie type cast te demonstreren. De code heeft meerdere threads, die gebruikmaken van de gedistribueerde architectuur van Azure Cosmos DB en het laden versnellen:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Een document maken

**DynamoDB**:

Het schrijven van een nieuw document in Amazon DynamoDB is niet van het type safe, het volgende voor beeld maakt gebruik van NewItem mag als document type:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB biedt u een type beveiliging via gegevens model. We gebruiken het gegevens model met de naam ' MovieModel ':

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

In Azure Cosmos DB NewItem mag wordt MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Een document lezen

**DynamoDB**:

Als u wilt lezen in Amazon DynamoDB, moet u primitieven definiëren:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Met Azure Cosmos DB is de query echter natuurlijk (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

De documenten verzameling in het bovenstaande voor beeld is:

- Typ veilig
- Geef een natuurlijke query optie op.

### <a name="update-an-item"></a>Een item bijwerken

**DynamoDB**: het item bijwerken in Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

In Azure Cosmos DB wordt update behandeld als een Upsert bewerking, wat betekent dat het document wordt ingevoegd als het niet bestaat:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Een document verwijderen

**DynamoDB**:

Als u een item in Amazon DynamoDB wilt verwijderen, moet u de volgende primitieven uitvoeren:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

In Azure Cosmos DB kunnen we het document asynchroon ophalen en verwijderen:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Query's uitvoeren voor documenten

**DynamoDB**:

In Amazon DynamoDB zijn API-functies vereist om de gegevens op te vragen:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

In Azure Cosmos DB kunt u projecteren en filteren binnen een eenvoudige SQL-query:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Voor bereik bewerkingen, bijvoorbeeld ' tussen ', moet u een scan uitvoeren in Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

In Azure Cosmos DB kunt u SQL-query en een instructie met één regel gebruiken:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Een container verwijderen

**DynamoDB**:

Als u de tabel in Amazon DynamoDB wilt verwijderen, kunt u het volgende opgeven:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Als u de verzameling in Azure Cosmos DB wilt verwijderen, kunt u het volgende opgeven:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Verwijder de data base dan ook als u dat nodig hebt:

```csharp
await cosmosDatabase.DeleteAsync();
```

Zoals u kunt zien, biedt Azure Cosmos DB ondersteuning voor natuurlijke query's (SQL), bewerkingen zijn asynchroon en veel gemakkelijker. U kunt eenvoudig uw complexe code migreren naar Azure Cosmos DB, die tijdens de migratie eenvoudiger wordt.

### <a name="next-steps"></a>Volgende stappen

- Meer informatie over [prestatie optimalisatie](performance-tips.md).
- Meer informatie over het [optimaliseren van lees-en schrijf bewerkingen](key-value-store-cost.md)
- Meer informatie over [bewaking in Cosmos DB](monitor-cosmos-db.md)


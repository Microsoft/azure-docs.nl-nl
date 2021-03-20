---
title: Azure Cosmos DB bindingen voor de functies 1. x
description: Meer informatie over het gebruik van Azure Cosmos DB triggers en bindingen in Azure Functions 1. x.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002104"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB bindingen voor Azure Functions 1. x

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1:](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In dit artikel wordt uitgelegd hoe u met [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindingen in azure functions kunt werken. Azure Functions ondersteunt trigger-, invoer-en uitvoer bindingen voor Azure Cosmos DB.

> [!NOTE]
> Dit artikel is voor Azure Functions 1. x. Zie [Azure Cosmos DB bindingen voor Azure functions 2. x](functions-bindings-cosmosdb-v2.md)voor meer informatie over het gebruik van deze bindingen in functions 2. x en hoger.
>
>Deze binding heeft oorspronkelijk de naam DocumentDB. In functions versie 1. x is alleen de naam van de trigger gewijzigd Cosmos DB. in de invoer binding, uitvoer binding en NuGet-pakket wordt de naam van de DocumentDB bewaard.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindingen worden alleen ondersteund voor gebruik met de SQL-API. Voor alle andere Azure Cosmos DB Api's moet u toegang krijgen tot de data base vanuit uw functie door gebruik te maken van de statische client voor uw API, inclusief de [API van Azure Cosmos DB voor MongoDb](../cosmos-db/mongodb-introduction.md), [CASSANDRA-API](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)en [Table-API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De Azure Cosmos DB-bindingen voor functie versie 1. x zijn opgenomen in het [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-pakket versie 1. x. De bron code voor de bindingen bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

De Azure Cosmos DB trigger gebruikt de [Azure Cosmos DB wijzigings feed](../cosmos-db/change-feed.md) om te Luis teren naar invoeg toepassingen en updates op meerdere partities. De wijzigings feed publiceert invoegingen en updates, en kan niet worden verwijderd.

## <a name="trigger---example"></a>Trigger-voor beeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die wordt aangeroepen wanneer er invoeg acties of updates in de opgegeven Data Base en verzameling zijn.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *function.jsin* bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de C# Script-code:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de JavaScript-code:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Trigger-kenmerken

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [trigger-configuratie](#trigger---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een `CosmosDBTrigger` voor beeld van een kenmerk in een methode handtekening:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Zie [Trigger: C#-voorbeeld](#trigger)voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="trigger---configuration"></a>Trigger-configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `CosmosDBTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `cosmosDBTrigger`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze para meter wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**name** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de lijst met documenten met wijzigingen vertegenwoordigt. |
|**connectionStringSetting**|**ConnectionStringSetting** | De naam van een app-instelling die de connection string bevat die wordt gebruikt om verbinding te maken met het Azure Cosmos DB account dat wordt bewaakt. |
|**databaseName**|**DatabaseName**  | De naam van de Azure Cosmos DB-Data Base met de verzameling die wordt bewaakt. |
|**collectionName** |**NaamVerzameling** | De naam van de verzameling die wordt bewaakt. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Beschrijving De naam van een app-instelling die de connection string bevat voor de service waarin de lease verzameling zich bevindt. Als deze niet wordt ingesteld, `connectionStringSetting` wordt de waarde gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. De connection string voor de leases-verzameling moet schrijf machtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Beschrijving De naam van de data base die de verzameling bevat die wordt gebruikt voor het opslaan van leases. Als deze niet wordt ingesteld, wordt de waarde van de `databaseName` instelling gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. |
|**leaseCollectionName** | **LeaseCollectionName** | Beschrijving De naam van de verzameling die wordt gebruikt voor het opslaan van leases. Als deze niet wordt ingesteld, `leases` wordt de waarde gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Beschrijving Als deze eigenschap `true` is ingesteld op, wordt de leases-verzameling automatisch gemaakt als deze nog niet bestaat. De standaardwaarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Beschrijving Hiermee wordt het aantal aanvraag eenheden gedefinieerd dat moet worden toegewezen wanneer de leases-verzameling wordt gemaakt. Deze instelling wordt alleen gebruikt wanneer `createLeaseCollectionIfNotExists` is ingesteld op `true` . Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Beschrijving Als deze optie is ingesteld, wordt er een voor voegsel toegevoegd aan de leases die zijn gemaakt in de lease verzameling voor deze functie, waardoor twee afzonderlijke Azure Functions dezelfde lease verzameling kunnen delen met behulp van verschillende voor voegsels.
|**feedPollDelay**| **FeedPollDelay**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden de vertraging tussen het navragen van een partitie voor nieuwe wijzigingen in de feed gedefinieerd nadat alle huidige wijzigingen zijn vertraagd. De standaard waarde is 5000 (5 seconden).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval voor het starten van een taak gedefinieerd om te berekenen of de partities gelijkmatig worden verdeeld over bekende exemplaren van hosts. De standaard waarde is 13000 (13 seconden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het interval voor het nemen van de lease op een lease die een partitie vertegenwoordigt. Als de lease niet binnen dit interval wordt vernieuwd, wordt het verlopen en het eigendom van de partitie verplaatst naar een ander exemplaar. De standaard waarde is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het Vernieuwings interval voor alle leases gedefinieerd voor partities die momenteel door een instantie worden beheerd. De standaard waarde is 17000 (17 seconden).
|**checkpointFrequency**| **CheckpointFrequency**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval tussen de controle punten van de lease gedefinieerd. De standaard waarde is altijd na elke functie aanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Beschrijving Als deze optie is ingesteld, wordt de maximum hoeveelheid ontvangen items per functie aanroep aangepast.
|**startFromBeginning**| **StartFromBeginning**| Beschrijving Wanneer dit is ingesteld, wordt aan de trigger aangegeven dat de wijzigingen moeten worden gelezen vanaf het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen de eerste keer dat de trigger wordt gestart, zoals bij volgende uitvoeringen, de controle punten al zijn opgeslagen. Als u dit instelt op `true` wanneer er al leases zijn gemaakt, heeft dit geen effect.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger-gebruik

Voor de trigger is een tweede verzameling vereist die wordt gebruikt voor het opslaan van _leases_ via de partities. Zowel de verzameling die wordt bewaakt als de verzameling die de leases bevat, moet beschikbaar zijn voor de werking van de trigger.

>[!IMPORTANT]
> Als er meerdere functies zijn geconfigureerd voor het gebruik van een Cosmos DB trigger voor dezelfde verzameling, moeten voor elk van de functies een toegewezen lease verzameling worden gebruikt, of moet voor elke functie een andere worden opgegeven `LeaseCollectionPrefix` . Anders wordt slechts een van de functies geactiveerd. Zie de [sectie configuratie](#trigger---configuration)voor meer informatie over het voor voegsel.

De trigger geeft niet aan of een document is bijgewerkt of ingevoegd, maar het document zelf wordt geleverd. Als u updates wilt afhandelen en anders wilt invoegen, kunt u dit doen door tijds tempel velden te implementeren voor invoegen of bijwerken.

## <a name="input"></a>Invoer

Bij een Azure Cosmos DB-invoerbinding wordt de SQL-API gebruikt voor het ophalen van een of meer Azure Cosmos DB-documenten en het doorgeven daarvan aan de invoerparameter van de functie. De document-id of queryparameters kunnen worden bepaald op basis van de trigger waarmee de functie wordt geactiveerd.

## <a name="input---example"></a>Invoer-voor beeld

# <a name="c"></a>[C#](#tab/csharp)

Deze sectie bevat de volgende voor beelden:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrij trigger, ID opzoeken vanuit JSON

Het volgende voor beeld toont een [C#-functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een wachtrij bericht dat een JSON-object bevat. De wachtrij trigger parseert de JSON naar een object `ToDoItemLookup` met de naam, die de id bevat die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

Het volgende voor beeld toont een [C#-functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

Het volgende voor beeld toont een [C#-functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoer voorbeelden overs Laan](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery

Het volgende voor beeld toont een [C#-functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoer voorbeelden overs Laan](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De query is opgegeven in de `SqlQuery` kenmerk eigenschap.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoer voorbeelden overs Laan](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, meerdere documenten ophalen met behulp van DocumentClient (C#)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De code gebruikt een `DocumentClient` exemplaar van de Azure Cosmos DB binding om een lijst met documenten te lezen. Het `DocumentClient` exemplaar kan ook worden gebruikt voor schrijf bewerkingen.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voor beelden:

* [Wachtrij trigger, ID opzoeken op basis van teken reeks](#queue-trigger-look-up-id-from-string-c-script)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

De voor beelden van HTTP-triggers verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Wachtrij trigger, ID opzoeken op basis van teken reeks

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *function.jsin* bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrij trigger om de query parameters aan te passen.

De trigger van de wachtrij bevat een para meter `departmentId` . Een wachtrij bericht waarin `{ "departmentId" : "Finance" }` alle records voor de afdeling Financiën worden geretourneerd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld wordt een [C#-script functie](functions-reference-csharp.md) weer gegeven waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Dit is de C# Script-code:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld wordt een [C#-script functie](functions-reference-csharp.md) weer gegeven waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Dit is de C# Script-code:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een [C#-script functie](functions-reference-csharp.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De query is opgegeven in de `SqlQuery` kenmerk eigenschap.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Dit is de C# Script-code:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient

In het volgende voor beeld ziet u een [C#-script functie](functions-reference-csharp.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De code gebruikt een `DocumentClient` exemplaar van de Azure Cosmos DB binding om een lijst met documenten te lezen. Het `DocumentClient` exemplaar kan ook worden gebruikt voor schrijf bewerkingen.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Dit is de C# Script-code:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Deze sectie bevat de volgende voor beelden:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-javascript)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrij trigger, ID opzoeken vanuit JSON

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Deze ID wordt gebruikt om een `ToDoItem` document op te halen uit de opgegeven Data Base en verzameling.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrij trigger om de query parameters aan te passen.

De trigger van de wachtrij bevat een para meter `departmentId` . Een wachtrij bericht waarin `{ "departmentId" : "Finance" }` alle records voor de afdeling Financiën worden geretourneerd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

---

## <a name="input---attributes"></a>Invoer kenmerken

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [de volgende configuratie sectie](#input---configuration)voor informatie over de instellingen en andere eigenschappen die u kunt configureren.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="input---configuration"></a>Invoer-configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `DocumentDB`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     | N.v.t. | Moet worden ingesteld op `documentdb`.        |
|**direction**     | N.v.t. | Moet worden ingesteld op `in`.         |
|**name**     | N.v.t. | De naam van de bindings parameter die het document in de functie vertegenwoordigt.  |
|**databaseName** |**DatabaseName** |De data base met het document.        |
|**collectionName** |**NaamVerzameling** | De naam van de verzameling die het document bevat. |
|**id**    | **Id** | De ID van het document dat moet worden opgehaald. Deze eigenschap ondersteunt [bindings expressies](./functions-bindings-expressions-patterns.md). Stel de eigenschappen **id** en **sqlQuery** niet in. Als u er niet een hebt ingesteld, wordt de volledige verzameling opgehaald. |
|**sqlQuery**  |**SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt om meerdere documenten op te halen. De eigenschap ondersteunt runtime bindingen, zoals in dit voor beeld: `SELECT * FROM c where c.departmentId = {departmentId}` . Stel de eigenschappen **id** en **sqlQuery** niet in. Als u er niet een hebt ingesteld, wordt de volledige verzameling opgehaald.|
|**connection**     |**ConnectionStringSetting**|De naam van de app-instelling met uw Azure Cosmos DB connection string.        |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de partitie sleutel waarde voor de zoek opdracht op. Kan bindende para meters bevatten.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer gebruik

# <a name="c"></a>[C#](#tab/csharp)

Wanneer de functie is afgesloten, worden alle wijzigingen die zijn aangebracht in het invoer document via benoemde invoer parameters automatisch behouden.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Wanneer de functie is afgesloten, worden alle wijzigingen die zijn aangebracht in het invoer document via benoemde invoer parameters automatisch behouden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Updates worden niet automatisch doorgevoerd bij het afsluiten van de functie. Gebruik `context.bindings.<documentName>In` en `context.bindings.<documentName>Out` om updates te maken. Zie het [invoer voorbeeld](#input).

---

## <a name="output"></a>Uitvoer

Met de Azure Cosmos DB uitvoer binding kunt u een nieuw document naar een Azure Cosmos DB-Data Base schrijven met behulp van de SQL-API.

## <a name="output---example"></a>Uitvoer-voor beeld

# <a name="c"></a>[C#](#tab/csharp)

Deze sectie bevat de volgende voor beelden:

* Wachtrij trigger, een document schrijven
* Wachtrij trigger, documenten schrijven met `IAsyncCollector`

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Wachtrij trigger, een document schrijven

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een document wordt toegevoegd aan een Data Base, met behulp van gegevens die zijn opgenomen in het bericht uit de wachtrij opslag.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrij trigger, documenten schrijven met IAsyncCollector

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee een verzameling documenten wordt toegevoegd aan een Data Base, met behulp van gegevens die zijn opgenomen in een JSON van een wachtrij bericht.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voor beelden:

* Wachtrij trigger, een document schrijven
* Wachtrij trigger, documenten schrijven met `IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrij trigger, documenten schrijven met IAsyncCollector

Als u meerdere documenten wilt maken, kunt u `ICollector<T>` een binding met of `IAsyncCollector<T>` `T` een van de ondersteunde typen.

Dit voor beeld verwijst naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV1
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
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Dit is de C# Script-code:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

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

---

## <a name="output---attributes"></a>Uitvoer kenmerken

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [uitvoer-configuratie](#output---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een `DocumentDB` voor beeld van een kenmerk in een methode handtekening:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Zie [output](#output)voor een volledig voor beeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="output---configuration"></a>Uitvoer-configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `DocumentDB`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     | N.v.t. | Moet worden ingesteld op `documentdb`.        |
|**direction**     | N.v.t. | Moet worden ingesteld op `out`.         |
|**name**     | N.v.t. | De naam van de bindings parameter die het document in de functie vertegenwoordigt.  |
|**databaseName** | **DatabaseName**|De data base met de verzameling waarin het document is gemaakt.     |
|**collectionName** |**NaamVerzameling**  | De naam van de verzameling waar het document wordt gemaakt. |
|**createIfNotExists**  |**CreateIfNotExists**    | Een Booleaanse waarde die aangeeft of de verzameling wordt gemaakt wanneer deze niet bestaat. De standaard waarde is *False* omdat nieuwe verzamelingen worden gemaakt met gereserveerde door Voer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/documentdb/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Wanneer `CreateIfNotExists` is ingesteld op True, wordt hiermee het pad van de partitie sleutel voor de gemaakte verzameling gedefinieerd.|
|**collectionThroughput**|**CollectionThroughput**| Wanneer `CreateIfNotExists` is ingesteld op True, wordt hiermee de [door Voer](../cosmos-db/set-throughput.md) van de gemaakte verzameling gedefinieerd.|
|**connection**    |**ConnectionStringSetting** |De naam van de app-instelling met uw Azure Cosmos DB connection string.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer gebruik

Wanneer u in uw functie naar de uitvoer parameter schrijft, wordt er standaard een document gemaakt in uw data base. Dit document heeft een automatisch gegenereerde GUID als document-ID. U kunt de document-ID van het uitvoer document opgeven door de `id` eigenschap op te geven in het JSON-object dat is door gegeven aan de uitvoer parameter.

> [!Note]
> Wanneer u de ID van een bestaand document opgeeft, wordt dit overschreven door het nieuwe uitvoer document.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Cosmos DB | [CosmosDB-fout codes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over database computing zonder server met Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->

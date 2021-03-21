---
title: De grafiek gebruiken de .NET-bibliotheek voor bulksgewijs uitvoeren met Azure Cosmos DB Gremlin API
description: Meer informatie over hoe u de bulk-uitvoerder bibliotheek kunt gebruiken om grafiek gegevens enorm te importeren in een Azure Cosmos DB Gremlin API-container.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e94dac02770bf28aae4cbfc4e337cb68b8be40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425319"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>De grafiek gebruiken om bulk bewerkingen uit te voeren in Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Deze zelf studie bevat instructies over het gebruik van de bulk-uitvoerder .NET-bibliotheek van Azure CosmosDB om grafiek objecten te importeren en bij te werken in een Azure Cosmos DB Gremlin API-container. Dit proces maakt gebruik van de klasse Graph in de [bibliotheek voor bulk](./bulk-executor-overview.md) -uitvoerder om vertex-en Edge-objecten programmatisch te maken en vervolgens meerdere aanvragen per netwerk aanvraag in te voegen. Dit gedrag kan worden geconfigureerd via de bibliotheek voor bulk-uitvoerder om optimaal gebruik te maken van zowel data base-als lokale geheugen bronnen.

In tegens telling tot het verzenden van Gremlin-query's naar een Data Base, waarbij de opdracht wordt geëvalueerd en vervolgens een voor een kan worden uitgevoerd, moet u in plaats daarvan de objecten lokaal maken en valideren met behulp van de bibliotheek voor bulk bewerking. Als de objecten zijn gemaakt, kunt u opeenvolgend grafen naar de databaseservice verzenden. Dankzij deze methode kunnen snelheden van gegevensopnames met een factor 100 worden verhoogd. Daardoor is het een ideale methode voor initiële gegevensmigraties of periodieke gegevensverplaatsingen. Ga voor meer informatie naar de GitHub-pagina van de [voorbeeld toepassing Azure Cosmos DB Graph-bulk](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)-uitvoerder.

## <a name="bulk-operations-with-graph-data"></a>Bulkbewerkingen met graafgegevens

De [bibliotheek bulk](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) -uitvoerder bevat een `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` naam ruimte om functionaliteit te bieden voor het maken en importeren van grafiek objecten. 

In het volgende proces wordt uiteengezet hoe gegevensmigratie voor een Gremlin API-container kan worden gebruikt:
1. Haal records op uit de gegevensbron.
2. Construeer `GremlinVertex`- en `GremlinEdge`-objecten uit de verkregen objecten en voeg ze toe aan een `IEnumerable`-gegevensstructuur. In dit deel van de toepassing moet de logica voor het detecteren en toevoegen van relaties worden geïmplementeerd in geval de gegevensbron geen grafendatabase is.
3. Gebruik de [Graph BulkImportAsync-methode](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync) voor het invoegen van de graafobjecten in de verzameling.

Met dit mechanisme wordt de efficiëntie van de gegevensmigratie verbeterd vergeleken met het gebruik van een Gremlin-client. Deze verbetering is merkbaar omdat voor het invoegen van gegevens met Gremlin de toepassing per keer een query moet verzenden die moet worden gevalideerd, geëvalueerd en vervolgens uitgevoerd om de gegevens te maken. De bulk-uitvoerder bibliotheek verwerkt de validatie in de toepassing en verzendt meerdere Graph-objecten per keer voor elke netwerk aanvraag.

### <a name="creating-vertices-and-edges"></a>Hoekpunten en randen maken

`GraphBulkExecutor` biedt de `BulkImportAsync`-methode, waarvoor een `IEnumerable`-lijst met `GremlinVertex`- of `GremlinEdge`-objecten is vereist, die beide zijn gedefinieerd in de `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`-naamruimte. In het voorbeeld worden de randen en hoekpunten gescheiden in twee BulkExecutor-importeertaken. Zie het voorbeeld hieronder:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Raadpleeg het [onderwerp BulkImportData to Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)voor meer informatie over de para meters van de bibliotheek voor bulk-uitvoerder.

De nettolading moet in `GremlinVertex`- en `GremlinEdge`-objecten worden gemaakt. Hier ziet u hoe deze objecten kunnen worden gemaakt:

**Hoekpunten**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Randen**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Het hulp programma bulk-uitvoerbaar controleert niet automatisch op bestaande hoek punten voordat er randen worden toegevoegd. Dit moet worden gevalideerd in de toepassing voordat de BulkImport-taken worden uitgevoerd.

## <a name="sample-application"></a>Voorbeeldtoepassing

### <a name="prerequisites"></a>Vereisten
* Visual Studio 2019 met de werk belasting Azure Development. U kunt gratis aan de slag met de [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) .
* Een Azure-abonnement. U kunt [hier een gratis Azure-account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). U kunt ook een Cosmos-database account maken met [gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement.
* Een Azure Cosmos DB Gremlin API-database met een **onbeperkte verzameling**. In deze handleiding leest u hoe u aan de slag kunt met [Azure Cosmos DB Gremlin API in .NET](./create-graph-dotnet.md).
* Git. Ga naar de [Git-pagina met downloads](https://git-scm.com/downloads) voor meer informatie.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen
In deze zelf studie volgen we de stappen om aan de slag te gaan met behulp van het [Azure Cosmos DB Graph](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) -voor beeld voor bulk gebruik dat wordt gehost op github. Deze toepassing bestaat uit een .NET-oplossing die willekeurig hoekpunt- en randobjecten maakt en ze vervolgens bulksgewijs invoegt voor het opgegeven grafendatabaseaccount. Voer de onderstaande opdracht `git clone` uit om de toepassing op te halen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Deze opslagplaats bevat het GraphBulkExecutor-voorbeeld met de volgende bestanden:

File|Beschrijving
---|---
`App.config`|Hier worden de voor de toepassing en database specifieke parameters gespecificeerd. Dit bestand moet eerst worden aangepast om verbinding te kunnen maken met de doeldatabase en de verzamelingen.
`Program.cs`| Dit bestand bevat de logica achter het maken van de `DocumentClient` verzameling, het afhandelen van de opschoon bewerkingen en het verzenden van de aanvragen voor bulk levering.
`Util.cs`| Dit bestand bevat een helperklasse die de logica bevat voor het genereren van testgegevens en het controleren op de aanwezigheid van de database en verzamelingen.

In het `App.config`-bestand zijn de configuratiewaarden die kunnen worden opgegeven, als volgt:

Instelling|Beschrijving
---|---
`EndPointUrl`|Dit is uw **.NET SDK-eindpunt**. Dit is aanwezig in de blade Overzicht van uw Azure Cosmos DB Gremlin API-databaseaccount. De indeling is `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Dit is de primaire of secundaire sleutel die vermeld staat onder uw Azure Cosmos DB-account. Meer informatie over [Toegang verkrijgen tot Azure Cosmos DB-gegevens](./secure-access-to-data.md#primary-keys)
`DatabaseName`, `CollectionName`|Dit zijn **de namen van de doeldatabase en verzameling**. Als `ShouldCleanupOnStart` is ingesteld op `true`, worden deze waarden, in combinatie met `CollectionThroughput`, gebruikt om ze te verwijderen en een nieuwe database en verzameling te maken. Op dezelfde manier geldt dat als `ShouldCleanupOnFinish` is ingesteld op `true`, de waarden worden gebruikt om de database te verwijderen zodra de gegevensopname is voltooid. Merk op dat de doelverzameling een **onbeperkte verzameling** moet zijn.
`CollectionThroughput`|Deze wordt gebruikt om een nieuwe verzameling te maken als de optie `ShouldCleanupOnStart` is ingesteld op `true`.
`ShouldCleanupOnStart`|Hiermee worden het databaseaccount en de verzamelingen verwijderd voordat het programma wordt uitgevoerd. Vervolgens worden nieuwe gemaakt aan de hand van de waarden `DatabaseName`, `CollectionName` en `CollectionThroughput`.
`ShouldCleanupOnFinish`|Hiermee worden het databaseaccount en de verzamelingen verwijderd met de opgegeven `DatabaseName` en `CollectionName` nadat het programma is uitvoeren.
`NumberOfDocumentsToImport`|Hiermee wordt het aantal hoekpunten en randen bepaald dat in het voorbeeld wordt gegenereerd. Dit aantal geldt voor zowel de hoekpunten als de randen.
`NumberOfBatches`|Hiermee wordt het aantal hoekpunten en randen bepaald dat in het voorbeeld wordt gegenereerd. Dit aantal geldt voor zowel de hoekpunten als de randen.
`CollectionPartitionKey`|Deze wordt gebruikt om de testhoekpunten en -randen te maken, waarbij deze eigenschap automatisch wordt toegewezen. Deze wordt ook gebruikt als de database en de verzamelingen opnieuw worden gemaakt als de optie `ShouldCleanupOnStart` is ingesteld op `true`.

### <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

1. Voeg uw specifieke databaseconfiguratieparameters toe aan `App.config`. Deze wordt gebruikt om een DocumentClient-exemplaar te maken. Als de database en de container nog niet zijn gemaakt, worden ze automatisch gemaakt.
2. Voer de toepassing uit. Hierdoor wordt `BulkImportAsync` tweemaal aangeroepen; eenmaal om hoekpunten te importeren en eenmaal om randen te importeren. Als een van de objecten bij het invoegen een fout genereert, wordt het object toegevoegd aan `.\BadVertices.txt` of `.\BadEdges.txt`.
3. Evalueer de resultaten door een query uit te voeren op de grafendatabase. Als de optie `ShouldCleanupOnFinish` is ingesteld op waar, wordt de database automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over NuGet-pakket gegevens en release opmerkingen van de .NET-bibliotheek voor bulk-uitvoerder- [SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Bekijk de [Tips voor prestaties](./bulk-executor-dot-net.md#performance-tips) om het gebruik van de bulk-uitvoerder verder te optimaliseren.
* Lees het artikel [BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) voor meer informatie over de klassen en methoden die in deze naamruimte zijn gedefinieerd.
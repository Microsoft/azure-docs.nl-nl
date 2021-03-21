---
title: 'Azure Cosmos DB SQL API: Java SDK v4-voorbeelden'
description: Vind Java-voorbeelden op GitHub voor veelvoorkomende taken met de SQL API in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018986"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4-voorbeelden
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-voorbeelden](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-voorbeelden](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK-voorbeelden](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-voorbeelden](sql-api-spring-data-sdk-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Zie voor meer informatie over Java SDK v4 de [opmerkingen bij de release](sql-api-sdk-java-v4.md) van Azure Cosmos DB Java SDK v4, de [Maven-opslagplaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [prestatietips](performance-tips-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 en de [probleemoplossingshandleiding](troubleshoot-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

De nieuwste voorbeeldtoepassingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Java-voorbeeldprojectbestanden. 
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

U hebt het volgende nodig om deze voorbeeldtoepassing uit te voeren:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

U kunt eventueel Maven gebruiken om de recentste binaire Azure Cosmos DB Java SDK v4-bestanden op te halen voor gebruik in uw project. Maven voegt automatisch eventuele vereiste afhankelijkheden toe. Anders kunt u de afhankelijkheden die worden vermeld in het bestand pom.xml, rechtstreeks downloaden en toevoegen aan uw build-pad.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**De voorbeeldtoepassingen uitvoeren**

De voorbeeldopslagplaats klonen:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

U kunt de voorbeelden uitvoeren met behulp van een IDE (Eclipse, IntelliJ of VSCODE) of vanaf de opdrachtregel met behulp van Maven.

Deze omgevingsvariabelen moeten worden ingesteld

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

om de voorbeelden lees-en schrijftoegang tot uw account te geven.

Als u een voorbeeld wilt uitvoeren, geeft u de hoofdklasse op 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

waarbij *sample.synchronicity.MainClass* kan zijn
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *(Changefeed heeft alleen een async-voorbeeld, geen synchronisatievoorbeeld.)* ... enzovoort...

> [!NOTE]
> Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. De voorbeelden doen meerdere aanroepen om een `CosmosContainer` te maken. Telkens wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor 1 uur gebruik voor de prestatielaag van de gemaakte verzameling. 
> 
> 

## <a name="database-examples"></a>Voorbeelden voor databases
Het bestand [Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) laat u zien hoe u de volgende taken uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-databases voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een database maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [Een database lezen op id](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [Alle databases lezen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Een database verwijderen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Voorbeelden voor verzamelingen
In het bestand [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ziet u hoe u de volgende taken uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een verzameling maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [Geconfigureerde prestaties van een verzameling wijzigen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Een verzameling ophalen op id](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Alle verzamelingen in een database lezen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Een verzameling verwijderen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Voorbeelden van verzamelingen voor automatische schaalaanpassing

Als u meer wilt weten over automatische schaalaanpassing voordat u deze voorbeelden uitvoert, bekijkt u deze instructies om automatische schaalaanpassing in te schakelen in uw [account](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) en in uw [databases en containers](./provision-throughput-autoscale.md).

In het bestand [autoscale Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) ziet u hoe u de volgende taken uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een database maken met de opgegeven maximale doorvoer voor automatische schaalaanpassing](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

In het bestand [autoscale Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) ziet u hoe u de volgende taken uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een verzameling maken met de opgegeven maximale doorvoer voor automatische schaalaanpassing](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [Geconfigureerde maximale doorvoer van automatische schaalaanpassing voor een verzameling wijzigen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [De doorvoerconfiguratie van een verzameling voor automatische schaalaanpassing lezen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Voorbeelden van verzameling voor analytische opslag

In het bestand [Analytical storage Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) ziet u hoe u de volgende taken uitvoert. Als u meer informatie wilt over de Azure Cosmos-verzamelingen voordat u de volgende voorbeelden uitvoert, kunt u lezen over Azure Cosmos DB Synapse en Analytical Store.

| Taak | API-verwijzing |
| --- | --- |
| [Een verzameling maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Voorbeelden voor documenten
In het bestand [Document CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) ziet u hoe u de volgende taken uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een document maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Een document lezen op id](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Een query uitvoeren voor documenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Een document vervangen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Een document invoegen of updaten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Een document verwijderen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Een document vervangen door voorwaardelijke ETag-controle](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Document alleen lezen als het is gewijzigd](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Voorbeelden van indexen
In het bestand [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ziet u hoe u de volgende taken uitvoert. Zie de conceptuele artikelen [Indexeringsbeleid](index-policy.md), [Indexeringstypen](index-overview.md#index-types) en [Indexeringspaden](index-policy.md#include-exclude-paths) voor meer informatie over het indexeren in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| Een document uitsluiten van de index | ExcludedIndex<br>IndexingPolicy |
| Luie indexering gebruiken | IndexingPolicy.IndexingMode |
| [Opgegeven documentpaden opnemen in de index ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Opgegeven documentpaden uitsluiten van de index ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Een samengestelde index maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Een bereikscanbewerking voor een met hash geïndexeerd pad afdwingen | FeedOptions.EnableScanInQuery |
| Bereikindexen voor tekenreeksen gebruiken | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Een indextransformatie uitvoeren | - |
| [Een georuimtelijke index maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Zie [Azure Cosmos DB-indexeringsbeleid](index-policy.md) voor meer informatie over indexering.

## <a name="query-examples"></a>Voorbeelden van query's
In het bestand [Query Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) ziet u hoe u de volgende taken kunt uitvoeren met behulp van de SQL-querygrammatica. Zie [SQL-queryvoorbeelden voor Azure Cosmos DB](./sql-query-getting-started.md) voor meer informatie over de SQL-queryreferentie in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een query uitvoeren voor alle documenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Query uitvoeren voor gelijkheid met behulp van ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Query uitvoeren voor ongelijkheid met behulp van != en NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Query uitvoeren met behulp van bereikoperators zoals >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Query uitvoeren met behulp van bereikoperators op tekenreeksen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Query uitvoeren met ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Query uitvoeren met DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Query uitvoeren met statistische functies](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Werken met subdocumenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Query uitvoeren met Joins tussen documenten](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Query uitvoeren met tekenreeks-, berekenings- en matrixoperators](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Query uitvoeren met SQL met parameters met behulp van SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Query uitvoeren met expliciete paginering](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Query gelijktijdig uitvoeren op gepartitioneerde verzamelingen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Query uitvoeren met ORDER BY voor gepartitioneerde verzamelingen | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Voorbeelden van wijzigingenfeeds 
Het bestand [Change Feed Processor Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) laat zien hoe u de volgende taken kunt uitvoeren. Als u meer wilt weten over de wijzigingenfeed in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert, raadpleegt u [Wijzigingenfeed in Azure Cosmos DB lezen](read-change-feed.md) en [Processor voor wijzigingenfeed](change-feed-processor.md).

| Taak | API-verwijzing |
| --- | --- |
| [Basisfunctionaliteit van wijzigingenfeed](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Wijzigingenfeed van een specifieke tijd lezen | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Wijzigingenfeed vanaf het begin lezen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server

In het bestand [Stored Procedure Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) ziet u hoe u de volgende taken kunt uitvoeren. Zie [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md) voor meer informatie over het programmeren op de server in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.execute |
| [Een opgeslagen procedure verwijderen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Voorbeelden van gebruikersbeheer
In het bestand User Management Sample ziet u hoe u de volgende taken kunt uitvoeren:

| Taak | API-verwijzing |
| --- | --- |
| Een gebruiker maken | - |
| Machtigingen instellen voor een verzameling of document | - |
| Een lijst met machtigingen van een gebruiker ophalen |- |

---
title: 'Azure Cosmos DB: Java-API voor bulkuitvoer, SDK & resources'
description: Meer informatie over de Java API en SDK voor bulksgewijs uitvoeren, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB Java SDK voor bulkuitvoering.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2d3c7026fd221b1a17b8efe56b03b2a26358c7ab
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364380"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotheek voor bulkuitvoer: informatie downloaden
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET wijzigingenfeed-SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

| | Koppeling/notities |
|---|---|
|**Beschrijving**|Met de bulkuitvoerbibliotheek kunnen clienttoepassingen bulkbewerkingen uitvoeren in Azure Cosmos DB accounts. bulkuitvoerbibliotheek biedt BulkImport- en BulkUpdate-naamruimten. De BulkImport-module kan documenten bulksgewijs opnemen op een geoptimaliseerde manier, zodat de doorvoer die is ingericht voor een verzameling maximaal wordt verbruikt. Met de module BulkUpdate kunt u bestaande gegevens in Azure Cosmos-containers bulksgewijs bijwerken als patches.|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bulkuitvoerbibliotheek in GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-documentatie**| [Java API-referentiedocumentatie](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Aan de slag**|[Aan de slag met de Java SDK voor de bulkuitvoerbibliotheek](bulk-executor-java.md)|
|**Minimaal ondersteunde runtime**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Opmerkingen bij de release

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Oplossing voor DocumentAnalyzer.java om geneste partitiesleutelwaarden correct uit json te extraheren.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Voeg functionaliteit toe in BulkDelete-bewerkingen om het opnieuw te proberen bij specifieke fouten en retourneerde ook een lijst met fouten voor de gebruiker die opnieuw konden worden uitgevoerd.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Update voor Cosmos SDK versie 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Oplossing voor 'mergeAll' om door te gaan met 'id' en partitiesleutelwaarde, zodat eventuele gepatchte documenteigenschappen die na 'id' en partitiesleutelwaarde worden geplaatst, worden toegevoegd aan de bijgewerkte itemlijst.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Werk de begingraad van gelijktijdigheid bij naar 1 en er zijn logboeken voor foutopsporing toegevoegd voor minibatch.
---
title: 'Azure Cosmos DB: SQL Async Java API, SDK & resources'
description: Meer informatie over de SQL Async Java API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a63194ae3ca9e5661b82113c450ebb929cc67e76
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364529"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK voor SQL API: opmerkingen bij de release en resources
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
> * [REST](/rest/api
> * [REST-resourceprovider](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

De SQL API Async Java SDK verschilt van de SQL API Java SDK door asynchrone bewerkingen te bieden met ondersteuning van de [Netty-bibliotheek](https://netty.io/). De al bestaande [SQL API Java SDK](sql-api-sdk-java.md) biedt geen ondersteuning voor asynchrone bewerkingen. 

> [!IMPORTANT]  
> Dit is *niet de* nieuwste Java SDK voor Azure Cosmos DB. U kunt [Azure Cosmos DB Java SDK v4 gebruiken](sql-api-sdk-java-v4.md) voor uw project. Volg de instructies in de handleiding Migreren naar Azure Cosmos DB [Java SDK v4](migrate-java-v4-sdk.md) en de [handleiding Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) om een upgrade uit te voeren. 
>

| | Koppelingen |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-documentatie** |[Java API-referentiedocumentatie](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**Bijdragen aan de SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Aan de slag** | [Aan de slag met de Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Codevoorbeeld** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Tips voor prestaties**| [GitHub-leesmij](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimaal ondersteunde runtime**|[JDK 8](/java/azure/jdk/) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
---
title: Azure Cosmos DB Java SDK v4 voor release-opmerkingen en resources voor SQL API
description: Meer informatie over de Azure Cosmos DB Java SDK v4 voor SQL API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d31f3d1c510ffe6c3f0a739a4e41313c8c6e7cf0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364818"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: opmerkingen bij de release en resources
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

De Azure Cosmos DB Java SDK v4 for Core (SQL) combineert een Asynchrone API en een Synchronisatie-API in één Maven-artefact. De v4 SDK biedt verbeterde prestaties, nieuwe API-functies en Async-ondersteuning op basis van Project Reactor en de [Netty-bibliotheek](https://netty.io/). Gebruikers kunnen verbeterde prestaties verwachten met Azure Cosmos DB Java SDK v4 ten opzichte van [de Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) en [de Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Deze release-opmerkingen zijn alleen Azure Cosmos DB Java SDK v4. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>
> Hier volgen drie stappen om snel aan de haal te gaan.
> 1. Installeer de [minimaal ondersteunde Java-runtime, JDK 8,](/java/azure/jdk/) zodat u de SDK kunt gebruiken.
> 2. Doorloopt u de [Snelstartgids voor Azure Cosmos DB Java SDK v4,](./create-sql-api-java.md) waarmee u toegang krijgt tot het Maven-artefact en basisaanvragen Azure Cosmos DB doorloopt.
> 3. Lees de Azure Cosmos DB java SDK [v4-prestatietips](performance-tips-java-sdk-v4-sql.md) en handleidingen voor probleemoplossing om de SDK voor uw toepassing te optimaliseren. [](troubleshoot-java-sdk-v4-sql.md)
>
> De [Azure Cosmos DB workshops en labs](https://aka.ms/cosmosworkshop) zijn een andere geweldige resource om te leren hoe u Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Nuttige inhoud

| Content | Koppeling |
|---|---|
|**SDK downloaden**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-documentatie** | [Java API-referentiedocumentatie](/java/api/overview/azure/cosmosdb/client) |
|**Bijdragen aan de SDK** | [Azure SDK voor Java Central-opslagplaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Aan de slag** | [Quickstart: Een Java-app bouwen om Azure Cosmos DB SQL API-gegevens te beheren](./create-sql-api-java.md) <br> [GitHub-opslagplaats met snelstartcode](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Eenvoudige codevoorbeelden** | [Azure Cosmos DB: Java-voorbeelden voor de SQL API](sql-api-java-sdk-samples.md) <br> [GitHub-opslagplaats met voorbeeldcode](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Console-app met wijzigingsfeed**| [Feed wijzigen - Java SDK v4-voorbeeld](create-sql-api-java-changefeed.md) <br> [GitHub-opslagplaats met voorbeeldcode](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Voorbeeld van web-app**| [Een web-app bouwen met Java SDK v4](sql-api-java-application.md) <br> [GitHub-opslagplaats met voorbeeldcode](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tips voor prestaties**| [Tips voor betere prestaties voor de Java-SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java-SDK v4 oplossen](troubleshoot-java-sdk-v4-sql.md) |
| **Migreren naar v4 vanaf een oudere SDK** | [Migreren naar Java v4-SDK](migrate-java-v4-sdk.md) |
| **Minimaal ondersteunde runtime**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB workshops en labs** |[Cosmos DB startpagina van workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Volgende stappen
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
---
title: Opmerkingen en bronnen voor de release van Java SDK v4 voor SQL API Azure Cosmos DB
description: Meer informatie over de Azure Cosmos DB Java SDK v4 voor SQL-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e23a1ed348f2a627181e0e4e4c20477ee9ba1ff8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210543"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for core (SQL) API: release opmerkingen en bronnen
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
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resource provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

De Azure Cosmos DB Java SDK v4 for core (SQL) combineert een async API en een API voor synchronisatie in één maven-artefact. De v4-SDK biedt verbeterde prestaties, nieuwe API-functies en async-ondersteuning op basis van project reactor en de [Netty-bibliotheek](https://netty.io/). Gebruikers kunnen betere prestaties verwachten met Azure Cosmos DB Java SDK v4 versus de [Azure Cosmos DB async Java SDK v2](sql-api-sdk-async-java.md) en de [Azure Cosmos DB Java SDK v2 te synchroniseren](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Deze release opmerkingen zijn alleen voor Azure Cosmos DB Java SDK v4. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>
> Hier volgen drie stappen om snel aan de slag te gaan.
> 1. Installeer de [Mini maal ondersteunde Java-runtime, JDK 8](/java/azure/jdk/) , zodat u de SDK kunt gebruiken.
> 2. Werk aan de [Snelstartgids voor Azure Cosmos DB Java SDK v4](./create-sql-api-java.md) , waarmee u toegang krijgt tot het maven-artefact en basis Azure Cosmos DB aanvragen doorloopt.
> 3. Lees de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md) en richt lijnen voor [probleem oplossing](troubleshoot-java-sdk-v4-sql.md) om de SDK voor uw toepassing te optimaliseren.
>
> De [Azure Cosmos DB workshops en Labs](https://aka.ms/cosmosworkshop) zijn een fantastische resource om te leren hoe u Azure Cosmos DB Java SDK v4 kunt gebruiken.
>

## <a name="helpful-content"></a>Nuttige inhoud

| Content | Koppeling |
|---|---|
|**SDK downloaden**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-documentatie** | [Naslag documentatie voor Java API](/java/api/overview/azure/cosmosdb/client) |
|**Bijdragen aan SDK** | [Azure SDK voor Java Central opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Aan de slag** | [Quickstart: Een Java-app bouwen om Azure Cosmos DB SQL API-gegevens te beheren](./create-sql-api-java.md) <br> [GitHub opslag plaats met Quick Start-code](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Basic-code voorbeelden** | [Azure Cosmos DB: Java-voorbeelden voor de SQL API](sql-api-java-sdk-samples.md) <br> [GitHub opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Console-app met wijzigings feed**| [Wijzigings feed-Java SDK v4-voor beeld](create-sql-api-java-changefeed.md) <br> [GitHub opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Voor beeld van web-app**| [Een web-app bouwen met Java SDK v4](sql-api-java-application.md) <br> [GitHub opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tips voor prestaties**| [Tips voor betere prestaties voor de Java-SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java-SDK v4 oplossen](troubleshoot-java-sdk-v4-sql.md) |
| **Migreren naar v4 vanuit een oudere SDK** | [Migreren naar Java v4-SDK](migrate-java-v4-sdk.md) |
| **Minimale ondersteunde runtime**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB workshops en Labs** |[Start pagina van Cosmos DB workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Volgende stappen
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
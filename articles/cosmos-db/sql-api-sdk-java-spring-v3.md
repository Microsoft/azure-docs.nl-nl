---
title: Lente data Azure Cosmos DB v3 voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de lente gegevens Azure Cosmos DB v3 voor SQL API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/28/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9c3209895902a11ad0b9f29ff28e9ac7f845b101
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692722"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Lente gegevens Azure Cosmos DB v3 voor Core-API (SQL): release opmerkingen en bronnen
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

Met de lente gegevens Azure Cosmos DB versie 3 voor core (SQL) kunnen ontwikkel aars Azure Cosmos DB gebruiken in lente-toepassingen. Lente gegevens Azure Cosmos DB maakt de lente gegevens interface beschikbaar voor het bewerken van data bases en verzamelingen, het werken met documenten en het uitgeven van query's. Zowel synchronisatie-als async-Api's (reactieve) worden ondersteund in hetzelfde maven-artefact. 

Lente gegevens Azure Cosmos DB een afhankelijkheid hebben van het lente data Framework. In het Azure Cosmos DB SDK-team worden maven-artefacten vrijgegeven voor lente gegevens versies 2,2 en 2,3.

Het [lente-Framework](https://spring.io/projects/spring-framework) is een programmeer-en configuratie model dat het ontwikkelen van Java-toepassingen stroomlijnt. Lente stroomlijnt de ' sanitaire ' van toepassingen met behulp van afhankelijkheids injectie. Veel ontwikkel aars, zoals lente, maken het bouwen en testen van toepassingen eenvoudiger. [Spring boot](https://spring.io/projects/spring-boot) breidt deze verwerking van de sanitaire toepassing uit met een ogen ding voor de ontwikkeling van een web-app en micro Services. [Lente gegevens](https://spring.io/projects/spring-data) zijn een programmeer model en een framework voor toegang tot gegevens opslag zoals Azure Cosmos DB vanuit de context van een lente-of Spring boot-toepassing. 

U kunt lente gegevens Azure Cosmos DB gebruiken in uw [Azure lente-Cloud](https://azure.microsoft.com/services/spring-cloud/) toepassingen.

> [!IMPORTANT]  
> Deze release-opmerkingen horen bij versie 3 van Spring Data Azure Cosmos DB. Hier vindt u [opmerkingen bij de release van versie 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Lees deze artikelen voor informatie over Spring Data op andere Azure Cosmos DB-API's:
> * [Spring Data voor Apache Cassandra met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Snel aan de slag

  Ga aan de slag met lente data Azure Cosmos DB door de [lente boot Starter Guide](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)te volgen. De Spring boot-start methode is de aanbevolen manier om aan de slag te gaan met de lente gegevens Azure Cosmos DB-connector.

  U kunt ook de lente gegevens Azure Cosmos DB afhankelijkheid toevoegen aan uw `pom.xml` bestand, zoals hieronder wordt weer gegeven:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Nuttige inhoud

| Content | Koppeling |
|---|---|
|**SDK downloaden**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API-documentatie** | [Naslag documentatie voor Java API](/java/api/com.azure.spring.data.cosmos) |
|**Bijdragen aan SDK** | [Azure SDK voor Java Central opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Aan de slag** | [Quick Start: een lente data Azure Cosmos DB-app maken voor het beheren van Azure Cosmos DB SQL-API-gegevens](./create-sql-api-spring-data.md) <br> [GitHub opslag plaats met Quick Start-code](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Basic-code voorbeelden** | [Azure Cosmos DB: voor beelden van Lente gegevens Azure Cosmos DB voor de SQL-API](sql-api-spring-data-sdk-samples.md) <br> [GitHub opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Tips voor prestaties**| [Tips voor prestaties voor Java SDK v4 (van toepassing op lente gegevens)](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java SDK v4 oplossen (van toepassing op lente gegevens)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshops en Labs** |[Start pagina van Cosmos DB workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Lente gegevens Azure Cosmos DB ondersteunen Java JDK 8 en Java JDK 11.
* Lente data 2,3 wordt momenteel ondersteund. er wordt momenteel geen lente data 2,4 ondersteund.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over het [lente-Framework](https://spring.io/projects/spring-framework).

Meer informatie over [Spring boot](https://spring.io/projects/spring-boot).

Meer informatie over [lente gegevens](https://spring.io/projects/spring-data).
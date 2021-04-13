---
title: Spring Data Azure Cosmos DB v3 for SQL API release notes and resources (Release-opmerkingen en resources voor Spring Data Azure Cosmos DB v3 voor SQL-API)
description: Meer informatie over de Spring Data Azure Cosmos DB v3 voor SQL-API, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363509"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 for Core (SQL) API: Opmerkingen bij de release en resources
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

Met de Spring Data Azure Cosmos DB versie 3 voor Core (SQL) kunnen ontwikkelaars Azure Cosmos DB in Spring-toepassingen. Spring Data Azure Cosmos DB de Spring Data-interface beschikbaar voor het bewerken van databases en verzamelingen, het werken met documenten en het uitgeven van query's. Synchronisatie- en Asynchrone (reactieve) API's worden ondersteund in hetzelfde Maven-artefact. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB is afhankelijk van het Spring Data-framework.
> 
> azure-spring-data-cosmos-versies van 3.0.0 tot 3.4.0 ondersteunen Spring Data-versies 2.2 en 2.3.
> 
> azure-spring-data-cosmos versie 3.5.0 en hoger ondersteunen Spring Data-versies 2.4.3 en hoger.
>

Het [Spring Framework](https://spring.io/projects/spring-framework) is een programmeer- en configuratiemodel dat de ontwikkeling van Java-toepassingen stroomlijnt. Spring stroomlijnt de 'aansluitingen' van toepassingen met behulp van afhankelijkheidsinjectie. Veel ontwikkelaars houden van Spring omdat het bouwen en testen van toepassingen eenvoudiger wordt. [Spring Boot](https://spring.io/projects/spring-boot) breidt deze verwerking van de aansluitingen uit met het oog op de ontwikkeling van webtoepassing en microservices. [Spring Data](https://spring.io/projects/spring-data) is een programmeermodel en framework voor toegang tot gegevensstores zoals Azure Cosmos DB vanuit de context van een Spring- of Spring Boot toepassing. 

U kunt Spring Data-Azure Cosmos DB gebruiken [](https://azure.microsoft.com/services/spring-cloud/) in uw Azure Spring Cloud toepassingen.

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

  Ga aan de haal met Spring Data Azure Cosmos DB door onze Spring Boot [Starter-handleiding te volgen.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) De Spring Boot Starter-methode is de aanbevolen manier om aan de slag te gaan met de Spring Data Azure Cosmos DB-connector.

  U kunt ook de Spring Data-afhankelijkheid Azure Cosmos DB toevoegen aan uw `pom.xml` bestand, zoals hieronder wordt weergegeven:

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
|**API-documentatie** | [Java API-referentiedocumentatie](/java/api/com.azure.spring.data.cosmos) |
|**Bijdragen aan de SDK** | [Azure SDK voor Java Central-opslagplaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Aan de slag** | [Quickstart: Een Spring Data Azure Cosmos DB-app maken voor het beheren van Azure Cosmos DB SQL API-gegevens](./create-sql-api-spring-data.md) <br> [GitHub-opslagplaats met snelstartcode](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Eenvoudige codevoorbeelden** | [Azure Cosmos DB: Spring Data Azure Cosmos DB voorbeelden voor de SQL-API](sql-api-spring-data-sdk-samples.md) <br> [GitHub-opslagplaats met voorbeeldcode](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Tips voor prestaties**| [Tips voor betere prestaties voor Java SDK v4 (van toepassing op Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java SDK v4 oplossen (van toepassing op Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshops en labs** |[Cosmos DB startpagina van workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Spring Data Azure Cosmos DB ondersteuning voor Java JDK 8 en Java JDK 11.
* Spring Data 2.3 wordt momenteel ondersteund, Spring Data 2.4 wordt momenteel niet ondersteund.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over het [Spring Framework](https://spring.io/projects/spring-framework).

Meer informatie over [Spring Boot](https://spring.io/projects/spring-boot).

Meer informatie over [Spring Data.](https://spring.io/projects/spring-data)
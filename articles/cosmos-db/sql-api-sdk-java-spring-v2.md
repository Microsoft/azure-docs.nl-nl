---
title: Spring Data Azure Cosmos DB v2 voor release-opmerkingen en resources van SQL-API
description: Meer informatie over de Spring Data Azure Cosmos DB v2 voor SQL-API, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363441"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 for Core (SQL) API: Opmerkingen bij de release en resources
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

 Met Spring Data Azure Cosmos DB versie 2 voor Core (SQL) kunnen ontwikkelaars Azure Cosmos DB gebruiken in Spring-toepassingen. Spring Data Azure Cosmos DB de Spring Data-interface beschikbaar voor het bewerken van databases en verzamelingen, het werken met documenten en het uitgeven van query's. Synchronisatie- en Async-API's (Reactieve) worden ondersteund in hetzelfde Maven-artefact. 

Het [Spring Framework](https://spring.io/projects/spring-framework) is een programmeer- en configuratiemodel dat de ontwikkeling van Java-toepassingen stroomlijnt. Spring stroomlijnt de 'aansluitingen' van toepassingen met behulp van afhankelijkheidsinjectie. Veel ontwikkelaars houden van Spring, omdat het bouwen en testen van toepassingen hierdoor eenvoudiger wordt. [Spring Boot](https://spring.io/projects/spring-boot) breidt deze verwerking van de aansluitingen uit met het oog op de ontwikkeling van webtoepassing en microservices. [Spring Data](https://spring.io/projects/spring-data) is een programmeermodel voor toegang tot gegevensstores zoals Azure Cosmos DB vanuit de context van een Spring- of Spring Boot toepassing. 

U kunt Spring Data-Azure Cosmos DB gebruiken [](https://azure.microsoft.com/services/spring-cloud/) in uw Azure Spring Cloud toepassingen.

> [!IMPORTANT]  
> Deze releasenotities zijn voor versie 2 van Spring Data Azure Cosmos DB. [Release-opmerkingen voor versie 3 vindt u hier](sql-api-sdk-java-spring-v3.md). 
>
> Spring Data Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Zie de volgende artikelen voor informatie over Spring Data op andere Azure Cosmos DB API's:
> * [Spring Data voor Apache Cassandra met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Wilt u snel aan de haal?
> 1. Installeer de [minimaal ondersteunde Java-runtime, JDK 8,](/java/azure/jdk/)zodat u de SDK kunt gebruiken.
> 2. Maak een Spring Data Azure Cosmos DB-app met behulp van de [starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Eenvoudiger kan niet!
> 3. Doorloopt de [Ontwikkelaarshandleiding Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)Spring Data, die u helpt bij het maken van Azure Cosmos DB basisaanvragen.
>
> U kunt snel Spring Boot Starter-apps maken met behulp van [Spring Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Resources

| Resource | Koppeling |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-documentatie** | [Spring Data Azure Cosmos DB-referentiedocumentatie]() |
|**Bijdragen aan de SDK** | [Spring Data Azure Cosmos DB-opslagplaats op GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot Starter-clientbibliotheek voor Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Voorbeeld van spring-todo-app met Azure Cosmos DB**| [End-to-end Java Experience in App Service Linux (deel 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Handleiding voor ontwikkelaars** | [Ontwikkelaarshandleiding voor Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Starter gebruiken** | [Spring Boot Starter gebruiken met de Azure Cosmos DB SQL-API](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-opslagplaats voor Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Voorbeeld met Azure App Service** | [Spring en Azure Cosmos DB met App Service op Linux gebruiken](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Voorbeeld van todo-app](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Releasegeschiedenis

### <a name="230-may-21-2020"></a>2.3.0 (21 mei 2020)
#### <a name="new-features"></a>Nieuwe functies
* Updates Spring Boot versie naar 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 mei 2020)
#### <a name="new-features"></a>Nieuwe functies
* Updates Azure Cosmos DB versie 3.7.3.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Bevat oplossingen voor geheugenlekken en upgrades van netty-versies Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 april 2020)
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* De vlag `allowTelemetry` wordt opgelost om rekening te houden met vanuit `CosmosDbConfig` .
* De eigenschap `TTL` voor de container wordt opgelost.

### <a name="223-february-25-2020"></a>2.2.3 (25 februari 2020)
#### <a name="new-features"></a>Nieuwe functies
* Nieuwe toegevoegd `findAll` op partitiesleutel-API.
* Updates Azure Cosmos DB versie naar 3.7.0.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Herstelt `collectionName`  ->  `containerName` .
* Oplossingen `entityClass` en `domainClass`  ->  `domainType` .
* Probleem opgelost met 'Retourentiteitverzameling opgeslagen door opslagplaats in plaats van invoerentiteit'.

### <a name="2110-february-25-2020"></a>2.1.10 (25 februari 2020)
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Oplossing voor backports voor 'Retourentiteitverzameling opgeslagen door opslagplaats in plaats van invoerentiteit'.

### <a name="222-january-15-2020"></a>2.2.2 (15 januari 2020)
#### <a name="new-features"></a>Nieuwe functies
* Updates Azure Cosmos DB versie naar 3.6.0.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten

### <a name="221-december-31-2019"></a>2.2.1 (31 december 2019)
#### <a name="new-features"></a>Nieuwe functies
* Updates Azure Cosmos DB SDK-versie naar 3.5.0.
* Voegt een aantekeningveld toe om het automatisch maken van verzamelingen in of uit te schakelen.
* Verbetert de afhandeling van uitzonderingen. Wordt via `CosmosClientException` `CosmosDBAccessException` bemaskerd.
* Maakt en `requestCharge` `activityId` via `ResponseDiagnostics` weer.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* SDK 3.5.0 update fixes "Exception when Cosmos DB HTTP response header is larger than 8192 bytes," "ConsistencyPolicy.defaultConsistencyLevel() fails on Bounded Staleness and Consistent Prefix."
* Het gedrag `findById` van de methode wordt opgelost. Voorheen retourneerde deze methode leeg als de entiteit niet werd gevonden in plaats van een uitzondering te geven.
* Lost een fout op waarbij sorteren niet is toegepast op de volgende pagina toen `CosmosPageRequest` werd gebruikt.

### <a name="219-december-26-2019"></a>2.1.9 (26 december 2019)
#### <a name="new-features"></a>Nieuwe functies
* Voegt een aantekeningveld toe om het automatisch maken van verzamelingen in of uit te schakelen.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
*  Het gedrag `findById` van de methode wordt opgelost. Voorheen retourneerde deze methode leeg als de entiteit niet werd gevonden in plaats van een uitzondering te geven.

### <a name="220-october-21-2019"></a>2.2.0 (21 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* Voltooi de ondersteuning voor Reactive Cosmos Repository.
* Azure Cosmos DB ondersteuning voor Query Diagnostics String en Query Metrics.
* Azure Cosmos DB SDK-versie-update naar 3.3.1.
* Spring Framework-versie-upgrade naar 5.2.0.RELEASE.
* Spring Data Commons-versie-upgrade naar 2.2.0.RELEASE.
* Voegt `findByIdAndPartitionKey` en `deleteByIdAndPartitionKey` API's toe.
* Hiermee verwijdert u afhankelijkheid uit azure-documentdb.
* DocumentDB wordt opnieuw Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Oplossing voor 'Sorteren geeft een uitzondering wanneer pageSize kleiner is dan het totale aantal items in de opslagplaats.'

### <a name="218-october-18-2019"></a>2.1.8 (18 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* DocumentDB-API's zijn afgeschaft.
* Voegt `findByIdAndPartitionKey` en `deleteByIdAndPartitionKey` API's toe.
* Voegt optimistische vergrendeling toe op basis van `_etag` .
* Hiermee schakelt u SpEL-expressie in voor de naam van de documentverzameling.
* Voegt `ObjectMapper` verbeteringen toe.

### <a name="217-october-18-2019"></a>2.1.7 (18 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* Voegt Azure Cosmos DB afhankelijkheid van SDK versie 3 toe.
* Reactieve Cosmos-opslagplaats toegevoegd.
* Werkt de implementatie van `DocumentDbTemplate` bij om Azure Cosmos DB SDK versie 3 te gebruiken.
* Voegt andere configuratiewijzigingen toe voor ondersteuning voor reactieve Cosmos-opslagplaatsen.

### <a name="212-march-19-2019"></a>2.1.2 (19 maart 2019)
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Hiermee verwijdert `applicationInsights` u de afhankelijkheid voor:
    * Mogelijk risico op afhankelijkheden die verderend zijn.
    * Incompatibiliteit met Java 11.
    * Mogelijke prestatie-impact op CPU en/of geheugen voorkomen.

### <a name="207-march-20-2019"></a>2.0.7 (20 maart 2019)
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Met backport wordt `applicationInsights` de afhankelijkheid verwijderd voor:
    * Mogelijk risico op afhankelijkheden die verderend zijn.
    * Incompatibiliteit met Java 11.
    * Mogelijke prestatie-impact op CPU en/of geheugen voorkomen.

### <a name="211-march-7-2019"></a>2.1.1 (7 maart 2019)
#### <a name="new-features"></a>Nieuwe functies
* Werkt de hoofdversie bij naar 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 maart 2019)
#### <a name="new-features"></a>Nieuwe functies
* Negeer alle uitzonderingen van telemetrie.

### <a name="210-december-17-2018"></a>2.1.0 (17 december 2018)
#### <a name="new-features"></a>Nieuwe functies
* Werkt versie 2.1.0 bij om het probleem op te lossen.

### <a name="205-september-13-2018"></a>2.0.5 (13 september 2018)
#### <a name="new-features"></a>Nieuwe functies
* Voegt trefwoorden en `exists` `startsWith` toe.
* Leesmij werkt bij.
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Probleem opgelost met 'Kan self-href niet rechtstreeks aanroepen voor entiteit'.
* Oplossing voor 'findAll mislukt als de verzameling niet is gemaakt'.

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (prerelease) (23 augustus 2018)
#### <a name="new-features"></a>Nieuwe functies
* Wijzigt de naam van het pakket van documentdb in cosmosdb.
* Voegt een nieuwe functie toe van het trefwoord van de querymethode. Er worden nu 16 trefwoorden uit de SQL-API ondersteund.
* Nieuwe functie van query toegevoegd met paginering en sortering.
* Vereenvoudigt de configuratie van spring-data-cosmosdb.
* Voegt `deleteCollection` en `deleteAll` API's toe.

#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Opgeloste fouten en oplossingen voor defecten.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over het [Spring Framework](https://spring.io/projects/spring-framework).

Meer informatie over [Spring Boot](https://spring.io/projects/spring-boot).

Meer informatie over [Spring Data](https://spring.io/projects/spring-data).
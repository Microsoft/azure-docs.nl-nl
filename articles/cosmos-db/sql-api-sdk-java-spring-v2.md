---
title: Lente gegevens Azure Cosmos DB v2 voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de lente gegevens Azure Cosmos DB v2 voor SQL API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1f78d7185415b66445c78f3b4cdb54ed0d63cb0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216612"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Lente gegevens Azure Cosmos DB v2 voor Core-API (SQL): release opmerkingen en bronnen
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

 Lente gegevens Azure Cosmos DB versie 2 voor core (SQL) kunnen ontwikkel aars Azure Cosmos DB gebruiken in lente-toepassingen. Lente gegevens Azure Cosmos DB maakt de lente gegevens interface beschikbaar voor het bewerken van data bases en verzamelingen, het werken met documenten en het uitgeven van query's. Zowel synchronisatie-als async-Api's (reactieve) worden ondersteund in hetzelfde maven-artefact. 

Het [lente-Framework](https://spring.io/projects/spring-framework) is een programmeer-en configuratie model dat het ontwikkelen van Java-toepassingen stroomlijnt. Lente stroomlijnt de ' sanitaire ' van toepassingen met behulp van afhankelijkheids injectie. Veel ontwikkel aars, zoals lente, maken het bouwen en testen van toepassingen eenvoudiger. [Spring boot](https://spring.io/projects/spring-boot) breidt deze verwerking van de sanitaire toepassing uit met een ogen ding voor de ontwikkeling van een web-app en micro Services. [Lente gegevens](https://spring.io/projects/spring-data) is een programmeer model voor het verkrijgen van toegang tot gegevens opslag zoals Azure Cosmos DB vanuit de context van een lente-of Spring boot-toepassing. 

U kunt lente gegevens Azure Cosmos DB gebruiken in uw [Azure lente-Cloud](https://azure.microsoft.com/services/spring-cloud/) toepassingen.

> [!IMPORTANT]  
> Deze release opmerkingen zijn voor versie 2 van Lente gegevens Azure Cosmos DB. U vindt [hier release opmerkingen voor versie 3](sql-api-sdk-java-spring-v3.md). 
>
> Spring Data Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Raadpleeg de volgende artikelen voor informatie over Lente gegevens op andere Azure Cosmos DB-Api's:
> * [Spring Data voor Apache Cassandra met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Wilt u snel aan de slag?
> 1. Installeer de [Mini maal ondersteunde Java-runtime, JDK 8](/java/azure/jdk/), zodat u de SDK kunt gebruiken.
> 2. Maak een lente data Azure Cosmos DB-app met behulp van de [starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Eenvoudiger kan niet!
> 3. Werk met de [lente gegevens Azure Cosmos DB ontwikkelaars handleiding](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), die de basis Azure Cosmos DB aanvragen doorloopt.
>
> U kunt snel opvallen van Lente-boot starter-apps met een [lente initialisatie functie](https://start.spring.io/).
>

## <a name="resources"></a>Resources

| Resource | Koppeling |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-documentatie** | [Referentie documentatie voor lente gegevens Azure Cosmos DB]() |
|**Bijdragen aan de SDK** | [Lente gegevens Azure Cosmos DB opslag plaats op GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring boot starter**| [Azure Cosmos DB lente boot starter-client bibliotheek voor Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Voor beeld van een app voor een nieuwe TODO met Azure Cosmos DB**| [End-to-end Java-ervaring in App Service Linux (deel 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Hand leiding voor ontwikkel aars** | [Ontwikkelaarshandleiding voor Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Starter gebruiken** | [Spring boot starter gebruiken met de Azure Cosmos DB SQL-API](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub opslag plaats voor Azure Cosmos DB Spring boot starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Voor beeld met Azure App Service** | [Spring en Azure Cosmos DB met App Service op Linux gebruiken](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Voor beeld van TODO-app](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Release geschiedenis

### <a name="230-may-21-2020"></a>2.3.0 (21 mei 2020)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt de installatie van veer boot naar 2.3.0 bijgewerkt.


### <a name="225-may-19-2020"></a>2.2.5 (19 mei 2020)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt Azure Cosmos DB versie bijgewerkt naar 3.7.3.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Bevat patches voor geheugen lekkage en Netty-versie-upgrades van Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 april 2020)
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt de `allowTelemetry` vlag opgelost waarmee rekening moet worden gehouden `CosmosDbConfig` .
* Hiermee wordt de `TTL` eigenschap voor de container opgelost.

### <a name="223-february-25-2020"></a>2.2.3 (25 februari 2020)
#### <a name="new-features"></a>Nieuwe functies
* Nieuwe `findAll` by-partitie sleutel-API toevoegen.
* Hiermee wordt Azure Cosmos DB versie bijgewerkt naar 3.7.0.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Oplossingen `collectionName`  ->  `containerName` .
* Oplossingen `entityClass` en `domainClass`  ->  `domainType` .
* Corrigeert ' entiteit verzameling retour neren die is opgeslagen door de opslag plaats in plaats van een invoer entiteit '.

### <a name="2110-february-25-2020"></a>2.1.10 (25 februari 2020)
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Backports-oplossing voor het retour neren van entiteits verzameling die is opgeslagen door de opslag plaats in plaats van de invoer entiteit.

### <a name="222-january-15-2020"></a>2.2.2 (15 januari 2020)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt Azure Cosmos DB versie bijgewerkt naar 3.6.0.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="221-december-31-2019"></a>2.2.1 (31 december 2019)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt Azure Cosmos DB SDK-versie bijgewerkt naar 3.5.0.
* Hiermee wordt een aantekening veld toegevoegd om het automatisch maken van verzamelingen in of uit te scha kelen.
* Verbetert de verwerking van uitzonde ringen. Wordt weer `CosmosClientException` gegeven `CosmosDBAccessException` .
* Beschrijft `requestCharge` en `activityId` via `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* SDK 3.5.0 update Fixes ' uitzonde ring wanneer Cosmos DB HTTP-reactie header groter is dan 8192 bytes, ' "ConsistencyPolicy. defaultConsistencyLevel () mislukt bij gebonden veroudering en consistent voor voegsel. '
* Hiermee wordt `findById` het gedrag van de methode opgelost. Voorheen retourneerde deze methode leeg als de entiteit niet is gevonden in plaats van een uitzonde ring te genereren.
* Hiermee wordt een bug opgelost waarbij sorteren niet is toegepast op de volgende pagina toen `CosmosPageRequest` werd gebruikt.

### <a name="219-december-26-2019"></a>2.1.9 (26 december 2019)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt een aantekening veld toegevoegd om het automatisch maken van verzamelingen in of uit te scha kelen.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
*  Hiermee wordt `findById` het gedrag van de methode opgelost. Voorheen retourneerde deze methode leeg als de entiteit niet is gevonden in plaats van een uitzonde ring te genereren.

### <a name="220-october-21-2019"></a>2.2.0 (21 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* Cosmos-ondersteuning voor reactieve opslag plaatsen volt ooien.
* Azure Cosmos DB-teken reeks voor diagnostische gegevens en query metrische ondersteuning.
* Azure Cosmos DB SDK-versie-update 3.3.1.
* Upgrade van Lente Framework-versie naar 5.2.0. RELEASE.
* Upgrade naar 2.2.0. RELEASE van Lente data Commons-versie.
* Toevoegen `findByIdAndPartitionKey` en `deleteByIdAndPartitionKey` api's.
* Hiermee verwijdert u de afhankelijkheid van Azure-documentdb.
* Rebrandt DocumentDB aan Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Oplossingen voor het sorteren van een uitzonde ring wanneer pageSize minder is dan het totale aantal items in de opslag plaats.

### <a name="218-october-18-2019"></a>2.1.8 (18 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* Reafschaffing DocumentDB-Api's.
* Toevoegen `findByIdAndPartitionKey` en `deleteByIdAndPartitionKey` api's.
* Hiermee wordt optimistische vergren deling toegevoegd op basis van `_etag` .
* Hiermee wordt de SpEL-expressie voor de naam van de document verzameling ingeschakeld.
* Hiermee worden `ObjectMapper` verbeteringen toegevoegd.

### <a name="217-october-18-2019"></a>2.1.7 (18 oktober 2019)
#### <a name="new-features"></a>Nieuwe functies
* Voegt Azure Cosmos DB SDK versie 3 afhankelijkheid toe.
* Hiermee wordt een reactieve Cosmos-opslag plaats toegevoegd.
* Update-implementatie van `DocumentDbTemplate` om Azure Cosmos DB SDK-versie 3 te gebruiken.
* Voegt andere configuratie wijzigingen toe voor de ondersteuning van Reactive Cosmos-opslag plaatsen.

### <a name="212-march-19-2019"></a>2.1.2 (19 maart 2019)
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* `applicationInsights`Afhankelijkheid verwijderen voor:
    * Mogelijk risico van verontreiniging van afhankelijkheden.
    * Java 11 incompatibiliteit.
    * Voorkom mogelijke prestatie-impact op CPU en/of geheugen.

### <a name="207-march-20-2019"></a>2.0.7 (20 maart 2019)
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Backport verwijdert `applicationInsights` afhankelijkheid voor:
    * Mogelijk risico van verontreiniging van afhankelijkheden.
    * Java 11 incompatibiliteit.
    * Voorkom mogelijke prestatie-impact op CPU en/of geheugen.

### <a name="211-march-7-2019"></a>2.1.1 (7 maart 2019)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt de hoofd versie bijgewerkt naar 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 maart 2019)
#### <a name="new-features"></a>Nieuwe functies
* Alle uitzonde ringen van telemetrie negeren.

### <a name="210-december-17-2018"></a>2.1.0 (17 december 2018)
#### <a name="new-features"></a>Nieuwe functies
* Hiermee wordt versie bijgewerkt naar 2.1.0 om het probleem op te lossen.

### <a name="205-september-13-2018"></a>2.0.5 (13 september 2018)
#### <a name="new-features"></a>Nieuwe functies
* Tref woorden toevoegen `exists` en `startsWith` .
* Leesmij voor updates.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Fixes ' kan Self href niet rechtstreeks voor entiteit aanroepen. '
* Fixes "findAll mislukt als de verzameling niet wordt gemaakt."

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (Prerelease) (23 augustus 2018)
#### <a name="new-features"></a>Nieuwe functies
* De naam van het pakket wordt gewijzigd van documentdb in cosmosdb.
* Hiermee voegt u een nieuwe functie van het sleutel woord query methode toe. 16 tref woorden uit de SQL-API worden nu ondersteund.
* Hiermee wordt een nieuwe functie van de query toegevoegd met paginering en sorteren.
* Vereenvoudigt de configuratie van de lente-data-cosmosdb.
* Toevoegen `deleteCollection` en `deleteAll` api's.

#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Problemen oplossen en afbreken.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over het [lente-Framework](https://spring.io/projects/spring-framework).

Meer informatie over [Spring boot](https://spring.io/projects/spring-boot).

Meer informatie over [lente gegevens](https://spring.io/projects/spring-data).
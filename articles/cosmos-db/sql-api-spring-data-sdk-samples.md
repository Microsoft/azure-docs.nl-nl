---
title: 'Azure Cosmos DB SQL API: Spring Data v3-voorbeelden'
description: Vind Spring Data v3-voorbeelden op GitHub voor veelvoorkomende taken met de SQL-API in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93091137"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: Spring Data Azure Cosmos DB v3-voorbeelden
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
> Deze release-opmerkingen horen bij versie 3 van Spring Data Azure Cosmos DB. Hier vindt u [opmerkingen bij de release van versie 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Lees deze artikelen voor informatie over Spring Data op andere Azure Cosmos DB-API's:
> * [Spring Data voor Apache Cassandra met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin met Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

De nieuwste voorbeeldtoepassingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Spring Data Azure Cosmos DB-voorbeeldprojectbestanden. 
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

U hebt het volgende nodig om deze voorbeeldtoepassing uit te voeren:

* Java Development Kit 8
* Spring Data Azure Cosmos DB v3

U kunt eventueel Maven gebruiken om de recentste binaire Spring Data Azure Cosmos DB v3-bestanden op te halen voor gebruik in uw project. Maven voegt automatisch eventuele vereiste afhankelijkheden toe. Anders kunt u de afhankelijkheden die worden vermeld in het bestand **pom.xml**, rechtstreeks downloaden en toevoegen aan uw build-pad.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**De voorbeeldtoepassingen uitvoeren**

De voorbeeldopslagplaats klonen:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

U kunt de voorbeelden uitvoeren met behulp van een IDE (Eclipse, IntelliJ of VSCODE) of vanaf de opdrachtregel met behulp van Maven.

Deze omgevingsvariabelen moeten worden ingesteld in **application.properties**

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

om de voorbeelden lees-en schrijftoegang tot uw account, databases en containers te geven.

Uw IDE kan u de mogelijkheid bieden om de voorbeeldcode van Spring Data uit te voeren. Anders kunt u de volgende terminalopdracht gebruiken om het voorbeeld uit te voeren:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>CRUD-voorbeelden van documenteren
Het bestand [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) laat u zien hoe u de volgende taken uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over Azure Cosmos-documenten voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een document maken](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [Een document lezen op id](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Alle documenten verwijderen](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Voorbeelden van afgeleide querymethoden
Het bestand [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) laat u zien hoe u de volgende taken uitvoert. Als u meer wilt weten over Azure Cosmos DB-query's voordat u de volgende voo beelden uitvoert, kan het zinvol zijn om het artikel [Afgeleide querymethoden van Baeldung in Spring](https://www.baeldung.com/spring-data-derived-queries) te lezen.

| [Voer een query uit voor documenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository.derivedQueryMethod |

## <a name="custom-query-examples"></a>Voorbeelden van aangepaste query's
In het bestand [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) ziet u hoe u de volgende taken kunt uitvoeren met behulp van de SQL-querygrammatica. Zie [SQL-queryvoorbeelden voor Azure Cosmos DB](./sql-query-getting-started.md) voor meer informatie over de SQL-queryreferentie in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 


| Taak | API-verwijzing |
| --- | --- |
| [Een query uitvoeren voor alle documenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query-annotatie |
| [Query uitvoeren voor gelijkheid met behulp van ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query-annotatie |
| [Query uitvoeren voor ongelijkheid met behulp van != en NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query-annotatie |
| [Query uitvoeren met behulp van bereikoperators zoals >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query-annotatie |
| [Query uitvoeren met behulp van bereikoperators op tekenreeksen](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query-annotatie |
| [Query uitvoeren met ORDER BY](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query-annotatie |
| [Query uitvoeren met DISTINCT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query-annotatie |
| [Query uitvoeren met statistische functies](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query-annotatie |
| [Werken met subdocumenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query-annotatie |
| [Query uitvoeren met Joins tussen documenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query-annotatie |
| [Query uitvoeren met tekenreeks-, berekenings- en matrixoperators](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query-annotatie |
---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector for SQL API (Preview) release notes and resources (3 OLTP-connector voor SQL API (preview)-releasenotities en -resources
description: Meer informatie over de Azure Cosmos DB Apache Spark 3 OLTP-connector voor SQL API (preview), met inbegrip van releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368607"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP Connector for Core (SQL) API (preview): opmerkingen bij de release en resources
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

**Azure Cosmos DB Spark 3 OLTP-connector (preview)** biedt Apache Spark v3-ondersteuning voor Azure Cosmos DB met behulp van de SQL-API.
[Azure Cosmos DB](introduction.md) is een wereldwijd gedistribueerde databaseservice waarmee ontwikkelaars met gegevens kunnen werken met behulp van verschillende standaard-API's, zoals SQL, MongoDB, Cassandra, Graph en Table.

> [!Note]
> Deze versie van Azure Cosmos DB Spark 3 OLTP-connector is een preview-build.
> Deze build is niet geladen of de prestaties zijn getest.
> Deze build wordt niet aanbevolen voor gebruik in productiescenario's.
>

## <a name="documentation"></a>Documentatie

- [Aan de slag](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Naslag voor configuratieparameters](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Versiecompatibiliteit

| Connector     | Spark         | Minimale Java-versie | Ondersteunde Scala-versies |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Downloaden

U kunt de Maven-coördinaat van het JAR gebruiken om de Spark-connector automatisch te installeren op Databricks Runtime 8 van Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

U kunt ook integreren met Cosmos DB Spark-connector in uw SBT-project:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark-connector is beschikbaar in [Maven Central-repo.](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)

### <a name="general"></a>Algemeen

Als u een fout tegenkomt, kunt u hier een probleem [melden.](https://github.com/Azure/azure-sdk-for-java/issues/new)

Als u een nieuwe functie of wijzigingen wilt voorstellen die kunnen worden aangebracht, kunt u een probleem op dezelfde manier indienen als bij een bug.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Volgende stappen

Lees onze [snelstartgids voor het werken met Azure Cosmos DB Spark 3 OLTP-connector](create-sql-api-spark.md).

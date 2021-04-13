---
title: Azure Cosmos DB Apache Spark 2 OLTP Connector for SQL API release notes and resources (2 OLTP-connector voor release-opmerkingen en resources voor SQL-API)
description: Meer informatie over de Azure Cosmos DB Apache Spark 2 OLTP-connector voor SQL API, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: bd948814b4b647bcc3fbfe58b090b1e794504232
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363628"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 2 OLTP Connector for Core (SQL) API: Opmerkingen bij de release en resources
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

U kunt de big data versnellen met behulp van de Azure Cosmos DB Apache Spark 2 OLTP Connector for Core (SQL). Met de Spark-connector kunt u [Spark-taken](https://spark.apache.org/) uitvoeren op gegevens die zijn opgeslagen in Azure Cosmos DB. Batch- en stroomverwerking worden ondersteund.

U kunt de connector gebruiken met [Azure Databricks](https://azure.microsoft.com/services/databricks) of [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)die beheerde Spark-clusters in Azure bieden. De volgende tabel bevat ondersteunde versies:

| Onderdeel | Versie |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x* en 2.1. *x* |
| Scala | 2,11 |
| Azure Databricks (runtimeversie) | Hoger dan 3.4 |

> [!WARNING]
> Deze connector ondersteunt de kern-API (SQL) van Azure Cosmos DB.
> Gebruik voor Cosmos DB API voor MongoDB de [MongoDB-connector voor Spark](https://docs.mongodb.com/spark-connector/master/).
> Gebruik voor Cosmos DB Cassandra-API de [Cassandra Spark-connector](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Resources

| Resource | Koppeling |
|---|---|
| **SDK downloaden** | [Download de meest recente .jar,](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-documentatie** | [Naslag voor Spark-connector]() |
|**Bijdragen aan de SDK** | [Azure Cosmos DB Connector voor Apache Spark op GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Aan de slag** | [Versnel big data analyse met behulp van de Apache Spark naar Azure Cosmos DB-connector](./spark-connector.md#bk_working_with_connector) <br> [Gebruik Apache Spark Structured Streaming met Apache Kafka en Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Releasegeschiedenis

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nieuwe functies
- Hiermee voegt u een nieuwe configuratieoptie toe, , die kan worden gebruikt om de begintijd op te geven voor het moment waarop de `changefeedstartfromdatetime` changefeed moet worden verwerkt. Zie Configuratieopties [voor meer informatie.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
- Hiermee wordt een regressie opgelost die overmatig geheugenverbruik op de uitvoerders heeft veroorzaakt voor grote resultatensets (bijvoorbeeld met miljoenen rijen), wat uiteindelijk resulteert in de fout `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Herstelt een streaming controlepunt rand geval waarin de `ID` het s pipe-teken (|) met de `ChangeFeedMaxPagesPerBatch` configuratie toegepast.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nieuwe functies
* Voegt ondersteuning toe voor bulkupdates wanneer geneste partitiesleutels worden gebruikt.
* Voegt ondersteuning toe voor de gegevenstypen Decimal en Float tijdens schrijf- Azure Cosmos DB.
* Voegt ondersteuning toe voor tijdstempeltypen wanneer ze Long (Unix-epoche) als waarde gebruiken.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Oplossing voor typecast-uitzondering die optreedt wanneer `WriteThroughputBudget` de configuratie wordt gebruikt.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nieuwe functies
* Hiermee voegt u foutinformatie toe voor bulkfouten in uitzonderingen en logboeken.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Problemen met streamingcontrolepunten opgelost.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Om ruis te verminderen, wordt het logboekniveau van een bericht onbedoeld opgelost met foutniveau.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Lost een fout in gestructureerd streamen op tijdens partitiesplitsingen. De fout kan leiden tot een aantal ontbrekende records van de wijzigingsfeed of Null-uitzonderingen voor schrijfpunten van controlepunten.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Lost een fout op die ervoor zorgt dat een aangepast schema dat is opgegeven voor readStream wordt genegeerd.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Herstelt een regressie (niet-gearceerde JAR omvat alle gearceerde afhankelijkheden) waardoor de bouwtijd met 50 procent wordt verhoogd.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Oplossingen voor belangrijke fouten
* Lost een afhankelijkheidsprobleem op dat ervoor zorgt dat Direct Transport via TCP mislukt met RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nieuwe functies
* Verbetert het verbindingsbeheer en de verbindingspooling om het aantal metagegevens-aanroepen te verminderen.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over [Apache Spark](https://spark.apache.org/).

---
title: Cosmos DB Apache Spark-connector voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de Azure Cosmos DB Apache Spark-connector voor SQL API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 7ff75bf9d45803dd96927bcf7c70e7c7912db979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097155"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark connector voor Core-API (SQL): release opmerkingen en bronnen
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

U kunt big data Analytics versnellen met behulp van de Azure Cosmos DB Apache Spark connector voor core (SQL). Met de Spark-connector kunt u [Spark](https://spark.apache.org/) -taken uitvoeren op gegevens die zijn opgeslagen in azure Cosmos db. Batch-en stroom verwerking worden ondersteund.

U kunt de connector gebruiken met [Azure Databricks](https://azure.microsoft.com/services/databricks) of [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), die beheerde Spark-clusters in Azure biedt. In de volgende tabel worden de ondersteunde versies weer gegeven:

| Onderdeel | Versie |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x* en 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (runtime versie) | Later dan 3,4 |

> [!WARNING]
> Deze connector ondersteunt de core-(SQL) API van Azure Cosmos DB.
> Voor de Cosmos DB-API voor MongoDB gebruikt u de [MongoDb-connector voor Spark](https://docs.mongodb.com/spark-connector/master/).
> Gebruik de [Cassandra Spark-connector](https://github.com/datastax/spark-cassandra-connector)voor de Cosmos DB Cassandra-API.
>

## <a name="resources"></a>Resources

| Resource | Koppeling |
|---|---|
| **SDK downloaden** | [Down load nieuwste. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-documentatie** | [Naslag informatie voor Spark-connector]() |
|**Bijdragen aan de SDK** | [Azure Cosmos DB connector voor Apache Spark op GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Aan de slag** | [big data Analytics versnellen met behulp van de Apache Spark om de connector te Azure Cosmos DB](./spark-connector.md#bk_working_with_connector) <br> [Apache Spark Structured streaming gebruiken met Apache Kafka en Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Release geschiedenis

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nieuwe functies
- Hiermee voegt u een nieuwe configuratie optie toe, `changefeedstartfromdatetime` die kan worden gebruikt om de start tijd op te geven waarop de changefeed moet worden verwerkt. Zie [configuratie opties](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)voor meer informatie.

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
- Hiermee wordt een regressie opgelost die het overmatige geheugen gebruik heeft veroorzaakt door de uitvoerendeers voor grote resultaten sets (bijvoorbeeld met miljoenen rijen), wat uiteindelijk resulteert in de fout `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een hoofd letter van een streaming-controle punt opgelost waarin de het `ID` sluis teken (|) bevat waarop de `ChangeFeedMaxPagesPerBatch` configuratie is toegepast.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nieuwe functies
* Voegt ondersteuning toe voor bulk updates wanneer geneste partitie sleutels worden gebruikt.
* Voegt ondersteuning toe voor de gegevens typen decimaal en float tijdens het schrijven naar Azure Cosmos DB.
* Voegt ondersteuning toe voor tijds tempel typen wanneer ze Long (UNIX-epoche) gebruiken als waarde.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een typecast-uitzonde ring opgelost die optreedt wanneer de `WriteThroughputBudget` configuratie wordt gebruikt.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nieuwe functies
* Voegt fout gegevens voor bulk fouten toe aan uitzonde ring en logboek.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee worden problemen met streaming-controle punten opgelost.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Om ruis te verminderen, herstelt het logboek niveau van een bericht per ongeluk naar een niveau fout.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een fout in gestructureerde streaming opgelost tijdens het splitsen van partities. De fout kan ertoe leiden dat sommige ontbrekende wijzigingen in de feed of null-uitzonde ringen voor schrijf bewerkingen in het controle punt worden uitgevoerd.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een bug opgelost die ervoor zorgt dat een aangepast schema dat is verschaft voor readStream, wordt genegeerd.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een regressie opgelost (ongearceerde JAR omvat alle gearceerde afhankelijkheden) die de build-tijd met 50 procent verg Roten.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een afhankelijkheids probleem opgelost dat ervoor zorgt dat direct Trans Port via TCP mislukt met RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nieuwe functies
* Verbetert het verbindings beheer en de Groepsgewijze verbinding om het aantal meta gegevens oproepen te verminderen.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over [Apache Spark](https://spark.apache.org/).

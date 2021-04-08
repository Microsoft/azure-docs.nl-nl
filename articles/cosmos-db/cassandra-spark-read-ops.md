---
title: Cassandra-API tabel gegevens lezen met behulp van Spark
titleSufix: Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u gegevens uit Cassandra-API tabellen in Azure Cosmos DB kunt lezen.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073493"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Gegevens uit Azure Cosmos DB Cassandra-API tabellen lezen met Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 In dit artikel wordt beschreven hoe u gegevens kunt lezen die zijn opgeslagen in Azure Cosmos DB Cassandra-API vanuit Spark.

## <a name="cassandra-api-configuration"></a>Cassandra-API configuratie
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Data frame-API

### <a name="read-table-using-sessionreadformat-command"></a>Tabel lezen met behulp van Session. Read. Format, opdracht

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Tabel lezen met behulp van Spark. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Specifieke kolommen in de tabel lezen

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Filters toepassen

U kunt predikaten naar de data base pushen om betere geoptimaliseerde Spark-query's toe te staan. Een predikaat is een voor waarde voor een query die waar of onwaar retourneert, meestal in de component WHERE. Met een predicaat-push omlaag worden de gegevens in de database query gefilterd, waardoor het aantal vermeldingen dat wordt opgehaald uit de data base en de query prestaties worden verbeterd. Standaard worden in de Spark-gegevensset-API automatisch geldige WHERE-componenten naar de data base gepusht. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

De sectie PushedFilters van het fysieke plan bevat het GreaterThan-push filter. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="partities":::

## <a name="rdd-api"></a>RDD-API

### <a name="read-table"></a>Tabel lezen
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Specifieke kolommen in de tabel lezen

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL-weer gaven 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Een tijdelijke weer gave maken op basis van een data frame

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Query's uitvoeren op de weer gave

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Volgende stappen

Hieronder vindt u meer artikelen over het werken met Azure Cosmos DB Cassandra-API van Spark:
 
 * [Upsert bewerkingen](cassandra-spark-upsert-ops.md)
 * [Verwijderbewerkingen](cassandra-spark-delete-ops.md)
 * [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
 * [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)


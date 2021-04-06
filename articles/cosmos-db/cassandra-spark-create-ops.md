---
title: Gegevens maken of invoegen in Azure Cosmos DB Cassandra-API vanuit Spark
description: In dit artikel wordt beschreven hoe u voorbeeld gegevens kunt invoegen in Azure Cosmos DB Cassandra-API tabellen
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 3ec44d20b763a98683d9b947c94ad6be75180113
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092233"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Gegevens maken/invoegen in Azure Cosmos DB Cassandra-API vanuit Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]
 
In dit artikel wordt beschreven hoe u voorbeeld gegevens invoegt in een tabel in Azure Cosmos DB Cassandra-API vanuit Spark.

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

### <a name="create-a-dataframe-with-sample-data"></a>Een data frame maken met voorbeeld gegevens

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> De functionaliteit ' maken indien niet bestaat ', op rijniveau, wordt nog niet ondersteund.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Blijven Azure Cosmos DB Cassandra-API

Bij het opslaan van gegevens kunt u ook time-to-Live-en consistentie beleids instellingen instellen, zoals wordt weer gegeven in het volgende voor beeld:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> TTL op kolom niveau wordt nog niet ondersteund.

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>API voor robuuste gedistribueerde data base (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Een RDD maken met voorbeeld gegevens
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Maken als de functionaliteit niet bestaat, wordt nog niet ondersteund.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Blijven Azure Cosmos DB Cassandra-API

Wanneer u gegevens opslaat voor Cassandra-API, kunt u ook time-to-Live en consistentie beleids instellingen instellen, zoals wordt weer gegeven in het volgende voor beeld:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt ingevoegd in de tabel Azure Cosmos DB Cassandra-API, gaat u door met de volgende artikelen om andere bewerkingen uit te voeren op de gegevens die zijn opgeslagen in Cosmos DB Cassandra-API:
 
* [Leesbewerkingen](cassandra-spark-read-ops.md)
* [Upsert bewerkingen](cassandra-spark-upsert-ops.md)
* [Verwijderbewerkingen](cassandra-spark-delete-ops.md)
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
* [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)


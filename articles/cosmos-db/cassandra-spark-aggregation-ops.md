---
title: Samenvoegbewerkingen op Azure Cosmos DB Cassandra-API-tabellen uit Spark
description: In dit artikel worden elementaire aggregatie bewerkingen behandeld ten opzichte van Azure Cosmos DB Cassandra-API tabellen uit Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087618"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Samenvoegbewerkingen op Azure Cosmos DB Cassandra-API-tabellen uit Spark 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel worden elementaire samenvoegbewerkingen voor Azure Cosmos DB Cassandra-API tabellen uit Spark beschreven. 

> [!NOTE]
> Filtering aan de server zijde en aggregatie op de server wordt momenteel niet ondersteund in Azure Cosmos DB Cassandra-API.

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
## <a name="sample-data-generator"></a>Voor beeld van gegevens generator

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Aantal bewerkingen


### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Uitvoer**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Data frame-API

Count op basis van dataframes wordt momenteel niet ondersteund.  In het voor beeld hieronder ziet u hoe u een data frame-aantal kunt uitvoeren nadat u de data frame als tijdelijke oplossing hebt door gegeven aan het geheugen.

Kies een [opslag optie]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) in de volgende beschik bare opties om te voor komen dat er problemen optreden met ' onvoldoende geheugen ':

* MEMORY_ONLY: dit is de standaard optie voor opslag. Slaat RDD op als gedeserialiseerd Java-objecten in de JVM. Als de RDD niet in het geheugen past, worden sommige partities niet in de cache opgeslagen en worden ze op elk gewenst moment opnieuw berekend.

* MEMORY_AND_DISK: slaat RDD op als gedeserialiseerd Java-objecten in de JVM. Als de RDD niet in het geheugen past, slaat u de partities op die niet op schijf passen en wanneer dat nodig is, leest u deze vanaf de locatie waar ze zijn opgeslagen.

* MEMORY_ONLY_SER (Java/scala): slaat RDD op als geserialiseerde Java-objecten-één-byte-matrix per partitie. Deze optie is in vergelijking met gedeserialiseerd objecten, met name bij het gebruik van een snelle serialisatiefunctie, maar meer CPU-intensief te lezen.

* MEMORY_AND_DISK_SER (Java/scala): deze opslag optie is als MEMORY_ONLY_SER, het enige verschil is dat er partities worden overgelopen die niet in het schijf geheugen passen in plaats van ze opnieuw te moeten worden gecomputing wanneer ze nodig zijn.

* DISK_ONLY: slaat alleen de RDD-partities op de schijf op.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: hetzelfde als de bovenstaande niveaus, maar repliceert elke partitie op twee cluster knooppunten.

* OFF_HEAP (experimenteel): vergelijkbaar met MEMORY_ONLY_SER, maar de gegevens worden opgeslagen in een geheugen van de buiten-heap, en hiervoor is een geheugen van een off-heap vereist om vooraf te worden ingeschakeld. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Gemiddelde bewerking

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Uitvoer**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Data frame-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Uitvoer**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Uitvoer**
```
16.016000175476073
```

## <a name="min-operation"></a>Minimale bewerking

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Uitvoer**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Data frame-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Uitvoer**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Uitvoer**
```
11.33
```

## <a name="max-operation"></a>Maximale bewerking

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Data frame-API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Uitvoer**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Uitvoer**
```
22.45
```

## <a name="sum-operation"></a>Sum-bewerking

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Uitvoer**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Data frame-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Uitvoer**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Uitvoer**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Bovenste of vergelijk bare bewerking

### <a name="rdd-api"></a>RDD-API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Uitvoer**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Data frame-API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Uitvoer**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Volgende stappen

Zie voor het uitvoeren van tabel Kopieer bewerkingen:

* [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)

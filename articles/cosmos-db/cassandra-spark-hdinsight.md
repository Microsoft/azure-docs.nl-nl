---
title: Toegang tot Azure Cosmos DB Cassandra-API vanuit Spark op GARENs met HDInsight
description: In dit artikel wordt beschreven hoe u met Azure Cosmos DB Cassandra-API kunt werken vanuit Spark op GARENs met HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359588"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Toegang tot Azure Cosmos DB Cassandra-API vanuit Spark op GARENs met HDInsight
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel wordt beschreven hoe u Azure Cosmos DB Cassandra-API vanuit Spark op GARENs kunt openen met HDInsight-Spark vanuit Spark-shell. HDInsight is de Hortonworks Hadoop PaaS van micro soft op Azure die gebruikmaakt van object opslag voor HDFS, en komt in verschillende soorten voor, waaronder [Spark](../hdinsight/spark/apache-spark-overview.md).  De inhoud van dit document verwijst naar HDInsight-Spark en is van toepassing op alle Hadoop-distributies.  

## <a name="prerequisites"></a>Vereisten

* [Azure Cosmos DB Cassandra-API inrichten](create-cassandra-dotnet.md#create-a-database-account)

* [Bekijk de basis beginselen van het maken van verbinding met Azure Cosmos DB Cassandra-API](cassandra-spark-generic.md)

* [Een HDInsight-Spark cluster inrichten](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [De code voorbeelden voor het werken met Cassandra-API bekijken](cassandra-spark-generic.md#next-steps)

* [Gebruik cqlsh voor validatie als u dat wilt](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra-API configuratie in Spark2** : de Spark-connector voor Cassandra vereist dat de Cassandra-verbindings Details worden geïnitialiseerd als onderdeel van de Spark-context. Wanneer u een Jupyter-notebook start, zijn de Spark-sessie en-context al geïnitialiseerd en is het niet raadzaam om de Spark-context te stoppen en opnieuw te initialiseren, tenzij deze is voltooid met elke configuratieset als onderdeel van de standaard instelling voor het opstarten van Jupyter-notebook van HDInsight. Een tijdelijke oplossing is om de Cassandra-exemplaar gegevens rechtstreeks toe te voegen aan de Ambari-, Spark2-service configuratie. Dit is een eenmalige activiteit per cluster waarvoor een Spark2-service opnieuw moet worden gestart.
 
  1. Ga naar Ambari, Spark2 service en selecteer configuraties.

  2. Ga vervolgens naar aangepaste spark2-standaard instellingen en voeg een nieuwe eigenschap toe met de volgende en start de Spark2-service opnieuw:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Toegang tot Azure Cosmos DB Cassandra-API vanuit Spark shell

Spark shell wordt gebruikt voor test-en exploratie doeleinden.

* Start Spark-shell met de vereiste maven-afhankelijkheden die compatibel zijn met de Spark-versie van uw cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Enkele DDL-en DML-bewerkingen uitvoeren

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* RUWE bewerkingen uitvoeren

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Toegang tot Azure Cosmos DB Cassandra-API vanuit Jupyter-notebooks

HDInsight-Spark wordt geleverd met Zeppelin-en Jupyter-notebook Services. Ze zijn zowel webgebaseerde notebook omgevingen die ondersteuning bieden voor scala en python. Notebooks zijn ideaal voor interactieve, experimentele analyses en samen werking, maar niet bedoeld voor operationele/productie processen.

U kunt de volgende Jupyter-notebooks uploaden naar uw HDInsight Spark-cluster en zo voor bereidingen voor het werken met Azure Cosmos DB Cassandra-API. Controleer het eerste notitie blok om de `1.0-ReadMe.ipynb` configuratie van Spark-services te controleren om verbinding te maken met Azure Cosmos DB Cassandra-API.

Down load deze notitie blokken onder [Azure-Cosmos-DB-Cassandra-API-Spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) naar uw computer.
  
### <a name="how-to-upload"></a>Uploaden:
Wanneer u Jupyter start, gaat u naar scala. Maak eerst een map en upload de notitie blokken naar de Directory. De knop uploaden bevindt zich boven aan de rechter kant.  

### <a name="how-to-run"></a>Uitvoeren:
Voer de notitie blokken en elke notebook-cel achter elkaar uit.  Klik op de knop uitvoeren boven aan elk notitie blok om alle cellen uit te voeren, of SHIFT + ENTER voor elke cel.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Toegang met Azure Cosmos DB Cassandra-API vanuit uw Spark scala-programma

Voor geautomatiseerde processen in productie worden Spark-Program ma's via [Spark](https://spark.apache.org/docs/latest/submitting-applications.html)verzonden naar het cluster.

## <a name="next-steps"></a>Volgende stappen

* [Een Spark scala-programma bouwen in een IDE en dit verzenden naar het HDInsight Spark-cluster via livy voor uitvoering](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Verbinding maken met Azure Cosmos DB Cassandra-API vanuit een Spark scala-programma](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Volledige lijst met code voorbeelden voor het werken met Cassandra-API](cassandra-spark-generic.md)

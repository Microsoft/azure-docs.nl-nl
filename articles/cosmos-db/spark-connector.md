---
title: Apache Spark verbinden met Azure Cosmos DB
description: Meer informatie over de Azure Cosmos DB Spark-connector waarmee u Apache Spark kunt verbinden met Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 06498a27b95a72148497efd2d1e600d802414359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359554"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>big data Analytics versnellen met behulp van de Apache Spark om de connector te Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

U kunt [Spark](https://spark.apache.org/) -taken uitvoeren met gegevens die zijn opgeslagen in azure Cosmos DB met behulp van de Cosmos DB Spark-connector. Cosmos kan worden gebruikt voor het verwerken van batches en streamen, en als een te leveren laag voor toegang met lage latentie.

U kunt de connector gebruiken met [Azure Databricks](https://azure.microsoft.com/services/databricks) of [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), die beheerde Spark-clusters in Azure biedt. In de volgende tabel worden de ondersteunde Spark-versies weer gegeven.

| Onderdeel | Versie |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x en 2.1. x |
| Scala | 2,11 |
| Runtime versie van Azure Databricks | > 3,4 |

> [!WARNING]
> Deze connector ondersteunt de core-(SQL) API van Azure Cosmos DB.
> Gebruik de [MongoDb Spark-connector](https://docs.mongodb.com/spark-connector/master/)voor Cosmos DB voor MONGODB-API.
> Voor Cosmos DB Cassandra-API gebruikt u de [Cassandra Spark-connector](https://github.com/datastax/spark-cassandra-connector).

> [!IMPORTANT]
> De Azure Cosmos DB Spark-connector wordt momenteel niet ondersteund voor [serverloze](serverless.md) accounts. Dit wordt verholpen omdat de serverloze aanbieding algemeen beschikbaar wordt.

## <a name="quickstart"></a>Snelstart

* Volg de stappen in aan [de slag met de Java SDK](./create-sql-api-java.md) om een Cosmos DB account in te stellen en een aantal gegevens in te vullen.
* Volg de stappen in [Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) aan de slag om een Azure Databricks-werk ruimte en-cluster in te stellen.
* U kunt nu nieuwe notitie blokken maken en de Cosmos DB-connector bibliotheek importeren. Ga aan [de slag met de Cosmos DB-connector](#bk_working_with_connector) voor meer informatie over het instellen van uw werk ruimte.
* De volgende sectie bevat fragmenten over het lezen en schrijven met behulp van de connector.

### <a name="batch-reads-from-cosmos-db"></a>Batch-Lees bewerkingen van Cosmos DB

Het volgende code fragment laat zien hoe u een Spark-data frame maakt om te lezen van Cosmos DB in PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

En hetzelfde code fragment in scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch schrijft naar Cosmos DB

Het volgende code fragment laat zien hoe u een gegevens frame schrijft om te Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

En hetzelfde code fragment in scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streaming-Lees bewerkingen van Cosmos DB

Het volgende code fragment laat zien hoe u verbinding maakt met en leest van Azure Cosmos DB wijzigings feed.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
En hetzelfde code fragment in scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming schrijft naar Cosmos DB

Het volgende code fragment laat zien hoe u een gegevens frame schrijft om te Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

En hetzelfde code fragment in scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Meer fragmenten en end-to-end-voor beelden vindt u in [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Werken met de connector

U kunt de connector bouwen vanuit een bron in GitHub of de uber-potten downloaden van Maven op de onderstaande koppelingen.

| Spark | Scala | Nieuwste versie |
|---|---|---|
| 2.4.0 | 2,11 | [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks-notebooks gebruiken

Maak een bibliotheek met behulp van uw Databricks-werk ruimte door de richt lijnen in de Azure Databricks Guide te volgen > [de Azure Cosmos DB Spark-connector te gebruiken](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> De pagina **Azure Cosmos DB Spark-connector gebruiken** is momenteel niet up-to-date. In plaats van de zes afzonderlijke potten in zes verschillende bibliotheken te downloaden, kunt u het uber jar downloaden van Maven op [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) en dit ene jar/bibliotheek installeren.
> 

### <a name="using-spark-cli"></a>Spark-CLI gebruiken

Als u wilt werken met de connector met behulp van de Spark-cli (dat wil zeggen,, `spark-shell` `pyspark` , `spark-submit` ), kunt u de `--packages` para meter gebruiken met de [maven-coördinaten](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)van de connector.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter-notebooks gebruiken

Als u Jupyter Notebook in HDInsight gebruikt, kunt u Spark-Magic- `%%configure` cel gebruiken om de Maven-coördinaten van de connector op te geven.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Houd er rekening mee dat de opname van de `spark.jars.excludes` specifiek is om potentiële conflicten tussen de connector, Apache Spark en livy te verwijderen.

### <a name="build-the-connector"></a>De connector bouwen

Op dit moment wordt dit connector project gebruikt `maven` om zonder afhankelijkheden te maken, kunt u het volgende uitvoeren:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Werken met onze voor beelden

De [Cosmos DB Spark github-opslag plaats](https://github.com/Azure/azure-cosmosdb-spark) heeft de volgende voorbeeld notitieblokken en-scripts die u kunt proberen.

* **On-time vlucht prestaties met Spark en Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): sluit Spark aan Cosmos DB met behulp van HDInsight Jupyter Notebook Service om Spark SQL, GraphFrames te demonstreren en vertragingen in de vlucht te voors pellen met behulp van ml-pijp lijnen.
* **Twitter-bron met Apache Spark en Azure Cosmos DB wijzigings feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Apache Spark gebruiken om Cosmos DB grafieken te doorzoeken**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Azure Databricks verbinding maken met Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** met behulp van `azure-cosmosdb-spark` .  Hier vindt u ook een Azure Databricks versie van de [laptop voor on-time vlucht prestaties](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Lambda-architectuur met Azure Cosmos DB en HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: u kunt de operationele overhead verminderen van het onderhouden van Big Data pijp lijnen met behulp van Cosmos DB en Spark.

## <a name="more-information"></a>Meer informatie

We hebben meer informatie in de `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) , waaronder:

* [Gebruikers handleiding voor de Spark-connector Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Aggregaties-voor beelden](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuratie en installatie

* [Configuratie van Spark-connector](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Setup van Spark naar Cosmos DB-connector](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (wordt uitgevoerd)
* [Power BI direct-query configureren voor Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Problemen oplossen

* [Cosmos DB aggregaties gebruiken](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Bekende problemen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestaties

* [Tips voor prestaties](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Query test uitvoeringen](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Test uitvoeringen schrijven](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed wijzigen

* [Wijzigingen in de stroom verwerking met Azure Cosmos DB wijzigings feed en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demonstraties van feeds wijzigen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demo's van Structured stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Bewaking

* [Spark-taken bewaken met Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Volgende stappen

Als u dat nog niet hebt gedaan, downloadt u de Spark naar Azure Cosmos DB connector van de [Azure-cosmosdb-Spark github-](https://github.com/Azure/azure-cosmosdb-spark) opslag plaats. Bekijk de volgende aanvullende bronnen in de opslag plaats:

* [Aggregaties-voor beelden](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Voorbeeld scripts en notitie blokken](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

U kunt ook de [Apache Spark SQL-, DataFrames-en gegevens sets gids](https://spark.apache.org/docs/latest/sql-programming-guide.html)bekijken en het [Apache Spark in azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) -artikel.
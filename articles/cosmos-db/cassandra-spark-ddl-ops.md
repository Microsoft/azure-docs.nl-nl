---
title: DDL-bewerkingen in Azure Cosmos DB Cassandra-API vanuit Spark
description: In dit artikel vindt u informatie over de bewerking van de toetsen ruimte en tabel-DDL voor Azure Cosmos DB Cassandra-API van Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 73d31fff362807937cbd87b8e1313cf601909802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092174"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-bewerkingen in Azure Cosmos DB Cassandra-API vanuit Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel vindt u informatie over de bewerking van de toetsen ruimte en tabel-DDL voor Azure Cosmos DB Cassandra-API van Spark.

## <a name="cassandra-api-related-configuration"></a>Configuratie met betrekking tot Cassandra-API 

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

## <a name="keyspace-ddl-operations"></a>DDL-bewerkingen voor Keys

### <a name="create-a-keyspace"></a>Een spatie maken

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht uit in cqlsh en Bekijk de spatie die u eerder hebt gemaakt.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Een spatie verwijderen

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>DDL-bewerkingen voor tabel

**Tot**  

- De door Voer kan worden toegewezen op tabel niveau met behulp van de instructie CREATE TABLE.  
- Met één partitie sleutel kan 20 GB aan gegevens worden opgeslagen.  
- Met één record kunnen Maxi maal 2 MB aan gegevens worden opgeslagen.  
- Een partitie sleutel bereik kan meerdere partitie sleutels bevatten.

### <a name="create-a-table"></a>Een tabel maken

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht uit in cqlsh en zie de tabel ' boeken: 

```bash
USE books_ks;
DESCRIBE books;
```

Ingerichte door Voer en standaard TTL-waarden worden niet weer gegeven in de uitvoer van de vorige opdracht. u kunt deze waarden ophalen uit de portal.

### <a name="alter-table"></a>ALTER TABLE

U kunt de volgende waarden wijzigen met behulp van de opdracht ALTER TABLE:

* ingerichte door Voer 
* time-to-Live-waarde
<br>Kolom wijzigingen worden momenteel niet ondersteund.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Tabel neerzetten

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht uit in cqlsh en u ziet dat de tabel ' boeken ' niet meer beschikbaar is:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de spatie en de tabel hebt gemaakt, gaat u verder met de volgende artikelen voor ruwe bewerkingen en meer:
 
* [Bewerkingen maken/invoegen](cassandra-spark-create-ops.md)  
* [Leesbewerkingen](cassandra-spark-read-ops.md)  
* [Upsert bewerkingen](cassandra-spark-upsert-ops.md)  
* [Verwijderbewerkingen](cassandra-spark-delete-ops.md)  
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)  
* [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)  

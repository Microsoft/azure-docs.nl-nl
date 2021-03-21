---
title: Werken met Azure Cosmos DB Cassandra-API vanuit Spark
description: Dit artikel is de belangrijkste pagina voor de integratie van Cosmos DB Cassandra-API van Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090508"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Verbinding maken met de Azure Cosmos DB Cassandra-API vanuit Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Dit artikel is een reeks artikelen over Azure Cosmos DB Cassandra-API integratie van Spark. De artikelen omvatten connectiviteit, DDL-bewerkingen (Data Definition Language), DML-bewerkingen (Basic data manipulatie Language) en geavanceerde Azure Cosmos DB Cassandra-API integratie van Spark. 

## <a name="prerequisites"></a>Vereisten
* [Een Azure Cosmos DB Cassandra-API-account inrichten.](create-cassandra-dotnet.md#create-a-database-account)

* Uw keuze van Spark-omgeving inrichten [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Overige].

## <a name="dependencies-for-connectivity"></a>Afhankelijkheden voor connectiviteit
* **Spark-connector voor Cassandra:** Spark-connector wordt gebruikt om verbinding te maken met Azure Cosmos DB Cassandra-API.  De versie van de connector die zich in [maven Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) bevindt, identificeren en gebruiken die compatibel is met de Spark-en scala-versies van uw Spark-omgeving.

* **Azure Cosmos DB helper-bibliotheek voor Cassandra-API:** Naast de Spark-connector hebt u een andere bibliotheek met de naam [Azure-Cosmos-Cassandra-Spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) van Azure Cosmos DB nodig. Deze bibliotheek bevat een aangepaste Connection Factory en beleids klassen voor opnieuw proberen.

  Het beleid voor opnieuw proberen in Azure Cosmos DB is geconfigureerd om de uitzonde ringen voor HTTP-status codes 429 (' hoge aanvraag frequentie ') te verwerken. Met de Azure Cosmos DB Cassandra-API worden deze uitzonde ringen omgezet in overbelaste fouten in het Cassandra native-protocol en kunt u het opnieuw proberen met behulp van back-ups. Omdat Azure Cosmos DB het ingerichte doorvoer model gebruikt, worden de aanvraag frequentie beperkingen uitzonde ringen veroorzaakt wanneer de ingangs-en uitlopende snelheid toeneemt. Met het beleid voor opnieuw proberen worden uw Spark-taken beschermd tegen gegevens pieken die de door Voer toegewezen aan uw container overschrijden.

  > [!NOTE] 
  > Het beleid voor opnieuw proberen kan uw Spark-taken alleen beveiligen tegen tijdelijke pieken. Als u onvoldoende RUs hebt geconfigureerd om uw werk belasting uit te voeren, is het beleid voor opnieuw proberen niet van toepassing en wordt de uitzonde ring opnieuw gegenereerd met de klasse beleid opnieuw proberen.

* **Verbindings details Azure Cosmos DB account:** De naam van uw Azure Cassandra-API-account, het account eindpunt en de sleutel.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Para meters voor doorvoer configuratie van Spark-connector

De volgende tabel geeft een lijst van Azure Cosmos DB Cassandra-API specifieke doorvoer configuratie parameters die door de connector worden opgegeven. Zie voor een gedetailleerde lijst met alle configuratie parameters de pagina [configuratie verwijzing](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) van de GitHub-opslag plaats in de Spark Cassandra-connector.

| **Eigenschapsnaam** | **Standaardwaarde** | **Beschrijving** |
|---------|---------|---------|
| spark.cassandra.output.bat. grootte. Rows |  1 |Aantal rijen per batch. Stel deze para meter in op 1. Deze para meter wordt gebruikt voor een hogere door Voer voor zware werk belastingen. |
| spark.cassandra.connection.connections_per_executor_max  | Geen | Maximum aantal verbindingen per knoop punt per uitvoerder. 10 * n is gelijk aan 10 verbindingen per knoop punt in een n-node Cassandra-cluster. Als u dus vijf verbindingen per knoop punt per uitvoerder nodig hebt voor een Cassandra-cluster van 5 knoop punten, moet u deze configuratie instellen op 25. Wijzig deze waarde op basis van de mate van parallellisme of het aantal uitvoerender waarvoor uw Spark-taken zijn geconfigureerd.   |
| Spark. Cassandra. output. gelijktijdige. writes  |  100 | Hiermee wordt het aantal parallelle schrijf bewerkingen gedefinieerd dat per uitvoering kan plaatsvinden. Omdat u ' batch. size. Rows ' op 1 hebt ingesteld, moet u ervoor zorgen dat deze waarde op basis van de schaal wordt aangepast. Wijzig deze waarde op basis van de mate van parallellisme of de door Voer die u wilt voor uw werk belasting. |
| Spark. Cassandra. gelijktijdige. Lees bewerkingen |  512 | Hiermee wordt het aantal parallelle Lees bewerkingen gedefinieerd dat per uitvoering kan plaatsvinden. Wijzig deze waarde op basis van de mate van parallellisme of de door Voer die u wilt voor uw werk belasting  |
| spark.cassandra.output.throughput_mb_per_sec  | Geen | Definieert de totale schrijf doorvoer per uitvoerder. Deze para meter kan worden gebruikt als een bovengrens voor de door Voer van de Spark-taak en deze baseren op de ingerichte door Voer van uw Cosmos-container.   |
| spark.cassandra.input.reads_per_sec| Geen   | Definieert de totale Lees doorvoer per uitvoerder. Deze para meter kan worden gebruikt als een bovengrens voor de door Voer van de Spark-taak en deze baseren op de ingerichte door Voer van uw Cosmos-container.  |
| spark.cassandra.output.batch. grouping. buffer. size |  1000  | Hiermee wordt het aantal batches per enkele Spark-taak gedefinieerd dat in het geheugen kan worden opgeslagen voordat het naar Cassandra-API kan worden verzonden |
| spark.cassandra.connection.keep_alive_ms | 60000 | Hiermee definieert u de periode waarna ongebruikte verbindingen beschikbaar zijn. | 

Pas de door Voer en de mate van parallelle uitvoering van deze para meters aan op basis van de werk belasting die u verwacht voor uw Spark-taken en de door Voer die u hebt ingericht voor uw Cosmos DB-account.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Verbinding maken met Azure Cosmos DB Cassandra-API vanuit Spark

### <a name="cqlsh"></a>cqlsh
De volgende opdrachten beschrijven hoe u verbinding maakt met Azure CosmosDB Cassandra-API van cqlsh.  Dit is handig voor validatie tijdens het uitvoeren van de voor beelden in Spark.<br>
**Van Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
In het volgende artikel wordt beschreven Azure Databricks cluster inrichting, cluster configuratie om verbinding te maken met Azure Cosmos DB Cassandra-API en verschillende voor beelden van notitie blokken voor DDL-bewerkingen, DML-bewerkingen en meer.<BR>
[Werken met Azure Cosmos DB Cassandra-API vanuit Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
In het onderstaande artikel worden HDinsight-Spark service, het inrichten, de cluster configuratie voor het maken van verbinding met Azure Cosmos DB Cassandra-API en verschillende voor beelden van notitie blokken beschreven die betrekking hebben op DDL-bewerkingen, DML-bewerkingen en meer.<BR>
[Werken met Azure Cosmos DB Cassandra-API vanuit Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Spark-omgeving in het algemeen
Hoewel de bovenstaande secties specifiek zijn voor Azure Spark-gebaseerde PaaS-Services, wordt in deze sectie een algemene Spark-omgeving besproken.  Afhankelijkheden, import bewerkingen en configuratie van Spark-sessies worden hieronder beschreven. De sectie ' volgende stappen ' behandelt code voorbeelden voor DDL-bewerkingen, DML-bewerkingen en meer.  

#### <a name="connector-dependencies"></a>Connector afhankelijkheden:

1. Voeg de Maven-coördinaten toe om de [Cassandra-connector voor Spark](cassandra-spark-generic.md#dependencies-for-connectivity) op te halen
2. Voeg de Maven-coördinaten toe voor de [Azure Cosmos DB helper-bibliotheek](cassandra-spark-generic.md#dependencies-for-connectivity) voor Cassandra-API

#### <a name="imports"></a>Rusland

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configuratie van Spark-sessie:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen wordt de Spark-integratie met Azure Cosmos DB Cassandra-API gedemonstreerd. 
 
* [DDL-bewerkingen](cassandra-spark-ddl-ops.md)
* [Bewerkingen maken/invoegen](cassandra-spark-create-ops.md)
* [Leesbewerkingen](cassandra-spark-read-ops.md)
* [Upsert bewerkingen](cassandra-spark-upsert-ops.md)
* [Verwijderbewerkingen](cassandra-spark-delete-ops.md)
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
* [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)
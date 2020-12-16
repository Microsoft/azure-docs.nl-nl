---
title: Gegevens migreren van Apache Cassandra naar Azure Cosmos DB Cassandra-API met behulp van Databricks (Spark)
description: Meer informatie over het migreren van gegevens uit Apache Cassandra Data Base naar Azure Cosmos DB Cassandra-API met behulp van Azure Databricks en Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 74088d749279ab72851e714a50b558dc2adbc0d7
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516552"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Gegevens migreren van Cassandra naar Azure Cosmos DB Cassandra-API-account met behulp van Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een uitstekende keuze voor zakelijke workloads die worden uitgevoerd op Apache Cassandra om verschillende redenen, zoals: 

* **Geen overhead voor beheer en controle:** Dit elimineert de overhead voor het beheren en bewaken van een talloze instellingen in OS-, JVM-en YAML-bestanden en hun interacties.

* **Aanzienlijke kosten besparingen:** U kunt kosten besparen met Azure Cosmos DB, met inbegrip van de kosten van VM'S, band breedte en eventuele van toepassing zijnde licenties. Bovendien hoeft u de data centers, servers, SSD-opslag, netwerken en elektriciteits kosten niet te beheren. 

* De **mogelijkheid om bestaande code en hulpprogram ma's te gebruiken:** Azure Cosmos DB biedt compatibiliteit van wire-protocol niveau met bestaande Cassandra-Sdk's en-hulpprogram ma's. Door deze compatibiliteit kunt u bestaande codebase gebruiken met de Azure Cosmos DB Cassandra-API, met slechts een klein aantal wijzigingen.

Er zijn verschillende manieren om de data base-workloads van het ene platform naar het andere te migreren. [Azure Databricks](https://azure.microsoft.com/services/databricks/) is een platform als een service-aanbieding voor [Apache Spark](https://spark.apache.org/) die een manier biedt om offline migraties op grote schaal uit te voeren. In dit artikel worden de stappen beschreven die nodig zijn voor het migreren van gegevens uit systeem eigen Apache Cassandra Keys/Tables naar Azure Cosmos DB Cassandra-API met behulp van Azure Databricks.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Cosmos DB Cassandra-API-account inrichten](create-cassandra-dotnet.md#create-a-database-account)

* [Bekijk de basis beginselen van het maken van verbinding met Azure Cosmos DB Cassandra-API](cassandra-spark-generic.md)

* Controleer de [ondersteunde functies in Azure Cosmos DB Cassandra-API](cassandra-support.md) om compatibiliteit te garanderen.

* Zorg ervoor dat u al lege Keys en tabellen in uw doel Azure Cosmos DB Cassandra-API account hebt gemaakt

* [Cqlsh of gehoste shell gebruiken voor validatie](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Een Azure Databricks cluster inrichten

U kunt instructies volgen om [een Azure Databricks cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)in te richten. Let echter op dat Apache Spark 3. x momenteel niet wordt ondersteund voor de Apache Cassandra-connector. U moet een Databricks-runtime inrichten met een ondersteunde versie van v2. x van Apache Spark. U kunt het beste een versie van de Databricks-runtime selecteren die de meest recente versie van Spark 2. x ondersteunt, met niet meer dan scala versie 2,11:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks-runtime":::


## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

U moet de Apache Spark Cassandra-connector bibliotheek toevoegen aan uw cluster om verbinding te maken met zowel systeem eigen als Cosmos DB Cassandra-eind punten. Selecteer in uw cluster tape wisselaars-> nieuwe > maven >-Zoek pakketten installeren:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks Zoek pakketten":::

Typ `Cassandra` in het zoekvak en selecteer de meest recente `spark-cassandra-connector` maven-opslag plaats die beschikbaar is en selecteer vervolgens installeren:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks selecteren":::

> [!NOTE]
> Zorg ervoor dat u het Databricks-cluster opnieuw opstarten nadat de Cassandra-connector bibliotheek is geïnstalleerd.

## <a name="create-scala-notebook-for-migration"></a>Scala-notebook maken voor migratie

Maak een scala-notebook in Databricks met de volgende code. Vervang uw bron-en doel-Cassandra-configuraties met overeenkomende referenties, en bron-en doel-en-tabellen en voer vervolgens de volgende handelingen uit:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> De `spark.cassandra.output.concurrent.writes` `connections_per_executor_max` configuraties en zijn belang rijk voor het voor komen van [frequentie beperkingen](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), wat gebeurt wanneer aanvragen om Cosmos DB de ingerichte door voer te overschrijden ([aanvraag eenheden](./request-units.md)). Het kan zijn dat u deze instellingen moet aanpassen, afhankelijk van het aantal uitnodigingen in het Spark-cluster en mogelijk de grootte (en dus RU cost) van elke record die naar de doel tabellen wordt geschreven.

## <a name="next-steps"></a>Volgende stappen

* [Doorvoer voor containers en databases inrichten](set-throughput.md) 
* [Best practices voor de partitie sleutel](partitioning-overview.md#choose-partitionkey)
* [Een raming van ru/s met de Azure Cosmos DB capaciteits planner](estimate-ru-with-capacity-planner.md) artikelen
* [Elastisch schalen in Azure Cosmos DB Cassandra-API](manage-scale-cassandra.md)
---
title: Gegevens migreren van Apache Cassandra naar het Azure Cosmos DB Cassandra-API met behulp van Databricks (Spark)
description: Meer informatie over het migreren van gegevens vanuit een Apache Cassandra-Data Base naar de Azure Cosmos DB Cassandra-API met behulp van Azure Databricks en Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801006"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Gegevens migreren van Cassandra naar een Azure Cosmos DB Cassandra-API-account met behulp van Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een uitstekende keuze voor zakelijke workloads die op Apache Cassandra worden uitgevoerd om verschillende redenen:

* **Geen overhead voor beheer en controle:** Het elimineert de overhead van beheer-en controle-instellingen voor OS-, JVM-en YAML-bestanden en hun interacties.

* **Aanzienlijke kosten besparingen:** U kunt kosten besparen met de Azure Cosmos DB, inclusief de kosten van Vm's, band breedte en eventuele toepasselijke licenties. U hoeft geen data centers, servers, SSD-opslag, netwerken en elektriciteits kosten te beheren.

* De **mogelijkheid om bestaande code en hulpprogram ma's te gebruiken:** De Azure Cosmos DB biedt compatibiliteit op protocol niveau met bestaande Cassandra-Sdk's en-hulpprogram ma's. Deze compatibiliteit zorgt ervoor dat u uw bestaande code base kunt gebruiken met de Azure Cosmos DB Cassandra-API met behulp van trivial changes.

Er zijn veel manieren om data base-workloads van het ene platform naar het andere te migreren. [Azure Databricks](https://azure.microsoft.com/services/databricks/) is een Paas-Aanbieding (platform as a Service) voor [Apache Spark](https://spark.apache.org/) die een manier biedt om offline migraties op grote schaal uit te voeren. In dit artikel worden de stappen beschreven die nodig zijn voor het migreren van gegevens van systeem eigen Apache Cassandra-en tabellen naar het Azure Cosmos DB Cassandra-API met behulp van Azure Databricks.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Cosmos DB Cassandra-API-account inrichten](create-cassandra-dotnet.md#create-a-database-account).

* [Bekijk de basis beginselen van het maken van verbinding met een Azure Cosmos DB Cassandra-API](cassandra-spark-generic.md).

* Controleer de [ondersteunde functies in het Azure Cosmos DB Cassandra-API](cassandra-support.md) om compatibiliteit te garanderen.

* Zorg ervoor dat u al lege Keys en tabellen in uw doel Azure Cosmos DB Cassandra-API account hebt gemaakt.

* [Gebruik cqlsh of gehoste shell voor validatie](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Een Azure Databricks cluster inrichten

U kunt instructies volgen om [een Azure Databricks cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)in te richten. We raden u aan om Databricks runtime versie 7,5 te selecteren, die ondersteuning biedt voor Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Scherm opname van het vinden van de Databricks runtime versie.":::

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

U moet de Apache Spark Cassandra-connector bibliotheek toevoegen aan uw cluster om verbinding te maken met zowel systeem eigen als Azure Cosmos DB Cassandra-eind punten. Selecteer in uw cluster **bibliotheken**  >  **nieuwe**  >  **maven** installeren en voeg deze vervolgens toe aan de `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` maven-coördinaten.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Scherm afbeelding waarin wordt gezocht naar maven-pakketten in Databricks.":::

Selecteer **installeren** en start het cluster opnieuw op wanneer de installatie is voltooid.

> [!NOTE]
> Zorg ervoor dat u het Databricks-cluster opnieuw opstarten nadat de Cassandra-connector bibliotheek is geïnstalleerd.

## <a name="create-scala-notebook-for-migration"></a>Scala-notebook maken voor migratie

Maak een scala-notebook in Databricks. Vervang de configuratie van de bron-en doel Cassandra door de bijbehorende referenties en de bron-en doel-en-tabellen. Voer vervolgens de volgende code uit:

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
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> De `spark.cassandra.output.batch.size.rows` waarden en en `spark.cassandra.output.concurrent.writes` het aantal werk rollen in uw Spark-cluster zijn belang rijke configuraties die u kunt afstemmen om [frequentie beperkingen](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)te voor komen. Snelheids beperking treedt op wanneer aanvragen om Azure Cosmos DB de ingerichte door Voer of [aanvraag eenheden](./request-units.md) (RUs) te overschrijden. Het kan zijn dat u deze instellingen moet aanpassen, afhankelijk van het aantal uitnodigingen in het Spark-cluster en mogelijk de grootte (en dus RU cost) van elke record die naar de doel tabellen wordt geschreven.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="rate-limiting-429-error"></a>Frequentie beperking (429-fout)

U ziet mogelijk een fout code van 429 of de tekst ' aanvraag frequentie is erg groot ', zelfs als u de instellingen hebt gereduceerd tot hun minimum waarden. De volgende scenario's kunnen een frequentie beperking veroorzaken:

* **De door Voer die is toegewezen aan de tabel, is kleiner dan 6.000 [aanvraag eenheden](./request-units.md)**. Zelfs op minimale instellingen kan Spark schrijven met een snelheid van ongeveer 6.000 aanvraag eenheden of meer. Als u een tabel in een spatie met een gedeelde door Voer hebt ingericht, is het mogelijk dat deze tabel minder dan 6.000 RUs tijdens runtime beschikbaar heeft.

    Zorg ervoor dat de tabel die u migreert, ten minste 6.000 RUs beschikbaar is wanneer u de migratie uitvoert. Wijs indien nodig toegewezen aanvraag eenheden toe aan de tabel.

* **Buitensporige gegevens scheefheid met groot gegevens volume**. Als u een grote hoeveelheid gegevens hebt om te migreren naar een bepaalde tabel, maar een aanzienlijke scheefheid heeft in de gegevens (dat wil zeggen, een groot aantal records dat wordt geschreven voor dezelfde partitie sleutel waarde), is het mogelijk dat er minder beperkingen gelden, zelfs als er verschillende [aanvraag eenheden](./request-units.md) zijn ingericht in de tabel. Aanvraag eenheden worden gelijkmatig verdeeld over fysieke partities en zware gegevens scheefheid kan leiden tot een knel punt van aanvragen naar één partitie.

    In dit scenario gaat u naar minimale doorvoer instellingen in Spark en dwingt u de migratie traag uit te voeren. Dit scenario kan vaker voor komen wanneer u referentie-of beheer tabellen migreert, waarbij toegang minder vaak is en scheefheid hoog kan zijn. Als er echter een aanzienlijk scheefheid aanwezig is in een ander type tabel, wilt u wellicht uw gegevens model controleren om problemen met de warme partitie voor uw werk belasting te voor komen tijdens een bewerking met een constante status.

## <a name="next-steps"></a>Volgende stappen

* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Best practices voor de partitie sleutel](partitioning-overview.md#choose-partitionkey)
* [Een raming van RU/s met behulp van de Azure Cosmos DB capaciteit planner](estimate-ru-with-capacity-planner.md)
* [Elastisch schalen in Azure Cosmos DB Cassandra-API](manage-scale-cassandra.md)

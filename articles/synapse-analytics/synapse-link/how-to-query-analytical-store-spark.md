---
title: Interactie met Azure Cosmos DB met behulp van Apache Spark in Azure Synapse Link
description: Hoe werkt interactie met Azure Cosmos DB met behulp van Apache Spark in Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 615998de45c4a9993899065a4aef7a15332a59c8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720403"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Interactie met Azure Cosmos DB met behulp van Apache Spark in Azure Synapse Link

In dit artikel leert u hoe u kunt communiceren met Azure Cosmos DB met behulp van Synapse Apache Spark. Met de volledige ondersteuning voor Scala, Python, SparkSQL en C#, staat Apache Spark Synapse centraal voor analyse, data engineering, gegevenswetenschap en gegevensexploratie in [Azure Synapse Link voor Azure Cosmos DB](../../cosmos-db/synapse-link.md).

De volgende mogelijkheden worden ondersteund bij interactie met Azure Cosmos DB:
* Met Synapse Apache Spark kunt u gegevens in uw Azure Cosmos DB-containers die zijn ingeschakeld met Azure Synapse Link in bijna realtime analyseren zonder dat dit van invloed is op de prestaties van uw transactionele workloads. De volgende twee opties zijn beschikbaar voor het opvragen van de Azure Cosmos DB-[analytische opslag](../../cosmos-db/analytical-store-introduction.md) van Spark:
    + Laden naar Spark DataFrame
    + Spark-tabel maken
* Met Synapse Apache Spark kunt u ook gegevens opnemen in Azure Cosmos DB. Het is belangrijk te weten dat gegevens altijd worden opgenomen in Azure Cosmos DB-containers via de transactionele opslag. Wanneer Synapse Link is ingeschakeld, worden nieuwe toevoegingen, updates en verwijderingen automatisch gesynchroniseerd met de analytische opslag.
* Synapse Apache Spark biedt ook ondersteuning voor Spark Structured Streaming met Azure Cosmos DB als een bron en een sink. 

In de volgende secties wordt stapsgewijs uitgelegd hoe u de syntaxis van de bovenstaande mogelijkheden kunt volgen. Bewegingen in Azure Synapse Analytics-werkruimte zijn ontworpen om een eenvoudige out-of-the-box-ervaring te bieden om aan de slag te gaan. Gebaren worden weergegeven wanneer u met de rechtermuisknop op een Azure Cosmos DB-container klikt op het tabblad **Gegevens** van de Synapse-werkruimte. Met bewegingen kunt u snel code genereren en deze aanpassen aan uw behoeften. Bewegingen zijn ook ideaal om met één klik gegevens te ontdekken.

> [!IMPORTANT]
> U moet rekening houden met enkele beperkingen in het analytische schema die kunnen leiden tot het onverwachte gedrag bij het laden van gegevens.
> Een voor beeld: alleen de eerste 1000 eigenschappen van het transactionele schema zijn beschikbaar in het analytische schema, eigenschappen met spaties zijn niet beschikbaar, enzovoort. Als u onverwachte resultaten ondervindt, controleert u de [beperkingen van het analytische archief-schema](../../cosmos-db/analytical-store-introduction.md#schema-constraints) voor meer informatie.

## <a name="query-azure-cosmos-db-analytical-store"></a>Analytische opslag van Azure Cosmos DB

Voordat u meer informatie krijgt over de twee mogelijke opties om een query uit te brengen op Azure Cosmos DB-analytische opslag, het laden van Spark DataFrame en het maken van een Spark-tabel, is het een goed idee om de opties in de ervaring te verkennen zodat u de optie kunt kiezen die geschikt is voor uw behoeften.

Het verschil in ervaring is om te bepalen of onderliggende gegevenswijzigingen in de Azure Cosmos DB-container automatisch moeten worden doorgevoerd in de analyse die in Spark wordt uitgevoerd. Wanneer een Spark DataFrame wordt geregistreerd of een Spark-tabel wordt gemaakt op basis van de analytische opslag van een container, worden de metagegevens van de huidige momentopname van de gegevens in het analytische archief naar Spark opgehaald voor efficiënte pushdown van de volgende analyse. Het is belangrijk te weten dat, omdat Spark een lui evaluatiebeleid volgt, tenzij een actie wordt aangeroepen op de Spark-DataFrame of een SparkSQL-query wordt uitgevoerd op basis van de Spark-tabel, de werkelijke gegevens niet worden opgehaald uit de analytische opslag van de onderliggende container.

In het geval van **het laden naar Spark DataFrame**, worden de opgehaalde metagegevens in de cache opgeslagen tijdens de levensduur van de Spark-sessie. Daarom worden de volgende acties die worden uitgevoerd op de DataFrame geëvalueerd op basis van de momentopname van de analytische opslag op het moment dat DataFrame wordt gemaakt.

Daarentegen, in het geval van **het maken van een Spark-tabel**, worden de metagegevens van de status van de analytische opslag niet in de cache opgeslagen in Spark en worden opnieuw geladen op elke SparkSQL-query-uitvoering in de Spark-tabel.

Daarom kunt u kiezen tussen het laden van Spark DataFrame en het maken van een Spark-tabel op basis van de vraag of u wilt dat uw Spark-analyse wordt geëvalueerd voor respectievelijk een vaste momentopname van de analytische opslag of met de meest recente momentopname van de analytische opslag.

> [!NOTE]
> Als u een query wilt uitvoeren voor de Azure Cosmos DB-API van Mongo DB-accounts, leest u meer over de [weergave van het schema voor volledige betrouwbaarheid](../../cosmos-db/analytical-store-introduction.md#analytical-schema) in de analytische opslag en de uitgebreide eigenschapsnamen die moeten worden gebruikt.

### <a name="load-to-spark-dataframe"></a>Laden naar Spark DataFrame

In dit voorbeeld maakt u een Spark-DataFrame dat verwijst naar de Azure Cosmos DB-analytische opslag. U kunt vervolgens extra analyses uitvoeren door Spark-acties aan te roepen voor het DataFrame. Deze bewerking heeft geen invloed op de transactionele opslag.

De syntaxis van **Python** is als volgt:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

De equivalente syntaxis in **Scala** is de volgende:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark-tabel maken

In dit voorbeeld maakt u een Spark-DataFrame dat verwijst naar de Azure Cosmos DB-analytische opslag. U kunt vervolgens extra analyses uitvoeren door Spark-acties aan te roepen voor het DataFrame. Deze bewerking heeft geen invloed op het transactionele archief en maakt geen gegevensverplaatsing. Als u besluit deze Spark-tabel te verwijderen, worden de onderliggende Azure Cosmos DB-container en de bijbehorende analytische opslag niet beïnvloed. 

Dit scenario is handig voor hergebruik van Spark-tabellen via hulpprogramma's van derden en om de gegevens voor de uitvoering toegankelijk te maken.

De syntaxis voor het maken van een Spark-tabel is als volgt:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Als u scenario's hebt waarbij het schema van de onderliggende Azure Cosmos DB container na verloop van tijd verandert en als u wilt dat het bijgewerkte schema automatisch wordt weergegeven in de query's voor de Spark-tabel, kunt u dit doen door de optie `spark.cosmos.autoSchemaMerge` in te stellen op `true` in de Spark-tabelopties.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Spark DataFrame naar Azure Cosmos DB-container schrijven

In dit voorbeeld schrijft u een Spark DataFrame naar een Azure Cosmos DB-container. Deze bewerking heeft invloed op de prestaties van transactionele workloads en verbruikt aanvraageenheden die zijn ingericht op de Azure Cosmos DB-container of de gedeelde database.

De syntaxis van **Python** is als volgt:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

De equivalente syntaxis in **Scala** is de volgende:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Streaming-dataframe laden vanuit een container
Met deze beweging gebruikt u de mogelijkheid van Spark-streaming om gegevens uit een container in een dataframe te laden. De gegevens worden opgeslagen in het primaire Data Lake-account (en bestandssysteem) dat u hebt gekoppeld aan de werkruimte. 
> [!NOTE]
> Als u op zoek bent naar externe bibliotheken in Synapse Apache Spark, vindt u [hier](#external-library-management) meer informatie. Als u bijvoorbeeld een Spark DataFrame wilt opnemen in een container met Cosmos DB API voor Mongo DB, kunt u [hier](https://docs.mongodb.com/spark-connector/master/) de Mongo DB-connector gebruiken voor Spark.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Streaming-DataFrame laden vanuit een Azure Cosmos DB-container
In dit voorbeeld gebruikt u de structured streaming-mogelijkheid van Spark voor het laden van gegevens uit een Azure Cosmos DB-container naar een Spark-streaming DataFrame met behulp van de wijzigingsfunctie voor feeds in Azure Cosmos DB. De controlepuntgegevens die worden gebruikt door Spark, worden opgeslagen in het primaire datalake-account (en bestandssysteem) dat u hebt verbonden met de werkruimte.

Als de map */localReadCheckpointFolder* niet is gemaakt (in het onderstaande voorbeeld), gebeurt dit automatisch. Deze bewerking heeft invloed op de prestaties van transactionele workloads en verbruikt aanvraageenheden die zijn ingericht op de Azure Cosmos DB-container of gedeelde database.

De syntaxis van **Python** is als volgt:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

De equivalente syntaxis in **Scala** is de volgende:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Streaming-DataFrame schrijven naar een Azure Cosmos DB-container
In dit voorbeeld schrijft u een streaming-DataFrame naar een Azure Cosmos DB-container. Deze bewerking heeft invloed op de prestaties van transactionele workloads en verbruikt aanvraageenheden die zijn ingericht op de Azure Cosmos DB-container of gedeelde database. Als de map */localWriteCheckpointFolder* niet is gemaakt (in het onderstaande voorbeeld), gebeurt dit automatisch. 

De syntaxis van **Python** is als volgt:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

De equivalente syntaxis in **Scala** is de volgende:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Extern bibliotheekbeheer

In dit voorbeeld leert u hoe u kunt verwijzen naar externe bibliotheken van JAR-bestanden wanneer u Spark-notebooks in Synpase Apache Spark-werkruimten gebruikt. U kunt de JAR-bestanden in een container plaatsen in het primaire datalake-account dat u hebt verbonden met de werkruimte en vervolgens de volgende `%configure`-instructie toevoegen in uw Spark-notebook:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Als u externe Spark-taakdefinities wilt verzenden naar een serverloze Apache Spark-pool, kunt u deze [zelfstudie](../spark/apache-spark-job-definitions.md) volgen om te leren hoe u verwijst naar externe bibliotheken.

## <a name="next-steps"></a>Volgende stappen

* [Voorbeelden om aan de slag te gaan met Azure Synapse Link op GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Wat wordt ondersteund in Azure Synapse Link voor Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Verbinding maken met Synapse Link voor Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)

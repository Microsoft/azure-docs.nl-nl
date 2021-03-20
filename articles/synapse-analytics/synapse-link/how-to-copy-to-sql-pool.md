---
title: Synapse Link voor Azure Cosmos DB-gegevens kopiëren naar een toegewezen SQL-pool, met behulp van Apache Spark
description: De gegevens in een Apache Spark-dataframe laden, de gegevens cureren, en ze laden in een toegewezen SQL-pooltabel
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 13891f9614e658be39adbb69fed1503a0c66d5e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309223"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Azure Cosmos DB-gegevens kopiëren naar een toegewezen SQL-pool, met behulp van Apache Spark

Met Azure Synapse Link voor Azure Cosmos DB kunnen gebruikers in bijna realtime analyses uitvoeren via operationele gegevens in Azure Cosmos DB. Er zijn echter momenten waarop sommige gegevens moeten worden geaggregeerd en verrijkt zodat datawarehouse-gebruikers ze kunnen gebruiken. Synapse Link-gegevens kunnen met slechts enkele cellen in een notebook worden gecureerd en geëxporteerd.

## <a name="prerequisites"></a>Vereisten
* [Een Synapse-werkruimte inrichten](../quickstart-create-workspace.md) met:
    * [een serverloze Apache Spark-pool](../quickstart-create-apache-spark-pool-studio.md)
    * [een toegewezen SQL-pool](../quickstart-create-sql-pool-studio.md)
* [Een Cosmos DB-account inrichten met een HTAP-container met gegevens](../../cosmos-db/configure-synapse-link.md)
* [De HTAP-container van Azure Cosmos DB verbinden met de werkruimte](./how-to-connect-synapse-link-cosmos-db.md)
* [De juiste instelling om vanuit Spark gegevens te importeren in een toegewezen SQL-pool](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Stappen
In deze zelfstudie gaat u verbinding maken met de analytische opslag, zodat de transactionele opslag niet wordt beïnvloed (er worden geen aanvraageenheden gebruikt). De volgende stappen worden uitgevoerd:
1. De HTAP-container van Cosmos DB uitlezen naar een Apache Spark-dataframe
2. De resultaten aggregeren in een nieuwe dataframe
3. De gegevens opnemen in een toegewezen SQL-pool

[![Apache Spark naar SQL: stappen 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Gegevens
In dit voorbeeld wordt een HTAP-container met de naam **RetailSales** gebruikt. Deze maakt deel uit van een gekoppelde service met de naam **ConnectedData** en heeft het volgende schema:
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

U gaat de verkoop (*quantity*, *revenue* (price x quantity)) aggregeren met *productCode* en *weekStarting* voor rapportagedoeleinden. Ten slotte exporteert u deze gegevens naar een toegewezen SQL-pooltabel met de naam **dbo.productsales**.

## <a name="configure-a-spark-notebook"></a>Een Apache Spark-notebook configureren
Maak een Apache Spark-notebook met Scala as Spark (Scala) als hoofdtaal. U gebruikt de standaardinstelling van de notebook voor deze sessie.

## <a name="read-the-data-in-spark"></a>De gegevens in Apache Spark uitlezen
Lees de HTAP-container van Cosmos DB uit met Apache Spark naar een dataframe in de eerste cel.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>De resultaten aggregeren in een nieuwe dataframe

In de tweede cel voert u de transformatie en aggregaties uit die nodig zijn voor de nieuwe dataframe, vóórdat u deze in een toegewezen SQL-pooldatabase laadt.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>De resultaten laden in een toegewezen SQL-pool

In de derde cel laadt u de gegevens in een toegewezen SQL-pool. Er worden automatisch een tijdelijke externe tabel, externe gegevensbron en externe bestandsindeling gemaakt, die worden verwijderd zodra de taak is uitgevoerd.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Query's uitvoeren op de resultaten met SQL

U kunt query's uitvoeren op het resultaat met een eenvoudige SQL-query, zoals het volgende SQL-script:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Met de query worden de volgende resultaten in een grafiek weergegeven: [![Apache Spark naar SQL: stappen 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Volgende stappen
* [Query's uitvoeren op de analytische opslag van Azure Cosmos DB met Apache Spark](./how-to-query-analytical-store-spark.md)
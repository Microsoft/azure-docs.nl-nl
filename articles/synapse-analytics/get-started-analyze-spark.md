---
title: 'Quickstart: Aan de slag met het analyseren met Spark'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363849"
---
# <a name="analyze-with-apache-spark"></a>Analyseren met behulp van Apache Spark

In deze zelfstudie leert u de basisstappen voor het laden en analyseren van gegevens met Apache Spark voor Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Een serverloze Apache Spark-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster **Beheren** > **Apache Spark-pools**.
1. Selecteer **Nieuw** 
1. Voer **Spark1** in voor **Naam van Apache Spark-pool**.
1. Voer **Klein** in voor **Grootte van knooppunt**.
1. Stel voor **Aantal knooppunten** het minimum in op 3 en het maximum op 3
1. Selecteer **Beoordelen en maken** > **Maken**. Uw Apache Spark-pool is binnen een paar seconden gereed.

## <a name="understanding-serverless-apache-spark-pools"></a>Informatie over serverloze Apache Spark groepen

Een serverloze Spark-pool is een manier om aan te geven hoe een gebruiker met Spark wil werken. Wanneer u een pool gaat gebruiken, wordt er indien nodig een Spark-sessie gemaakt. De pool bepaalt hoeveel Spark-resources door die sessie worden gebruikt en hoe lang de sessie duurt voordat deze automatisch wordt onderbroken. U betaalt voor Spark-resources die tijdens die sessie worden gebruikt, niet voor de pool zelf. Op deze manier kunt u met een Spark-pool werken met Spark, zonder dat u zich zorgen hoeft te maken over het beheren van clusters. Dit is vergelijkbaar met de manier waarop een serverloze SQL-pool werkt.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Nyc Taxi-gegevens analyseren met een Spark-pool

1. Ga Synapse Studio naar de hub **Ontwikkelen**
2. Een nieuwe notebook maken
3. Maak een nieuwe codecel en plak de volgende code in die cel.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. Kies in het notebook in **het** menu Koppelen aan de **serverloze Spark-pool Spark1** die we eerder hebben gemaakt.
1. Selecteer **Uitvoeren** op de cel. Synapse start een nieuwe Spark-sessie om deze cel uit te voeren, indien nodig. Als er een nieuwe Spark-sessie nodig is, duurt het in eerste instantie ongeveer twee seconden om deze te maken. 
1. Als u alleen wilt zien hoe een cel met de volgende code wordt uitgevoerd met het schema van het dataframe:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Gegevens zijn beschikbaar via het gegevensframe met de **naam df**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Voeg een nieuwe codecel toe aan het notebook en voer de volgende code in:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Maak een nieuwe codecel en voer de volgende code in. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de cel uit om de NYC Taxi-gegevens weer te geven die we in de **Spark-database nyctaxi** hebben geladen.
1. Maak een nieuwe codecel en voer de volgende code in. We analyseren deze gegevens en slaan de resultaten op in een tabel met de naam **nyctaxi.passengercountstats.**

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Selecteer **Grafiek** in de celresultaten om de gevisualiseerde gegevens weer te geven.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren met toegewezen SQL-pool](get-started-analyze-sql-pool.md)

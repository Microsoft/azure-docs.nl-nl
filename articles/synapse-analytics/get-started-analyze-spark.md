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
ms.date: 12/31/2020
ms.openlocfilehash: 8559bd0a354a64872e58d014d1027ed971773b60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655339"
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

## <a name="understanding-serverless-apache-spark-pools"></a>Informatie over serverloze Apache Spark Pools

Een Spark-groep zonder server is een manier om aan te geven hoe een gebruiker met Spark wil werken. Wanneer u begint met het gebruik van een pool, wordt indien nodig een Spark-sessie gemaakt. De pool bepaalt hoeveel Spark-resources worden gebruikt door deze sessie en hoe lang de sessie duurt voordat deze automatisch wordt onderbroken. U betaalt voor Spark-resources die tijdens deze sessie worden gebruikt, niet voor de groep zelf. Op deze manier kunt u met Spark werken, zonder dat u zich geen zorgen hoeft te maken dat clusters worden beheerd. Dit is vergelijkbaar met de werking van een serverloze SQL-pool.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC Taxi-gegevens analyseren in blob-opslag met Spark

1. Ga in Synapse Studio naar de **ontwikkelende** hub
2. Maak een newnNotebook met de standaard taal ingesteld op **PySpark (python)**.
3. Maak een nieuwe code-cel en plak de volgende code in die cel.
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. Kies in het notitie blok in het menu **koppelen aan** de **Spark1** -serverloze Spark-pool die u eerder hebt gemaakt.
1. Selecteer **Uitvoeren** op de cel. Synapse start een nieuwe Spark-sessie om deze cel zo nodig uit te voeren. Als er een nieuwe Spark-sessie nodig is, duurt het in eerste instantie ongeveer twee seconden om te maken. 
1. Als u alleen wilt zien hoe een cel met de volgende code wordt uitgevoerd met het schema van het dataframe:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Gegevens zijn beschikbaar via de data frame met de naam **gegevens**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Voeg een nieuwe toe aan het notitie blok en voer de volgende code in:

    ```py
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Ga terug naar uw notebook.
1. Maak een nieuwe code-cel en voer de volgende code in. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de cel uit om de NYC-taxi gegevens weer te geven die we in de **nyctaxi** Spark-data base hebben geladen.
1. Maak een nieuwe code-cel en voer de volgende code in. Voer vervolgens de cel uit om dezelfde analyse uit te voeren die eerder met de toegewezen SQL-groep **SQLPOOL1** is uitgevoerd. Met deze code worden de resultaten van de analyse opgeslagen en weer gegeven in een tabel met de naam **nyctaxi. passengercountstats**.

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
> [Gegevens analyseren met een toegewezen SQL-groep](get-started-analyze-sql-pool.md)

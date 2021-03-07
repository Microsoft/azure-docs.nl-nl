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
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423710"
---
# <a name="analyze-with-apache-spark"></a>Analyseren met behulp van Apache Spark

In deze zelfstudie leert u de basisstappen voor het laden en analyseren van gegevens met Apache Spark voor Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC Taxi-gegevens analyseren in blob-opslag met Spark

1. Klik in de **Data** hub op de **+** knop om **een nieuwe resource toe te voegen** en klik vervolgens op >> **Blader galerie**. 
1. Zoek **NYC Taxi & Limousine Commission - records van Yellow Taxi-ritten** en klik erop. 
1. Klik onder aan de pagina op **door gaan** en vervolgens op **gegevensset toevoegen**. 
1. Klik na een moment in **Data** hub onder **gekoppeld** met de rechter muisknop op **Azure Blob Storage >> voorbeeld gegevens sets >> nyc_tlc_yellow** en selecteer **Nieuw notitie blok** en vervolgens **laden naar gegevens frame**.
1. Hiermee maakt u een nieuw notebook met de volgende code:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. Kies in het notitie blok in het menu **koppelen aan** de **Spark1** -serverloze Spark-pool die u eerder hebt gemaakt.
1. Selecteer **Uitvoeren** op de cel. Synapse start een nieuwe Spark-sessie om deze cel zo nodig uit te voeren. Als er een nieuwe Spark-sessie nodig is, duurt het ongeveer twee seconden voordat Intially wordt gemaakt. 
1. Als u alleen wilt zien hoe een cel met de volgende code wordt uitgevoerd met het schema van het dataframe:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Er zijn gegevens beschikbaar in een tabel in **SQLPOOL1**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Ga in Synapse Studio naar de hub **Ontwikkelen**.
1. Selecteer **+**  > **Notebook**.
1. Stel bovenaan de notebook de waarde voor **Koppelen aan** in op **Spark1**.
1. In de eerste cel van het nieuwe notitie blok en voer de volgende code in:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Voer het script uit. Het kan 2-3 minuten duren.
1. Klik op de **Data** hub op het tabblad **werk ruimte** met de rechter muisknop op **data bases** en selecteer vervolgens **vernieuwen**. Nu ziet u de Data Base **nyctaxi (Spark)** in de lijst.


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

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Gegevens uit een Apache Spark-tabel laden in een toegewezen SQL-pooltabel

Eerder hebt u gegevens uit de toegewezen SQL-pooltabel **SQLPOOL1.dbo.Trip** gekopieerd naar de Spark-tabel **nyctaxi.trip**. Vervolgens aggregeerde u de gegevens in de Spark-tabel **nyctaxi.passengercountstats**. Nu kopieert u de gegevens uit **nyctaxi. passengercountstats** naar een exclusieve SQL-groeps tabel met de naam **SQLPOOL1. dbo. passengercountstats**.

1. Maak een nieuwe code-cel en voer de volgende code in. Voer de cel uit in uw notitie blok. Hiermee wordt de geaggregeerde Apache Spark-tabel weer gekopieerd naar de toegewezen SQL-pooltabel.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren met een serverloze SQL-pool](get-started-analyze-sql-on-demand.md)

---
title: Een interactieve Spark-shell gebruiken in azure HDInsight
description: Een interactieve Spark-shell biedt een proces voor lezen-uitvoeren-afdrukken voor het uitvoeren van Spark-opdrachten een voor een tegelijk en de resultaten te zien.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 324852a967b5de015a9b1e9b465d4b4703e573cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929683"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark uitvoeren vanuit de Spark-shell

Een interactieve [Apache Spark](https://spark.apache.org/) shell biedt een repl-omgeving (read-execute-print-lus) voor het uitvoeren van Spark-opdrachten een voor een tegelijk en de resultaten te bekijken. Dit proces is handig voor het ontwikkelen en opsporen van fouten. Spark biedt één shell voor elk van de ondersteunde talen: scala, python en R.

## <a name="run-an-apache-spark-shell"></a>Een Apache Spark shell uitvoeren

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark biedt shells voor scala (Spark-shell) en python (pyspark). Voer in uw SSH-sessie *een* van de volgende opdrachten in:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Als u een optionele configuratie wilt gebruiken, controleert u eerst de [OutOfMemoryError-uitzonde ring voor Apache Spark](./apache-spark-troubleshoot-outofmemory.md).

1. Enkele eenvoudige voorbeeld opdrachten. Kies de relevante taal:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Een query uitvoeren op een CSV-bestand. Let op de onderstaande taal voor `spark-shell` en `pyspark` .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Een query uitvoeren op een CSV-bestand en de resultaten opslaan in een variabele:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Resultaten weer geven:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Afsluiten

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-en SparkContext-instanties

Standaard worden instanties van SparkSession en SparkContext automatisch voor u geïnstantieerd wanneer u de Spark-shell uitvoert.

Voer in om toegang te krijgen tot het SparkSession-exemplaar `spark` . Voer in om toegang te krijgen tot het SparkContext-exemplaar `sc` .

## <a name="important-shell-parameters"></a>Belang rijke shell parameters

De Spark shell-opdracht ( `spark-shell` of `pyspark` ) ondersteunt veel opdracht regel parameters. Als u een volledige lijst met para meters wilt weer geven, start u de Spark-shell met de switch `--help` . Sommige van deze para meters zijn mogelijk alleen van toepassing op `spark-submit` , die in de Spark-shell worden geplaatst.

| schakelen | beschrijving | Hierbij |
| --- | --- | --- |
| --hoofd MASTER_URL | Hiermee geeft u de hoofd-URL op. In HDInsight is deze waarde altijd `yarn` . | `--master yarn`|
| --potten JAR_LIST | Een door komma's gescheiden lijst met lokale potten die moeten worden toegevoegd aan het stuur programma en de namen van de uitvoerder. In HDInsight bestaat deze lijst uit paden naar het standaard bestands systeem in Azure Storage of Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pakketten MAVEN_COORDS | Een door komma's gescheiden lijst met maven-coördinaten van potten die moeten worden vermeld op het stuur programma en de uitvoerder classpath. Doorzoekt de lokale maven opslag plaats, vervolgens maven Central, en eventuele extra externe opslag plaatsen die zijn opgegeven bij `--repositories` . De notatie voor de coördinaten is *groupid*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-bestanden lijst | Alleen voor python: een door komma's gescheiden lijst met zip-, eier-of py-bestanden die op de PYTHONPATH moet worden geplaatst. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Volgende stappen

- Zie [Inleiding tot Apache Spark in azure HDInsight](apache-spark-overview.md) voor een overzicht.
- Zie [een Apache Spark cluster in azure HDInsight maken](apache-spark-jupyter-spark-sql.md) voor gebruik met Spark-clusters en SparkSQL.
- Zie [Wat is Apache Spark Structured streaming?](apache-spark-streaming-overview.md) om toepassingen te schrijven die streaminggegevens verwerken met Spark.

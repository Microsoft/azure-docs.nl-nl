---
title: Apache Spark bewerkingen die worden ondersteund door Hive Warehouse connector in azure HDInsight
description: Meer informatie over de verschillende mogelijkheden van de component Warehouse connector op Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871585"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Spark bewerkingen die worden ondersteund door Hive Warehouse connector in azure HDInsight

Dit artikel bevat Spark-bewerkingen die worden ondersteund door Hive Warehouse connector (HWC). Alle voor beelden die hieronder worden weer gegeven, worden uitgevoerd via de Apache Spark-shell.

## <a name="prerequisite"></a>Vereiste

Voltooi de installatie stappen van het [Hive Warehouse-connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Aan de slag

Voer de volgende stappen uit om een Spark-shell-sessie te starten:

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw Apache Spark cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer vanuit uw SSH-sessie de volgende opdracht uit om de `hive-warehouse-connector-assembly` versie te noteren:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Bewerk de onderstaande code met de `hive-warehouse-connector-assembly` hierboven aangegeven versie. Voer vervolgens de opdracht uit om de Spark-shell te starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Nadat u de Spark-shell hebt gestart, kunt u een component Warehouse connector-exemplaar starten met behulp van de volgende opdrachten:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Een Spark-DataFrames maken met hive-query's

De resultaten van alle query's die gebruikmaken van de HWC-bibliotheek worden geretourneerd als een data frame. De volgende voor beelden laten zien hoe u een eenvoudige Hive-query maakt.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

De resultaten van de query zijn Spark DataFrames, die kunnen worden gebruikt met Spark-bibliotheken als MLIB en SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark-DataFrames naar Hive-tabellen schrijven

Spark ondersteunt geen systeem eigen ondersteuning voor het schrijven naar de tabellen van de beheerde zuren van de component. Met HWC kunt u echter elke data frame naar een Hive-tabel schrijven. U kunt deze functionaliteit op het volgende voor beeld bekijken:

1. Maak een tabel `sampletable_colorado` met de naam en geef de kolommen op met behulp van de volgende opdracht:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filter de tabel `hivesampletable` waarbij de kolom `state` gelijk is aan `Colorado` . Deze Hive-query retourneert een Spark data frame ant SIS dat is opgeslagen in de Hive-tabel `sampletable_colorado` met behulp van de `write` functie.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Bekijk de resultaten met de volgende opdracht:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="component-Warehouse connector-Hive weer geven" border="true":::


## <a name="structured-streaming-writes"></a>Gestructureerd streamen schrijven

Met hive Warehouse connector kunt u met Spark streaming gegevens schrijven naar Hive-tabellen.

> [!IMPORTANT]
> Gestructureerde streaming-schrijf bewerkingen worden niet ondersteund in met ESP ingeschakelde Spark 4,0-clusters.

Volg de onderstaande stappen om gegevens op te nemen uit een Spark-stroom op localhost-poort 9999 in een Hive-tabel via. Hive Warehouse-connector.

1. Start vanuit uw open Spark-shell een Spark-stroom met de volgende opdracht:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Genereer gegevens voor de Spark-stroom die u hebt gemaakt door de volgende stappen uit te voeren:
    1. Open een tweede SSH-sessie op hetzelfde Spark-cluster.
    1. Typ bij de opdracht prompt `nc -lk 9999` . Met deze opdracht wordt het netcat-hulp programma gebruikt voor het verzenden van gegevens van de opdracht regel naar de opgegeven poort.

1. Ga terug naar de eerste SSH-sessie en maak een nieuwe Hive-tabel om de streaminggegevens te bewaren. Voer in de Spark-shell de volgende opdracht in:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Schrijf vervolgens de streaminggegevens naar de zojuist gemaakte tabel met behulp van de volgende opdracht:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > De `metastoreUri` `database` Opties en moeten momenteel hand matig worden ingesteld vanwege een bekend probleem in Apache Spark. Zie [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460)voor meer informatie over dit probleem.

1. Ga terug naar de tweede SSH-sessie en voer de volgende waarden in:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Ga terug naar de eerste SSH-sessie en noteer de korte activiteit. Gebruik de volgende opdracht om de gegevens weer te geven:

    ```scala
    hive.table("stream_table").show()
    ```

Gebruik **CTRL + C** om netcat te stoppen bij de tweede SSH-sessie. Gebruiken `:q` om Spark-shell te verlaten voor de eerste SSH-sessie.

## <a name="next-steps"></a>Volgende stappen

* [HWC-integratie met Apache Spark en Apache Hive](./apache-hive-warehouse-connector.md)
* [Interactieve query gebruiken met HDInsight](./apache-interactive-query-get-started.md)
* [HWC-integratie met Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)

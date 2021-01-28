---
title: MapReduce-en SSH-verbinding met Apache Hadoop-Azure HDInsight
description: Meer informatie over het gebruik van SSH voor het uitvoeren van MapReduce-taken met Apache Hadoop op HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939641"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>MapReduce gebruiken met Apache Hadoop op HDInsight met SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Meer informatie over het verzenden van MapReduce-taken van een SSH-verbinding (Secure Shell) naar HDInsight.

> [!NOTE]
> Zie [op Linux gebaseerde hdinsight-tips](../hdinsight-hadoop-linux-information.md)als u al bekend bent met het gebruik van Apache Hadoop servers die zijn gebaseerd op Linux, maar geen ervaring hebt met hdinsight.

## <a name="prerequisites"></a>Vereisten

Een Apache Hadoop cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Hadoop-opdrachten gebruiken

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nadat u verbinding hebt gemaakt met het HDInsight-cluster, gebruikt u de volgende opdracht om een MapReduce-taak te starten:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Met deze opdracht start u de `wordcount` klasse, die deel uitmaakt van het `hadoop-mapreduce-examples.jar` bestand. Het document wordt gebruikt `/example/data/gutenberg/davinci.txt` als invoer en de uitvoer wordt opgeslagen in `/example/data/WordCountOutput` .

    > [!NOTE]
    > Zie [MapReduce gebruiken in Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over deze MapReduce-taak en de voorbeeld gegevens.

    De taak verzendt details tijdens het proces en retourneert informatie die lijkt op de volgende tekst wanneer de taak is voltooid:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de uitvoer bestanden weer te geven:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Met deze opdracht worden twee bestanden weer gegeven, `_SUCCESS` en `part-r-00000` . Het `part-r-00000` bestand bevat de uitvoer voor deze taak.

    > [!NOTE]  
    > Sommige MapReduce-taken kunnen de resultaten splitsen over meerdere **deel-r-# # # #** -bestanden. Als dit het geval is, gebruikt u het achtervoegsel # # # # # om de volg orde van de bestanden aan te geven.

1. Als u de uitvoer wilt weer geven, gebruikt u de volgende opdracht:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Met deze opdracht wordt een lijst weer gegeven met de woorden die zijn opgenomen in het **wasbs://example/data/Gutenberg/-davinci.txt** bestand en het aantal keer dat elk woord is opgetreden. De volgende tekst is een voor beeld van de gegevens die in het bestand zijn opgenomen:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Volgende stappen

Zoals u ziet, bieden Hadoop-opdrachten een eenvoudige manier om MapReduce-taken uit te voeren in een HDInsight-cluster en vervolgens de taak uitvoer weer te geven. Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [MapReduce gebruiken in HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

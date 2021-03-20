---
title: Apache Ambari-Hive-weer gave gebruiken met Apache Hadoop in azure HDInsight
description: Meer informatie over het gebruik van de Hive-weer gave in uw webbrowser om Hive-query's in te dienen. De Hive-weer gave maakt deel uit van de Ambari-webinterface die is meegeleverd met het HDInsight-cluster op basis van Linux.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 1f2dbef014f1b48b554e6bc30af83b936fe532a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944875"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Apache Ambari Hive-weergave gebruiken met Apache Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het uitvoeren van Hive-query's met behulp van de Apache Ambari-Hive-weer gave. Met de Hive-weer gave kunt u Hive-query's ontwerpen, optimaliseren en uitvoeren vanuit uw webbrowser.

## <a name="prerequisites"></a>Vereisten

Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Selecteer uw cluster in de [Azure Portal](https://portal.azure.com/).  Zie een [lijst en clusters weer geven](../hdinsight-administer-use-portal-linux.md#showClusters) voor instructies. Het cluster wordt geopend in een nieuwe portal weergave.

1. Selecteer **Ambari-weer gaven** in **cluster dashboards**. Wanneer u wordt gevraagd om te verifiëren, gebruikt u de account naam en het wacht woord voor de cluster aanmelding (standaard `admin` ) die u hebt opgegeven tijdens het maken van het cluster. U kunt ook naar `https://CLUSTERNAME.azurehdinsight.net/#/main/views` in uw browser navigeren `CLUSTERNAME` , waarbij de naam van uw cluster is.

1. Selecteer in de lijst met weer gaven de __Hive-weer gave__.

    ![Apache Ambari Select Apache Hive View](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    De pagina Hive-weer gave is vergelijkbaar met de volgende afbeelding:

    ![Afbeelding van het query werkblad voor de Hive-weer gave](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Plak op het tabblad __query__ de volgende HiveQL-instructies in het werk blad:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Deze instructies doen de volgende acties:

    |Instructie | Beschrijving |
    |---|---|
    |DROP TABLE|Hiermee verwijdert u de tabel en het gegevens bestand, voor het geval de tabel al bestaat.|
    |CREATE EXTERNAL TABLE|Hiermee maakt u een nieuwe tabel ' External ' in de component. Externe tabellen slaan alleen de tabel definitie in Hive op. De gegevens blijven op de oorspronkelijke locatie.|
    |RIJ-INDELING|Laat zien hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
    |OPGESLAGEN ALS TEXTFILE-LOCATIE|Hier wordt weer gegeven waar de gegevens worden opgeslagen en dat deze worden opgeslagen als tekst.|
    |SELECT|Hiermee wordt het aantal rijen geselecteerd waarin de kolom T4 de waarde [ERROR] bevat.|

   > [!IMPORTANT]  
   > De __database__ selectie __standaard__ behouden. De voor beelden in dit document gebruiken de standaard database die is opgenomen in HDInsight.

1. Als u de query wilt starten, selecteert u onder het werk blad **uitvoeren** . De knop wordt oranje en de tekst verandert in **stoppen**.

1. Nadat de query is voltooid, worden de resultaten van de bewerking weer gegeven op het tabblad **resultaten** . De volgende tekst is het resultaat van de query:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    U kunt het tabblad **logboek** gebruiken om de logboek gegevens weer te geven die de taak heeft gemaakt.

   > [!TIP]  
   > Down load of sla de resultaten op in het dialoog venster **acties** , onder het tabblad  **resultaten** .

### <a name="visual-explain"></a>Visuele uitleg

Als u een visualisatie van het query plan wilt weer geven, selecteert u het tabblad **visuele uitleg** onder het werk blad.

De **visuele uitleg** weergave van de query kan nuttig zijn bij het inzicht in de stroom van complexe query's.

### <a name="tez-ui"></a>TEZ-gebruikers interface

Als u de TEZ-gebruikers interface voor de query wilt weer geven, selecteert u het tabblad **TEZ gebruikers interface** onder het werk blad.

> [!IMPORTANT]  
> TEZ wordt niet gebruikt om alle query's op te lossen. U kunt veel query's oplossen zonder TEZ te gebruiken.

## <a name="view-job-history"></a>Taakgeschiedenis weergeven

Op het tabblad __taken__ wordt een overzicht van Hive-query's weer gegeven.

![Geschiedenis van tabblad taken Apache Hive weer geven](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Database tabellen

U kunt het tabblad __tabellen__ gebruiken om te werken met tabellen in een Hive-data base.

![Afbeelding van het tabblad Apache Hive tabellen](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Opgeslagen query's

Op het tabblad **query** kunt u desgewenst query's opslaan. Nadat u een query hebt opgeslagen, kunt u deze opnieuw gebruiken op het tabblad __opgeslagen query's__ .

![Tabblad opgeslagen query's in Apache Hive weer gaven](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Opgeslagen query's worden opgeslagen in de standaard cluster opslag. U kunt de opgeslagen query's vinden in het pad `/user/<username>/hive/scripts` . Deze worden opgeslagen als bestanden met tekst zonder opmaak `.hql` .
>
> Als u het cluster verwijdert, maar de opslag gebruikt, kunt u een hulp programma zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) of Data Lake Storage Verkenner (vanuit de [Azure-Portal](https://portal.azure.com)) gebruiken om de query's op te halen.

## <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

U kunt het onderdeel uitbreiden via door de gebruiker gedefinieerde functies (UDF). Gebruik een UDF om functionaliteit of logica te implementeren die niet eenvoudig kan worden gemodelleerd in HiveQL.

Declareer en sla een set Udf's op met behulp van het tabblad **UDF** boven aan de Hive-weer gave. Deze Udf's kunnen worden gebruikt in combi natie met de **query-editor**.

![Weer gave van het tabblad Udf's Apache Hive weer geven](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Onder aan de **query-editor** wordt de knop **udf's toevoegen** weer gegeven. In deze vermelding wordt een vervolg keuzelijst weer gegeven van de Udf's die in de Hive-weer gave zijn gedefinieerd. Als u een UDF selecteert, worden HiveQL-instructies aan uw query toegevoegd om de UDF in te scha kelen.

Als u bijvoorbeeld een UDF hebt gedefinieerd met de volgende eigenschappen:

* Resource naam: myudfs

* Bronpad:/myudfs.jar

* UDF-naam: myawesomeudf

* Naam UDF-klasse: com. myudfs. meester

Met behulp van de knop **Udfs invoegen** wordt een vermelding weer gegeven met de naam **myudfs**, met een andere vervolg keuzelijst voor elke UDF die voor die bron is gedefinieerd. In dit geval is het **myawesomeudf**. Als u deze vermelding selecteert, voegt u het volgende toe aan het begin van de query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

U kunt de UDF vervolgens gebruiken in uw query. Bijvoorbeeld `SELECT myawesomeudf(name) FROM people;`.

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Udf's met hive in HDInsight:

* [Python gebruiken met Apache Hive en Apache varken in HDInsight](python-udf-hdinsight.md)
* [Een Java UDF gebruiken met Apache Hive in HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive-instellingen

U kunt verschillende Hive-instellingen wijzigen, zoals het wijzigen van de engine voor het uitvoeren van een component van TEZ (de standaard instelling) in MapReduce.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over de Hive op HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [De Apache Beeline-client gebruiken met Apache Hive](apache-hadoop-use-hive-beeline.md)

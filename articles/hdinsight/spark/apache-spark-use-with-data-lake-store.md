---
title: Azure Data Lake Storage Gen1 met HDInsight-Apache Spark analyseren
description: Apache Spark taken uitvoeren om gegevens te analyseren die zijn opgeslagen in Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 42b9958678c76c1d0eed67cc9d97361c84f1dea9
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821159"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in Data Lake Storage Gen1

In dit artikel gebruikt u [Jupyter notebook](https://jupyter.org/) die beschikbaar zijn in HDInsight Spark-clusters om een taak uit te voeren die gegevens uit een Data Lake Storage-account leest.

## <a name="prerequisites"></a>Vereisten

* Azure Data Lake Storage Gen1-account. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark cluster met Data Lake Storage Gen1 als opslag. Volg de instructies op [Quick Start: clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prepare-the-data"></a>De gegevens voorbereiden

> [!NOTE]  
> U hoeft deze stap niet uit te voeren als u het HDInsight-cluster hebt gemaakt met Data Lake Storage als standaard opslag. Het proces voor het maken van een cluster voegt enkele voorbeeld gegevens toe aan het Data Lake Storage-account dat u opgeeft tijdens het maken van het cluster. Ga naar de sectie HDInsight Spark-cluster gebruiken met Data Lake Storage.

Als u een HDInsight-cluster hebt gemaakt met Data Lake Storage als extra opslag en Azure Storage Blob als standaard opslag, moet u eerst een aantal voorbeeld gegevens kopiëren naar het Data Lake Storage-account. U kunt de voorbeeld gegevens gebruiken uit het Azure Storage Blob dat is gekoppeld aan het HDInsight-cluster. U kunt het [hulp programma ADLCopy](https://www.microsoft.com/download/details.aspx?id=50358) gebruiken om dit te doen. Down load en installeer het hulp programma via de koppeling.

1. Open een opdracht prompt en navigeer naar de map waarin AdlCopy is geïnstalleerd `%HOMEPATH%\Documents\adlcopy` .

2. Voer de volgende opdracht uit om een specifieke BLOB te kopiëren van de bron container naar Data Lake Storage:

    ```scala
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Kopieer het **HVAC.csv** voorbeeld gegevensbestand op **/HdiSamples/HdiSamples/SensorSampleData/hvac/** naar het Azure data Lake Storage-account. Het code fragment moet er als volgt uitzien:

    ```scala
    AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

   > [!WARNING]  
   > Zorg ervoor dat de namen van de bestanden en het pad het juiste hoofdletter gebruik gebruiken.

3. U wordt gevraagd om de referenties in te voeren voor het Azure-abonnement waarmee u uw Data Lake Storage-account hebt. Het volgende (of een vergelijkbaar) codefragment wordt weergegeven:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Copy Completed. 1 file copied.
    ```

    Het gegevens bestand (**HVAC.csv**) wordt gekopieerd onder een map **/hvac** in het data Lake Storage-account.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Een HDInsight Spark-cluster gebruiken met Data Lake Storage Gen1

1. Klik vanuit het [Azure Portal](https://portal.azure.com/), in het start Board, op de tegel voor uw Apache Spark cluster (als u het hebt vastgemaakt aan de start Board). U kunt ook naar uw cluster navigeren onder **Bladeren in alle**  >  **HDInsight-clusters**.

2. Klik vanuit de blade Spark-cluster op **Snelkoppelingen**. Klik vervolgens vanuit het **Cluster-dashboard** op **Jupyter Notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

   > [!NOTE]  
   > Mogelijk bereikt u de Jupyter-notebook voor uw cluster ook door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Maak een nieuwe notebook. Klik op **Nieuw** en klik vervolgens op **PySpark**.

    ![Een nieuwe Jupyter Notebook maken](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Een nieuwe Jupyter Notebook maken")

4. Omdat u de notebook met behulp van de PySpark-kernel hebt gemaakt, hoeft u niet expliciet contexten te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. Als eerste stap importeert u de typen die voor dit scenario zijn vereist. Plak hiertoe het volgende codefragment in een cel en druk op **SHIFT + ENTER**.

    ```scala
    from pyspark.sql.types import *
    ```

    Telkens wanneer u een taak in Jupyter uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** samen met de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek. Nadat de taak is voltooid, verandert deze in een lege cirkel.

     ![Status van een Jupyter Notebook taak](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Status van een Jupyter Notebook taak")

5. Laad voorbeeld gegevens in een tijdelijke tabel met behulp van het **HVAC.csv** -bestand dat u naar het data Lake Storage gen1-account hebt gekopieerd. U kunt toegang krijgen tot de gegevens in het Data Lake Storage-account met behulp van het volgende URL-patroon.

   * Als u Data Lake Storage Gen1 als standaard opslag hebt, bevinden HVAC.csv zich op hetzelfde pad als de volgende URL:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

       U kunt ook een verkorte indeling gebruiken, zoals het volgende:

        ```scala
        adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

   * Als u Data Lake Storage als extra opslag ruimte, is HVAC.csv de locatie waar u deze hebt gekopieerd, zoals:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>
        ```

     Plak het volgende code voorbeeld in een lege cel, vervang **MYDATALAKESTORE** door de naam van uw data Lake Storage-account en druk op **SHIFT + ENTER**. Dit codevoorbeeld registreert de gegevens in een tijdelijke tabel genaamd **hvac**.

      ```scala
      # Load the data. The path below assumes Data Lake Storage is   default storage for the Spark cluster
      hvacText = sc.textFile("adl://MYDATALAKESTORazuredatalakestore.  net/cluster/mysparkclusteHdiSamples/HdiSamples/  SensorSampleData/hvac/HVAC.csv")

      # Create the schema
      hvacSchema = StructType([StructField("date", StringTy(), False)  ,StructField("time", StringType(), FalseStructField  ("targettemp", IntegerType(), FalseStructField("actualtemp",   IntegerType(), FalseStructField("buildingID", StringType(),   False)])

      # Parse the data in hvacText
      hvac = hvacText.map(lambda s: s.split(",")).filt(lambda s: s  [0] != "Date").map(lambda s:(str(s[0]), s(s[1]), int(s[2]), int  (s[3]), str(s[6]) ))

      # Create a data frame
      hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

      # Register the data fram as a table to run queries against
      hvacdf.registerTempTable("hvac")
      ```

6. Omdat u een PySpark-kernel gebruikt, kunt u nu rechtstreeks een SQL-query uitvoeren op de tijdelijke tabel **hvac**, die u zojuist hebt gemaakt met behulp van de `%%sql`-magic. `%%sql`Zie [kernels die beschikbaar zijn op Jupyter-notebooks met Apache Spark HDInsight-clusters](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over het Magic, evenals andere magics die beschikbaar zijn in de PySpark-kernel.

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
7. Nadat de taak is voltooid, wordt standaard de volgende uitvoer in tabelvorm weergegeven.

      ![Tabeluitvoer van het queryresultaat](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabeluitvoer van het queryresultaat")

     U kunt de resultaten ook in andere visualisaties bekijken. Zo ziet een gebiedsgrafiek voor dezelfde uitvoer er als volgt uit.

     ![Gebiedsgrafiek van het queryresultaat](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Gebiedsgrafiek van het queryresultaat")

8. Wanneer u klaar bent met het uitvoeren van de toepassing, moet u de notebook afsluiten om de resources vrij te geven. Dit doet u door in het menu **Bestand** in de notebook te klikken op **Sluiten en stoppen**. Hiermee wordt de notebook afgesloten.


## <a name="next-steps"></a>Volgende stappen

* [Een zelfstandige scala-toepassing maken om uit te voeren op Apache Spark cluster](apache-spark-create-standalone-application.md)
* [HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight Spark Linux-cluster te maken](apache-spark-intellij-tool-plugin.md)
* [HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse gebruiken om Apache Spark-toepassingen voor een HDInsight Spark Linux-cluster te maken](apache-spark-eclipse-tool-plugin.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
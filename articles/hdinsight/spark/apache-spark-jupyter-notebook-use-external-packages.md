---
title: Aangepaste maven-pakketten gebruiken met Jupyter in Spark-Azure HDInsight
description: Stapsgewijze instructies voor het configureren van Jupyter-notebooks die beschikbaar zijn met HDInsight Spark-clusters voor het gebruik van aangepaste maven-pakketten.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: a3d787059345b5971d99ebd7a1e26a9135b61bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930342"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Externe pakketten met Jupyter-notebooks gebruiken in Apache Spark clusters in HDInsight

Meer informatie over het configureren van een [Jupyter notebook](https://jupyter.org/) in Apache Spark cluster op HDInsight voor het gebruik van externe, door de Community bijgedragen Apache **maven** -pakketten die niet out-of-the-box in het cluster zijn opgenomen.

U kunt in de [maven-opslag plaats](https://search.maven.org/) zoeken naar de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. Een volledige lijst met door de Community bijgedragen pakketten is bijvoorbeeld beschikbaar in [Spark-pakketten](https://spark-packages.org/).

In dit artikel leert u hoe u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket gebruikt met de Jupyter notebook.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight. Zie [Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) voor meer informatie.

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag voor uw clusters. Dit is `wasb://` voor Azure Storage, `abfs://` voor Azure Data Lake Storage Gen2 of `adl://` voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data Lake Storage Gen2, wordt de URI `wasbs://` of `abfss://` , respectievelijk, weer geven, [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Externe pakketten gebruiken met Jupyter-notebooks

1. Ga naar `https://CLUSTERNAME.azurehdinsight.net/jupyter` de locatie waar `CLUSTERNAME` de naam van uw Spark-cluster zich bevindt.

1. Maak een nieuwe notebook. Selecteer **Nieuw** en selecteer vervolgens **Spark**.

    ![Een nieuwe Spark-Jupyter Notebook maken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Een nieuwe Jupyter Notebook maken")

1. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Selecteer de naam van het notitie blok bovenaan en geef een beschrijvende naam op.

    ![Een naam opgeven voor de notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Een naam opgeven voor de notebook")

1. U gebruikt de `%%configure` Magic om het notitie blok te configureren voor het gebruik van een extern pakket. Zorg er in notitie blokken die gebruikmaken van externe pakketten voor dat u het Magic aanroept `%%configure` in de eerste cel van de code. Dit zorgt ervoor dat de kernel zo is geconfigureerd dat het pakket wordt gebruikt voordat de sessie wordt gestart.

    >[!IMPORTANT]  
    >Als u vergeet de kernel in de eerste cel te configureren, kunt u de gebruiken `%%configure` met de `-f` para meter, maar wordt de sessie opnieuw gestart en gaat alle voortgang verloren.

    | HDInsight-versie | Opdracht |
    |-------------------|---------|
    | Voor HDInsight 3,5 en HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Voor HDInsight 3,3 en HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Het bovenstaande fragment verwacht de Maven-coördinaten voor het externe pakket in de centrale opslag plaats maven. In dit fragment `com.databricks:spark-csv_2.11:1.5.0` is de Maven-coördinaat voor het **Spark-CSV-** pakket. Hier kunt u de coördinaten voor een pakket maken.

    a. Zoek het pakket in de Maven-opslag plaats. Voor dit artikel gebruiken we [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Verzamel de waarden voor **GroupId**, **ArtifactId** en **Version** uit de opslag plaats. Zorg ervoor dat de waarden die u verzamelt, overeenkomen met uw cluster. In dit geval gebruiken we een scala 2,11-en Spark 1.5.0-pakket, maar u moet mogelijk verschillende versies selecteren voor de juiste scala-of Spark-versie in uw cluster. U kunt de scala-versie in uw cluster vinden door uit te voeren `scala.util.Properties.versionString` op de Spark Jupyter-kernel of op Spark-verzen ding. U kunt de Spark-versie op uw cluster vinden door `sc.version` op Jupyter-notebooks uit te voeren.

    ![Externe pakketten gebruiken met Jupyter Notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter Notebook")

    c. De drie waarden samen voegen, gescheiden door een dubbele punt (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Voer de code-cel uit met het `%%configure` Magic. Hiermee configureert u de onderliggende livy-sessie voor het gebruik van het pakket dat u hebt ingevoerd. In de volgende cellen in het notitie blok kunt u het pakket nu gebruiken, zoals hieronder wordt weer gegeven.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    Voor HDInsight 3,4 en lager moet u het volgende code fragment gebruiken.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. U kunt vervolgens de fragmenten uitvoeren, zoals hieronder wordt weer gegeven, om de gegevens weer te geven van de data frame die u in de vorige stap hebt gemaakt.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Externe Python-pakketten gebruiken met Jupyter-notebooks in Apache Spark clusters in HDInsight Linux](apache-spark-python-package-installation.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)

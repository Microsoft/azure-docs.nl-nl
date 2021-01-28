---
title: 'Zelfstudie: een Apache Spark-app voor machine learning bouwen - Azure HDInsight'
description: 'Zelfstudie: hier vindt u stapsgewijze instructies voor het bouwen van een Apache Spark-toepassing voor machine learning in HDInsight Spark-clusters met behulp van een Jupyter-notebook.'
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc, devx-track-python
ms.date: 04/07/2020
ms.openlocfilehash: 88a71eb5a8ca9a6f2bf1d17fc4eaf9780527d2cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930541"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Zelfstudie: een Apache Spark-toepassing voor machine learning bouwen in Azure HDInsight

In deze zelfstudie leert u hoe u het [Jupyter-notebook](https://jupyter.org/) gebruikt voor het bouwen van een [Apache Spark](./apache-spark-overview.md)-toepassing voor Machine Learning voor Azure HDInsight.

[MLlib](https://spark.apache.org/docs/latest/ml-guide.html) is de aanpasbare machine learning-bibliotheek van Spark die bestaat uit veelvoorkomende machine learning-algoritmen en -hulpprogramma's. (Classificatie, regressie, clustering, gezamenlijke filterfuncties en dimensionaliteitsvermindering. En niet te vergeten onderliggende optimalisatieprimitieven.)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Apache Spark-toepassing voor Machine Learning ontwikkelen

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Een Apache Spark-cluster maken](./apache-spark-jupyter-spark-sql-use-portal.md).

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight. Zie [Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) voor meer informatie.

## <a name="understand-the-data-set"></a>Informatie over de gegevensset

De toepassing gebruikt de voorbeeldgegevens uit **HVAC.csv**. Dit bestand is standaard beschikbaar in alle clusters. Het bestand bevindt zich op `\HdiSamples\HdiSamples\SensorSampleData\hvac`. De gegevens hebben betrekking op de gewenste temperatuur en de werkelijke temperatuur in enkele gebouwen waarin HVAC-systemen zijn geïnstalleerd. De kolom **System** bevat de id van het betreffende systeem en de kolom **SystemAge** geeft het aantal jaren aan dat het HVAC-systeem wordt gebruikt in het gebouw. U kunt voorspellen of het in een gebouw warmer of kouder zal zijn gebaseerd op de richttemperatuur, een systeem-id en de leeftijd van het systeem.

![Momentopname van gegevens die worden gebruikt voor het Spark-voorbeeld van machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Momentopname van gegevens die worden gebruikt voor het Spark-voorbeeld van machine learning")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Een Spark-toepassing voor machine learning ontwikkelen met Spark MLib

Deze toepassing gebruikt een [ML-pijplijn](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) van Spark om een documentclassificatie uit te voeren. ML-pijplijnen bieden een uniforme set algemene API's die zijn gebaseerd op DataFrames. Met de DataFrames kunnen gebruikers praktische pijplijnen voor machine learning maken en afstemmen. In de pijplijn splitst u het document op in woorden, converteert u de woorden naar een numerieke functievector en bouwt u ten slotte een voorspellend model met behulp van de functievectoren en labels. Voer de volgende stappen uit om de toepassing te maken.

1. Maak een Jupyter-notebook met behulp van de PySpark-kernel. Zie [Een Jupyter Notebook-bestand maken](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file) voor de instructies.

1. Importeer de typen die nodig zijn voor dit scenario. Plak het volgende codefragment in een lege cel en druk op **Shift+Enter**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Laad de gegevens (hvac.csv), parseer de gegevens en gebruik ze voor het trainen van het model.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    In het codefragment definieert u een functie waarmee de werkelijke temperatuur wordt vergeleken met de gewenste temperatuur. Als de werkelijke temperatuur hoger is, is het warm in het gebouw, aangegeven door de waarde **1.0**. Anders is het koud in het gebouw, wat wordt aangegeven door de waarde **0.0**.

1. Configureer de pijplijn voor de Spark-toepassing voor machine learning. Deze bestaat uit drie fasen: tokenizer, hashingTF en lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Zie [Apache Spark machine learning pipeline](https://spark.apache.org/docs/latest/ml-pipeline.html) (Pijplijn voor machine learning van Apache Spark) voor meer informatie over pijplijnen en hoe ze werken.

1. Koppel de pijplijn aan het trainingsdocument.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Controleer het trainingsdocument om de voortgang met de toepassing te controleren.

    ```PySpark
    training.show()
    ```

    De uitvoer is vergelijkbaar met:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Vergelijk de uitvoer met het onbewerkte CSV-bestand. De eerste rij van het CSV-bestand bevat bijvoorbeeld deze gegevens:

    ![Momentopname van uitvoergegevens voor het Spark-voorbeeld van machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Momentopname van uitvoergegevens voor het Spark-voorbeeld van machine learning")

    U ziet hoe de werkelijke temperatuur lager is dan de richttemperatuur, wat betekent dat het koud is in het gebouw. De waarde voor **label** in de eerste rij is **0.0**, wat betekent dat het niet warm is in het gebouw.

1. Bereid een gegevensset voor die kan worden uitgevoerd op het getrainde model. Hiervoor geeft u een systeem-id en systeemleeftijd (aangeduid als **SystemInfo** in de training-uitvoer) door. Het model voorspelt of het gebouw met die systeem-id en systeemleeftijd warmer (aangeduid met 1.0) of kouder (aangeduid met 0.0) wordt.

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Genereer als laatste voorspellingen op basis van de testgegevens.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    De uitvoer is vergelijkbaar met:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Bekijk de eerste rij van de voorspelling. Voor een HVAC-systeem met de id 20 en een leeftijd van 25 jaar, is het warm in het gebouw (**voorspelling=1.0**). De eerste waarde voor DenseVector (0.49999) komt overeen met de voorspelling 0.0 en de tweede waarde (0.5001) komt overeen met de voorspelling 1.0. In de uitvoer toont het model **prediction=1.0**, ook al is de tweede waarde maar een fractie hoger.

1. Sluit het notebook om de resources vrij te geven. Selecteer hiervoor **Sluiten en stoppen** in het menu **Bestand** van het notebook. Met deze actie wordt het notebook afgesloten en gesloten.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>scikit-learn-bibliotheek van Anaconda gebruiken voor een Spark-toepassing voor machine learning

Apache Spark-clusters in HDInsight bevatten Anaconda-bibliotheken, waaronder de bibliotheek **scikit-learn** voor machine learning. De bibliotheek bevat ook verschillende gegevenssets die u kunt gebruiken om rechtstreeks vanuit een Jupyter-notebook voorbeeldtoepassingen te bouwen. Zie [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html) voor voorbeelden van het gebruik van de bibliotheek scikit-learn.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet meer gebruikt, verwijdert u het cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die wordt weergegeven, de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

![Azure-portal verwijdert een HDInsight-cluster](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het Jupyter-notebook gebruikt voor het bouwen van een Apache Spark-toepassing voor machine learning voor Azure HDInsight. Ga naar de volgende zelfstudie voor meer informatie over het gebruik van IntelliJ IDEA voor Spark-taken.

> [!div class="nextstepaction"]
> [Een Scala Maven-toepassing maken met behulp van IntelliJ](./apache-spark-create-standalone-application.md)

---
title: "Zelfstudie: Gegevens laden en query's uitvoeren met Apache Spark - Azure HDInsight"
description: "Zelfstudie: leer hoe u gegevens laadt en interactieve query's uitvoert op Spark-clusters in Azure HDInsight."
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: d3c8a08a14b23492c4bf032dd2b722c59bdf80ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930082"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight

In deze zelfstudie leert u hoe u een gegevensframe maakt van een CSV-bestand en hoe u interactieve Spark SQL-query's uitvoert op een [Apache Spark](https://spark.apache.org/)-cluster in Azure HDInsight. In Spark is een gegevensframe een gedistribueerde verzameling gegevens die zijn geordend in benoemde kolommen. Er ligt hetzelfde concept aan ten grondslag als aan een tabel in een relationele database of aan een gegevensframe in R/Python.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een gegevensframe maken van een CSV-bestand
> * Query's uitvoeren op het gegevensframe

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Een Apache Spark-cluster maken](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Een Jupyter Notebook maken

Jupyter Notebook is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. Bewerk URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` door `SPARKCLUSTER` te vervangen door de naam van uw Spark-cluster. Voer vervolgens de bewerkte URL in een webbrowser in. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

2. Selecteer op de webpagina van Jupyter **Nieuw** > **PySpark** om een notebook te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt met de naam Untitled(`Untitled.ipynb`) en vervolgens geopend.

    > [!NOTE]  
    > Door de PySpark-kernel te gebruiken voor het maken van een notebook, wordt de `spark`-sessie automatisch voor u gemaakt wanneer u de eerste cel met code uitvoert. U hoeft de sessie dus niet expliciet te maken.

## <a name="create-a-dataframe-from-a-csv-file"></a>Een gegevensframe maken van een CSV-bestand

Toepassingen kunnen dataframes rechtstreeks maken vanuit bestanden of mappen op de externe opslag, zoals Azure Storage of Azure Data Lake Storage, vanuit een Hive-tabel, of vanuit andere gegevensbronnen die worden ondersteund door Apache Spark, zoals Cosmos DB, Azure SQL DB, DW enzovoort. In de volgende schermafbeelding ziet u een momentopname van het bestand HVAC.csv dat wordt gebruikt in deze zelfstudie. Het CSV-bestand wordt geleverd met alle HDInsight Spark-clusters. De gegevens hebben betrekking op de schommelingen in temperatuur van sommige gebouwen.

![Momentopname van gegevens voor de interactieve Spark SQL-query](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Momentopname van gegevens voor de interactieve Spark SQL-query")

1. Plak de volgende code in een lege cel van het Jupyter-notebook en druk op **Shift + Enter** om de code uit te voeren. Met de code importeert u de typen die voor dit scenario zijn vereist:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Wanneer u een interactieve query uitvoert in Jupyter, ziet u in het venster van de webbrowser of de titelbalk van het tabblad de status **(Busy)**, samen met de titel van het notebook. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek. Wanneer de taak is voltooid, verandert deze in een lege cirkel.

    ![Status van interactieve Spark SQL-query](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status van interactieve Spark SQL-query")

1. Noteer de sessie-id die wordt geretourneerd. In de bovenstaande afbeelding is de sessie-id 0. Desgewenst kunt u de details van de sessie ophalen door naar `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` te gaan, waarbij CLUSTERNAME de naam van uw Spark-cluster is en id uw sessie-id-nummer.

1. Voer de volgende code uit om een gegevensframe en een tijdelijke tabel (**hvac**) te maken.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Query's uitvoeren op het gegevensframe

Zodra de tabel is gemaakt, kunt u een interactieve query uitvoeren op de gegevens.

1. Voer de volgende code uit in een lege cel van de notebook:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   De uitvoer wordt weergegeven in een tabel.

     ![Tabeluitvoer van het resultaat van de interactieve Spark-query](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabeluitvoer van het resultaat van de interactieve Spark-query")

2. U kunt de resultaten ook in andere visualisaties bekijken. Als u een vlakdiagram wilt zien voor dezelfde uitvoer, selecteert u **Area** en stelt u de andere waarden in zoals deze worden weergegeven.

    ![Grafiekoppervlak van het resultaat van de interactieve Spark-query](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafiekoppervlak van het resultaat van de interactieve Spark-query")

3. Ga vanaf de menubalk van de notebook naar **File** > **Save and Checkpoint**.

4. Als u direct verdergaat met de [volgende zelfstudie](apache-spark-use-bi-tools.md), laat u het notebook open. Anders sluit u het notebook om de clusterresources vrij te geven. Ga hiervoor in de menubalk naar **File** >  **Close and Halt**.

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens en Jupyter-notebooks opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer dit niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie, is het beter om het cluster te behouden.

Open het cluster in Azure Portal en selecteer **Verwijderen**.

![HDInsight-cluster verwijderen](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Als u de resourcegroep verwijdert, verwijdert u zowel het HDInsight Spark-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een gegevensframe te maken van een CSV-bestand en interactieve Spark SQL-query's uit te voeren op een Apache Spark-cluster in Azure HDInsight. Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Apache Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Gegevens analyseren met BI-tools](apache-spark-use-bi-tools.md)

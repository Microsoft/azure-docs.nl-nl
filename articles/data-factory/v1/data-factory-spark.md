---
title: Spark-Program ma's aanroepen vanuit Azure Data Factory
description: Meer informatie over het aanroepen van Spark-Program ma's vanuit een Azure-data factory met behulp van de MapReduce-activiteit.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 644a0d645abb186731ddb1f408cd36e5b9ba3c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780018"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Spark-Program ma's aanroepen vanuit Azure Data Factory pijp lijnen

> [!div class="op_single_selector" title1="Transformatie activiteiten"]
> * [Hive-activiteit](data-factory-hive-activity.md)
> * [Pig-activiteit](data-factory-pig-activity.md)
> * [MapReduce-activiteit](data-factory-map-reduce.md)
> * [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
> * [Spark-activiteit](data-factory-spark.md)
> * [Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)](data-factory-azure-ml-batch-execution-activity.md)
> * [Resource-activiteit voor het bijwerken van Azure Machine Learning Studio (klassiek)](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedure activiteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [Aangepaste .NET-activiteit](data-factory-use-custom-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory, die algemeen beschikbaar is. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van de Apache Spark-activiteit in Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introductie
De Spark-activiteit is een van de [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) die door Data Factory worden ondersteund. Met deze activiteit wordt het opgegeven Spark-programma uitgevoerd op uw Spark-cluster in azure HDInsight.

> [!IMPORTANT]
> - De Spark-activiteit biedt geen ondersteuning voor HDInsight Spark-clusters die gebruikmaken van Azure Data Lake Store als primaire opslag.
> - De Spark-activiteit ondersteunt alleen bestaande (uw eigen) HDInsight Spark-clusters. Er wordt geen ondersteuning geboden voor een gekoppelde HDInsight-service op aanvraag.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Walkthrough: een pijp lijn maken met een Spark-activiteit
Hier volgen de typische stappen voor het maken van een data factory pijp lijn met een Spark-activiteit:

* Een data factory maken.
* Maak een Azure Storage gekoppelde service om uw opslag die aan uw HDInsight Spark-cluster is gekoppeld, te koppelen aan de data factory.
* Maak een gekoppelde HDInsight-service om uw Spark-cluster in HDInsight te koppelen aan de data factory.
* Een gegevensset maken die verwijst naar de opslag-gekoppelde service. Op dit moment moet u een uitvoer gegevensset opgeven voor een activiteit, zelfs als er geen uitvoer wordt geproduceerd.
* Maak een pijp lijn met Spark-activiteit die verwijst naar de gekoppelde HDInsight-service die u hebt gemaakt. De activiteit is geconfigureerd met de gegevensset die u in de vorige stap hebt gemaakt als een uitvoer gegevensset. De uitvoer gegevensset is de planning (elk uur, dagelijks). Daarom moet u de uitvoer gegevensset opgeven, zelfs als de activiteit niet echt een uitvoer produceert.

### <a name="prerequisites"></a>Vereisten
1. Maak een opslag account voor algemeen gebruik door de instructies in [een opslag account maken](../../storage/common/storage-account-create.md)te volgen.

1. Maak een Spark-cluster in HDInsight door de instructies in de zelf studie [een Spark-cluster maken in hdinsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)te volgen. Koppel het opslag account dat u in stap 1 hebt gemaakt met dit cluster.

1. Down load en controleer het python-script bestand **test.py** dat zich bevindt in [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Upload **test.py** naar de map **pyFiles** in de container **adfspark** in uw Blob-opslag. Maak de container en de map als deze nog niet bestaan.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
Volg deze stappen om een data factory te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Nieuw** > **Gegevens en analyses** > **Data Factory**.

1. Voer op de Blade **nieuw Data Factory** onder **naam** **SparkDF** in.

   > [!IMPORTANT]
   > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de fout ' Data Factory-naam SparkDF is niet beschikbaar is ' wordt weer gegeven, wijzigt u de naam van de data factory. Gebruik bijvoorbeeld yournameSparkDFdate en maak de data factory opnieuw. Zie voor meer informatie over naamgevingsregels [Data Factory: naamgevingsregels](data-factory-naming-rules.md).

1. Selecteer onder **Abonnement** het Azure-abonnement waarvoor u de data factory wilt maken.

1. Selecteer een bestaande resource groep of maak een Azure-resource groep.

1. Schakel het selectievakje bij **Vastmaken aan dashboard** in.

1. Selecteer **Maken**.

   > [!IMPORTANT]
   > Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepniveau.

1. U ziet de data factory zoals deze is gemaakt in het dash board van de Azure Portal.

1. Nadat de data factory is gemaakt, ziet u de pagina **Data factory**. Hier wordt de inhoud van de data factory weergegeven. Als de pagina **Data Factory** niet wordt weer gegeven, selecteert u de tegel voor uw Data Factory op het dash board.

    ![Blade Gegevensfactory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap maakt u twee gekoppelde services. Een service koppelt uw Spark-cluster aan uw data factory en de andere service koppelt uw opslag aan uw data factory.

#### <a name="create-a-storage-linked-service"></a>Een gekoppelde Storage-service maken
In deze stap koppelt u uw opslagaccount aan uw data factory. Een gegevensset die u in een stap later in dit overzicht maakt, verwijst naar deze gekoppelde service. De gekoppelde HDInsight-service die u in de volgende stap definieert, verwijst ook naar deze gekoppelde service.

1. Selecteer op de Blade **Data Factory** de optie **ontwerpen en implementeren**. De Data Factory editor wordt weer gegeven.

1. Selecteer **Nieuwe gegevensopslag** en kies **Azure Storage**.

   ![Nieuwe gegevens opslag](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Het JSON-script dat u gebruikt voor het maken van een gekoppelde opslag service wordt weer gegeven in de editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Vervang de **account naam** en de **account sleutel** door de naam en de toegangs sleutel van uw opslag account. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.

1. Als u de gekoppelde service wilt implementeren, selecteert u **implementeren** op de opdracht balk. Nadat de gekoppelde service is geïmplementeerd, verdwijnt het venster Draft-1. U ziet **AzureStorageLinkedService** in de structuurweergave links.

#### <a name="create-an-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service maken
In deze stap maakt u een gekoppelde HDInsight-service om uw HDInsight Spark-cluster te koppelen aan de data factory. Het HDInsight-cluster wordt gebruikt voor het uitvoeren van het Spark-programma dat is opgegeven in de Spark-activiteit van de pijp lijn in dit voor beeld.

1. Selecteer in de Data Factory editor **meer**  >  **nieuwe Compute**  >  **HDInsight-cluster**.

    ![Gekoppelde HDInsight-service maken](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. Voer de volgende stappen uit in de JSON-editor:

    1. Geef de URI voor het HDInsight Spark-cluster op. Bijvoorbeeld: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Geef de naam op van de gebruiker die toegang heeft tot het Spark-cluster.

    1. Geef het wachtwoord voor de gebruiker op.

    1. Geef de gekoppelde opslag service op die is gekoppeld aan het HDInsight Spark-cluster. In dit voor beeld is het AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - De Spark-activiteit biedt geen ondersteuning voor HDInsight Spark-clusters die gebruikmaken van Azure Data Lake Store als primaire opslag.
    > - De Spark-activiteit ondersteunt alleen bestaande (uw eigen) HDInsight Spark-clusters. Er wordt geen ondersteuning geboden voor een gekoppelde HDInsight-service op aanvraag.

    Zie [gekoppelde hdinsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)voor meer informatie over de aan hdinsight gekoppelde service.

1. Als u de gekoppelde service wilt implementeren, selecteert u **implementeren** op de opdracht balk.

### <a name="create-the-output-dataset"></a>De uitvoergegevensset maken
De uitvoer gegevensset is de planning (elk uur, dagelijks). Daarom moet u een uitvoer gegevensset opgeven voor de Spark-activiteit in de pijp lijn, zelfs als de activiteit geen uitvoer produceert. Het opgeven van een invoer gegevensset voor de activiteit is optioneel.

1. Selecteer in de Data Factory Editor **Meer** > **Nieuwe gegevensset** > **Azure Blob-opslag**.

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. In het JSON-code fragment wordt een gegevensset gedefinieerd met de naam **output dataset**. Bovendien geeft u op dat de resultaten worden opgeslagen in de BLOB-container met de naam **adfspark** en de map met de naam **pyFiles/output**. Zoals eerder vermeld, is deze gegevensset een dummy-gegevensset. Het Spark-programma in dit voor beeld produceert geen uitvoer. De sectie **Beschik baarheid** geeft aan dat de uitvoer gegevensset dagelijks wordt geproduceerd.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Als u de gegevensset wilt implementeren, selecteert u **implementeren** op de opdracht balk.


### <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijp lijn met een HDInsightSpark-activiteit. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. Daarom is er geen invoer gegevensset opgegeven in dit voor beeld.

1. Selecteer in de Data Factory Editor **Meer** > **Nieuwe pijplijn**.

1. Vervang het script in het venster Draft-1 door het volgende script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Houd rekening met de volgende punten:

    1. De eigenschap **type** wordt ingesteld op **HDInsightSpark**.

    1. De eigenschap **rootPath** is ingesteld op **adfspark \\ pyFiles** , waarbij adfspark de BLOB-container is en pyFiles is bestandsmap in die container. In dit voor beeld is de Blob-opslag het account dat is gekoppeld aan het Spark-cluster. U kunt het bestand uploaden naar een ander opslag account. Als u dit doet, maakt u een gekoppelde opslag service om het opslag account te koppelen aan de data factory. Geef vervolgens de naam van de gekoppelde service op als waarde voor de eigenschap **sparkJobLinkedService** . Zie [Eigenschappen van Spark-activiteit](#spark-activity-properties)voor meer informatie over deze eigenschap en andere eigenschappen die door de Spark-activiteit worden ondersteund.

    1. De eigenschap **entryFilePath** is ingesteld op **test.py**, het python-bestand.

    1. De eigenschap **getDebugInfo** is ingesteld op **Always**, wat betekent dat de logboek bestanden altijd worden gegenereerd (geslaagd of mislukt).

       > [!IMPORTANT]
       > U wordt aangeraden deze eigenschap niet `Always` in te stellen in een productie omgeving, tenzij u een probleem wilt oplossen.

    1. De sectie **outputs** heeft één uitvoer gegevensset. U moet een uitvoer gegevensset opgeven, zelfs als het Spark-programma geen uitvoer produceert. De uitvoer gegevensset stuurt de planning voor de pijp lijn (per uur, dagelijks).

    Zie de sectie [Eigenschappen van Spark-activiteit](#spark-activity-properties)voor meer informatie over de eigenschappen die door de Spark-activiteit worden ondersteund.

1. Selecteer **implementeren** op de opdracht balk om de pijp lijn te implementeren.

### <a name="monitor-a-pipeline"></a>Een pijplijn bewaken
1. Selecteer op de Blade **Data Factory** de optie **controleren & beheren** om de bewakings toepassing op een ander tabblad te starten.

    ![De tegel Bewaking en beheer](media/data-factory-spark/monitor-and-manage-tile.png)

1. Wijzig het filter **begin tijd** aan de bovenkant van **2/1/2017** en selecteer **Toep assen**.

1. Er wordt slechts één activiteiten venster weer gegeven omdat er slechts één dag tussen de begin datum (2017-02-01) en de eind tijd (2017-02-02) van de pijp lijn. Controleer of het gegevens segment de status **gereed** heeft.

    ![De pijplijn bewaken](media/data-factory-spark/monitor-and-manage-app.png)

1. Selecteer in de lijst **activiteit Windows** een uitvoering van de activiteit om de details ervan weer te geven. Als er een fout optreedt, ziet u details hierover in het rechterdeel venster.

### <a name="verify-the-results"></a>De resultaten controleren

1. Start de Jupyter Notebook voor uw HDInsight Spark-cluster door naar te gaan `https://CLUSTERNAME.azurehdinsight.net/jupyter` . U kunt ook een cluster dashboard voor uw HDInsight Spark-cluster openen en vervolgens de Jupyter Notebook starten.

1. Selecteer **nieuwe**  >  **PySpark** om een nieuw notitie blok te starten.

    ![Jupyter nieuwe notebook](media/data-factory-spark/jupyter-new-book.png)

1. Voer de volgende opdracht uit door de tekst te kopiëren en te plakken en op SHIFT + ENTER te drukken aan het einde van de tweede instructie:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Controleer of de gegevens uit de tabel HVAC worden weer gegeven.

    ![Jupyter-query resultaten](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Zie voor gedetailleerde instructies de sectie [een Spark SQL-query uitvoeren](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Problemen oplossen
Omdat u getDebugInfo instelt op **Always**, ziet u een logboek submap in de map pyFiles in uw BLOB-container. Het logboek bestand in de logboekmap bevat aanvullende informatie. Dit logboek bestand is vooral nuttig als er een fout optreedt. In een productie omgeving wilt u deze mogelijk instellen op **mislukt**.

Voer de volgende stappen uit voor meer probleem oplossing:


1. Ga naar `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Gebruikers interface-toepassing in garen](media/data-factory-spark/yarnui-application.png)

1. Selecteer **Logboeken** voor een van de uitvoerings pogingen.

    ![Toepassings pagina](media/data-factory-spark/yarn-applications.png)

1. U ziet de volgende aanvullende fout informatie op de logboek pagina:

    ![Logboek fout](media/data-factory-spark/yarnui-application-error.png)

De volgende secties bevatten informatie over de data factory entiteiten om een Spark-cluster en Spark-activiteit in uw data factory te gebruiken.

## <a name="spark-activity-properties"></a>Eigenschappen van Spark-activiteit
Hier volgt een voor beeld van de JSON-definitie van een pijp lijn met een Spark-activiteit:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| naam | De naam van de activiteit in de pijp lijn. | Yes |
| beschrijving | Tekst die beschrijft wat de activiteit doet. | No |
| type | Deze eigenschap moet worden ingesteld op HDInsightSpark. | Yes |
| linkedServiceName | De naam van de gekoppelde HDInsight-service waarop het Spark-programma wordt uitgevoerd. | Yes |
| rootPath | De BLOB-container en de map waarin het Spark-bestand zich bevindt. De bestands naam is hoofdletter gevoelig. | Yes |
| entryFilePath | Relatief pad naar de hoofdmap van de Spark-code/-pakket. | Yes |
| className | De hoofd klasse java/Spark van de toepassing. | No |
| opmerkingen | Een lijst met opdracht regel argumenten voor het Spark-programma. | No |
| proxyUser | Het gebruikers account dat moet worden geïmiteerd voor het uitvoeren van het Spark-programma. | No |
| sparkConfig | Geef waarden op voor de Spark-configuratie-eigenschappen die worden vermeld in [Spark-configuratie: toepassings eigenschappen](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No |
| getDebugInfo | Hiermee geeft u op wanneer de Spark-logboek bestanden worden gekopieerd naar de opslag die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door sparkJobLinkedService. Toegestane waarden zijn geen, altijd of mislukt. De standaard waarde is geen. | No |
| sparkJobLinkedService | De gekoppelde opslag service die het Spark-taak bestand, de afhankelijkheden en de logboeken bevat. Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag gebruikt die aan het HDInsight-cluster is gekoppeld. | No |

## <a name="folder-structure"></a>Mapstructuur
De Spark-activiteit biedt geen ondersteuning voor een inline-script als Pig en Hive-activiteiten. Spark-taken zijn ook uitbreidbaarer dan Pig/Hive-taken. U kunt voor Spark-taken meerdere afhankelijkheden opgeven, zoals jar-pakketten (die in het Java-KLASSENPAD zijn geplaatst), python-bestanden (die zijn geplaatst op de PYTHONPATH) en andere bestanden.

Maak de volgende mapstructuur in de Blob-opslag waarnaar wordt verwezen door de gekoppelde HDInsight-service. Upload vervolgens afhankelijke bestanden naar de juiste submappen in de hoofdmap die wordt vertegenwoordigd door **entryFilePath**. Upload bijvoorbeeld python-bestanden naar de pyFiles-submap en JAR-bestanden naar de submap potten van de hoofdmap. Tijdens runtime verwacht de Data Factory-service de volgende mapstructuur in de Blob-opslag:

| Pad | Beschrijving | Vereist | Type |
| ---- | ----------- | -------- | ---- |
| . | Het pad naar de hoofdmap van de Spark-taak in de gekoppelde Storage-service. | Ja | Map |
| &lt;door de gebruiker gedefinieerd &gt; | Het pad dat naar het invoer bestand van de Spark-taak verwijst. | Yes | File |
| ./jars | Alle bestanden in deze map worden geüpload en geplaatst op het Java-klassenpad van het cluster. | Nee | Map |
| ./pyFiles | Alle bestanden in deze map worden geüpload en geplaatst op de PYTHONPATH van het cluster. | Nee | Map |
| ./files | Alle bestanden in deze map worden geüpload en geplaatst op de werk directory van de uitvoerder. | Nee | Map |
| ./archives | Alle bestanden in deze map worden niet gecomprimeerd. | Nee | Map |
| ./logs | De map waarin de logboeken van het Spark-cluster worden opgeslagen.| Nee | Map |

Hier volgt een voor beeld van opslag met twee Spark-taak bestanden in de Blob-opslag waarnaar wordt verwezen door de gekoppelde HDInsight-service:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```

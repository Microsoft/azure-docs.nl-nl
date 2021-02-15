---
title: MapReduce-programma aanroepen vanuit Azure Data Factory
description: Meer informatie over hoe u gegevens kunt verwerken door MapReduce-Program ma's uit te voeren op een Azure HDInsight-cluster vanuit een Azure-data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f5ad917609d6f9ea401abc898f80631474a44475
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371070"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>MapReduce-Program Ma's aanroepen vanuit Data Factory
> [!div class="op_single_selector" title1="Transformatie activiteiten"]
> * [Hive-activiteit](data-factory-hive-activity.md) 
> * [Pig-activiteit](data-factory-pig-activity.md)
> * [MapReduce-activiteit](data-factory-map-reduce.md)
> * [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
> * [Spark-activiteit](data-factory-spark.md)
> * [Azure Machine Learning Studio-batchuitvoeringsactiviteit (klassiek)](data-factory-azure-ml-batch-execution-activity.md)
> * [Resourceactiviteit voor Azure Machine Learning Studio (klassiek)](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [Aangepaste .NET-activiteit](data-factory-use-custom-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van MapReduce-activiteit in Data Factory](../transform-data-using-hadoop-map-reduce.md).


Met de HDInsight MapReduce-activiteit in een Data Factory [pijp lijn](data-factory-create-pipelines.md) worden MapReduce-Program ma's uitgevoerd op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) gebaseerd HDInsight-cluster op basis van Windows/Linux. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

> [!NOTE] 
> Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](data-factory-introduction.md) en voer de zelf studie uit: [bouw uw eerste gegevens pijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest.  

## <a name="introduction"></a>Introductie
Met een pijp lijn in een Azure-data factory worden gegevens verwerkt in gekoppelde opslag Services met behulp van gekoppelde Compute-Services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkings bewerking uitvoert. In dit artikel wordt beschreven hoe u de HDInsight MapReduce-activiteit gebruikt.

Zie [Pig](data-factory-pig-activity.md) en [Hive](data-factory-hive-activity.md) voor meer informatie over het uitvoeren van Pig/Hive-scripts op een op Windows/Linux gebaseerd HDInsight-cluster via een pijp lijn met behulp van hdinsight varken en Hive-activiteiten. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON voor HDInsight MapReduce-activiteit
In de JSON-definitie voor de HDInsight-activiteit: 

1. Stel het **type** van de **activiteit** in op **HDInsight**.
2. Geef de naam op van de klasse voor de eigenschap **className** .
3. Geef het pad naar het JAR-bestand op, inclusief de bestands naam voor de eigenschap **jarFilePath** .
4. Geef de gekoppelde service op die verwijst naar de Azure-Blob Storage die het JAR-bestand bevat voor de eigenschap **jarLinkedService** .   
5. Geef in de sectie **argumenten** een argument op voor het MapReduce-programma. Tijdens runtime ziet u enkele extra argumenten (bijvoorbeeld: MapReduce. job. Tags) van het MapReduce-Framework. Als u de argumenten wilt onderscheiden met de MapReduce argumenten, kunt u de optie en waarde als argumenten gebruiken, zoals wordt weer gegeven in het volgende voor beeld (-s,--input,--output etc., zijn opties direct gevolgd door hun waarden).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   U kunt de HDInsight MapReduce-activiteit gebruiken om een MapReduce jar-bestand uit te voeren op een HDInsight-cluster. In de volgende JSON-voorbeeld definitie van een pijp lijn is de HDInsight-activiteit geconfigureerd om een mahout JAR-bestand uit te voeren.

## <a name="sample-on-github"></a>Voor beeld op GitHub
U kunt een voor beeld downloaden van het gebruik van de HDInsight MapReduce-activiteit van: [Data Factory voor beelden op github](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Het programma voor het tellen van woorden uitvoeren
Met de pijp lijn in dit voor beeld wordt de telling van het aantal woorden in het Azure HDInsight-cluster uitgevoerd.   

### <a name="linked-services"></a>Gekoppelde services
Eerst maakt u een gekoppelde service om de Azure Storage die wordt gebruikt door het Azure HDInsight-cluster te koppelen aan de Azure-data factory. Als u de volgende code kopieert/plakt, vergeet dan niet om de **account naam** en de **account sleutel** te vervangen door de naam en sleutel van uw Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
Vervolgens maakt u een gekoppelde service om uw Azure HDInsight-cluster te koppelen aan de Azure-data factory. Als u de volgende code kopieert/plakt, vervangt u de **hdinsight-cluster naam** door de naam van uw HDInsight-cluster en wijzigt u de waarden voor de gebruikers naam en het wacht woord.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Gegevenssets
#### <a name="output-dataset"></a>Uitvoer gegevensset
De pijp lijn in dit voor beeld heeft geen invoer. U geeft een uitvoer gegevensset op voor de HDInsight MapReduce-activiteit. Deze gegevensset is slechts een dummy-gegevensset die is vereist om de pijplijn planning te verzorgen.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijp lijn in dit voor beeld heeft maar één activiteit van het type: HDInsightMapReduce. Enkele van de belang rijke eigenschappen in de JSON zijn: 

| Eigenschap | Notities |
|:--- |:--- |
| type |Het type moet zijn ingesteld op **HDInsightMapReduce**. |
| className |De naam van de klasse is: **WordCount** |
| jarFilePath |Het pad naar het jar-bestand met de klasse. Als u de volgende code kopieert/plakt, vergeet dan niet om de naam van het cluster te wijzigen. |
| jarLinkedService |Azure Storage gekoppelde service die het jar-bestand bevat. Deze gekoppelde service verwijst naar de opslag die is gekoppeld aan het HDInsight-cluster. |
| opmerkingen |Het WordCount-programma neemt twee argumenten, een invoer en een uitvoer. Het invoer bestand is het davinci.txt-bestand. |
| frequency/interval |De waarden voor deze eigenschappen komen overeen met de uitvoer gegevensset. |
| linkedServiceName |verwijst naar de gekoppelde HDInsight-service die u eerder hebt gemaakt. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark-Program ma's uitvoeren
U kunt de MapReduce-activiteit gebruiken om Spark-programma's uit te voeren op uw HDInsight Spark-cluster. Zie [Spark-programma's aanroepen vanuit Azure Data Factory](data-factory-spark.md) voor meer informatie.  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Zie ook
* [Hive-activiteit](data-factory-hive-activity.md)
* [Pig-activiteit](data-factory-pig-activity.md)
* [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
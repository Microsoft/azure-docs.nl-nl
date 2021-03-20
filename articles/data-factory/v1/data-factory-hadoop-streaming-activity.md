---
title: Gegevens transformeren met behulp van Hadoop streaming-activiteit-Azure
description: Meer informatie over hoe u de activiteit Hadoop streaming kunt gebruiken in een Azure data factory om gegevens te transformeren door Hadoop-streaming-Program ma's uit te voeren op een op aanvraag/uw eigen HDInsight-cluster.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 532ece3bcc8c62771511fb2608e824de846b54fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383089"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hadoop streaming-activiteit in Azure Data Factory
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van Hadoop streaming-activiteit in Data Factory](../transform-data-using-hadoop-streaming.md).


U kunt de activiteit HDInsightStreamingActivity gebruiken om een Hadoop-streaming-taak vanuit een Azure Data Factory pijp lijn aan te roepen. In het volgende JSON-code fragment ziet u de syntaxis voor het gebruik van de HDInsightStreamingActivity in een pipeline JSON-bestand. 

Met de activiteit HDInsight streaming in een Data Factory [pijp lijn](data-factory-create-pipelines.md) worden Hadoop-streaming-Program ma's uitgevoerd op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) gebaseerd HDInsight-cluster op basis van Windows/Linux. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

> [!NOTE] 
> Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](data-factory-introduction.md) en voer de zelf studie uit: [bouw uw eerste gegevens pijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="json-sample"></a>JSON-voor beeld
Het HDInsight-cluster wordt automatisch gevuld met voorbeeld programma's (wc.exe en cat.exe) en gegevens (davinci.txt). De naam van de container die wordt gebruikt door het HDInsight-cluster, is standaard de naam van het cluster zelf. Als uw cluster naam bijvoorbeeld myhdicluster is, zou de naam van de gekoppelde BLOB-container myhdicluster zijn. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Houd rekening met de volgende punten:

1. Stel de **linkedServiceName** in op de naam van de gekoppelde service die verwijst naar uw HDInsight-cluster waarop de streaming MapReduce-taak wordt uitgevoerd.
2. Stel het type van de activiteit in op **HDInsightStreaming**.
3. Geef voor de **toewijzings** eigenschap de naam van het uitvoer bare toewijzings programma op. In het voor beeld is cat.exe het uitvoer bare toewijzings programma.
4. Voor de eigenschap **reducer** geeft u de naam van het uitvoer bare bestand voor verkleining op. In het voor beeld is wc.exe het uitvoerbaar bestand voor de reducer.
5. Geef voor de eigenschap **invoer** type het invoer bestand (inclusief de locatie) voor de Mapper op. In het voor beeld: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` : adfsample is de BLOB-container, voor beeld/data/Gutenberg is de map en davinci.txt is de blob.
6. Geef voor de eigenschap **uitvoer** type het uitvoer bestand op (met inbegrip van de locatie) voor de verkorter. De uitvoer van de Hadoop streaming-taak wordt geschreven naar de locatie die is opgegeven voor deze eigenschap.
7. Geef in de sectie **filepath** de paden op voor de uitvoer bare bestanden van de Mapper en de uitvoerder. In het voor beeld: ' adfsample/example/apps/wc.exe ', adfsample is de BLOB-container, voor beeld/apps is de map en wc.exe het uitvoer bare bestand is.
8. Geef voor de eigenschap **fileLinkedService** de Azure Storage gekoppelde service op die staat voor de Azure-opslag met de bestanden die zijn opgegeven in de sectie filepath.
9. Geef voor de eigenschap **Arguments** de argumenten voor de streaming-taak op.
10. De eigenschap **getDebugInfo** is een optioneel element. Als deze is ingesteld op fout, worden de Logboeken alleen gedownload bij fout. Als deze eigenschap is ingesteld op always, worden logboeken altijd gedownload, ongeacht de uitvoerings status.

> [!NOTE]
> Zoals in het voor beeld wordt weer gegeven, geeft u een uitvoer gegevensset op voor de activiteit Hadoop streaming voor de eigenschap **outputs** . Deze gegevensset is slechts een dummy-gegevensset die is vereist om de pijplijn planning te verzorgen. U hoeft geen invoer gegevensset op te geven voor de activiteit voor de eigenschap **inputs** .  
> 
> 

## <a name="example"></a>Voorbeeld
De pijp lijn in deze walkthrough voert het programma voor streaming van woorden tellen/verminderen uit op uw Azure HDInsight-cluster. 

### <a name="linked-services"></a>Gekoppelde services
#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Eerst maakt u een gekoppelde service om de Azure Storage die wordt gebruikt door het Azure HDInsight-cluster te koppelen aan de Azure-data factory. Als u de volgende code kopieert/plakt, vergeet dan niet om de account naam en de account sleutel te vervangen door de naam en sleutel van uw Azure Storage. 

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
Vervolgens maakt u een gekoppelde service om uw Azure HDInsight-cluster te koppelen aan de Azure-data factory. Als u de volgende code kopieert/plakt, vervangt u de HDInsight-cluster naam door de naam van uw HDInsight-cluster en wijzigt u de waarden voor de gebruikers naam en het wacht woord. 

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
De pijp lijn in dit voor beeld heeft geen invoer. U geeft een uitvoer gegevensset op voor de activiteit HDInsight streaming. Deze gegevensset is slechts een dummy-gegevensset die is vereist om de pijplijn planning te verzorgen. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijp lijn in dit voor beeld heeft maar één activiteit van het type: **HDInsightStreaming**. 

Het HDInsight-cluster wordt automatisch gevuld met voorbeeld programma's (wc.exe en cat.exe) en gegevens (davinci.txt). De naam van de container die wordt gebruikt door het HDInsight-cluster, is standaard de naam van het cluster zelf. Als uw cluster naam bijvoorbeeld myhdicluster is, zou de naam van de gekoppelde BLOB-container myhdicluster zijn.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Zie ook
* [Hive-activiteit](data-factory-hive-activity.md)
* [Pig-activiteit](data-factory-pig-activity.md)
* [MapReduce-activiteit](data-factory-map-reduce.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)


---
title: Gegevens transformeren met behulp van Pig-activiteit in Azure Data Factory
description: Meer informatie over hoe u de Pig-activiteit in Azure Data Factory v1 kunt gebruiken om Pig-scripts uit te voeren op een op aanvraag/uw eigen HDInsight-cluster.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 2f59734b5452b5a06b49583954f8851e84e8f84d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387169"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Pig-activiteit in Azure Data Factory
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van Pig-activiteit in Data Factory](../transform-data-using-hadoop-pig.md).


Met de HDInsight Pig-activiteit in een Data Factory [pijp lijn](data-factory-create-pipelines.md) worden Pig-query's uitgevoerd op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) gebaseerd HDInsight-cluster op basis van Windows/Linux. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

> [!NOTE] 
> Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](data-factory-introduction.md) en voer de zelf studie uit: [bouw uw eerste gegevens pijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntax

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Syntaxis Details

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam |Naam van de activiteit |Yes |
| beschrijving |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |No |
| type |HDinsightPig |Yes |
| invoer |Een of meer invoer waarden die worden gebruikt door de Pig-activiteit |No |
| uitvoer |Een of meer uitvoer die wordt geproduceerd door de Pig-activiteit |Yes |
| linkedServiceName |Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory |Yes |
| script |Inline van het Pig-script opgeven |No |
| scriptPath |Sla het Pig-script op in een Azure Blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap script of scriptPath. Beide kunnen niet tegelijk worden gebruikt. De bestands naam is hoofdletter gevoelig. |No |
| compliant |Para meters opgeven als sleutel/waarde-paren voor het verwijzen binnen het Pig-script |No |

## <a name="example"></a>Voorbeeld
Laten we eens kijken naar een voor beeld van een spel logboek analyse waarin u de tijd wilt identificeren die wordt besteed aan spelers die games spelen die door uw bedrijf worden gestart.

Het volgende voor beeld van een spel logboek is een door komma's (,) gescheiden bestand. Het bevat de volgende velden: ProfileID, SessionStart, duration, SrcIPAddress en GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Het **Pig-script** voor het verwerken van deze gegevens:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Voer de volgende stappen uit om dit Pig-script uit te voeren in een Data Factory pijp lijn:

1. Maak een gekoppelde service om [uw eigen hdinsight Compute-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) te registreren of configureer het [hdinsight-berekenings cluster op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). We bellen u de **HDInsightLinkedService** van de gekoppelde service.
2. Maak een [gekoppelde service](data-factory-azure-blob-connector.md) om de verbinding met Azure Blob Storage te configureren die als host fungeert voor de gegevens. We bellen u de **StorageLinkedService** van de gekoppelde service.
3. Maak gegevens [sets](data-factory-create-datasets.md) die verwijzen naar de invoer en de uitvoer gegevens. We gaan de invoer gegevensset **PigSampleIn** en de uitvoer gegevensset **PigSampleOut** noemen.
4. Kopieer de Pig-query in een bestand met de Azure-Blob Storage die in stap #2 zijn geconfigureerd. Als de Azure-opslag die als host fungeert voor de gegevens een andere is dan die als host fungeert voor het query bestand, maakt u een afzonderlijke Azure Storage gekoppelde service. Raadpleeg de gekoppelde service in de configuratie van de activiteit. Gebruik **ScriptPath** om het pad naar het Pig-script bestand en **scriptLinkedService** op te geven. 
   
   > [!NOTE]
   > U kunt ook het Pig-script inline in de definitie van de activiteit opgeven met behulp van de eigenschap **script** . Deze aanpak wordt echter niet aanbevolen omdat alle speciale tekens in het script moeten worden ontsnapd en kan problemen met de fout opsporing veroorzaken. De best practice moet worden gevolgd door een stap #4.
   >
   >
5. Maak de pijp lijn met de activiteit HDInsightPig. Met deze activiteit worden de invoer gegevens verwerkt door Pig-script uit te voeren op HDInsight-cluster.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Implementeer de pijp lijn. Zie [het artikel pijp lijnen maken](data-factory-create-pipelines.md) voor meer informatie. 
7. Bewaak de pijp lijn met behulp van de data factory controle-en beheer weergaven. Zie het artikel [Data Factory pipelines controleren en beheren](data-factory-monitor-manage-pipelines.md) voor meer informatie.

## <a name="specifying-parameters-for-a-pig-script"></a>Para meters opgeven voor een Pig-script
Bekijk het volgende voor beeld: spel logboeken worden dagelijks opgenomen in Azure Blob Storage en opgeslagen in een map die is gepartitioneerd op basis van datum en tijd. U wilt het Pig-script para meters en de locatie van de invoercel dynamisch tijdens runtime door geven en ook de uitvoer gepartitioneerd met datum en tijd.

Ga als volgt te werk om het Pig-script te gebruiken:

* Definieer de para meters in **definieert**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* In het Pig-script raadpleegt u de para meters met behulp van '**$parameterName**', zoals wordt weer gegeven in het volgende voor beeld:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Zie ook
* [Hive-activiteit](data-factory-hive-activity.md)
* [MapReduce-activiteit](data-factory-map-reduce.md)
* [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

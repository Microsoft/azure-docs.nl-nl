---
title: Gegevens transformeren met hive-activiteit-Azure
description: Meer informatie over hoe u de Hive-activiteit in Azure Data Factory v1 kunt gebruiken om Hive-query's uit te voeren op een op aanvraag/uw eigen HDInsight-cluster.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63e726c98922b789977a884bf747f12186707d57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782704"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hive-activiteit in Azure Data Factory 
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met hive-activiteit in Data Factory](../transform-data-using-hadoop-hive.md).

De HDInsight Hive-activiteit in een Data Factory [pijp lijn](data-factory-create-pipelines.md) voert Hive-query's uit op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) gebaseerd HDInsight-cluster op basis van Windows/Linux. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

> [!NOTE] 
> Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](data-factory-introduction.md) en voer de zelf studie uit: [bouw uw eerste gegevens pijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Syntaxis Details
| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam |Naam van de activiteit |Yes |
| beschrijving |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |No |
| type |HDinsightHive |Yes |
| invoer |Invoer die wordt gebruikt door de Hive-activiteit |No |
| uitvoer |Uitvoer geproduceerd door de Hive-activiteit |Yes |
| linkedServiceName |Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory |Yes |
| script |Het Hive-script inline opgeven |No |
| scriptPath |Sla het Hive-script op in een Azure Blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap script of scriptPath. Beide kunnen niet tegelijk worden gebruikt. De bestands naam is hoofdletter gevoelig. |No |
| compliant |Geef para meters op als sleutel/waarde-paren voor het verwijzen in het Hive-script met behulp van ' hiveconf ' |No |

## <a name="example"></a>Voorbeeld
Laten we eens kijken naar een voor beeld van een spel logboek analyse waarin u de tijd wilt identificeren die gebruikers spelen die door uw bedrijf worden gestart. 

Het volgende logboek is een voor beeld van een spel logboek, een komma ( `,` ) van elkaar gescheiden en bevat de volgende velden: ProfileID, SessionStart, duration, SrcIPAddress en GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Het **Hive-script** voor het verwerken van deze gegevens:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Als u dit Hive-script in een Data Factory-pijp lijn wilt uitvoeren, moet u het volgende doen:

1. Maak een gekoppelde service om [uw eigen hdinsight Compute-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) te registreren of configureer het [hdinsight-berekenings cluster op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). We bellen u de gekoppelde service ' HDInsightLinkedService '.
2. Maak een [gekoppelde service](data-factory-azure-blob-connector.md) om de verbinding met Azure Blob Storage te configureren die als host fungeert voor de gegevens. We noemen deze gekoppelde service ' StorageLinkedService '
3. Maak gegevens [sets](data-factory-create-datasets.md) die verwijzen naar de invoer en de uitvoer gegevens. We bellen de invoer gegevensset "HiveSampleIn" en de uitvoer gegevensset "HiveSampleOut"
4. Kopieer de Hive-query als een bestand naar Azure Blob Storage geconfigureerd in stap #2. Als de opslag voor het hosten van de gegevens verschilt van het hosten van dit query bestand, maakt u een afzonderlijke Azure Storage gekoppelde service en raadpleegt u deze in de activiteit. Gebruik **ScriptPath** om het pad naar het Hive-query bestand en **scriptLinkedService** op te geven om de Azure-opslag met het script bestand op te geven. 
   
   > [!NOTE]
   > U kunt ook het Hive-script inline in de definitie van de activiteit opgeven met behulp van de eigenschap **script** . Deze benadering wordt niet aanbevolen omdat alle speciale tekens in het script in het JSON-document moeten worden ontsnapd en kan problemen met de fout opsporing veroorzaken. De best practice moet worden gevolgd door een stap #4.
   > 
   > 
5. Maak een pijp lijn met de activiteit HDInsightHive. Met de activiteit worden de gegevens verwerkt/getransformeerd.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. Implementeer de pijp lijn. Zie [het artikel pijp lijnen maken](data-factory-create-pipelines.md) voor meer informatie. 
7. Bewaak de pijp lijn met behulp van de data factory controle-en beheer weergaven. Zie het artikel [Data Factory pipelines controleren en beheren](data-factory-monitor-manage-pipelines.md) voor meer informatie. 

## <a name="specifying-parameters-for-a-hive-script"></a>Para meters opgeven voor een Hive-script
In dit voor beeld worden Game-logboeken dagelijks opgenomen in Azure Blob Storage en opgeslagen in een map die is gepartitioneerd met datum en tijd. U het Hive-script wilt para meters en de locatie van de invoercel dynamisch wilt door geven tijdens runtime en ook de uitvoer gepartitioneerd met datum en tijd.

Ga als volgt te werk om het Hive-script met para meters te gebruiken

* Definieer de para meters in **definieert**.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* In het Hive-script, raadpleegt u de para meter met **$ {hiveconf: para meternaam}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Zie ook
* [Pig-activiteit](data-factory-pig-activity.md)
* [MapReduce-activiteit](data-factory-map-reduce.md)
* [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)


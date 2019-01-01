---
title: Pijplijnen en activiteiten in Azure Data Factory | Microsoft Docs
description: Meer informatie over pijplijnen en activiteiten in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: ca64c87a0211ae00218493fe7bfddcbbb81a032a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109436"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pijplijnen en activiteiten in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt
:"]
> * [Versie 1:](v1/data-factory-create-pipelines.md)
> * [Huidige versie](concepts-pipelines-activities.md)

Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw gegevensverplaatsingen en scenario’s voor gegevensverwerking.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Een pijplijn kan bijvoorbeeld een set van activiteiten bevatten die logboekgegevens opnemen en opschonen, en vervolgens een Apache Spark-taak starten op een HDInsight-cluster voor het analyseren van de logboekgegevens. Het voordeel van een pijplijn is dat u de activiteiten kunt beheren als een set in plaats van afzonderlijk. U kunt de pijplijn bijvoorbeeld in zijn geheel implementeren en plannen, in plaats van de afzonderlijke activiteiten.

Met activiteiten in een pijplijn definieert u welk acties moeten worden uitgevoerd voor uw gegevens. U kunt bijvoorbeeld een kopieeractiviteit gebruiken om gegevens van een on-premises SQL Server naar Azure Blob Storage te kopiëren. Vervolgens kunt u een Hive-activiteit gebruiken waarmee een Hive-script op een Azure HDInsight-cluster wordt uitgevoerd om gegevens uit Blob Storage te verwerken/transformeren en uitvoergegevens te produceren. Gebruik tot slot een tweede kopieeractiviteit voor het kopiëren van de uitvoergegevens naar een Azure SQL Data Warehouse waarop rapportageoplossingen voor business intelligence (BI) zijn gebouwd.

Data Factory ondersteunt drie soorten activiteiten: [activiteiten voor gegevensverplaatsing](copy-activity-overview.md), [activiteiten voor gegevenstransformatie](transform-data.md) en [beheeractiviteiten](control-flow-web-activity.md). Een activiteit kan nul of meer [invoergegevenssets](concepts-datasets-linked-services.md) hebben en een of meer [uitvoergegevenssets](concepts-datasets-linked-services.md) produceren. Het volgende diagram toont de relatie tussen de pijplijn, activiteit en gegevensset in Data Factory:

![Relatie tussen de gegevensset, activiteit en pijplijn](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Een invoergegevensset vertegenwoordigt de invoer voor een activiteit in de pijplijn en een uitvoergegevensset vertegenwoordigt de uitvoer voor de activiteit. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Nadat u een gegevensset hebt gemaakt, kunt u deze gebruiken voor activiteiten in een pijplijn. Een gegevensset kan bijvoorbeeld een gegevensset voor invoer/uitvoer van een kopieeractiviteit of een HDInsightHive-activiteit zijn. Zie het artikel [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md) voor meer informatie over gegevenssets.

## <a name="data-movement-activities"></a>Activiteiten voor gegevensverplaatsing
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de gegevensarchieven die worden vermeld in deze sectie. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Zie het artikel [Kopieeractiviteit - overzicht](copy-activity-overview.md) voor meer informatie.

## <a name="data-transformation-activities"></a>Activiteiten voor gegevenstransformatie
Azure Data Factory ondersteunt de volgende transformaties, die individueel of gekoppeld aan andere activiteiten kunnen worden toegevoegd aan pijplijnen.

Activiteiten voor gegevenstransformatie | Compute-omgeving
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning-activiteiten: batchuitvoering en resources bijwerken](transform-data-using-machine-learning.md) | Azure VM
[Opgeslagen procedure](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse of SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Aangepaste code](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks

Zie het artikel [Activiteiten voor gegevenstransformatie](transform-data.md) voor meer informatie.

## <a name="control-activities"></a>Controleactiviteiten
De volgende controlestroomactiviteiten worden ondersteund:

Controleactiviteit | Beschrijving
---------------- | -----------
[Execute Pipeline Activity](control-flow-execute-pipeline-activity.md) | De Execute Pipeline Activity stelt een Data Factory-pijplijn in staat om een andere pijplijn aan te roepen.
[ForEachActivity](control-flow-for-each-activity.md) | De ForEachActivity definieert een herhalende controlestroom in de pijplijn. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen.
[WebActivity](control-flow-web-activity.md) | De WebActivity kan worden gebruikt om een aangepast REST-eindpunt aan te roepen vanaf een Data Factory-pijplijn. U kunt gegevenssets en gekoppelde services doorgeven die moten worden verbruikt door en die toegankelijk zijn voor de activiteit.
[Lookup Activity](control-flow-lookup-activity.md) | De Lookup Activity kan worden gebruikt om een record/tabelnaam/waarde van een externe bron te lezen of op te zoeken. Er kan naar deze uitvoer worden verwezen door volgende activiteiten.
[Get Metadata Activity](control-flow-get-metadata-activity.md) | De Get Metadata Activity kan worden gebruikt voor het ophalen van metagegevens van gegevens in Azure Data Factory.
[Until Activity](control-flow-until-activity.md) | Hiermee implementeert u een Doen totdat-lus die vergelijkbaar is met een Doen totdat-lusstructuur in computertalen. Er wordt een reeks activiteiten uitgevoerd totdat de voorwaarde die aan de activiteit is gekoppeld, resulteert in waar. U kunt in Data Factory een time-outwaarde voor de Until-activiteit opgeven.
[If Condition Activity](control-flow-if-condition-activity.md) | De If Condition kan worden gebruikt als vertakking onder de voorwaarde dat deze resulteert in waar of onwaar. De If Condition Activity biedt dezelfde functionaliteit als een If-instructie in een programmeertaal. Er wordt een reeks activiteiten mee geëvalueerd als de voorwaarde resulteert in `true` en een andere reeks activiteiten als de voorwaarde resulteert in `false`.
[Wait Activity](control-flow-wait-activity.md) | Als u een Wait Activity in een pijplijn gebruikt, wacht de pijplijn tot de opgegeven periode voorbij is voordat de volgende activiteiten worden uitgevoerd.

## <a name="pipeline-json"></a>Pijplijn in JSON-indeling
Een pijplijn wordt als volgt in de JSON-indeling gedefinieerd:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Label | Beschrijving | Type | Vereist
--- | ----------- | ---- | --------
naam | Naam van de pijplijn. Geef een naam op die staat voor de actie die de pijplijn uitvoert. <br/><ul><li>Maximum aantal tekens: 140</li><li>Moet beginnen met een letter, cijfer of onderstrepingsteken (_)</li><li>De volgende tekens zijn niet toegestaan: '.', '+', '?', '/', '<', '>', ' * ', '%', '&', ':', '\'</li></ul> | Reeks | Ja
description | Voer een beschrijving in van het doel waarvoor de pijplijn wordt gebruikt. | Reeks | Nee
activities | De sectie **activities** kan één of meer activiteiten bevatten die zijn gedefinieerd binnen de activiteit. Zie de sectie [Activity in JSON](#activity-json) voor meer informatie over het JSON-element activities. | Matrix | Ja
parameters | De sectie **parameters** kan één of meer parameters bevatten die zijn gedefinieerd in de pijplijn, waardoor uw pijplijn kan worden hergebruikt. | Lijst | Nee

## <a name="activity-json"></a>Activity in JSON
De sectie **activities** kan één of meer activiteiten bevatten die zijn gedefinieerd binnen de activiteit. Er zijn twee soorten activiteiten: uitvoerings- en controleactiviteiten.

### <a name="execution-activities"></a>Uitvoeringsactiviteiten
Uitvoeringsactiviteiten zijn [activiteiten voor gegevensverplaatsing](#data-movement-activities) en [activiteiten voor gegevenstransformatie](#data-transformation-activities). Ze hebben de volgende structuur op het hoogste niveau:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

De volgende tabel beschrijft de eigenschappen in de JSON-definitie activity:

Label | Beschrijving | Vereist
--- | ----------- | ---------
naam | De naam van de activiteit. Geef een naam op die staat voor de actie die de activiteit uitvoert. <br/><ul><li>Maximum aantal tekens: 55</li><li>Moet beginnen met een letter, cijfer of onderstrepingsteken (_)</li><li>De volgende tekens zijn niet toegestaan: '.', '+', '?', '/', '<', '>', ' * ', '%', '&', ':', '\' | Ja</li></ul>
description | Beschrijving van het doel waarvoor de activiteit of wordt gebruikt | Ja
type | Type activiteit. Bekijk de secties [Activiteiten voor gegevensverplaatsing](#data-movement-activities), [Activiteiten voor gegevenstransformatie](#data-transformation-activities) en [Controleactiviteiten](#control-activities) voor andere typen activiteiten. | Ja
linkedServiceName | De naam van de gekoppelde service die door de activiteit wordt gebruikt.<br/><br/>Een activiteit kan vereisen dat u de gekoppelde service opgeeft die is gekoppeld aan de vereiste rekenomgeving. | Ja voor HDInsight Activity, Azure Machine Learning Batch Scoring Activity en Stored Procedure Activity. <br/><br/>Nee voor alle andere
typeProperties | Eigenschappen in de sectie typeProperties zijn afhankelijk van elk type activiteit. Klik op koppelingen naar de activiteiten in de vorige sectie om typeProperties voor een activiteit te bekijken. | Nee
policy | Beleidsregels die van invloed zijn op het runtimegedrag van de activiteit. Deze eigenschap bevat gedrag voor time-outs en opnieuw proberen. Als hier niets is opgegeven, worden de standaardwaarden gebruikt. Zie voor meer informatie de sectie [Beleidsregels voor activiteiten](#activity-policy). | Nee
dependsOn | Deze eigenschap wordt gebruikt voor het definiëren van afhankelijkheden van de activiteit, en hoe de volgende activiteiten afhankelijk zijn van vorige activiteiten. Zie voor meer informatie de sectie [Afhankelijkheid van activiteiten](#activity-dependency) | Nee

### <a name="activity-policy"></a>Beleidsregels voor activiteiten
Beleidsregels beïnvloeden het de runtimegedrag van een activiteit, waarbij configuratiemogelijkheden worden geboden. Beleidsregels voor activiteiten zijn alleen beschikbaar voor uitvoeringsactiviteiten.

### <a name="activity-policy-json-definition"></a>JSON-definitie van beleidsregels voor activiteiten

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
JSON-naam | Beschrijving | Toegestane waarden | Vereist
--------- | ----------- | -------------- | --------
timeout | Hiermee geeft u de time-out op voor de activiteit die moet worden uitgevoerd. | Periode | Nee. De standaardwaarde is 7 dagen.
retry | Maximaal aantal nieuwe pogingen | Geheel getal | Nee. De standaardwaarde is 0
retryIntervalInSeconds | De vertraging tussen nieuwe pogingen in seconden | Geheel getal | Nee. De standaardwaarde is 20 seconden
secureOutput | Wanneer dit is ingesteld op waar, wordt uitvoer van de activiteit als veilig beschouwd en niet ter controle opgeslagen in een logboek. | Booleaans | Nee. De standaardinstelling is onwaar.

### <a name="control-activity"></a>Controleactiviteit
Controleactiviteiten hebben de volgende structuur op het hoogste niveau:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Label | Beschrijving | Vereist
--- | ----------- | --------
naam | De naam van de activiteit. Geef een naam op die staat voor de actie die de activiteit uitvoert.<br/><ul><li>Maximum aantal tekens: 55</li><li>Moet beginnen met een letter, cijfer of onderstrepingsteken (_)</li><li>De volgende tekens zijn niet toegestaan: '.', '+', '?', '/', '<', '>', ' * ', '%', '&', ':', '\' | Ja</li><ul>
description | Beschrijving van het doel waarvoor de activiteit of wordt gebruikt | Ja
type | Type activiteit. Bekijk de secties [Activiteiten voor gegevensverplaatsing](#data-movement-activities), [Activiteiten voor gegevenstransformatie](#data-transformation-activities) en [Controleactiviteiten](#control-activities) voor andere typen activiteiten. | Ja
typeProperties | Eigenschappen in de sectie typeProperties zijn afhankelijk van elk type activiteit. Klik op koppelingen naar de activiteiten in de vorige sectie om typeProperties voor een activiteit te bekijken. | Nee
dependsOn | Deze eigenschap wordt gebruikt voor het definiëren van afhankelijkheden van de activiteit, en hoe de volgende activiteiten afhankelijk zijn van vorige activiteiten. Zie voor meer informatie de sectie [Afhankelijkheid van activiteiten](#activity-dependency). | Nee

### <a name="activity-dependency"></a>Afhankelijkheid van activiteiten
De afhankelijkheid van activiteiten definieert hoe latere activiteiten afhankelijk zijn van vorige activiteiten. Op basis hiervan wordt bepaald of de volgende taak kan worden uitgevoerd. Een activiteit kan afhankelijk zijn van een of meer eerdere activiteiten met verschillende afhankelijkheidsvoorwaarden.

De verschillende afhankelijkheidsvoorwaarden zijn: Succeeded, Failed, Skipped, Completed.

Als een pijplijn bijvoorbeeld Activiteit A -> Activiteit B heeft, zijn dit de verschillende scenario's die kunnen ontstaan:

- Activiteit B heeft de afhankelijkheidsvoorwaarde **succeeded** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B alleen kan worden uitgevoerd als Activiteit A de definitieve status succeeded heeft
- Activiteit B heeft de afhankelijkheidsvoorwaarde **failed** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B alleen kan worden uitgevoerd als Activiteit A de definitieve status failed heeft
- Activiteit B heeft de afhankelijkheidsvoorwaarde **completed** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B wordt uitgevoerd als Activiteit A de definitieve status succeeded of failed heeft
- Activiteit B heeft de afhankelijkheidsvoorwaarde **skipped** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B wordt uitgevoerd als Activiteit A de definitieve status skipped heeft. Skipped komt voor in het scenario Activiteit X -> Activiteit Y -> Activiteit Z, waarbij elke activiteit alleen wordt uitgevoerd als de vorige activiteit is geslaagd. Als Activiteit X is mislukt, heeft Activiteit Y de status 'Skipped' omdat deze niet is uitgevoerd. Activiteit Z heeft om dezelfde reden ook de status 'Skipped'.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Voorbeeld: Activiteit 2 is afhankelijk van het slagen van Activiteit 1

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Voorbeeld van kopieerpijplijn
De volgende voorbeeldpijplijn bevat een activiteit van het type **Copy** in de sectie **activities**. In dit voorbeeld kopieert de [kopieeractiviteit](copy-activity-overview.md) gegevens vanuit een Azure-blobopslag naar een Azure SQL-database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Houd rekening met de volgende punten:

- In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**.
- De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**. Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor informatie over het definiëren van gegevenssets in JSON.
- In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Klik in de sectie [Activiteiten voor gegevensverplaatsing](#data-movement-activities) op het gegevensarchief dat u wilt gebruiken als een bron of een sink voor meer informatie over het verplaatsen van gegevens naar/van het betreffende gegevensarchief.

Zie [Snelstartgids: Een data factory maken](quickstart-create-data-factory-powershell.md)voor een volledige procedure voor het maken van deze pijplijn.

## <a name="sample-transformation-pipeline"></a>Voorbeeld van pijplijn voor transformatie
De volgende voorbeeldpijplijn bevat een activiteit van het type **HDInsightHive** in de sectie **activities**. In dit voorbeeld transformeert de [HDInsight Hive-activiteit](transform-data-using-hadoop-hive.md) gegevens uit een Azure-blobopslag door een Hive-scriptbestand uit te voeren op een Azure HDInsight Hadoop-cluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Houd rekening met de volgende punten:

- In het gedeelte activities is er slechts één activiteit waarvan **type** is ingesteld op **HDInsightHive**.
- Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam AzureStorageLinkedService) en in de map script van de container `adfgetstarted`.
- De sectie `defines` wordt gebruikt om de runtime-instellingen op te geven die worden doorgegeven aan het Hive-script, zoals Hive-configuratiewaarden (bijvoorbeeld $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

De sectie **typeProperties** verschilt voor elke transformatieactiviteit. Voor meer informatie over ondersteunde typeProperties voor een transformatieactiviteit, klikt u op de transformatieactiviteit in [Activiteiten voor gegevenstransformatie](#data-transformation-activities).

Zie [Zelfstudie: Gegevens transformeren met Spark](tutorial-transform-data-spark-powershell.md)voor een volledige procedure voor het maken van deze pijplijn.

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijplijn
De vorige twee voorbeeldpijplijnen bevatten slechts één activiteit. Een pijplijn kan echter meer dan één activiteit hebben. Als een pijplijn meerdere activiteiten bevat en latere activiteiten niet afhankelijk zijn van de vorige activiteiten, kunnen de activiteiten parallel worden uitgevoerd.

U kunt twee activiteiten koppelen met de [afhankelijkheid van activiteiten](#activity-dependency), waarmee u definieert hoe latere activiteiten afhankelijk zijn van vorige activiteiten. Op basis hiervan wordt bepaald of de volgende taak kan worden uitgevoerd. Een activiteit kan één of meerdere vorige activiteiten beschermen met verschillende afhankelijkheidsvoorwaarden.

## <a name="scheduling-pipelines"></a>Pijplijnen plannen
Pijplijnen worden door triggers gepland. Er zijn verschillende soorten triggers (scheduler-triggers, waarmee pijplijnen worden geactiveerd volgens een wandklokplanning, en handmatige triggers, waarmee pijplijnen op aanvraag worden geactiveerd). Zie het artikel [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md)voor meer informatie over triggers.

Als u wilt dat de trigger een pijplijnuitvoering activeert, moet u een verwijzing naar de betreffende pijplijn opnemen in de definitie van de trigger. Pijplijnen en triggers hebben een 'n-m-relatie'. Meerdere triggers kunnen één pijplijn starten en één trigger kan meerdere pijplijnen starten. Als de trigger is gedefinieerd, moet u de trigger starten zodat deze de pijplijn kan activeren. Zie het artikel [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md)voor meer informatie over triggers.

Stel dat u een scheduler-trigger hebt met de naam 'Trigger A' waarmee u de pijplijn 'MyCopyPipeline' wilt activeren. U definieert de trigger zoals in het volgende voorbeeld wordt weergegeven:

### <a name="trigger-a-definition"></a>Definitie van Trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies voor stapsgewijze instructies voor het maken van pijplijnen met activiteiten:

- [Een pijplijn met een kopieerbewerking maken](quickstart-create-data-factory-powershell.md)
- [Een pijplijn met een transformatieactiviteit voor gegevens bouwen](tutorial-transform-data-spark-powershell.md)

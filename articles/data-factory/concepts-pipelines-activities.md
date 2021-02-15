---
title: Pijplijnen en activiteiten in Azure Data Factory
description: Meer informatie over pijplijnen en activiteiten in Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 870c812a68f765f987cfd3d1b953e0afeb3e9055
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364508"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pijplijnen en activiteiten in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-create-pipelines.md)
> * [Huidige versie](concepts-pipelines-activities.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw gegevensverplaatsingen en scenario’s voor gegevensverwerking.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Een pijplijn kan bijvoorbeeld een set activiteiten bevatten die logboekgegevens opnemen en verwijderen, en vervolgens een toewijzingsgegevensstroom in gang zetten voor het analyseren van de logboekgegevens. Met de pijplijn kunt u de activiteiten beheren als een set in plaats van elk afzonderlijk. U implementeert en plant de pijplijn in plaats van de afzonderlijke activiteiten.

De activiteiten in een pijplijn bepalen acties die moeten worden uitgevoerd op uw gegevens. U kunt bijvoorbeeld een Kopieer activiteit gebruiken om gegevens te kopiëren van SQL Server naar een Azure Blob Storage. Gebruik vervolgens een gegevens stroom activiteit of een Databricks notebook-activiteit voor het verwerken en transformeren van gegevens van de Blob-opslag naar een Azure Synapse Analytics-groep op het hoogste niveau waarop business intelligence rapportage oplossingen zijn gebouwd.

Data Factory heeft drie soorten activiteiten: activiteiten voor [gegevens verplaatsing](copy-activity-overview.md), [activiteiten voor gegevens transformatie](transform-data.md)en [controle activiteiten](#control-flow-activities). Een activiteit kan nul of meer [invoergegevenssets](concepts-datasets-linked-services.md) hebben en een of meer [uitvoergegevenssets](concepts-datasets-linked-services.md) produceren. Het volgende diagram toont de relatie tussen de pijplijn, activiteit en gegevensset in Data Factory:

![Relatie tussen de gegevensset, activiteit en pijplijn](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Een invoer-gegevensset vertegenwoordigt de invoer voor een activiteit in de pijp lijn en een uitvoer gegevensset vertegenwoordigt de uitvoer voor de activiteit. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Nadat u een gegevensset hebt gemaakt, kunt u deze gebruiken voor activiteiten in een pijplijn. Een gegevensset kan bijvoorbeeld een gegevensset voor invoer/uitvoer van een kopieeractiviteit of een HDInsightHive-activiteit zijn. Zie het artikel [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md) voor meer informatie over gegevenssets.

## <a name="data-movement-activities"></a>Activiteiten voor gegevensverplaatsing

De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de gegevensarchieven die worden vermeld in deze sectie. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Zie het artikel [Kopieeractiviteit - overzicht](copy-activity-overview.md) voor meer informatie.

## <a name="data-transformation-activities"></a>Activiteiten voor gegevenstransformatie
Azure Data Factory ondersteunt de volgende transformaties, die individueel of gekoppeld aan andere activiteiten kunnen worden toegevoegd aan pijplijnen.

Activiteiten voor gegevenstransformatie | Compute-omgeving
---------------------------- | -------------------
[Gegevensstroom](control-flow-execute-data-flow-activity.md) | Azure Databricks beheerd door Azure Data Factory
[Azure-functie](control-flow-azure-function-activity.md) | Azure Functions
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop-streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Azure Machine Learning Studio (klassiek) activiteiten: Batchuitvoering en resource bijwerken](transform-data-using-machine-learning.md) | Azure VM
[Opgeslagen procedure](transform-data-using-stored-procedure.md) | Azure SQL, Azure Synapse Analytics of SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Aangepaste activiteit](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks
[Databricks Jar-activiteit](transform-data-databricks-jar.md) | Azure Databricks
[Databricks Python-activiteit](transform-data-databricks-python.md) | Azure Databricks

Zie het artikel [Activiteiten voor gegevenstransformatie](transform-data.md) voor meer informatie.

## <a name="control-flow-activities"></a>Controle stroom activiteiten
De volgende controlestroomactiviteiten worden ondersteund:

Controleactiviteit | Beschrijving
---------------- | -----------
[Variabele toevoegen](control-flow-append-variable-activity.md) | Voeg een waarde toe aan een bestaande matrix variabele.
[Pijp lijn uitvoeren](control-flow-execute-pipeline-activity.md) | De Execute Pipeline Activity stelt een Data Factory-pijplijn in staat om een andere pijplijn aan te roepen.
[Filter](control-flow-filter-activity.md) | Een filter expressie Toep assen op een invoer matrix
[Voor elke](control-flow-for-each-activity.md) | De ForEachActivity definieert een herhalende controlestroom in de pijplijn. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen.
[Metagegevens ophalen](control-flow-get-metadata-activity.md) | De Get Metadata Activity kan worden gebruikt voor het ophalen van metagegevens van gegevens in Azure Data Factory.
[If Condition Activity](control-flow-if-condition-activity.md) | De If Condition kan worden gebruikt als vertakking onder de voorwaarde dat deze resulteert in waar of onwaar. De If Condition Activity biedt dezelfde functionaliteit als een If-instructie in een programmeertaal. Er wordt een reeks activiteiten geëvalueerd wanneer de voor waarde wordt geëvalueerd `true` en een andere set activiteiten wanneer de voor waarde resulteert in `false.`
[Opzoekactiviteit](control-flow-lookup-activity.md) | De Lookup Activity kan worden gebruikt om een record/tabelnaam/waarde van een externe bron te lezen of op te zoeken. Er kan naar deze uitvoer worden verwezen door volgende activiteiten.
[Variabele instellen](control-flow-set-variable-activity.md) | Stel de waarde van een bestaande variabele in.
[Until-activiteit](control-flow-until-activity.md) | Hiermee implementeert u een Doen totdat-lus die vergelijkbaar is met een Doen totdat-lusstructuur in computertalen. Er wordt een reeks activiteiten uitgevoerd totdat de voorwaarde die aan de activiteit is gekoppeld, resulteert in waar. U kunt in Data Factory een time-outwaarde voor de Until-activiteit opgeven.
[Validatie-activiteit](control-flow-validation-activity.md) | Zorg ervoor dat een pijp lijn alleen de uitvoering voortzet als er een referentie gegevensset bestaat die voldoet aan de opgegeven criteria, of dat er een time-out is bereikt.
[Wachtactiviteit](control-flow-wait-activity.md) | Wanneer u een wait-activiteit in een pijp lijn gebruikt, wacht de pijp lijn op het opgegeven tijdstip voordat de volgende activiteiten worden uitgevoerd.
[Webactiviteit](control-flow-web-activity.md) | De WebActivity kan worden gebruikt om een aangepast REST-eindpunt aan te roepen vanaf een Data Factory-pijplijn. U kunt gegevenssets en gekoppelde services doorgeven die moten worden verbruikt door en die toegankelijk zijn voor de activiteit.
[Webhook-activiteit](control-flow-webhook-activity.md) | Gebruik de webhook-activiteit om een eind punt aan te roepen en een call back-URL door te geven. De pijplijn uitvoering wacht totdat de retour aanroep is aangeroepen voordat u verdergaat met de volgende activiteit.

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
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

Tag | Beschrijving | Type | Vereist
--- | ----------- | ---- | --------
naam | Naam van de pijplijn. Geef een naam op die staat voor de actie die de pijplijn uitvoert. <br/><ul><li>Maximum aantal tekens: 140</li><li>Moet beginnen met een letter, cijfer of onderstrepings teken ( \_ )</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', ' \" </li></ul> | Tekenreeks | Ja
beschrijving | Voer een beschrijving in van het doel waarvoor de pijplijn wordt gebruikt. | Tekenreeks | No
activities | De sectie **activities** kan één of meer activiteiten bevatten die zijn gedefinieerd binnen de activiteit. Zie de sectie [Activity in JSON](#activity-json) voor meer informatie over het JSON-element activities. | Matrix | Ja
parameters | De sectie **parameters** kan één of meer parameters bevatten die zijn gedefinieerd in de pijplijn, waardoor uw pijplijn kan worden hergebruikt. | Lijst | No
gelijktijdigheid | Het maximum aantal gelijktijdige uitvoeringen van de pijp lijn kan hebben. Standaard is er geen maximum. Als de limiet voor gelijktijdigheid is bereikt, worden extra pijplijn uitvoeringen in de wachtrij geplaatst totdat eerdere versies zijn voltooid | Aantal | No 
aantekeningen | Een lijst met tags die zijn gekoppeld aan de pijp lijn | Matrix | No

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

Tag | Beschrijving | Vereist
--- | ----------- | ---------
naam | De naam van de activiteit. Geef een naam op die staat voor de actie die de activiteit uitvoert. <br/><ul><li>Maximum aantal tekens: 55</li><li>Moet beginnen met een letter-cijfer of een onderstrepings teken ( \_ )</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', ' \" | Yes</li></ul>
beschrijving | Beschrijving van het doel waarvoor de activiteit of wordt gebruikt | Yes
type | Type activiteit. Bekijk de secties [activiteiten voor gegevens verplaatsing](#data-movement-activities), [activiteiten voor gegevens transformatie](#data-transformation-activities)en [controle activiteiten](#control-flow-activities) voor verschillende typen activiteiten. | Yes
linkedServiceName | De naam van de gekoppelde service die door de activiteit wordt gebruikt.<br/><br/>Een activiteit kan vereisen dat u de gekoppelde service opgeeft die is gekoppeld aan de vereiste rekenomgeving. | Ja voor HDInsight-activiteit, Azure Machine Learning Studio (klassiek) batch Score activiteit, opgeslagen procedure activiteit. <br/><br/>Nee voor alle andere
typeProperties | Eigenschappen in de sectie typeProperties zijn afhankelijk van elk type activiteit. Klik op koppelingen naar de activiteiten in de vorige sectie om typeProperties voor een activiteit te bekijken. | No
policy | Beleidsregels die van invloed zijn op het runtimegedrag van de activiteit. Deze eigenschap bevat een time-out en het gedrag voor opnieuw proberen. Als deze niet is opgegeven, worden de standaard waarden gebruikt. Zie voor meer informatie de sectie [Beleidsregels voor activiteiten](#activity-policy). | No
dependsOn | Deze eigenschap wordt gebruikt voor het definiëren van afhankelijkheden van de activiteit, en hoe de volgende activiteiten afhankelijk zijn van vorige activiteiten. Zie voor meer informatie de sectie [Afhankelijkheid van activiteiten](#activity-dependency) | No

### <a name="activity-policy"></a>Beleidsregels voor activiteiten
Beleidsregels beïnvloeden het de runtimegedrag van een activiteit, waarbij configuratiemogelijkheden worden geboden. Beleidsregels voor activiteiten zijn alleen beschikbaar voor uitvoeringsactiviteiten.

### <a name="activity-policy-json-definition"></a>JSON-definitie van beleidsregels voor activiteiten

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
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

JSON-naam | Description | Toegestane waarden | Vereist
--------- | ----------- | -------------- | --------
timeout | Hiermee geeft u de time-out op voor de activiteit die moet worden uitgevoerd. | Periode | Nee. De standaardwaarde is 7 dagen.
retry | Maximaal aantal nieuwe pogingen | Geheel getal | Nee. De standaardwaarde is 0
retryIntervalInSeconds | De vertraging tussen nieuwe pogingen in seconden | Geheel getal | Nee. De standaard waarde is 30 seconden
secureOutput | Als deze eigenschap is ingesteld op True, wordt de uitvoer van de activiteit beschouwd als beveiligd en niet geregistreerd voor bewaking. | Booleaans | Nee. De standaardinstelling is onwaar.

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

Tag | Beschrijving | Vereist
--- | ----------- | --------
naam | De naam van de activiteit. Geef een naam op die staat voor de actie die de activiteit uitvoert.<br/><ul><li>Maximum aantal tekens: 55</li><li>Moet beginnen met een letter nummer of een onderstrepings teken ( \_ )</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', ' \" | Yes</li><ul>
beschrijving | Beschrijving van het doel waarvoor de activiteit of wordt gebruikt | Yes
type | Type activiteit. Bekijk de secties [Activiteiten voor gegevensverplaatsing](#data-movement-activities), [Activiteiten voor gegevenstransformatie](#data-transformation-activities) en [Controleactiviteiten](#control-flow-activities) voor andere typen activiteiten. | Yes
typeProperties | Eigenschappen in de sectie typeProperties zijn afhankelijk van elk type activiteit. Klik op koppelingen naar de activiteiten in de vorige sectie om typeProperties voor een activiteit te bekijken. | No
dependsOn | Deze eigenschap wordt gebruikt voor het definiëren van afhankelijkheden van de activiteit, en hoe de volgende activiteiten afhankelijk zijn van vorige activiteiten. Zie [afhankelijkheid van activiteiten](#activity-dependency)voor meer informatie. | No

### <a name="activity-dependency"></a>Afhankelijkheid van activiteiten
De afhankelijkheid van activiteiten bepaalt hoe latere activiteiten afhankelijk zijn van vorige activiteiten, waarbij de voor waarde wordt bepaald of de volgende taak moet blijven uitvoeren. Een activiteit kan afhankelijk zijn van een of meer eerdere activiteiten met verschillende afhankelijkheidsvoorwaarden.

De verschillende afhankelijkheidsvoorwaarden zijn: Succeeded, Failed, Skipped, Completed.

Als een pijplijn bijvoorbeeld Activiteit A -> Activiteit B heeft, zijn dit de verschillende scenario's die kunnen ontstaan:

- Activiteit B heeft de afhankelijkheidsvoorwaarde **succeeded** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B alleen kan worden uitgevoerd als Activiteit A de definitieve status succeeded heeft
- Activiteit B heeft de afhankelijkheidsvoorwaarde **failed** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B alleen kan worden uitgevoerd als Activiteit A de definitieve status failed heeft
- Activiteit B heeft de afhankelijkheidsvoorwaarde **completed** ten opzichte van Activiteit A. Dit houdt in dat Activiteit B wordt uitgevoerd als Activiteit A de definitieve status succeeded of failed heeft
- Activiteit B heeft een afhankelijkheids voorwaarde voor activiteit A met **overgeslagen**: activiteit B wordt uitgevoerd als activiteit a de uiteindelijke status overgeslagen heeft. Skipped komt voor in het scenario Activiteit X -> Activiteit Y -> Activiteit Z, waarbij elke activiteit alleen wordt uitgevoerd als de vorige activiteit is geslaagd. Als activiteit X mislukt, heeft activiteit Y de status overgeslagen, omdat deze nooit wordt uitgevoerd. Daarnaast heeft activity Z ook de status overgeslagen.

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
De volgende voorbeeldpijplijn bevat een activiteit van het type **Copy** in de sectie **activities**. In dit voor beeld kopieert de [Kopieer activiteit](copy-activity-overview.md) gegevens uit een Azure Blob-opslag naar een data base in Azure SQL database.

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
- Het Hive-script bestand **partitionweblogs. HQL** wordt opgeslagen in het Azure Storage-account (opgegeven door de scriptLinkedService, de naam AzureStorageLinkedService) en in de map script in de container `adfgetstarted` .
- De sectie `defines` wordt gebruikt om de runtime-instellingen op te geven die worden doorgegeven aan het Hive-script, zoals Hive-configuratiewaarden (bijvoorbeeld $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

De sectie **typeProperties** verschilt voor elke transformatieactiviteit. Voor meer informatie over ondersteunde typeProperties voor een transformatieactiviteit, klikt u op de transformatieactiviteit in [Activiteiten voor gegevenstransformatie](#data-transformation-activities).

Zie [Zelfstudie: Gegevens transformeren met Spark](tutorial-transform-data-spark-powershell.md)voor een volledige procedure voor het maken van deze pijplijn.

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijplijn
De vorige twee voorbeeldpijplijnen bevatten slechts één activiteit. Een pijplijn kan echter meer dan één activiteit hebben. Als een pijplijn meerdere activiteiten bevat en latere activiteiten niet afhankelijk zijn van de vorige activiteiten, kunnen de activiteiten parallel worden uitgevoerd.

U kunt twee activiteiten koppelen met de [afhankelijkheid van activiteiten](#activity-dependency), waarmee u definieert hoe latere activiteiten afhankelijk zijn van vorige activiteiten. Op basis hiervan wordt bepaald of de volgende taak kan worden uitgevoerd. Een activiteit kan één of meerdere vorige activiteiten beschermen met verschillende afhankelijkheidsvoorwaarden.

## <a name="scheduling-pipelines"></a>Pijplijnen plannen
Pijplijnen worden door triggers gepland. Er zijn verschillende soorten triggers (scheduler-trigger, waarmee pijp lijnen kunnen worden geactiveerd op basis van een schema voor de klok en de hand matige trigger, waarmee pijp lijnen op aanvraag worden geactiveerd). Zie het artikel [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie over triggers.

Als u wilt dat de trigger een pijplijnuitvoering activeert, moet u een verwijzing naar de betreffende pijplijn opnemen in de definitie van de trigger. Pijplijnen en triggers hebben een 'n-m-relatie'. Meerdere triggers kunnen één pijp lijn starten en dezelfde trigger kan meerdere pijp lijnen starten. Als de trigger is gedefinieerd, moet u de trigger starten zodat deze de pijplijn kan activeren. Zie het artikel [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie over triggers.

Stel dat u een scheduler-trigger hebt, ' trigger A ' die ik wil starten met mijn pijp lijn, ' MyCopyPipeline '. U definieert de trigger, zoals wordt weer gegeven in het volgende voor beeld:

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

---
title: ForEach-activiteit in Azure Data Factory
description: De voor elke activiteit definieert een herhalende controle stroom in de pijp lijn. Dit wordt gebruikt om een verzameling te herhalen en opgegeven activiteiten uit te voeren.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: a0c3a3cbaa71d627f54550cf92c067afbb1eb3f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786206"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach-activiteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De ForEach-activiteit definieert een herhalende controle stroom in de pijp lijn. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen.

## <a name="syntax"></a>Syntax
De eigenschappen worden verderop in dit artikel beschreven. De eigenschap items is de verzameling en elk item in de verzameling waarnaar wordt verwezen met behulp `@item()` van de zoals wordt weer gegeven in de volgende syntaxis:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | De naam van de for-each-activiteit. | Tekenreeks | Ja
type | Moet worden ingesteld op **foreach** | Tekenreeks | Ja
isSequential | Hiermee wordt aangegeven of de lus opeenvolgend of parallel moet worden uitgevoerd.  Maxi maal 20 herhalings herhalingen kunnen gelijktijdig worden uitgevoerd. Als u bijvoorbeeld een ForEach-activiteit doorloopt over een Kopieer activiteit met tien verschillende bron-en Sink-gegevens sets waarvoor **isSequential** is ingesteld op False, worden alle kopieën tegelijk uitgevoerd. De standaard waarde is False. <br/><br/> Als "isSequential" is ingesteld op False, moet u ervoor zorgen dat er een juiste configuratie is om meerdere uitvoer bare bestanden uit te voeren. Anders moet deze eigenschap worden gebruikt om te voor komen dat er schrijf conflicten ontstaan. Zie de sectie [parallel uitvoeren](#parallel-execution) voor meer informatie. | Booleaans | Nee. De standaard waarde is False.
batchCount | Batch aantal dat moet worden gebruikt voor het beheren van het aantal parallelle uitvoeringen (wanneer isSequential is ingesteld op false). Dit is de bovengrens limiet, maar de for-each-activiteit wordt niet altijd uitgevoerd op dit nummer | Geheel getal (maximum 50) | Nee. De standaard waarde is 20.
Items | Een expressie die een JSON-matrix retourneert die moet worden herhaald. | Expressie (waarmee een JSON-matrix wordt geretourneerd) | Yes
Activiteiten | De activiteiten die moeten worden uitgevoerd. | Lijst met activiteiten | Yes

## <a name="parallel-execution"></a>Parallelle uitvoering
Als **isSequential** is ingesteld op False, wordt de activiteit parallel herhaald met een maximum van 20 gelijktijdige herhalingen. Deze instelling moet voorzichtig worden gebruikt. Als de gelijktijdige herhalingen naar dezelfde map, maar naar verschillende bestanden schrijven, is deze benadering nauw keurig. Als de gelijktijdige herhalingen gelijktijdig naar het exacte bestand worden geschreven, veroorzaakt deze methode waarschijnlijk een fout. 

## <a name="iteration-expression-language"></a>Taal van iteratie expressie
Geef in de ForEach-activiteit een matrix op die u voor de eigenschaps **items** wilt herhalen. " Gebruiken `@item()` om een enkele opsomming in de foreach-activiteit te herhalen. Als bijvoorbeeld **items** een matrix is: [1, 2, 3], `@item()` retourneert 1 in de eerste iteratie, 2 in de tweede iteratie en 3 in de derde iteratie.

## <a name="iterating-over-a-single-activity"></a>Meerdere activiteiten herhalen
**Scenario:** Kopieer vanuit hetzelfde bron bestand in Azure Blob naar meerdere doel bestanden in Azure Blob.

### <a name="pipeline-definition"></a>Pijplijn definitie

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definitie van BLOB-gegevensset

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Parameter waarden uitvoeren

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Meerdere activiteiten herhalen
Het is mogelijk om meerdere activiteiten te herhalen (bijvoorbeeld: Copy en web activities) in een ForEach-activiteit. In dit scenario raden we u aan om meerdere activiteiten samen te stellen in een afzonderlijke pijp lijn. Vervolgens kunt u de ExecutePipeline- [activiteit](control-flow-execute-pipeline-activity.md) in de pijp lijn met de foreach-activiteit gebruiken om de afzonderlijke pijp lijn met meerdere activiteiten aan te roepen. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Voorbeeld
**Scenario:** Herhaal een InnerPipeline binnen een ForEach-activiteit met een activiteit voor het uitvoeren van de pijp lijn. De binnenste pijp lijn wordt gekopieerd met de para meter schema definities.

#### <a name="master-pipeline-definition"></a>Model pijplijn definitie

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definitie van interne pijp lijn

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definitie van bron gegevensset

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definitie van Sink-gegevensset

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Hoofd pijplijn parameters
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Samen voegen van uitvoer

Als u de uitvoer van __foreach__ -activiteit wilt samen voegen, gebruikt u _variabelen_ en _voegt u variabele_ -activiteit toe.

Declareer eerst een `array` _variabele_ in de pijp lijn. Roep vervolgens _toevoeg variabele_ -activiteit aan in elke __foreach__ -lus. Daarna kunt u de aggregatie uit uw matrix ophalen.

## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

Hier volgen enkele beperkingen van de ForEach-activiteit en voorgestelde tijdelijke oplossingen.

| Beperking | Tijdelijke oplossing |
|---|---|
| U kunt een ForEach-lus niet nesten binnen een andere ForEach-lus (of een lus until). | Ontwerp een pijp lijn op twee niveaus waarbij de buitenste pijp lijn met de buitenste ForEach-lus met de geneste lus wordt herhaald. |
| De ForEach-activiteit heeft een maximum `batchCount` van 50 voor parallelle verwerking en Maxi maal 100.000 items. | Ontwerp een pijp lijn op twee niveaus waarbij de buitenste pijp lijn met de ForEach-activiteit wordt herhaald via een inner pijp lijn. |
| SetVariable kan niet worden gebruikt binnen een ForEach-activiteit die parallel wordt uitgevoerd omdat de variabelen globaal zijn voor de hele pijp lijn, ze niet zijn afgestemd op een ForEach-of andere activiteit. | Overweeg het gebruik van sequentiële ForEach of het uitvoeren van de pijp lijn in ForEach (variabele/para meter verwerkt in onderliggende pijp lijn).|
| | |

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)

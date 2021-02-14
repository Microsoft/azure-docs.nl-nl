---
title: De activiteit van de pijp lijn uitvoeren in Azure Data Factory
description: Meer informatie over hoe u de activiteit pijp lijn uitvoeren kunt gebruiken om een Data Factory pijp lijn vanuit een andere Data Factory pijp lijn aan te roepen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9bcf8fd62e3b20701136d7912074afb37d567e2f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383344"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>De activiteit van de pijp lijn uitvoeren in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De activiteit Execute Pipeline stelt een Data Factory-pijplijn in staat om een andere pijplijn aan te roepen.



## <a name="syntax"></a>Syntax

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | De naam van de activiteit voor het uitvoeren van de pijp lijn. | Tekenreeks | Ja
type | Moet worden ingesteld op: **ExecutePipeline**. | Tekenreeks | Ja
pijp lijn | Pijplijn verwijzing naar de afhankelijke pijp lijn die deze pijp lijn aanroept. Een pijplijn referentie object heeft twee eigenschappen: **referentie** naam en **type**. De eigenschap refernaam geeft de naam van de referentie pijplijn aan. De eigenschap type moet worden ingesteld op PipelineReference. | PipelineReference | Yes
parameters | De para meters die moeten worden door gegeven aan de aangeroepen pijp lijn | Een JSON-object waarmee parameter namen worden toegewezen aan argument waarden | No
waitOnCompletion | Hiermee definieert u of de uitvoering van de activiteit wacht totdat de afhankelijke uitvoering van de pijp lijn is voltooid. De standaardinstelling is onwaar. | Booleaans | No

## <a name="sample"></a>Voorbeeld
Dit scenario heeft twee pijp lijnen:

- **Hoofd pijplijn** : deze pijp lijn heeft één activiteit voor het uitvoeren van een pijp lijn die de aangeroepen pijp lijn aanroept. Voor de hoofd pijplijn worden twee para meters gebruikt: `masterSourceBlobContainer` , `masterSinkBlobContainer` .
- **Aangeroepen pijp lijn** : deze pijp lijn heeft één Kopieer activiteit die gegevens kopieert van een Azure Blob-bron naar Azure Blob-sink. De aangeroepen pijp lijn heeft twee para meters: `sourceBlobContainer` , `sinkBlobContainer` .

### <a name="master-pipeline-definition"></a>Model pijplijn definitie

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>De definitie van de pijp lijn is aangeroepen

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Gekoppelde service**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Brongegevensset**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Sink-gegevensset**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>De pijp lijn uitvoeren

Als u de hoofd pijplijn in dit voor beeld wilt uitvoeren, worden de volgende waarden door gegeven voor de para meters masterSourceBlobContainer en masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

De hoofd pijplijn stuurt deze waarden naar de aangeroepen pijp lijn, zoals wordt weer gegeven in het volgende voor beeld: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)

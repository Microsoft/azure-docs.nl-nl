---
title: Opzoek activiteit in Azure Data Factory
description: Leer hoe u de opzoek activiteit kunt gebruiken om een waarde te zoeken vanuit een externe bron. Naar deze uitvoer kan worden verwezen door geslaagde activiteiten.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.openlocfilehash: 4b2fb49899b6a676520fe0912dd122dd72cce023
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712909"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Opzoek activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met opzoek activiteit kan een gegevensset worden opgehaald uit een van de Azure Data Factory ondersteunde gegevens bronnen. u kunt deze gebruiken om dynamisch te bepalen welke objecten in een volgende activiteit moeten worden gebruikt, in plaats van de naam van het object vast te schrijven. Enkele voor beelden van objecten zijn bestanden en tabellen.

Met opzoek activiteit wordt de inhoud van een configuratie bestand of-tabel gelezen en geretourneerd. Het resultaat van het uitvoeren van een query of opgeslagen procedure wordt ook geretourneerd. De uitvoer kan een singleton-waarde zijn of een matrix met kenmerken die kan worden gebruikt in een volgende Kopieer-, trans formatie-of controle stroom activiteiten zoals ForEach-activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Houd rekening met het volgende:

- De opzoek activiteit kan Maxi maal **5000 rijen** opleveren. Als de resultatenset meer records bevat, worden de eerste 5000 rijen geretourneerd.
- De uitvoer van de opzoek activiteit ondersteunt Maxi maal **4 MB** . de activiteit mislukt als de grootte de limiet overschrijdt. 
- De langste duur voor de opzoek activiteit voordat de time-out **24 uur** is.
- Wanneer u een query of opgeslagen procedure gebruikt voor het opzoeken van gegevens, moet u ervoor zorgen dat er één en exact één resultaatset wordt geretourneerd. Anders mislukt de opzoek activiteit.

De volgende gegevens bronnen worden ondersteund voor opzoek activiteiten. 

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Type-eigenschappen

Naam | Beschrijving | Type | Vereist?
---- | ----------- | ---- | --------
sets | Bevat de referentie voor de gegevensset voor de zoek actie. Details ophalen uit de sectie **Eigenschappen van gegevensset** in elk bijbehorend connector artikel. | Sleutel/waarde-paar | Ja
source | Bevat eigenschappen van een gegevensset, hetzelfde als de bron van de Kopieer activiteit. Details ophalen uit de sectie **Eigenschappen van Kopieer activiteit** in elk bijbehorende connector-artikel. | Sleutel/waarde-paar | Ja
firstRowOnly | Geeft aan of alleen de eerste rij of alle rijen worden geretourneerd. | Booleaans | Nee. De standaardwaarde is `true`.

> [!NOTE]
> 
> * Bron kolommen met het type **byteas** worden niet ondersteund.
> * De **structuur** wordt niet ondersteund in de definitie van de gegevensset. Gebruik voor tekstindelings bestanden de rij met koppen om de kolom naam op te geven.
> * Als uw lookup-bron een JSON-bestand is, wordt de `jsonPathDefinition` instelling voor het vorm geven van het JSON-object niet ondersteund. De volledige objecten worden opgehaald.

## <a name="use-the-lookup-activity-result"></a>Het resultaat van de opzoek activiteit gebruiken

Het Zoek resultaat wordt geretourneerd in de `output` sectie van het resultaat van de uitvoering van de activiteit.

* **Wanneer `firstRowOnly` is ingesteld op `true` (standaard)**, is de uitvoer indeling zoals wordt weer gegeven in de volgende code. Het Zoek resultaat bevindt zich onder een vaste `firstRow` sleutel. Gebruik het patroon van om het resultaat in de volgende activiteit te gebruiken  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Wanneer `firstRowOnly` is ingesteld op `false`**, is de uitvoer indeling zoals in de volgende code wordt weer gegeven. Een `count` veld geeft aan hoeveel records er worden geretourneerd. Gedetailleerde waarden worden weer gegeven onder een vaste `value` matrix. In een dergelijk geval wordt de opzoek activiteit gevolgd door een [foreach-activiteit](control-flow-for-each-activity.md). U geeft de `value` matrix door aan het veld foreach-activiteit met `items` behulp van het patroon van `@activity('MyLookupActivity').output.value` . Als u elementen in de `value` matrix wilt openen, gebruikt u de volgende syntaxis: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Een voorbeeld is `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Voorbeeld

In dit voor beeld bevat de pijp lijn twee activiteiten: **zoeken** en **kopiëren**. De Kopieer activiteit kopieert gegevens uit een SQL-tabel in uw Azure SQL Database-exemplaar naar Azure Blob-opslag. De naam van de SQL-tabel wordt opgeslagen in een JSON-bestand in Blob Storage. De opzoek activiteit zoekt de tabel naam tijdens runtime. JSON wordt dynamisch gewijzigd met behulp van deze methode. U hoeft geen pijp lijnen of gegevens sets opnieuw te implementeren. 

In dit voor beeld wordt alleen de zoek functie voor de eerste rij gedemonstreerd. Zie de voor beelden in [meerdere tabellen tegelijk kopiëren met behulp van Azure Data Factory](tutorial-bulk-copy.md)voor het opzoeken van alle rijen en het koppelen van de resultaten met de foreach-activiteit.


### <a name="pipeline"></a>Pijplijn

- De opzoek activiteit is geconfigureerd voor het gebruik van **LookupDataset**, die verwijst naar een locatie in Azure Blob Storage. De opzoek activiteit leest de naam van de SQL-tabel vanuit een JSON-bestand op deze locatie. 
- De Kopieer activiteit maakt gebruik van de uitvoer van de opzoek activiteit. Dit is de naam van de SQL-tabel. De eigenschap **TableName** in de **Source dataset** is geconfigureerd voor het gebruik van de uitvoer van de opzoek activiteit. Met de Kopieer activiteit worden gegevens uit de SQL-tabel gekopieerd naar een locatie in Azure Blob Storage. De locatie wordt opgegeven door de eigenschap **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Zoek gegevensset

De **lookup** -gegevensset is de **sourcetable.jsvoor** het bestand in de Azure Storage lookup-map die is opgegeven door het type **AzureBlobStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Bron** gegevensset voor kopieer activiteit

De **bron** -gegevensset gebruikt de uitvoer van de opzoek activiteit, die de naam van de SQL-tabel is. Met de Kopieer activiteit worden gegevens uit deze SQL-tabel gekopieerd naar een locatie in Azure Blob Storage. De locatie wordt opgegeven door de **sink** -gegevensset. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Sink** -gegevensset voor kopieer activiteit

Met de Kopieer activiteit worden gegevens uit de SQL-tabel gekopieerd naar het **filebylookup.csv** -bestand in de **CSV** -map in azure Storage. Het bestand wordt opgegeven door de eigenschap **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsop

U kunt de volgende twee soorten indelingen gebruiken voor **sourcetable.js** voor het bestand.

#### <a name="set-of-objects"></a>Set met objecten

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Matrix van objecten

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

Hier volgen enkele beperkingen van de opzoek activiteit en voorgestelde tijdelijke oplossingen.

| Beperking | Tijdelijke oplossing |
|---|---|
| De opzoek activiteit heeft Maxi maal 5.000 rijen en een maximale grootte van 4 MB. | Ontwerp een tweedelige pijp lijn waarbij de buitenste pijp lijn zich herhaalt over een interne pijp lijn, waarmee gegevens worden opgehaald die niet groter zijn dan de maximum rijen of-grootte. |
| | |

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit Web](control-flow-web-activity.md)

---
title: Activiteit van meta gegevens in Azure Data Factory ophalen
description: Meer informatie over het gebruik van de activiteit meta gegevens ophalen in een Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: 91cb10d601f0a44cf9895fffe558c03fdbe06eef
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710223"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activiteit van meta gegevens in Azure Data Factory ophalen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt de activiteit meta gegevens ophalen gebruiken om de meta gegevens van alle gegevens in de Azure Data Factory op te halen. U kunt de uitvoer van de activiteit meta gegevens ophalen in voorwaardelijke expressies gebruiken om validatie uit te voeren of de meta gegevens in volgende activiteiten te consumeren.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De activiteit meta gegevens ophalen neemt een gegevensset als invoer en retourneert meta gegevens als uitvoer. Momenteel worden de volgende connectors en de bijbehorende opgehaalde meta gegevens ondersteund. De maximale grootte van de geretourneerde meta gegevens is **4 MB**.

### <a name="supported-connectors"></a>Ondersteunde connectors

**File Storage**

| Connector/meta gegevens | itemName<br>(bestand/map) | Item type<br>(bestand/map) | grootte<br>Profiler | toegevoegd<br>(bestand/map) | lastModified<sup>1</sup><br>(bestand/map) |childItems<br>map |contentMD5<br>Profiler | structuur<sup>2</sup><br/>Profiler | aantal kolommen<sup>2</sup><br>Profiler | bestaat<sup>3</sup><br>(bestand/map) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Bestandssysteem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> meta gegevens `lastModified` :
- Voor Amazon S3 en Google Cloud Storage `lastModified` is van toepassing op de Bucket en de sleutel, maar niet op de virtuele map, en `exists` is van toepassing op de Bucket en de sleutel, maar niet op het voor voegsel of de virtuele map. 
- Voor Azure Blob Storage `lastModified` geldt voor de container en de blob, maar niet voor de virtuele map.

<sup>2</sup> meta gegevens `structure` en `columnCount` worden niet ondersteund bij het ophalen van meta gegevens van binaire, JSON-of XML-bestanden.

<sup>3</sup> meta gegevens `exists` : voor Amazon S3 en Google Cloud Storage, `exists` is van toepassing op de Bucket en de sleutel, maar niet op het voor voegsel of de virtuele map.

Houd rekening met het volgende:

- Wanneer u activiteit voor het ophalen van meta gegevens voor een map gebruikt, moet u ervoor zorgen dat u beschikt over de machtiging lijst/uitvoeren voor de opgegeven map.
- Het Joker teken filter voor mappen/bestanden wordt niet ondersteund voor de activiteit meta gegevens ophalen.
- `modifiedDatetimeStart` en `modifiedDatetimeEnd` filter ingesteld op connector:

    - Deze twee eigenschappen worden gebruikt om de onderliggende items te filteren bij het ophalen van meta gegevens uit een map. Het is niet van toepassing wanneer u meta gegevens ophaalt uit een bestand.
    - Wanneer een dergelijk filter wordt gebruikt, `childItems` bevat de uitvoer alleen de bestanden die zijn gewijzigd binnen het opgegeven bereik, maar niet op mappen.
    - Als u een dergelijk filter wilt Toep assen, worden alle bestanden in de opgegeven map geïnventariseerd met GetMetadata-activiteit en wordt de gewijzigde tijd gecontroleerd. Wijs niet naar een map met een groot aantal bestanden toe, zelfs niet als het verwachte aantal in aanmerking komende bestanden klein is. 

**Relationele database**

| Connector/meta gegevens | structuur | Aantal | reeds |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opties voor meta gegevens

U kunt de volgende typen meta gegevens opgeven in de velden lijst activiteit meta gegevens ophalen om de bijbehorende gegevens op te halen:

| Meta gegevens type | Beschrijving |
|:--- |:--- |
| itemName | De naam van het bestand of de map. |
| Item type | Het type van het bestand of de map. Geretourneerde waarde is `File` of `Folder` . |
| grootte | Grootte van het bestand in bytes. Alleen van toepassing op bestanden. |
| toegevoegd | Er is een datum/tijd gemaakt van het bestand of de map. |
| lastModified | Datum/tijd waarop het bestand of de map voor het laatst is gewijzigd. |
| childItems | Lijst met submappen en bestanden in de opgegeven map. Alleen van toepassing op mappen. Geretourneerde waarde is een lijst met de naam en het type van elk onderliggend item. |
| contentMD5 | MD5 van het bestand. Alleen van toepassing op bestanden. |
| structuur | De gegevens structuur van het bestand of de relationele database tabel. Geretourneerde waarde is een lijst met kolom namen en kolom typen. |
| Aantal | Het aantal kolommen in het bestand of de tabel relationeel. |
| reeds| Hiermee wordt aangegeven of een bestand, map of tabel bestaat. Als `exists` is opgegeven in de lijst met velden ophalen van meta gegevens, mislukt de activiteit zelfs als het bestand, de map of de tabel niet bestaat. In plaats daarvan `exists: false` wordt geretourneerd in de uitvoer. |

>[!TIP]
>Als u wilt controleren of een bestand, map of tabel bestaat, geeft u `exists` in de velden lijst activiteit van meta gegevens ophalen op. U kunt vervolgens het `exists: true/false` resultaat controleren in de uitvoer van de activiteit. Als `exists` niet wordt opgegeven in de lijst met velden, mislukt de activiteit meta gegevens ophalen als het object niet is gevonden.

## <a name="syntax"></a>Syntax

**Activiteit meta gegevens ophalen**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Gegevensset**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Type-eigenschappen

Op dit moment kunnen met de activiteit meta gegevens ophalen de volgende typen meta gegevens worden geretourneerd:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
Velden | De typen meta gegevens die zijn vereist. Zie de sectie [meta gegevens opties](#metadata-options) in dit artikel voor meer informatie over ondersteunde meta gegevens. | Ja 
sets | De referentie gegevensset waarvan de meta gegevens moeten worden opgehaald door de activiteit meta gegevens ophalen. Zie de sectie [mogelijkheden](#capabilities) voor informatie over ondersteunde connectors. Raadpleeg de onderwerpen over de specifieke connector voor de syntaxis van de gegevensset. | Ja
formatSettings | Toep assen bij gebruik van gegevensset voor indelings type. | Nee
storeSettings | Toep assen bij gebruik van gegevensset voor indelings type. | Nee

## <a name="sample-output"></a>Voorbeelduitvoer

De resultaten van de meta gegevens ophalen worden weer gegeven in de uitvoer van de activiteit. Hieronder vindt u twee voor beelden van uitgebreide opties voor meta gegevens. Gebruik dit patroon om de resultaten in een volgende activiteit te gebruiken: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Meta gegevens van een bestand ophalen

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Meta gegevens van een map ophalen

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over andere controle stroom activiteiten die door Data Factory worden ondersteund:

- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [Activiteit Web](control-flow-web-activity.md)

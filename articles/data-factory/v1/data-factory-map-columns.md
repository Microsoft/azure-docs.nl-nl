---
title: Gegevensset-kolommen toewijzen in Azure Data Factory
description: Meer informatie over het toewijzen van bron kolommen aan doel kolommen.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371121"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Kolommen van bron gegevensset toewijzen aan kolommen van doel gegevensset
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

Kolom toewijzing kan worden gebruikt om op te geven hoe kolommen die zijn opgegeven in de bron tabel toewijzing, moeten worden opgegeven in de kolommen ' Structure ' van de Sink-tabel. De eigenschap **columnMapping** is beschikbaar in het gedeelte **TypeProperties** van de Kopieer activiteit.

Kolom toewijzing ondersteunt de volgende scenario's:

* Alle kolommen in de structuur van de bron verzameling worden toegewezen aan alle kolommen in de structuur van de Sink-gegevensset.
* Een subset van de kolommen in de structuur van de bron verzameling wordt toegewezen aan alle kolommen in de structuur van de Sink-gegevensset.

Hieronder vindt u fout voorwaarden die resulteren in een uitzonde ring:

* Minder kolommen of meer kolommen in de ' Structure ' van de Sink-tabel dan is opgegeven in de toewijzing.
* Dubbele toewijzing.
* Het SQL-query resultaat heeft geen kolom naam die is opgegeven in de toewijzing.

> [!NOTE]
> De volgende voor beelden zijn voor Azure SQL en Azure Blob, maar zijn van toepassing op alle gegevens archieven die ondersteuning bieden voor rechthoekige data sets. Pas de definities van de gegevensset en de gekoppelde service in voor beelden aan om naar gegevens in de relevante gegevens bron te verwijzen.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Voor beeld 1: kolom toewijzing van Azure SQL naar Azure Blob
In dit voor beeld heeft de invoer tabel een structuur en verwijst deze naar een SQL-tabel in Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

In dit voor beeld heeft de uitvoer tabel een structuur en verwijst deze naar een BLOB in een Azure Blob-opslag.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

De volgende JSON definieert een Kopieer activiteit in een pijp lijn. De kolommen van de bron die is toegewezen aan de kolommen in Sink (**columnMappings**) met behulp van de eigenschap **Translator** .

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Kolom toewijzings stroom:**

![Kolom toewijzings stroom](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Voor beeld 2: kolom toewijzing met SQL-query van Azure SQL naar Azure Blob
In dit voor beeld wordt een SQL-query gebruikt om gegevens op te halen uit Azure SQL in plaats van eenvoudigweg de tabel naam en kolom namen in de sectie structure op te geven. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
In dit geval worden de query resultaten eerst toegewezen aan kolommen die zijn opgegeven in ' Structure ' van de bron. Vervolgens worden de kolommen van bron ' structuur ' toegewezen aan kolommen in Sink ' Structure ' met de regels die zijn opgegeven in columnMappings.  Stel dat de query vijf kolommen retourneert, nog twee kolommen die zijn opgegeven in de ' Structure ' van de bron.

**Kolom toewijzings stroom**

![Kolom toewijzings stroom-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Volgende stappen
Zie het artikel voor een zelf studie over het gebruik van Copy activity: 

- [Gegevens kopiëren van Blob Storage naar SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

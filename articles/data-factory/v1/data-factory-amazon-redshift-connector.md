---
title: Gegevens verplaatsen van Amazon Redshift met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van Amazon Redshift met behulp van Azure Data Factory Copy-activiteit.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 025250f47bf0630be5ae988140a5feeecfd0eaf0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377547"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Gegevens verplaatsen van Amazon Redshift met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-amazon-redshift-connector.md)
> * [Versie 2 (huidige versie)](../connector-amazon-redshift.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [Amazon Redshift-connector in v2](../connector-amazon-redshift.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van Amazon Redshift te verplaatsen. Het artikel bouwt voort op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van Amazon Redshift naar een [ondersteunde Sink-gegevens opslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Het verplaatsen van gegevens van andere gegevens archieven naar Amazon Redshift wordt niet ondersteund.

> [!TIP]
> Voor de beste prestaties bij het kopiëren van grote hoeveel heden gegevens van Amazon Redshift kunt u de ingebouwde Redshift **Unload** opdracht gebruiken via de Amazon Simple Storage-service (Amazon S3). Zie [Unload gebruiken om gegevens te kopiëren van Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift)voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Als u gegevens naar een on-premises gegevens archief verplaatst, installeert u [Data Management Gateway](data-factory-data-management-gateway.md) op een on-premises machine. Verleen toegang voor een gateway aan het Amazon Redshift-cluster door gebruik te maken van het IP-adres van de on-premises computer. Zie [toegang tot het cluster machtigen](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)voor instructies.
* Als u gegevens wilt verplaatsen naar een Azure-gegevens archief, raadpleegt u het [IP-adres van de berekening en SQL-bereiken die worden gebruikt door de Microsoft Azure data centers](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit om gegevens van een Amazon Redshift-bron te verplaatsen met behulp van verschillende hulpprogram ma's en Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de wizard kopiëren Azure Data Factory. Zie de [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard kopiëren.

U kunt ook een pijp lijn maken met behulp van Visual Studio, Azure PowerShell of andere hulpprogram ma's. Azure Resource Manager sjablonen, de .NET API of de REST API kunnen ook worden gebruikt om de pijp lijn te maken. Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak gekoppelde services om invoer-en uitvoer gegevens archieven te koppelen aan uw data factory.
2. Gegevens sets maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een pijp lijn met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard kopiëren gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten gemaakt. Wanneer u hulpprogram ma's of Api's gebruikt (met uitzonde ring van de .NET API), definieert u de Data Factory entiteiten met behulp van de JSON-indeling. Het JSON-voor beeld: gegevens kopiëren van Amazon Redshift naar Azure Blob-opslag toont de JSON-definities voor de Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een Amazon Redshift-gegevens archief.

In de volgende secties worden de JSON-eigenschappen beschreven die worden gebruikt voor het definiëren van de Data Factory entiteiten voor Amazon Redshift.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor een gekoppelde service van Amazon Redshift.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **type** |Deze eigenschap moet worden ingesteld op **AmazonRedshift**. |Yes |
| **naam** |Het IP-adres of de hostnaam van de Amazon Redshift-server. |Yes |
| **Importeer** |Het nummer van de TCP-poort die de Amazon Redshift-server gebruikt om te Luis teren naar client verbindingen. |Nee (de standaard waarde is 5439) |
| **enddatabase** |De naam van de Amazon Redshift-data base. |Yes |
| **gebruikers** |De naam van de gebruiker die toegang heeft tot de data base. |Yes |
| **password** |Het wacht woord voor het gebruikers account. |Yes |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De secties **structuur**, **Beschik baarheid** en **beleid** zijn vergelijkbaar voor alle typen gegevensset. Voor beelden van typen gegevens sets zijn Azure SQL, Azure Blob Storage en Azure-tabel opslag.

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in de Store. **De sectie typeProperties** voor een gegevensset van het type **RelationalTable**, die de Amazon Redshift-gegevensset bevat, heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **tableName** |De naam van de tabel in de Amazon Redshift-data base waarnaar de gekoppelde service verwijst. |Nee (als de eigenschap **query** van een Kopieer activiteit van het type **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. De **naam**, **Beschrijving**, **invoer** tabel, **uitvoer** tabel en **beleids** eigenschappen zijn beschikbaar voor alle typen activiteiten. De eigenschappen die beschikbaar zijn in de sectie **typeProperties** , variëren per type activiteit. Voor kopieer activiteiten zijn de eigenschappen afhankelijk van de typen gegevens bronnen en Sinks.

Voor kopieer activiteit, wanneer de bron van het type **AmazonRedshiftSource** is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **ophalen** | Gebruik de aangepaste query om de gegevens te lezen. |Nee (als de eigenschap **TableName** van een gegevensset is opgegeven) |
| **redshiftUnloadSettings** | Bevat de eigenschappen groep wanneer de Redshift **Unload** opdracht wordt gebruikt. | No |
| **s3LinkedServiceName** | Amazon S3 om te gebruiken als een tijdelijke opslag. De gekoppelde service wordt opgegeven met behulp van een Azure Data Factory naam van het type **awsaccesskey worden**. | Vereist wanneer de eigenschap **redshiftUnloadSettings** wordt gebruikt |
| **Bucket** | Hiermee wordt de Bucket van Amazon S3 aangegeven die moet worden gebruikt om de tussenliggende gegevens op te slaan. Als deze eigenschap niet wordt gegeven, wordt door de Kopieer activiteit automatisch een Bucket gegenereerd. | Vereist wanneer de eigenschap **redshiftUnloadSettings** wordt gebruikt |

U kunt ook het type **RelationalSource** gebruiken, dat Amazon Redshift bevat, met de volgende eigenschap in de sectie **typeProperties** . Opmerking dit bron type biedt geen ondersteuning voor de opdracht Redshift **Unload** .

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **ophalen** |Gebruik de aangepaste query om de gegevens te lezen. | Nee (als de eigenschap **TableName** van een gegevensset is opgegeven) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>VERWIJDEREN gebruiken om gegevens van Amazon Redshift te kopiëren

De opdracht Amazon Redshift [**UNload**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) verwijdert de resultaten van een query naar een of meer bestanden in Amazon S3. Deze opdracht wordt aanbevolen door Amazon voor het kopiëren van grote gegevens sets van RedShift.

**Voor beeld: gegevens kopiëren van Amazon Redshift naar Azure Synapse Analytics**

In dit voor beeld worden gegevens van Amazon Redshift naar Azure Synapse Analytics gekopieerd. In het voor beeld worden de opdracht Redshift **Unload** , Copyed data en micro soft poly base gebruikt.

Voor deze voorbeeld use-case worden met de Kopieer activiteit eerst de gegevens uit Amazon Redshift naar Amazon S3 verwijderd, zoals geconfigureerd in de  **redshiftUnloadSettings** -optie. Vervolgens worden de gegevens gekopieerd van Amazon S3 naar Azure Blob-opslag, zoals opgegeven in de optie **stagingSettings** . Ten slotte laadt poly base de gegevens in azure Synapse Analytics. Alle tijdelijke notaties worden verwerkt door de Kopieer activiteit.

![Werk stroom kopiëren van Amazon Redshift naar Azure Synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-voor beeld: gegevens kopiëren van Amazon Redshift naar Azure Blob-opslag
In dit voor beeld ziet u hoe u gegevens kopieert van een Amazon Redshift-Data Base naar Azure Blob Storage. Gegevens kunnen rechtstreeks naar elke [ondersteunde Sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats) worden gekopieerd met behulp van de Kopieer activiteit.

Het voor beeld heeft de volgende data factory entiteiten:

* Een gekoppelde service van het type [AmazonRedshift](#linked-service-properties)
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](#dataset-properties)
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van de eigenschappen [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

In het voor beeld worden gegevens gekopieerd van een query resultaat in Amazon Redshift naar een Azure-Blob per uur. De JSON-eigenschappen die in het voor beeld worden gebruikt, worden beschreven in de secties die volgen op de entiteits definities.

**Gekoppelde service van Amazon RedShift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Gekoppelde Azure Blob Storage-service**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift-invoer gegevensset**

De eigenschap **External** wordt ingesteld op ' True ' om de Data Factory-service op de hoogte te stellen dat de gegevensset extern is voor de Data Factory. De instelling van deze eigenschap geeft aan dat de gegevensset niet wordt geproduceerd door een activiteit in de data factory. Stel de eigenschap in op waar voor een invoer-gegevensset die niet wordt geproduceerd door een activiteit in de pijp lijn.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven door de eigenschap **Frequency** in te stellen op ' hour ' en de eigenschap **interval** op 1. De eigenschap **FolderPath** voor de BLOB wordt dynamisch geëvalueerd. De waarde van de eigenschap is gebaseerd op de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van de delen jaar, maand, dag en uur van de begin tijd.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieer activiteit in een pijp lijn met een Azure Redshift-bron (van het type RelationalSource) en een Azure Blob-Sink**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets. De pijp lijn is ingepland om elk uur te worden uitgevoerd. In de JSON-definitie voor de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de **query** -eigenschap selecteert u de gegevens die van het afgelopen uur moeten worden gekopieerd.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Type toewijzing voor Amazon RedShift
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatische type conversies uit van bron type naar Sink-type. De typen worden geconverteerd met behulp van een twee stappen:

1. Converteren van een systeem eigen bron type naar een .NET-type
2. Converteren van een .NET-type naar een systeem eigen Sink-type

De volgende toewijzingen worden gebruikt wanneer Kopieer activiteit de gegevens van een Amazon Redshift-type naar een .NET-type converteert:

| Amazon Redshift-type | .NET-type |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimaal |
| REAL |Enkelvoudig |
| DUBBELE PRECISIE |Dubbel |
| BOOLEAN |Tekenreeks |
| CHAR |Tekenreeks |
| VARCHAR |Tekenreeks |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in de Sink-gegevensset.

## <a name="repeatable-reads-from-relational-sources"></a>Herhaal bare Lees bewerkingen van relationele bronnen
Wanneer u gegevens uit een relationele gegevens opslag kopieert, moet u zich herhalen om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook het **beleid** voor opnieuw proberen voor een gegevensset zodanig configureren dat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Zorg ervoor dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat het segment opnieuw wordt uitgevoerd. Zorg er ook voor dat dezelfde gegevens worden gelezen, ongeacht hoe u het segment opnieuw uitvoert. Zie [Herhaal bare Lees bewerkingen van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van de Kopieer activiteit en op manieren om de prestaties te optimaliseren in de hand leiding voor het kopiëren van de [Kopieer activiteit](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Volgende stappen
Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met Kopieer activiteit.

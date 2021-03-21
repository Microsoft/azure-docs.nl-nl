---
title: Gegevens verplaatsen van de Amazon Simple Storage-service met behulp van Data Factory
description: Meer informatie over het verplaatsen van gegevens uit de Amazon Simple Storage-service (S3) met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d254a535b1db53478772b481bd029a8c4db6f3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361343"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Gegevens verplaatsen van de Amazon Simple Storage-service met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-amazon-simple-storage-service-connector.md)
> * [Versie 2 (huidige versie)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [Amazon S3-connector in v2](../connector-amazon-simple-storage-service.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van de Amazon Simple Storage-service (S3). Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van Amazon S3 kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van Amazon S3 naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar Amazon S3.

## <a name="required-permissions"></a>Vereiste machtigingen
Als u gegevens wilt kopiëren uit Amazon S3, moet u ervoor zorgen dat u de volgende machtigingen hebt gekregen:

* `s3:GetObject` en `s3:GetObjectVersion` voor de bewerkingen van het Amazon S3-object.
* `s3:ListBucket` voor Amazon S3-Bucket bewerkingen. Als u de wizard kopiëren Data Factory gebruikt, `s3:ListAllMyBuckets` is ook vereist.

Zie [machtigingen opgeven in een beleid](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)voor meer informatie over de volledige lijst met Amazon S3-machtigingen.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een Amazon S3-bron met behulp van verschillende hulpprogram ma's of Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's of Api's gebruikt (met uitzonde ring van .NET API), definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een Amazon S3-gegevens archief het gedeelte [JSON-voor beeld: gegevens kopiëren van Amazon S3 naar Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) in dit artikel.

> [!NOTE]
> Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md)voor meer informatie over ondersteunde bestands-en compressie-indelingen voor een Kopieer activiteit.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Amazon S3.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Een gekoppelde service koppelt een gegevens archief aan een data factory. U maakt een gekoppelde service van het type **awsaccesskey worden** om uw Amazon S3-gegevens archief te koppelen aan uw Data Factory. De volgende tabel bevat een beschrijving van de JSON-elementen die specifiek zijn voor de gekoppelde service van Amazon S3 (Awsaccesskey worden).

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| accessKeyID |ID van de geheime toegangs sleutel. |tekenreeks |Ja |
| secretAccessKey |De geheime toegangs sleutel zelf. |Versleutelde geheime teken reeks |Ja |

>[!NOTE]
>Deze connector vereist toegangs sleutels voor IAM-account om gegevens te kopiëren van Amazon S3. [Tijdelijke beveiligings referenties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) worden niet ondersteund.
>

Hier volgt een voorbeeld:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoer gegevens in Azure Blob-opslag weer te geven, stelt u de eigenschap type van de gegevensset in op **AmazonS3**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde service van Amazon S3. Zie [gegevens sets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Secties zoals structuur, Beschik baarheid en beleid zijn vergelijkbaar voor alle typen gegevens sets (zoals SQL database, Azure Blob en Azure Table). De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor een gegevensset van het type **AmazonS3** (die de Amazon S3-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Bucket |De naam van de S3-Bucket. |Tekenreeks |Ja |
| sleutel |De object sleutel S3. |Tekenreeks |Nee |
| beleids |Voor voegsel voor de object sleutel S3. Objecten waarvan de sleutels beginnen met dit voor voegsel worden geselecteerd. Is alleen van toepassing als de sleutel leeg is. |Tekenreeks |Nee |
| versie |De versie van het S3-object, als S3-versie beheer is ingeschakeld. |Tekenreeks |Nee |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie de secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet-indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u bestanden wilt kopiëren als-zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. | |Nee |
| compressie | Geef het type en compressie niveau voor de gegevens op. De ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. De ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. | |Nee |


> [!NOTE]
> **Bucket + sleutel** Hiermee geeft u de locatie van het S3-object, waarbij Bucket de basis container voor S3-objecten is, en sleutel het volledige pad naar het S3-object.

### <a name="sample-dataset-with-prefix"></a>Voor beeld van gegevensset met voor voegsel

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Voor beeld-gegevensset (met versie)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Dynamische paden voor S3
In het voor gaande voor beeld worden vaste waarden gebruikt voor de eigenschappen **Key** en **Bucket** in de Amazon S3-gegevensset.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

U kunt deze eigenschappen in de runtime dynamisch Data Factory berekenen door systeem variabelen zoals slice start te gebruiken.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

U kunt hetzelfde doen voor de eigenschap **prefix** van een Amazon S3-gegevensset. Zie [Data Factory functies en systeem variabelen](data-factory-functions-variables.md)voor een lijst met ondersteunde functies en variabelen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie [pijp lijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleids regels zijn beschikbaar voor alle typen activiteiten. De eigenschappen die beschikbaar zijn in de **typeProperties** -sectie van de activiteit, verschillen per type activiteit. Voor de Kopieer activiteit kunnen eigenschappen variëren, afhankelijk van de typen bronnen en Sinks. Wanneer een bron in de Kopieer activiteit van het type **FileSystemSource** (inclusief Amazon S3) is, is de volgende eigenschap beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Hiermee geeft u op of S3-objecten in de directory recursief moeten worden weer geven. |waar/onwaar |Nee |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-voor beeld: gegevens kopiëren van Amazon S3 naar Azure Blob-opslag
In dit voor beeld ziet u hoe u gegevens van Amazon S3 kopieert naar een Azure Blob-opslag. Gegevens kunnen echter rechtstreeks naar [een van de sinks worden gekopieerd die worden ondersteund](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieer activiteit in Data Factory.

Het voor beeld bevat JSON-definities voor de volgende Data Factory entiteiten. U kunt deze definities gebruiken om een pijp lijn te maken voor het kopiëren van gegevens van Amazon S3 naar Blob Storage, met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Power shell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Een gekoppelde service van het type [awsaccesskey worden](#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AmazonS3](#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van Amazon S3 naar een Azure-Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

### <a name="amazon-s3-linked-service"></a>Gekoppelde service van Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3-invoer gegevensset

**Externe instelling:** als u de Data Factory-service informeert, is de gegevensset extern voor de Data Factory. Stel deze eigenschap in op waar voor een invoer-gegevensset die niet is geproduceerd door een activiteit in de pijp lijn.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van de delen jaar, maand, dag en uur van de begin tijd.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopieer activiteit in een pijp lijn met een Amazon S3-bron en een BLOB-Sink

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **FileSystemSource** en wordt het **sink** -type ingesteld op **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Als u kolommen uit een bron-gegevensset wilt toewijzen aan kolommen uit een Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* Voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in Data Factory en verschillende manieren om deze te optimaliseren, raadpleegt u de [hand leiding Copy activity Performance and Tuning](data-factory-copy-activity-performance.md).

* Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

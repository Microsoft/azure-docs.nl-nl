---
title: Gegevens verplaatsen van/naar Azure-tabel
description: Meer informatie over het verplaatsen van gegevens naar/van Azure Table Storage met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc8e0272dc690ed541883ae943c118cbbe2f1854
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392116"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Gegevens verplaatsen van en naar een Azure-tabel met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-table-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-table-storage.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure Table Storage-connector in v2](../connector-azure-table-storage.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van/naar Azure Table Storage. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit. 

U kunt gegevens van elk ondersteund bron gegevens archief kopiëren naar Azure Table Storage of van Azure Table Storage naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een Azure-Table Storage met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst: 

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie de sectie [JSON-voor beelden](#json-examples) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt om gegevens te kopiëren van/naar een Azure-Table Storage.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Table Storage: 

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Er zijn twee soorten gekoppelde services die u kunt gebruiken om een Azure Blob-opslag te koppelen aan een Azure-data factory. Dit zijn: **opslag** gekoppelde service en **azurestoragesas zijn** gekoppelde service. De Azure Storage gekoppelde service biedt de data factory globale toegang tot de Azure Storage. Overwegende dat de gekoppelde service van Azure Storage SAS (Shared Access Signature) de data factory met beperkte/gebonden toegang tot de Azure Storage biedt. Er zijn geen andere verschillen tussen deze twee gekoppelde services. Kies de gekoppelde service die aansluit bij uw behoeften. In de volgende secties vindt u meer informatie over deze twee gekoppelde services.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **AzureTable** heeft de volgende eigenschappen.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de Azure Table-Data Base-instantie waarnaar de gekoppelde service verwijst. |Ja. Wanneer een TableName zonder azureTableSourceQuery is opgegeven, worden alle records uit de tabel naar de bestemming gekopieerd. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoen, gekopieerd naar de bestemming. |

### <a name="schema-by-data-factory"></a>Schema door Data Factory
Voor gegevens archieven zonder schema, zoals Azure Table, leidt de Data Factory-service het schema op een van de volgende manieren af:

1. Als u de structuur van gegevens opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt deze structuur door de Data Factory-service als schema geaccepteerd. Als in dit geval een rij geen waarde voor een kolom bevat, wordt er een null-waarde voor gegeven.
2. Als u de structuur van gegevens niet opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt het schema door Data Factory afgeleid van de eerste rij in de gegevens. Als in dit geval de eerste rij niet het volledige schema bevat, worden bepaalde kolommen niet in het resultaat van de Kopieer bewerking gemist.

Daarom is het best practice voor gegevens bronnen met een schema om de structuur van de gegevens op te geven met behulp van de eigenschap **structure** .

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer gegevens sets en beleids regels zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de typeProperties-sectie van de activiteit, verschillen per type activiteit. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

**AzureTableSource** ondersteunt de volgende eigenschappen in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableSourceQuery |Gebruik de aangepaste query om gegevens te lezen. |Query reeks voor Azure Table. Zie de voor beelden in de volgende sectie. |Nee. Wanneer een TableName zonder azureTableSourceQuery is opgegeven, worden alle records uit de tabel naar de bestemming gekopieerd. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoen, gekopieerd naar de bestemming. |
| azureTableSourceIgnoreTableNotFound |Geef aan of de uitzonde ring van de tabel niet bestaat. |TRUE<br/>FALSE |Nee |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-voor beelden
Als de kolom van een Azure-tabel van het teken reeks type is:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Als de Azure-tabel kolom van het type datetime is:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** ondersteunt de volgende eigenschappen in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standaard waarde voor de partitie sleutel die door de Sink kan worden gebruikt. |Een teken reeks waarde. |Nee |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitie sleutels. Als u niets opgeeft, wordt AzureTableDefaultPartitionKeyValue gebruikt als partitie sleutel. |Een kolom naam. |Nee |
| azureTableRowKeyName |Geef de naam van de kolom op waarvan de kolom waarden worden gebruikt als rijdefinitie. Als u deze niet opgeeft, gebruikt u een GUID voor elke rij. |Een kolom naam. |Nee |
| azureTableInsertType |De modus om gegevens in de Azure-tabel in te voegen.<br/><br/>Met deze eigenschap bepaalt u of bestaande rijen in de uitvoer tabel met overeenkomende partitie-en rijlabels hun waarden vervangen of samengevoegd hebben. <br/><br/>Zie entiteit [invoegen of samen voegen](/rest/api/storageservices/Insert-Or-Merge-Entity) en onderwerpen over [entiteiten invoegen of vervangen](/rest/api/storageservices/Insert-Or-Replace-Entity) voor meer informatie over hoe deze instellingen (samen voegen en vervangen) werken. <br/><br> Deze instelling is van toepassing op het niveau van de rij, niet op het tabel niveau, en geen van beide opties verwijdert rijen in de uitvoer tabel die niet voor komen in de invoer. |samen voegen (standaard)<br/>vervangen |Nee |
| writeBatchSize |Hiermee worden gegevens in de Azure-tabel ingevoegd wanneer de writeBatchSize of writeBatchTimeout wordt bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Hiermee worden gegevens in de Azure-tabel ingevoegd wanneer de writeBatchSize of writeBatchTimeout wordt bereikt |tijdsbestek<br/><br/>Voor beeld: "00:20:00" (20 minuten) |Nee (standaard waarde voor standaard time-outwaarde voor Storage-client 90 sec) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Wijs een bron kolom toe aan een doel kolom met behulp van de JSON-eigenschap Translator voordat u de doel kolom kunt gebruiken als de azureTablePartitionKeyName.

In het volgende voor beeld wordt bron kolom DivisionID toegewezen aan de doel kolom: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
De DivisionID is opgegeven als de partitie sleutel.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-voor beelden
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kopieert van en naar Azure Table Storage en Azure Blob data base. Gegevens kunnen echter **rechtstreeks** vanuit een van de bronnen naar een van de ondersteunde sinks worden gekopieerd. Zie de sectie ondersteunde gegevens archieven en-indelingen in [gegevens verplaatsen met behulp van Kopieer activiteit](data-factory-data-movement-activities.md)voor meer informatie.

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Voor beeld: gegevens kopiëren van een Azure-tabel naar een Azure-Blob
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties) (gebruikt voor beide tabel & blob).
2. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureTable](#dataset-properties).
3. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van AzureTableSource en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens gekopieerd die deel uitmaken van de standaard partitie in een Azure-tabel naar een blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure Storage-service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory ondersteunt twee typen Azure Storage gekoppelde services: **opslag** en **azurestoragesas zijn**. Voor de eerste geeft u de connection string op die de account sleutel bevat en voor de latere versie, geeft u de Shared Access Signature (SAS)-URI op. Zie de sectie [gekoppelde services](#linked-service-properties) voor meer informatie.  

**Invoer gegevensset voor Azure-tabel:**

In het voor beeld wordt ervan uitgegaan dat u in azure Table een tabel ' MyTable ' hebt gemaakt.

Als u ' Extern ' instelt, informeert de Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopieer activiteit in een pijp lijn met AzureTableSource en BlobSink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **AzureTableSource** en het **sink** -type is ingesteld op **BlobSink**. De SQL-query die is opgegeven met de eigenschap **AzureTableSourceQuery** selecteert de gegevens van de standaard partitie elk uur om te kopiëren.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Voor beeld: gegevens kopiëren van Azure Blob naar Azure-tabel
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties) (gebruikt voor beide tabel & blob)
2. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureTable](#dataset-properties).
4. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [AzureTableSink](#copy-activity-properties).

In het voor beeld worden gegevens van een tijd reeks gekopieerd van een Azure-Blob naar een Azure-tabel per uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Azure-opslag (voor zowel Azure Table & blob) gekoppelde service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

Azure Data Factory ondersteunt twee typen Azure Storage gekoppelde services: **opslag** en **azurestoragesas zijn**. Voor de eerste geeft u de connection string op die de account sleutel bevat en voor de latere versie, geeft u de Shared Access Signature (SAS)-URI op. Zie de sectie [gekoppelde services](#linked-service-properties) voor meer informatie.

**Invoer gegevensset voor Azure Blob:**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte Year, month en Day van de begin tijd en de bestands naam maakt gebruik van het uur gedeelte van de begin tijd. met de instelling ' Extern ': ' waar ' wordt de Data Factory-service informeert dat de gegevensset extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

**Azure-tabel uitvoer gegevensset:**

In het voor beeld worden gegevens gekopieerd naar een tabel met de naam ' MyTable ' in azure Table. Maak een Azure-tabel met hetzelfde aantal kolommen als u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopieer activiteit in een pijp lijn met BlobSource en AzureTableSink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
## <a name="type-mapping-for-azure-table"></a>Type toewijzing voor Azure-tabel
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen.

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar & vanuit een Azure-tabel, worden de volgende toewijzingen die zijn [gedefinieerd door azure Table service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) vanuit Azure Table OData-typen gebruikt tot .net-type en vice versa.

| OData-gegevenstype | .NET-type | Details |
| --- | --- | --- |
| EDM. binary |byte [] |Een byte matrix van Maxi maal 64 KB. |
| Edm.Boolean |booleaans |Een booleaanse waarde. |
| EDM. DateTime |DateTime |Een waarde van 64 bits, uitgedrukt als Coordinated Universal Time (UTC). Het ondersteunde DateTime-bereik begint van 12:00 middernacht, 1 januari 1601 n. (C.E.), UTC. Het bereik eindigt op 31 december 9999. |
| Edm.Double |double |Een 64-bits drijvende-komma waarde. |
| EDM. GUID |Guid |Een 128-bits Globally Unique Identifier. |
| Edm.Int32 |Int32 |Een 32-bits geheel getal. |
| Edm.Int64 |Int64 |Een 64-bits geheel getal. |
| Edm.String |Tekenreeks |Een UTF-16-gecodeerde waarde. Teken reeks waarden kunnen Maxi maal 64 KB zijn. |

### <a name="type-conversion-sample"></a>Voor beeld van type conversie
Het volgende voor beeld is voor het kopiëren van gegevens van een Azure-Blob naar een Azure-tabel met type conversies.

Stel dat de BLOB-gegevensset in CSV-indeling is en drie kolommen bevat. Een van de twee is een datum/tijd-kolom met een aangepaste datum/tijd notatie met verkorte Franse namen voor de dag van de week.

Definieer de bron-gegevensset van de BLOB als volgt, samen met de type definities voor de kolommen.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
        },
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
Op basis van het type toewijzing van het OData-type van de Azure Table aan .NET-type, definieert u de tabel in azure Table met het volgende schema.

**Azure-tabel schema:**

| Kolomnaam | Type |
| --- | --- |
| userid |Edm.Int64 |
| naam |Edm.String |
| lastlogindate |EDM. DateTime |

Definieer vervolgens de Azure Table-gegevensset als volgt. U hoeft geen sectie ' Structure ' op te geven met het type informatie omdat de type-informatie al is opgegeven in het onderliggende gegevens archief.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

In dit geval typt Data Factory automatisch conversies, inclusief het veld datetime met de aangepaste datum/tijd-indeling met de ' fr-fr ' cultuur bij het verplaatsen van gegevens van BLOB naar Azure Table.

> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren, de [Kopieer activiteit prestaties & Tuning Guide](data-factory-copy-activity-performance.md).
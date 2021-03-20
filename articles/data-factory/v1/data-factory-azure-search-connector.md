---
title: Gegevens naar zoek index pushen met behulp van Data Factory
description: Meer informatie over het pushen van gegevens naar Azure Cognitive Search index met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0226ab75d53733b94a9ae5734b42b7340998759c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379264"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Gegevens pushen naar een Azure Cognitive Search-index met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-search-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-search.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure Cognitive Search-connector in v2](../connector-azure-search.md).

In dit artikel wordt beschreven hoe u de Kopieer activiteit gebruikt om gegevens uit een ondersteund brongegevens archief te pushen naar een Azure Cognitive Search-index. Ondersteunde brongegevens archieven worden weer gegeven in de kolom Bron van de tabel [ondersteunde bronnen en sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemene beschrijving biedt van gegevens verplaatsing met Kopieer activiteiten en ondersteunde combi Naties van gegevens archieven.

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Als u wilt toestaan dat Data Factory-service verbinding maakt met een on-premises gegevens archief, installeert u Data Management Gateway in uw on-premises omgeving. U kunt de gateway installeren op dezelfde computer die als host fungeert voor het brongegevens archief of op een afzonderlijke machine om te voor komen dat bronnen worden geconcurrerend met het gegevens archief.

Data Management Gateway maakt on-premises gegevens bronnen op een veilige en beheerde manier verbinding met Cloud Services. Zie [gegevens verplaatsen tussen on-premises en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit waarmee gegevens uit een brongegevens archief naar een zoek index worden gepusht met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beeld: gegevens kopiëren van SQL Server naar een index van Azure Cognitive Search](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens naar de zoek index.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een zoek index:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de gekoppelde Azure Cognitive Search-service.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet worden ingesteld op: **AzureSearch**. | Ja |
| url | URL voor de zoek service. | Ja |
| sleutel | De beheerders sleutel voor de zoek service. | Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevensset. De sectie **typeProperties** verschilt voor elk type gegevensset. De sectie typeProperties voor een gegevensset van het type **AzureSearchIndex** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet worden ingesteld op **AzureSearchIndex**.| Ja |
| indexName | De naam van de zoek index. Data Factory maakt de index niet. De index moet bestaan in azure Cognitive Search. | Ja |


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en verschillende beleids regels zijn beschikbaar voor alle typen activiteiten. Overwegende dat eigenschappen die beschikbaar zijn in de sectie typeProperties, afhankelijk zijn van elk type activiteit. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Als de Sink van het type **AzureSearchIndexSink** is, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Hiermee geeft u op of u wilt samen voegen of vervangen wanneer een document al aanwezig is in de index. Zie de [eigenschap WriteBehavior](#writebehavior-property).| Samen voegen (standaard)<br/>Uploaden| Nee |
| WriteBatchSize | Uploadt gegevens in de zoek index wanneer de buffer grootte writeBatchSize bereikt. Zie de [eigenschap WriteBatchSize](#writebatchsize-property) voor meer informatie. | 1 tot en met 1.000. De standaard waarde is 1000. | Nee |

### <a name="writebehavior-property"></a>Eigenschap WriteBehavior
AzureSearchSink upsert bij het schrijven van gegevens. Met andere woorden, wanneer u een document schrijft en de document sleutel al in de zoek index bestaat, werkt Azure Cognitive Search het bestaande document bij in plaats van een conflict uitzondering uit te voeren.

De AzureSearchSink biedt de volgende twee upsert-gedragingen (met behulp van AzureSearch SDK):

- **Samen voegen**: alle kolommen in het nieuwe document combi neren met de bestaande. Voor kolommen met een null-waarde in het nieuwe document, blijft de waarde in de bestaande.
- **Uploaden**: het nieuwe document vervangt de bestaande. Voor kolommen die niet in het nieuwe document zijn opgegeven, wordt de waarde ingesteld op NULL, ongeacht of er een andere waarde dan Null is in het bestaande document of niet.

Het standaard gedrag is **samen voegen**.

### <a name="writebatchsize-property"></a>Eigenschap WriteBatchSize
Azure Cognitive Search service ondersteunt het schrijven van documenten als een batch. Een batch kan 1 tot 1.000 acties bevatten. Een actie behandelt één document om de upload/samenvoeg bewerking uit te voeren.

### <a name="data-type-support"></a>Ondersteuning voor gegevens typen
In de volgende tabel wordt aangegeven of een Azure Cognitive Search-gegevens type wordt ondersteund.

| Azure Cognitive Search-gegevens type | Ondersteund in azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Tekenreeks | J |
| Int32 | J |
| Int64 | J |
| Dubbel | J |
| Booleaans | J |
| DataTimeOffset | J |
| Teken reeks matrix | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>JSON-voor beeld: gegevens kopiëren van SQL Server naar Azure Cognitive Search index

In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [AzureSearch](#linked-service-properties).
2. Een gekoppelde service van het type [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSearchIndex](#dataset-properties).
4. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) en [AzureSearchIndexSink](#copy-activity-properties).

In het voor beeld worden gegevens van de tijd reeks gekopieerd van een SQL Server-Data Base om de index per uur te doorzoeken. De JSON-eigenschappen die in dit voor beeld worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway op uw on-premises computer. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**Gekoppelde Azure Cognitive Search-service:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server gekoppelde service**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Invoer gegevensset SQL Server**

In het voor beeld wordt ervan uitgegaan dat u in SQL Server een tabel ' MyTable ' hebt gemaakt en een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens. U kunt een query uitvoeren op meerdere tabellen binnen dezelfde data base met één gegevensset, maar er moet één tabel worden gebruikt voor de tabel naam van de gegevensset typeProperty.

Als u ' Extern ' instelt, informeert Data Factory service dat de gegevensset zich buiten het data factory bevindt en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Uitvoer gegevensset Azure Cognitive Search:**

In het voor beeld worden gegevens gekopieerd naar een Azure Cognitive Search index met de naam **producten**. Data Factory maakt de index niet. Als u het voor beeld wilt testen, maakt u een index met deze naam. Maak de zoek index met hetzelfde aantal kolommen als in de invoer-gegevensset. Nieuwe vermeldingen worden elk uur toegevoegd aan de zoek index.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Kopieer activiteit in een pijp lijn met SQL-bron en Azure Cognitive Search index Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlSource** en het **sink** -type is ingesteld op **AzureSearchIndexSink**. Met de SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Als u gegevens uit een gegevens archief in de Cloud naar Azure Cognitive Search kopieert, `executionLocation` is de eigenschap vereist. In het volgende JSON-fragment wordt de wijziging in de Kopieer activiteit `typeProperties` als voor beeld weer gegeven. Controleer de sectie [gegevens kopiëren tussen Cloud gegevensopslags](data-factory-data-movement-activities.md#global) voor ondersteunde waarden en meer details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopiëren uit een Cloud bron
Als u gegevens uit een gegevens archief in de Cloud naar Azure Cognitive Search kopieert, `executionLocation` is de eigenschap vereist. In het volgende JSON-fragment wordt de wijziging in de Kopieer activiteit `typeProperties` als voor beeld weer gegeven. Controleer de sectie [gegevens kopiëren tussen Cloud gegevensopslags](data-factory-data-movement-activities.md#global) voor ondersteunde waarden en meer details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

U kunt ook kolommen van de bron-gegevensset toewijzen aan kolommen uit Sink-gegevensset in de definitie van de Kopieer activiteit. Zie [gegevensset-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [hand leiding Copy activity Performance and Tuning (Engelstalig](data-factory-copy-activity-performance.md) ) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) en verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* [Zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

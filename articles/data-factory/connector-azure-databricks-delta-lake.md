---
title: Gegevens kopiëren van en naar Azure Databricks Delta Lake
description: Informatie over het kopiëren van gegevens van en naar Azure Databricks Delta Lake met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: e32b93c669bffd382b1eb648111f9b8931b07eac
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221139"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Databricks Delta Lake met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar Azure Databricks Delta Lake te kopiëren. Het is gebaseerd op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md) artikel, waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Databricks Delta Lake connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md) tabel
- [Activiteit Lookup](control-flow-lookup-activity.md)

Over het algemeen ondersteunt Azure Data Factory Delta Lake met de volgende mogelijkheden om te voldoen aan uw verschillende behoeften.

- De Kopieer activiteit ondersteunt Azure Databricks Delta Lake connector om gegevens te kopiëren van een ondersteund brongegevens archief naar Azure Databricks Delta Lake-tabel en van de Delta Lake-tabel naar een ondersteunde Sink-gegevens opslag. De oplossing maakt gebruik van uw Databricks-cluster om de gegevens verplaatsing uit te voeren. Zie de sectie met de [vereisten](#prerequisites)voor meer informatie.
- [Toewijzing van gegevens stroom](concepts-data-flow-overview.md) ondersteunt algemene [Delta-indeling](format-delta.md) op Azure Storage als bron en Sink voor het lezen en schrijven van Delta bestanden voor code-Free ETL en wordt uitgevoerd op beheerde Azure Integration runtime.
- [Databricks-activiteiten](transform-data-databricks-notebook.md) bieden ondersteuning voor het organiseren van uw code gerichte ETL of machine learning werk belasting boven op Delta Lake.

## <a name="prerequisites"></a>Vereisten

Als u deze Azure Databricks Delta Lake connector wilt gebruiken, moet u een cluster instellen in Azure Databricks.

- Als u gegevens wilt kopiëren naar Delta Lake, wordt met de Kopieer activiteit Azure Databricks cluster aangeroepen om gegevens te lezen van een Azure Storage. Dit is de oorspronkelijke bron of een faserings gebied waarnaar Data Factory de bron gegevens in eerste exemplaar opslaat via een ingebouwde gefaseerde kopie. Meer informatie over [Delta Lake als de Sink](#delta-lake-as-sink).
- Als u gegevens wilt kopiëren van Delta Lake, worden met Kopieer activiteiten Azure Databricks cluster aangeroepen om gegevens te schrijven naar een Azure Storage. Dit is de oorspronkelijke sink of een faserings gebied waar Data Factory gegevens naar de uiteindelijke Sink gaat schrijven via de ingebouwde gestarte kopie. Meer informatie over [Delta Lake als bron](#delta-lake-as-source).

Het Databricks-cluster moet toegang hebben tot de Azure-Blob of het Azure Data Lake Storage Gen2-account, zowel de opslag container/het bestands systeem die wordt gebruikt voor bron/Sink/staging als het container/bestands systeem waar u de Delta Lake-tabellen wilt schrijven.

- Als u **Azure data Lake Storage Gen2** wilt gebruiken, kunt u een **Service-Principal** of toegangs sleutel voor een **opslag account** configureren op het Databricks-cluster als onderdeel van de Apache Spark configuratie. Volg de stappen in [Access direct met Service-Principal](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) of [toegang rechtstreeks met de toegangs sleutel voor het opslag account](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Als u **Azure Blob Storage** wilt gebruiken, kunt u een **toegangs sleutel voor een opslag account** of een **SAS-token** configureren op het Databricks-cluster als onderdeel van de configuratie van de Apache Spark. Volg de stappen in [toegang tot Azure Blob-opslag met behulp van de RDD-API](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Als het cluster dat u hebt geconfigureerd, tijdens het uitvoeren van de Kopieer activiteit is beëindigd, wordt het door Data Factory automatisch gestart. Als u pijp lijn ontwerpt met Data Factory gebruikers interface voor bewerkingen als voor beeld van gegevens, moet u een live cluster hebben, Data Factory het cluster niet namens u kunt starten.

#### <a name="specify-the-cluster-configuration"></a>De cluster configuratie opgeven

1. Selecteer in de vervolg keuzelijst **cluster modus** de optie **standaard**.

2. Selecteer in de vervolg keuzelijst **Databricks runtime versie** een Databricks runtime-versie.

3. Schakel [automatisch optimaliseren](/azure/databricks/delta/optimizations/auto-optimize) in door de volgende eigenschappen toe te voegen aan uw [Spark-configuratie](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Configureer uw cluster afhankelijk van de behoeften van uw integratie en schaal.

Zie [clusters configureren](/azure/databricks/clusters/configure)voor meer informatie over de cluster configuratie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een Azure Databricks Delta Lake connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een aan Azure Databricks Delta Lake gekoppelde service.

| Eigenschap    | Beschrijving                                                  | Vereist |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | De eigenschap type moet worden ingesteld op **AzureDatabricksDeltaLake**. | Yes      |
| domein      | Geef de URL van de Azure Databricks werk ruimte op, bijvoorbeeld `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Geef de cluster-ID op van een bestaand cluster. Dit moet een al gemaakt interactief cluster zijn. <br>U vindt de cluster-ID van een interactief cluster op Databricks werkruimte-> clusters-> interactieve cluster naam-> configuratie->-Tags. [Meer informatie](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Er is een toegangs token vereist om Data Factory te verifiëren bij Azure Databricks. Het toegangs token moet worden gegenereerd op basis van de databricks-werk ruimte. Meer gedetailleerde stappen om het toegangs token te vinden, vindt u [hier](/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | De [Integration runtime](concepts-integration-runtime.md) die wordt gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration runtime gebruikt. | No       |

**Voorbeeld:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

De volgende eigenschappen worden ondersteund voor de Azure Databricks Delta Lake-gegevensset.

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De eigenschap type van de DataSet moet worden ingesteld op **AzureDatabricksDeltaLakeDataset**. | Yes                         |
| database | De naam van de data base. |Nee voor bron, ja voor Sink  |
| tabel | De naam van de Delta tabel. |Nee voor bron, ja voor Sink  |

**Voorbeeld:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Databricks Delta Lake-bron en Sink.

### <a name="delta-lake-as-source"></a>Delta Lake als bron

Als u gegevens wilt kopiëren uit Azure Databricks Delta Lake, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AzureDatabricksDeltaLakeSource**. | Yes      |
| query          | Geef de SQL-query op om gegevens te lezen. Volg het onderstaande patroon voor het besturings element tijd reizen:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | No       |
| exportSettings | Geavanceerde instellingen voor het ophalen van gegevens uit de Delta tabel. | No       |
| ***Onder `exportSettings` :** _ |  |  |
| type | Het type opdracht voor exporteren, ingesteld op _ * AzureDatabricksDeltaLakeExportCommand * *. | Yes |
| Notatie | Format teer het datum type in een teken reeks met een datum notatie. Aangepaste datum notaties volgen de notaties op [DateTime-patroon](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Als u niets opgeeft, wordt de standaard waarde gebruikt `yyyy-MM-dd` . | No |
| timestampFormat | Notatie van tijds tempel type op teken reeks met een notatie van een tijds tempel. Aangepaste datum notaties volgen de notaties op [DateTime-patroon](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Als u niets opgeeft, wordt de standaard waarde gebruikt `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-from-delta-lake"></a>Directe kopie van Delta Lake

Als uw Sink-gegevens archief en-indeling voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van Azure Databricks Delta tabel naar sink. Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de volgende criteria wordt voldaan:

- De **gekoppelde Sink-service** is [Azure Blob-opslag](connector-azure-blob-storage.md) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md). De account referenties moeten vooraf worden geconfigureerd in Azure Databricks cluster configuratie, meer informatie over de [vereisten](#prerequisites).

- De **sink-gegevens indeling** is **Parquet**, **tekst met scheidings tekens** of **Avro** met de volgende configuraties en verwijst naar een map in plaats van naar een bestand.

    - Voor de indeling **Parquet** is de compressie-codec **geen**, **Snappy** of **gzip**.
    - Voor **tekst indeling met scheidings tekens** :
        - `rowDelimiter` is een wille keurig teken.
        - `compression` kan **geen**, **bzip2**, **gzip** zijn.
        - `encodingName` UTF-7 wordt niet ondersteund.
    - Voor de indeling **Avro** is de compressie-codec **geen**, **Deflate** of **Snappy**.

- In de bron van de Kopieer activiteit `additionalColumns` is niet opgegeven.
- Als u gegevens naar tekst met scheidings tekens kopieert, moet u de Sink voor kopieer activiteiten `fileExtension` ". csv" hebben.
- In de toewijzing van de Kopieer activiteit is type conversie niet ingeschakeld.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Gefaseerde kopie van Delta Lake

Als uw Sink-gegevens archief of-indeling niet overeenkomt met de criteria voor direct kopiëren, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in met behulp van een tussentijds Azure Storage-exemplaar. De functie voor gefaseerd kopiëren biedt u ook een betere door voer. Data Factory exporteert gegevens van Azure Databricks Delta Lake naar staging-opslag, kopieert vervolgens de gegevens die moeten worden gesinkd en worden de tijdelijke gegevens van de faserings opslag opgeschoond. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor informatie over het kopiëren van gegevens met behulp van fase ring.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2 gekoppelde service](connector-azure-data-lake-storage.md#linked-service-properties) die verwijst naar het opslag account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in de Kopieer activiteit.

>[!NOTE]
>De referentie voor het faserings opslag account moet vooraf worden geconfigureerd in Azure Databricks cluster configuratie, meer informatie over de [vereisten](#prerequisites).

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake as-Sink

Als u gegevens wilt kopiëren naar Azure Databricks Delta Lake, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | De eigenschap type van de Sink van de Kopieer activiteit is ingesteld op **AzureDatabricksDeltaLakeSink**. | Yes      |
| preCopyScript | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit die moet worden uitgevoerd voordat gegevens worden geschreven in de Databricks-Delta tabel in elke run. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen of een afgekapte tabel of vacuüm instructie toe te voegen. | No       |
| importSettings | Geavanceerde instellingen voor het schrijven van gegevens in de Delta tabel. | No |
| **_Onder `importSettings` :_* _ |                                                              |  |
| type | Het type import opdracht, ingesteld op _ * AzureDatabricksDeltaLakeImportCommand * *. | Yes |
| Notatie | Teken reeks tot datum type opmaken met een datum notatie. Aangepaste datum notaties volgen de notaties op [DateTime-patroon](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Als u niets opgeeft, wordt de standaard waarde gebruikt `yyyy-MM-dd` . | No |
| timestampFormat | Teken reeks opmaken naar tijds tempel type met een notatie van een tijds tempel. Aangepaste datum notaties volgen de notaties op [DateTime-patroon](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Als u niets opgeeft, wordt de standaard waarde gebruikt `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-to-delta-lake"></a>Directe kopie naar Delta Lake

Als uw bron gegevens opslag en-indeling voldoen aan de criteria die in deze sectie worden beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van de bron naar Azure Databricks Delta Lake. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de volgende criteria wordt voldaan:

- De **gekoppelde bron service** is [Azure Blob-opslag](connector-azure-blob-storage.md) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md). De account referenties moeten vooraf worden geconfigureerd in Azure Databricks cluster configuratie, meer informatie over de [vereisten](#prerequisites).

- De **indeling van de bron gegevens** is **Parquet**, **tekst met scheidings tekens** of **Avro** met de volgende configuraties en verwijst naar een map in plaats van een bestand.

    - Voor de indeling **Parquet** is de compressie-codec **geen**, **Snappy** of **gzip**.
    - Voor **tekst indeling met scheidings tekens** :
        - `rowDelimiter` is standaard of één teken.
        - `compression` kan **geen**, **bzip2**, **gzip** zijn.
        - `encodingName` UTF-7 wordt niet ondersteund.
    - Voor de indeling **Avro** is de compressie-codec **geen**, **Deflate** of **Snappy**.

- In de bron van de Kopieer activiteit: 

    - `wildcardFileName` bevat alleen een Joker teken `*` , maar niet `?` , en `wildcardFolderName` is niet opgegeven.
    - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` en `enablePartitionDiscovery` zijn niet opgegeven.
    - `additionalColumns` is niet opgegeven.

- In de toewijzing van de Kopieer activiteit is type conversie niet ingeschakeld.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Gefaseerde kopie naar Delta Lake

Als uw brongegevens archief of-indeling niet overeenkomt met de criteria voor direct kopiëren, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in met behulp van een tussentijds Azure Storage-exemplaar. De functie voor gefaseerd kopiëren biedt u ook een betere door voer. Data Factory worden de gegevens automatisch geconverteerd om te voldoen aan de vereisten voor gegevens formaat in faserings opslag en vervolgens gegevens in Delta Lake te laden. Ten slotte worden de tijdelijke gegevens uit de opslag opgeschoond. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor meer informatie over het kopiëren van gegevens met behulp van staging.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2 gekoppelde service](connector-azure-data-lake-storage.md#linked-service-properties) die verwijst naar het opslag account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in de Kopieer activiteit.

>[!NOTE]
>De referentie voor het faserings opslag account moet vooraf worden geconfigureerd in Azure Databricks cluster configuratie, meer informatie over de [vereisten](#prerequisites).

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Bewaking

Azure Data Factory biedt dezelfde functionaliteit voor het [bewaken van de Kopieer activiteit](copy-activity-monitoring.md) als andere connectors. Omdat het laden van gegevens van/naar Delta Lake wordt uitgevoerd op uw Azure Databricks cluster, kunt u bovendien [gedetailleerde cluster logboeken weer geven](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) en de [prestaties bewaken](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie [lookup activity](control-flow-lookup-activity.md)(Engelstalig) voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.

---
title: Gegevens kopiëren en transformeren in sneeuw vlokken
description: Meer informatie over het kopiëren en transformeren van gegevens in sneeuw met behulp van Data Factory.
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
ms.openlocfilehash: c0d0e3154360d787bfc2072c5ae1fe878fa1d138
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96003651"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in sneeuw met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar sneeuw te kopiëren en gegevens stroom te gebruiken voor het transformeren van gegevens in sneeuw vlokken. Zie het [artikel Inleiding](introduction.md)voor meer informatie over Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze sneeuw connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md) tabel
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

Deze sneeuw connector ondersteunt de volgende functies voor de Kopieer activiteit:

- Kopieer gegevens van sneeuw die gebruikmaken van de kopie van het sneeuw punt in de opdracht [[locatie]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) om de beste prestaties te krijgen.
- Kopieer gegevens naar sneeuw die gebruikmaken van de kopie van sneeuw in de opdracht [[Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) om de beste prestaties te krijgen. Het ondersteunt sneeuw op Azure. 

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een sneeuw-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een service met sneeuw-koppeling.

| Eigenschap         | Beschrijving                                                  | Vereist |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | De eigenschap type moet worden ingesteld op **sneeuw vlokken**.              | Ja      |
| connectionString | Hiermee geeft u de gegevens op die nodig zijn om verbinding te maken met het sneeuw-exemplaar. U kunt ervoor kiezen om het wacht woord of de hele connection string in Azure Key Vault in te stellen. Raadpleeg de voor beelden onder de tabel, evenals de [archief referenties in azure Key Vault](store-credentials-in-key-vault.md) artikel, voor meer informatie.<br><br>Enkele typische instellingen:<br>- **Account naam:** De  [volledige account naam](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) van uw sneeuw-account (met inbegrip van aanvullende segmenten die de regio en het Cloud platform identificeren), bijvoorbeeld xy12345. Oost-US-2. Azure.<br/>- **Gebruikers naam:** De aanmeldings naam van de gebruiker voor de verbinding.<br>- **Wacht woord:** Het wacht woord voor de gebruiker.<br>- **Data Base:** De standaard database die moet worden gebruikt wanneer verbinding wordt gemaakt. Dit moet een bestaande Data Base zijn waarvoor de opgegeven rol bevoegdheden heeft.<br>- **Warehouse:** Het virtuele magazijn dat moet worden gebruikt als de verbinding tot stand is gebracht. Dit moet een bestaand magazijn zijn waarvoor de opgegeven rol bevoegdheden heeft.<br>- **Rol:** De standaard functie voor toegangs beheer die in de sneeuw-sessie moet worden gebruikt. De opgegeven rol moet een bestaande rol zijn die al is toegewezen aan de opgegeven gebruiker. De standaard functie is openbaar. | Yes      |
| connectVia       | De [Integration runtime](concepts-integration-runtime.md) die wordt gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration runtime gebruikt. | No       |

**Voorbeeld:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Wacht woord in Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

De volgende eigenschappen worden ondersteund voor de gegevensset van sneeuw vlokken.

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De eigenschap type van de DataSet moet worden ingesteld op **SnowflakeTable**. | Yes                         |
| schema | De naam van het schema. Opmerking: de schema naam is hoofdletter gevoelig in ADF. |Nee voor bron, ja voor Sink  |
| table | De naam van de tabel/weer gave. Opmerking de naam van de tabel is hoofdletter gevoelig in ADF. |Nee voor bron, ja voor Sink  |

**Voorbeeld:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de sneeuw-bron en Sink.

### <a name="snowflake-as-the-source"></a>Sneeuw als bron

Sneeuw connector maakt gebruik van de Kopieer opdracht van sneeuw naar [[locatie]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) om de beste prestaties te krijgen.

Als de Sink-gegevens opslag en-indeling systeem eigen worden ondersteund door de sneeuw COPY-opdracht, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van sneeuw naar sink. Zie voor meer informatie [direct kopiëren van sneeuw](#direct-copy-from-snowflake). Gebruik anders de ingebouwde, [gefaseerde kopie van sneeuw](#staged-copy-from-snowflake).

Als u gegevens van sneeuw vlokken wilt kopiëren, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SnowflakeSource**. | Yes      |
| query          | Hiermee geeft u de SQL-query op voor het lezen van gegevens van sneeuw. Als de namen van het schema, de tabel en de kolommen een kleine letter bevatten, wordt de object-id in query als aanhalings teken genoteerd, bijvoorbeeld `select * from "schema"."myTable"` .<br>Het uitvoeren van een opgeslagen procedure wordt niet ondersteund. | No       |
| exportSettings | Geavanceerde instellingen voor het ophalen van gegevens uit sneeuw. U kunt de bestanden configureren die worden ondersteund door de opdracht kopiëren naar, die Data Factory door gegeven wanneer u de instructie aanroept. | No       |
| ***Onder `exportSettings` :** _ |  |  |
| type | Het type opdracht voor exporteren, ingesteld op _ * SnowflakeExportCopyCommand * *. | Yes |
| additionalCopyOptions | Aanvullende kopieer opties, gegeven als een woorden lijst met sleutel-waardeparen. Voor beelden: MAX_FILE_SIZE, OVERSCHRIJVEN. Zie Opties voor het [kopiëren van sneeuw vlokken](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)voor meer informatie. | No |
| additionalFormatOptions | Aanvullende opties voor bestands indelingen die als een woorden lijst met sleutel-waardeparen worden meegeleverd. Voor beelden: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Zie [Opties voor type sneeuw-indeling](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)voor meer informatie. | No |

#### <a name="direct-copy-from-snowflake"></a>Directe kopie van sneeuw

Als uw Sink-gegevens archief en-indeling voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van sneeuw naar sink. Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de volgende criteria wordt voldaan:

- De **gekoppelde Sink-service** is [**Azure Blob-opslag**](connector-azure-blob-storage.md) met verificatie van de **Shared Access-hand tekening** .

- De **sink-gegevens indeling** is **Parquet**, **tekst met scheidings tekens** of **JSON** met de volgende configuraties:

    - Voor de indeling **Parquet** is de compressie-codec **geen**, **Snappy** of **lzo**.
    - Voor **tekst indeling met scheidings tekens** :
        - `rowDelimiter` is **\r\n** of een wille keurig teken.
        - `compression` kan **geen compressie**, **gzip**, **bzip2** of **Deflate** zijn.
        - `encodingName` is standaard ingesteld op **UTF-8**.
        - `quoteChar` is **dubbele aanhalings** tekens, **enkele aanhalings** tekens of **lege teken reeks** (geen aanhalings teken).
    - Voor de **JSON** -indeling biedt direct kopiëren alleen ondersteuning voor het geval dat de bron sneeuw Table of het query resultaat slechts één kolom heeft en het gegevens type van deze kolom **Variant**, **object** of **matrix** is.
        - `compression` kan **geen compressie**, **gzip**, **bzip2** of **Deflate** zijn.
        - `encodingName` is standaard ingesteld op **UTF-8**.
        - `filePattern` de Sink voor kopieer activiteiten is standaard ingesteld op **setOfObjects**.

- De bron van de Kopieer activiteit `additionalColumns` is niet opgegeven.
- Er is geen kolom toewijzing opgegeven.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Gefaseerde kopie van sneeuw

Als uw Sink-gegevens archief of-indeling niet systeem eigen compatibel is met de opdracht sneeuw COPY, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in met een tussentijds Azure Blob Storage-exemplaar. De functie voor gefaseerd kopiëren biedt u ook een betere door voer. Data Factory exporteert gegevens van sneeuw naar staging-opslag, kopieert de gegevens naar Sink en worden de tijdelijke gegevens uit de staging-opslag opgeschoond. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor informatie over het kopiëren van gegevens met behulp van fase ring.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in de Kopieer activiteit.

> [!NOTE]
> De gekoppelde Azure Blob Storage-service voor fase ring moet gebruikmaken van verificatie van de hand tekening voor gedeelde toegang, zoals vereist is door de Kopieer opdracht van sneeuw vlokken. 

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Sneeuw vlokken als Sink

Sneeuw connector gebruikt de Kopieer opdracht van sneeuw [in [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) om de beste prestaties te krijgen. Het biedt ondersteuning voor het schrijven van gegevens naar sneeuw vlokken op Azure.

Als het brongegevens archief en de indeling systeem eigen worden ondersteund door de sneeuw COPY-opdracht, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van bron naar sneeuw vlokken. Zie [direct kopiëren naar sneeuw vlokken](#direct-copy-to-snowflake)voor meer informatie. Gebruik anders de ingebouwde [gefaseerde kopie naar sneeuw vlokken](#staged-copy-to-snowflake).

Als u gegevens wilt kopiëren naar sneeuw vlokken, worden de volgende eigenschappen ondersteund in het gedeelte **sink** voor kopieer activiteit.

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De eigenschap type van de Sink van de Kopieer activiteit is ingesteld op **SnowflakeSink**. | Yes                                           |
| preCopyScript     | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar sneeuw worden geschreven. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | No                                            |
| importSettings | Geavanceerde instellingen voor het schrijven van gegevens naar sneeuw. U kunt de bestanden configureren die worden ondersteund door de opdracht kopiëren naar, die Data Factory door gegeven wanneer u de instructie aanroept. | No |
| **_Onder `importSettings` :_* _ |                                                              |  |
| type | Het type import opdracht, ingesteld op _ * SnowflakeImportCopyCommand * *. | Yes |
| additionalCopyOptions | Aanvullende kopieer opties, gegeven als een woorden lijst met sleutel-waardeparen. Voor beelden: ON_ERROR, FORCEren, LOAD_UNCERTAIN_FILES. Zie Opties voor het [kopiëren van sneeuw vlokken](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)voor meer informatie. | No |
| additionalFormatOptions | Aanvullende opties voor de bestands indeling die worden meegeleverd met de Kopieer opdracht, als een woorden lijst met sleutel-waardeparen. Voor beelden: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Zie [Opties voor type sneeuw-indeling](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)voor meer informatie. | No |

#### <a name="direct-copy-to-snowflake"></a>Directe kopie naar sneeuw vlokken

Als uw bron gegevens opslag en-indeling voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van bron naar sneeuw vlokken. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de volgende criteria wordt voldaan:

- De **gekoppelde bron service** is [**Azure Blob-opslag**](connector-azure-blob-storage.md) met verificatie van de **hand tekening voor gedeelde toegang** .

- De **indeling van de bron gegevens** is **Parquet**, **tekst met scheidings tekens** of **JSON** met de volgende configuraties:

    - Voor de indeling **Parquet** is de compressie-codec **geen** of **Snappy**.

    - Voor **tekst indeling met scheidings tekens** :
        - `rowDelimiter` is **\r\n** of een wille keurig teken. Als het scheidings teken van de rij niet ' \r\n ' is, `firstRowAsHeader` moet **Onwaar** zijn en `skipLineCount` niet opgegeven.
        - `compression` kan **geen compressie**, **gzip**, **bzip2** of **Deflate** zijn.
        - `encodingName` is standaard ingesteld op ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' BIG5 ', ' EUC-JP ', ' EUC-KR ', ' GB18030 ', ' ISO-2022-JP ', ' ISO-2022-KR ', ' ISO-8859-1 "," ISO-8859-2 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 ".
        - `quoteChar` is **dubbele aanhalings** tekens, **enkele aanhalings** tekens of **lege teken reeks** (geen aanhalings teken).
    - Voor de **JSON** -indeling ondersteunt direct kopiëren alleen het geval dat de tabel met de Sink-sneeuw vlokken slechts één kolom heeft en het gegevens type van deze kolom **Variant**, **object** of **matrix** is.
        - `compression` kan **geen compressie**, **gzip**, **bzip2** of **Deflate** zijn.
        - `encodingName` is standaard ingesteld op **UTF-8**.
        - Er is geen kolom toewijzing opgegeven.

- In de bron van de Kopieer activiteit: 

   -  `additionalColumns` is niet opgegeven.
   - Als uw bron een map is, `recursive` wordt ingesteld op waar.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` en `enablePartitionDiscovery` zijn niet opgegeven.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Gefaseerde kopie naar sneeuw vlokken

Als uw brongegevens archief of-indeling niet systeem eigen compatibel is met de opdracht sneeuw COPY, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in met een tussentijds Azure Blob Storage-exemplaar. De functie voor gefaseerd kopiëren biedt u ook een betere door voer. Data Factory converteert de gegevens automatisch om te voldoen aan de vereisten voor gegevens formaat van sneeuw vlokken. Vervolgens wordt de Kopieer opdracht aangeroepen voor het laden van gegevens naar sneeuw vlokken. Ten slotte ruimt de app de tijdelijke gegevens op uit de Blob-opslag. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor meer informatie over het kopiëren van gegevens met behulp van staging.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in de Kopieer activiteit.

> [!NOTE]
> De gekoppelde Azure Blob Storage-service voor de fase ring moet gebruikmaken van verificatie van de hand tekening voor gedeelde toegang, zoals vereist is door de Kopieer opdracht van sneeuw vlokken.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u lezen uit en schrijven naar tabellen in sneeuw vlokken. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen. U kunt kiezen voor het gebruik van een sneeuw-gegevensset of een [inline-gegevensset](data-flow-source.md#inline-datasets) als bron-en Sink-type.

### <a name="source-transformation"></a>Bron transformatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door sneeuw vlokken. U kunt deze eigenschappen bewerken op het tabblad **bron opties** . De connector gebruikt interne sneeuw- [gegevens overdracht](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer).

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabel | Als u tabel als invoer selecteert, haalt de gegevens stroom alle gegevens op uit de tabel die is opgegeven in de data-gegevensset met sneeuw vlokken of in de bron opties bij gebruik van inline DataSet. | Nee | Tekenreeks | *(alleen voor inline-gegevensset)*<br>tableName<br>schemaName |
| Query | Als u query als invoer selecteert, voert u een query in om gegevens op te halen uit sneeuw vlokken. Deze instelling heeft voor rang op elke tabel die u in dataset hebt gekozen.<br>Als de namen van het schema, de tabel en de kolommen een kleine letter bevatten, wordt de object-id in query als aanhalings teken genoteerd, bijvoorbeeld `select * from "schema"."myTable"` . | Nee | Tekenreeks | query |

#### <a name="snowflake-source-script-examples"></a>Voor beelden van het bron script sneeuw vlokken

Wanneer u de waarde sneeuw-gegevensset als bron type gebruikt, is het gekoppelde gegevensstroom script:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Als u inline dataset gebruikt, is het bijbehorende gegevens stroom script:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Sink-trans formatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door sneeuw sink. U kunt deze eigenschappen bewerken op het tabblad **instellingen** . Bij gebruik van een inline-gegevensset worden extra instellingen weer geven, die hetzelfde zijn als de eigenschappen die worden beschreven in de sectie [Eigenschappen van gegevensset](#dataset-properties) . De connector gebruikt interne sneeuw- [gegevens overdracht](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer).

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update methode | Geef op welke bewerkingen zijn toegestaan op uw sneeuw-bestemming.<br>Als u rijen wilt bijwerken, upsert of verwijderen, is een [ALTER Row Transform](data-flow-alter-row.md) vereist om rijen voor die acties te labelen. | Yes | `true` of `false` | verwijderd <br/>invoegen <br/>bij te werken <br/>upsertable |
| Sleutel kolommen | Voor updates, upsert en verwijderen moet een sleutel kolom of-kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd. | No | Matrix | keys |
| Tabel actie | Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.<br>- **Geen**: er wordt geen actie uitgevoerd voor de tabel.<br>- **Opnieuw maken**: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.<br>- **Afkappen**: alle rijen uit de doel tabel worden verwijderd. | No | `true` of `false` | opnieuw maken<br/>afkappen |

#### <a name="snowflake-sink-script-examples"></a>Voor beelden van sneeuw-Sink-scripts

Wanneer u een sneeuw-gegevensset als Sink-type gebruikt, is het gekoppelde gegevensstroom script:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Als u inline dataset gebruikt, is het bijbehorende gegevens stroom script:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie [lookup activity](control-flow-lookup-activity.md)(Engelstalig) voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.

---
title: Gegevens kopiëren van en naar Azure Database for MySQL
description: Informatie over het kopiëren van gegevens van en naar Azure Database for MySQL met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: 4b6cc13343a7ca7af1bba84ed84d2ce5c8387b1f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381372"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Database for MySQL met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Azure Database for MySQL te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

Deze connector is speciaal voor [Azure database for MySQL service](../mysql/overview.md). Als u gegevens wilt kopiëren van een algemene MySQL-data base die zich on-premises of in de Cloud bevindt, gebruikt u [mysql-connector](connector-mysql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for MySQL-Connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Azure Database for MySQL kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar Azure Database for MySQL. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Database for MySQL-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Azure Database for MySQL gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureMySql** | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Database for MySQL exemplaar. <br/> U kunt ook wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Yes |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

Een typische connection string is `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Meer eigenschappen die u per case kunt instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| SSLMode | Met deze optie geeft u op of het stuur programma TLS-versleuteling en verificatie gebruikt bij het verbinden met MySQL. Bijvoorbeeld `SSLMode=<0/1/2/3/4>`| UITGESCHAKELD (0)/voor keur (1) **(standaard)** /vereist (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Met deze optie geeft u aan of u een CA-certificaat wilt gebruiken uit het archief van het systeem vertrouwen of van een opgegeven PEM-bestand. Bijvoorbeeld `UseSystemTrustStore=<0/1>;`| Ingeschakeld (1)/uitgeschakeld (0) **(standaard)** | No |

**Voorbeeld:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: wacht woord opslaan in Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door Azure Database for MySQL DataSet worden ondersteund.

Als u gegevens wilt kopiëren uit Azure Database for MySQL, stelt u de eigenschap type van de gegevensset in op **AzureMySqlTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureMySqlTable** | Yes |
| tableName | De naam van de tabel in de MySQL-data base. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Azure Database for MySQL bron en Sink.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL als bron

Als u gegevens wilt kopiëren uit Azure Database for MySQL, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **AzureMySqlSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |
| queryCommandTimeout | De wacht tijd voordat de query aanvraag een time-out heeft. De standaard waarde is 120 minuten (02:00:00) | No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL als Sink

Als u gegevens wilt kopiëren naar Azure Database for MySQL, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op: **AzureMySqlSink** | Yes |
| preCopyScript | Geef een SQL-query op voor de Kopieer activiteit die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar Azure Database for MySQL worden geschreven. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | No |
| writeBatchSize | Hiermee worden gegevens in de Azure Database for MySQL tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt.<br>De toegestane waarde is een geheel getal dat het aantal rijen vertegenwoordigt. | Nee (de standaard waarde is 10.000) |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br>Toegestane waarden zijn time span. Een voor beeld is 00:30:00 (30 minuten). | Nee (de standaard waarde is 00:00:30) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Toewijzing van gegevens type voor Azure Database for MySQL

Bij het kopiëren van gegevens uit Azure Database for MySQL worden de volgende toewijzingen gebruikt van MySQL-gegevens typen om te Azure Data Factory interim-gegevens typen. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| Azure Database for MySQL gegevens type | Data Factory-gegevens type interim |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

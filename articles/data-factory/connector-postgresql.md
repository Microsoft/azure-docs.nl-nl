---
title: Gegevens kopiëren van PostgreSQL met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit PostgreSQL naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jingwang
ms.openlocfilehash: c0d8783c4b07c8c87e57ccd9a5d65983825347c9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384092"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Gegevens kopiëren van PostgreSQL met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-postgresql-connector.md)
> * [Huidige versie](connector-postgresql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een PostgreSQL-data base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze PostgreSQL-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de PostgreSQL-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Met name deze PostgreSQL-connector ondersteunt PostgreSQL **versie 7,4 en hoger**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd PostgreSQL-stuur programma vanaf versie 3,7. Daarom hoeft u niet hand matig een stuur programma te installeren.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor PostgreSQL-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor PostgreSQL gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **postgresql** | Ja |
| connectionString | Een ODBC-connection string om verbinding te maken met Azure Database for PostgreSQL. <br/>U kunt ook wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Yes |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

Een typische connection string is `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Meer eigenschappen die u per case kunt instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| De methode die het stuur programma gebruikt voor het versleutelen van gegevens die worden verzonden tussen het stuur programma en de database server. Bijvoorbeeld,  `EncryptionMethod=<0/1/6>;`| 0 (geen versleuteling) **(standaard)** /1 (SSL)/6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Hiermee wordt bepaald of het stuur programma het certificaat valideert dat door de database server wordt verzonden wanneer SSL-versleuteling is ingeschakeld (versleutelings methode = 1). Bijvoorbeeld,  `ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** /1 (ingeschakeld) | No |

**Voorbeeld:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;",
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

Als u PostgreSQL gekoppelde service met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

**Vorige nettolading:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de PostgreSQL-gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens uit PostgreSQL:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **PostgreSqlTable** | Yes |
| schema | De naam van het schema. |Nee (als "query" in activiteit bron is opgegeven)  |
| tabel | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe werk belasting. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door PostgreSQL-bron worden ondersteund.

### <a name="postgresql-as-source"></a>PostgreSQL als bron

Als u gegevens wilt kopiëren uit PostgreSQL, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **PostgreSqlSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

> [!NOTE]
> Schema-en tabel namen zijn hoofdletter gevoelig. Plaats deze in `""` (dubbele aanhalings tekens) in de query.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

---
title: Gegevens kopiëren van en naar Azure Database for PostgreSQL
description: Informatie over het kopiëren van gegevens van en naar Azure Database for PostgreSQL met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296502"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Database for PostgreSQL met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de functie Copy activity in Azure Data Factory gebruikt om gegevens te kopiëren van Azure Database for PostgreSQL. Het is gebaseerd op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md) artikel, waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

Deze connector is speciaal bedoeld voor de [Azure database for PostgreSQL-service](../postgresql/overview.md). Als u gegevens wilt kopiëren vanuit een algemene PostgreSQL-data base die zich on-premises of in de Cloud bevindt, gebruikt u de [postgresql-connector](connector-postgresql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for PostgreSQL-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Azure Database for PostgreSQL kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar Azure Database for PostgreSQL. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. Daarom hoeft u geen stuur Programma's hand matig te installeren om deze connector te gebruiken.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bieden informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Database for PostgreSQL-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Azure Database for PostgreSQL gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzurePostgreSql**. | Ja |
| connectionString | Een ODBC-connection string om verbinding te maken met Azure Database for PostgreSQL.<br/>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) voor meer informatie. | Yes |
| connectVia | Deze eigenschap vertegenwoordigt de [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

Een typische connection string is `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Hier vindt u meer eigenschappen die u per case kunt instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| De methode die het stuur programma gebruikt voor het versleutelen van gegevens die worden verzonden tussen het stuur programma en de database server. Bijvoorbeeld:  `EncryptionMethod=<0/1/6>;`| 0 (geen versleuteling) **(standaard)** /1 (SSL)/6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Hiermee wordt bepaald of het stuur programma het certificaat valideert dat door de database server wordt verzonden wanneer SSL-versleuteling is ingeschakeld (versleutelings methode = 1). Bijvoorbeeld:  `ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** /1 (ingeschakeld) | No |

**Voor beeld**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Voor beeld**:

**_Wacht woord opslaan in azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets in azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die Azure Database for PostgreSQL in gegevens sets ondersteunt.

Als u gegevens wilt kopiëren uit Azure Database for PostgreSQL, stelt u de eigenschap type van de gegevensset in op _ * AzurePostgreSqlTable * *. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzurePostgreSqlTable** | Yes |
| tableName | Naam van de tabel | Nee (als "query" in activiteit bron is opgegeven) |

**Voor beeld**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen en activiteiten in azure Data Factory](concepts-pipelines-activities.md). Deze sectie bevat een lijst met eigenschappen die worden ondersteund door een Azure Database for PostgreSQL bron.

### <a name="azure-database-for-postgresql-as-source"></a>Azure data base for PostgreSql als bron

Als u gegevens wilt kopiëren uit Azure Database for PostgreSQL, stelt u het bron type in de Kopieer activiteit in op **AzurePostgreSqlSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AzurePostgreSqlSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `SELECT * FROM mytable` of `SELECT * FROM "MyTable"` . Opmerking in PostgreSQL wordt de naam van de entiteit beschouwd als niet-hoofdletter gevoelig als deze niet in een aanhalings teken wordt vermeld. | Nee (als de eigenschap TableName in de gegevensset is opgegeven) |

**Voor beeld**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL als Sink

Als u gegevens wilt kopiëren naar Azure Database for PostgreSQL, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **AzurePostgreSQLSink**. | Yes |
| preCopyScript | Geef een SQL-query op voor de Kopieer activiteit die moet worden uitgevoerd voordat u in elke uitvoering gegevens in Azure Database for PostgreSQL schrijft. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | No |
| writeBatchSize | Hiermee worden gegevens in de Azure Database for PostgreSQL tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt.<br>Toegestane waarde is een geheel getal dat het aantal rijen vertegenwoordigt. | Nee (de standaard waarde is 10.000) |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br>Toegestane waarden zijn time span-teken reeksen. Een voor beeld is 00:30:00 (30 minuten). | Nee (de standaard waarde is 00:00:30) |

**Voor beeld**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie voor meer informatie over de eigenschappen [lookup-activiteit in azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

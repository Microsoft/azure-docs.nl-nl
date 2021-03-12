---
title: Gegevens kopiëren en transformeren in Azure Database for MySQL
description: Verdien hoe u gegevens in Azure Database for MySQL kunt kopiëren en transformeren met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012600"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Gegevens in Azure Database for MySQL kopiëren en transformeren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Database for MySQL, en om gegevens in Azure Database for MySQL te transformeren met behulp van gegevens stromen. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

Deze connector is speciaal voor [Azure database for MySQL service](../mysql/overview.md). Als u gegevens wilt kopiëren van een algemene MySQL-data base die zich on-premises of in de Cloud bevindt, gebruikt u [mysql-connector](connector-mysql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for MySQL-Connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Database for MySQL-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Azure Database for MySQL gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureMySql** | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Database for MySQL exemplaar. <br/> U kunt ook wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

Een typische connection string is `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Meer eigenschappen die u per case kunt instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| SSLMode | Met deze optie geeft u op of het stuur programma TLS-versleuteling en verificatie gebruikt bij het verbinden met MySQL. Bijvoorbeeld `SSLMode=<0/1/2/3/4>`| UITGESCHAKELD (0)/voor keur (1) **(standaard)** /vereist (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nee |
| UseSystemTrustStore | Met deze optie geeft u aan of u een CA-certificaat wilt gebruiken uit het archief van het systeem vertrouwen of van een opgegeven PEM-bestand. Bijvoorbeeld `UseSystemTrustStore=<0/1>;`| Ingeschakeld (1)/uitgeschakeld (0) **(standaard)** | Nee |

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
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureMySqlTable** | Ja |
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
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **AzureMySqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |
| queryCommandTimeout | De wacht tijd voordat de query aanvraag een time-out heeft. De standaard waarde is 120 minuten (02:00:00) | Nee |

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
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op: **AzureMySqlSink** | Ja |
| preCopyScript | Geef een SQL-query op voor de Kopieer activiteit die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar Azure Database for MySQL worden geschreven. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |
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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit Azure Database for MySQL. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen. U kunt ervoor kiezen om een Azure Database for MySQL-gegevensset of een [inline-gegevensset](data-flow-source.md#inline-datasets) als bron-en Sink-type te gebruiken.

### <a name="source-transformation"></a>Bron transformatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door Azure Database for MySQL bron. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabel | Als u tabel als invoer selecteert, haalt de gegevens stroom alle gegevens op uit de tabel die is opgegeven in de gegevensset. | Nee | - |*(alleen voor inline-gegevensset)*<br>tableName |
| Query’s uitvoeren | Als u query als invoer selecteert, geeft u een SQL-query op om gegevens op te halen uit de bron, waardoor elke tabel die u in dataset opgeeft, wordt overschreven. Het gebruik van query's is een uitstekende manier om rijen te verminderen voor testen of lookups.<br><br>**Order by** -component wordt niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL waarmee een tabel wordt geretourneerd die u in de gegevens stroom kunt gebruiken.<br>Query voorbeeld: `select * from mytable where customerId > 1000 and customerId < 2000` or `select * from "MyTable"` .| Nee | Tekenreeks | query |
| Batchgrootte | Geef een batch grootte op om grote hoeveel heden gegevens naar batches te segmenteren. | Nee | Geheel getal | batchSize |
| Isolatie niveau | Kies een van de volgende isolatie niveaus:<br>-Doorgevoerde lezen<br>-Niet-doorgevoerde lezen (standaard)<br>-Herhaal bare Lees bewerking<br>-Serialiseerbaar<br>-Geen (isolatie niveau negeren) | Nee | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>GEEN</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Voor beeld van Azure Database for MySQL-bron script

Wanneer u Azure Database for MySQL als bron type gebruikt, is het gekoppelde gegevensstroom script:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Sink-trans formatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door Azure Database for MySQL sink. U kunt deze eigenschappen bewerken op het tabblad **sink-opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update methode | Geef op welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan.<br>Als u rijen wilt bijwerken, upsert of verwijderen, is een [ALTER Row Transform](data-flow-alter-row.md) vereist om rijen voor die acties te labelen. | Ja | `true` of `false` | verwijderd <br/>invoegen <br/>bij te werken <br/>upsertable |
| Sleutel kolommen | Voor updates, upsert en verwijderen moeten de sleutel kolom (men) worden ingesteld om te bepalen welke rij moet worden gewijzigd.<br>De kolom naam die u kiest als de sleutel, wordt gebruikt als onderdeel van de volgende update, upsert, verwijderen. Daarom moet u een kolom kiezen die in de Sink-toewijzing voor komt. | Nee | Matrix | keys |
| Schrijf sleutel kolommen overs Laan | Als u de waarde niet naar de sleutel kolom wilt schrijven, selecteert u "schrijven van sleutel kolommen overs Laan". | Nee | `true` of `false` | skipKeyWrites |
| Tabel actie |Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.<br>- **Geen**: er wordt geen actie uitgevoerd voor de tabel.<br>- **Opnieuw maken**: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.<br>- **Afkappen**: alle rijen uit de doel tabel worden verwijderd. | Nee | `true` of `false` | opnieuw maken<br/>afkappen |
| Batchgrootte | Geef op hoeveel rijen er in elke batch worden geschreven. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens. | Nee | Geheel getal | batchSize |
| SQL-scripts vooraf en na | Geef meerdere SQL-scripts op die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base zijn geschreven. | Nee | Tekenreeks | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Voor beeld van Azure Database for MySQL-Sink-script

Wanneer u Azure Database for MySQL als Sink-type gebruikt, is het gekoppelde gegevensstroom script:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
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

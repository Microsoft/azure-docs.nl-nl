---
title: Gegevens kopiëren en transformeren in Azure Database for PostgreSQL
description: Meer informatie over het kopiëren en transformeren van gegevens in Azure Database for PostgreSQL met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738052"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Gegevens in Azure Database for PostgreSQL kopiëren en transformeren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Database for PostgreSQL, en om gegevens in Azure Database for PostgreSQL te transformeren met behulp van gegevens stromen. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

Deze connector is speciaal bedoeld voor de [Azure database for PostgreSQL-service](../postgresql/overview.md). Als u gegevens wilt kopiëren vanuit een algemene PostgreSQL-data base die zich on-premises of in de Cloud bevindt, gebruikt u de [postgresql-connector](connector-postgresql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for PostgreSQL-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

Op dit moment ondersteunt gegevens stroom Azure Data Base voor PostgreSQL één server, maar niet flexibele server-of grootschalige (Citus).

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bieden informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Database for PostgreSQL-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Azure Database for PostgreSQL gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzurePostgreSql**. | Ja |
| connectionString | Een ODBC-connection string om verbinding te maken met Azure Database for PostgreSQL.<br/>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) voor meer informatie. | Ja |
| connectVia | Deze eigenschap vertegenwoordigt de [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

Een typische connection string is `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Hier vindt u meer eigenschappen die u per case kunt instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| De methode die het stuur programma gebruikt voor het versleutelen van gegevens die worden verzonden tussen het stuur programma en de database server. Bijvoorbeeld:  `EncryptionMethod=<0/1/6>;`| 0 (geen versleuteling) **(standaard)** /1 (SSL)/6 (RequestSSL) | Nee |
| ValidateServerCertificate (VSC) | Hiermee wordt bepaald of het stuur programma het certificaat valideert dat door de database server wordt verzonden wanneer SSL-versleuteling is ingeschakeld (versleutelings methode = 1). Bijvoorbeeld:  `ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** /1 (ingeschakeld) | Nee |

**Voorbeeld**:

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

**Voorbeeld**:

***Wacht woord opslaan in Azure Key Vault***

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

Als u gegevens wilt kopiëren uit Azure Database for PostgreSQL, stelt u de eigenschap type van de gegevensset in op **AzurePostgreSqlTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzurePostgreSqlTable** | Ja |
| tableName | Naam van de tabel | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**:

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
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AzurePostgreSqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `SELECT * FROM mytable` of `SELECT * FROM "MyTable"` . Opmerking in PostgreSQL wordt de naam van de entiteit beschouwd als niet-hoofdletter gevoelig als deze niet in een aanhalings teken wordt vermeld. | Nee (als de eigenschap TableName in de gegevensset is opgegeven) |

**Voorbeeld**:

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
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Geef een SQL-query op voor de Kopieer activiteit die moet worden uitgevoerd voordat u in elke uitvoering gegevens in Azure Database for PostgreSQL schrijft. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |
| writeMethod | De methode die wordt gebruikt om gegevens naar Azure Database for PostgreSQL te schrijven.<br>Toegestane waarden zijn: **CopyCommand** (preview, meer presteert), **BulkInsert** (standaard). | Nee |
| writeBatchSize | Het aantal rijen dat in Azure Database for PostgreSQL per batch wordt geladen.<br>Toegestane waarde is een geheel getal dat het aantal rijen vertegenwoordigt. | Nee (de standaard waarde is 1.000.000) |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br>Toegestane waarden zijn time span-teken reeksen. Een voor beeld is 00:30:00 (30 minuten). | Nee (de standaard waarde is 00:30:00) |

**Voorbeeld**:

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
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit Azure Database for PostgreSQL. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen. U kunt ervoor kiezen om een Azure Database for PostgreSQL-gegevensset of een [inline-gegevensset](data-flow-source.md#inline-datasets) als bron-en Sink-type te gebruiken.

### <a name="source-transformation"></a>Bron transformatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door Azure Database for PostgreSQL bron. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabel | Als u tabel als invoer selecteert, haalt de gegevens stroom alle gegevens op uit de tabel die is opgegeven in de gegevensset. | Nee | - |*(alleen voor inline-gegevensset)*<br>tableName |
| Query’s uitvoeren | Als u query als invoer selecteert, geeft u een SQL-query op om gegevens op te halen uit de bron, waardoor elke tabel die u in dataset opgeeft, wordt overschreven. Het gebruik van query's is een uitstekende manier om rijen te verminderen voor testen of lookups.<br><br>**Order by** -component wordt niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL waarmee een tabel wordt geretourneerd die u in de gegevens stroom kunt gebruiken.<br>Query voorbeeld: `select * from mytable where customerId > 1000 and customerId < 2000` or `select * from "MyTable"` . Opmerking in PostgreSQL wordt de naam van de entiteit beschouwd als niet-hoofdletter gevoelig als deze niet in een aanhalings teken wordt vermeld.| Nee | Tekenreeks | query |
| Batchgrootte | Geef een batch grootte op om grote hoeveel heden gegevens naar batches te segmenteren. | Nee | Geheel getal | batchSize |
| Isolatie niveau | Kies een van de volgende isolatie niveaus:<br>-Doorgevoerde lezen<br>-Niet-doorgevoerde lezen (standaard)<br>-Herhaal bare Lees bewerking<br>-Serialiseerbaar<br>-Geen (isolatie niveau negeren) | Nee | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>GEEN</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Voor beeld van Azure Database for PostgreSQL-bron script

Wanneer u Azure Database for PostgreSQL als bron type gebruikt, is het gekoppelde gegevensstroom script:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Sink-trans formatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door Azure Database for PostgreSQL sink. U kunt deze eigenschappen bewerken op het tabblad **sink-opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update methode | Geef op welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan.<br>Als u rijen wilt bijwerken, upsert of verwijderen, is een [ALTER Row Transform](data-flow-alter-row.md) vereist om rijen voor die acties te labelen. | Ja | `true` of `false` | verwijderd <br/>invoegen <br/>bij te werken <br/>upsertable |
| Sleutel kolommen | Voor updates, upsert en verwijderen moeten de sleutel kolom (men) worden ingesteld om te bepalen welke rij moet worden gewijzigd.<br>De kolom naam die u kiest als de sleutel, wordt gebruikt als onderdeel van de volgende update, upsert, verwijderen. Daarom moet u een kolom kiezen die in de Sink-toewijzing voor komt. | Nee | Matrix | keys |
| Schrijf sleutel kolommen overs Laan | Als u de waarde niet naar de sleutel kolom wilt schrijven, selecteert u "schrijven van sleutel kolommen overs Laan". | Nee | `true` of `false` | skipKeyWrites |
| Tabel actie |Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.<br>- **Geen**: er wordt geen actie uitgevoerd voor de tabel.<br>- **Opnieuw maken**: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.<br>- **Afkappen**: alle rijen uit de doel tabel worden verwijderd. | Nee | `true` of `false` | opnieuw maken<br/>afkappen |
| Batchgrootte | Geef op hoeveel rijen er in elke batch worden geschreven. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens. | Nee | Geheel getal | batchSize |
| SQL-scripts vooraf en na | Geef meerdere SQL-scripts op die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base zijn geschreven. | Nee | Tekenreeks | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Voor beeld van Azure Database for PostgreSQL-Sink-script

Wanneer u Azure Database for PostgreSQL als Sink-type gebruikt, is het gekoppelde gegevensstroom script:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie voor meer informatie over de eigenschappen [lookup-activiteit in azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

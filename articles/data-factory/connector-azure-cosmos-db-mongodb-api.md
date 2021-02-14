---
title: Gegevens kopiëren van de API van Azure Cosmos DB voor MongoDB
description: Informatie over het kopiëren van gegevens van ondersteunde brongegevens archieven naar of van de Azure Cosmos DB-API voor MongoDB naar ondersteunde Sink-archieven met behulp van Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 6f1e865daf9ba42126c0f8a341a54d87ac7f374a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393085"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Gegevens kopiëren naar of vanuit de API voor MongoDB van Azure Cosmos DB met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar de API van Azure Cosmos DB voor MongoDB. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

>[!NOTE]
>Deze connector ondersteunt alleen het kopiëren van gegevens van/naar de API van Azure Cosmos DB voor MongoDB. Raadpleeg [Cosmos DB SQL API connector](connector-azure-cosmos-db.md)voor SQL API. Andere API-typen worden momenteel niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens van Azure Cosmos DB-API voor MongoDB kopiëren naar een ondersteunde Sink-gegevens opslag of gegevens uit een ondersteund gegevens archief kopiëren naar de API van Azure Cosmos DB voor MongoDB. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

U kunt de API van de Azure Cosmos DB voor MongoDB-connector gebruiken voor het volgende:

- Gegevens kopiëren van en naar de [API van de Azure Cosmos DB voor MongoDb](../cosmos-db/mongodb-introduction.md).
- Schrijf naar Azure Cosmos DB als **Insert** of **upsert**.
- JSON-documenten importeren en exporteren als-is of gegevens kopiëren van of naar een gegevensset in tabel vorm. Voor beelden zijn een SQL database en een CSV-bestand. Zie JSON-documenten importeren of exporteren als u documenten wilt kopiëren naar of van JSON-bestanden of naar of van een andere Azure Cosmos DB verzameling.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de API van Azure Cosmos DB voor MongoDB.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de API van de Azure Cosmos DB voor MongoDB gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **CosmosDbMongoDbApi**. | Ja |
| connectionString |Geef de connection string op voor de API van uw Azure Cosmos DB voor MongoDB. U kunt deze vinden in de Azure Portal-> uw Cosmos DB-Blade-> primaire of secundaire connection string, met het patroon van `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` . <br/><br />U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie.|Yes |
| database | De naam van de data base die u wilt openen. | Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voorbeeld**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende eigenschappen worden ondersteund voor de API van Azure Cosmos DB voor MongoDB-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **CosmosDbMongoDbApiCollection**. |Yes |
| collectionName |De naam van de verzameling Azure Cosmos DB. |Yes |

**Voorbeeld**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die de API van de Azure Cosmos DB voor MongoDB-bron-en Sink-ondersteuning.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>De API van Azure Cosmos DB voor MongoDB als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **CosmosDbMongoDbApiSource**. |Yes |
| filter | Hiermee geeft u selectie filter op met behulp van query operators. Als u alle documenten in een verzameling wilt retour neren, laat u deze para meter weg of geeft u een leeg document ( {} ) door. | No |
| cursorMethods. project | Hiermee geeft u de velden op die moeten worden geretourneerd in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retour neren, laat u deze para meter weg. | No |
| cursorMethods. sort | Hiermee geeft u de volg orde waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods. Limit |    Hiermee geeft u het maximum aantal documenten op dat door de server wordt geretourneerd. Raadpleeg [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No | 
| cursorMethods. Skip | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en waar MongoDB begint met het retour neren van resultaten. Raadpleeg [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden geretourneerd in elke batch van het antwoord van de MongoDB-instantie. In de meeste gevallen heeft het wijzigen van de Batch grootte geen invloed op de gebruiker of de toepassing. Cosmos DB limieten voor elke batch kan niet groter zijn dan 40MB. Dit is de som van de grootte van het batchSize aantal documenten. Verlaag deze waarde dus als de grootte van het document groot is. | No<br/>(de standaard waarde is **100**) |

>[!TIP]
>ADF-ondersteuning die BSON-document verbruikt in de **strikte modus**. Zorg ervoor dat uw filter query in de strikte modus in plaats van in de shell modus is. Meer beschrijving vindt u in [MongoDb hand matig](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>De API van Azure Cosmos DB voor MongoDB als Sink

De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **CosmosDbMongoDbApiSink**. |Yes |
| writeBehavior |Hierin wordt beschreven hoe u gegevens naar Azure Cosmos DB schrijft. Toegestane waarden: **Insert** en **upsert**.<br/><br/>Het gedrag van **upsert** is om het document te vervangen als er al een document met dezelfde `_id` bestaat, anders voegt u het document in.<br /><br />**Opmerking**: Data Factory genereert automatisch een `_id` voor een document als er geen `_id` is opgegeven in het oorspronkelijke document of op kolom toewijzing. Dit betekent dat u ervoor moet zorgen dat uw document een ID heeft om **upsert** te laten werken zoals verwacht. |No<br />(de standaard waarde is **Invoegen**) |
| writeBatchSize | De eigenschap **writeBatchSize** bepaalt de grootte van documenten die in elke batch moeten worden geschreven. U kunt proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als de grootte van uw document groot is. |No<br />(de standaard waarde is **10.000**) |
| writeBatchTimeout | De wacht tijd waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt. De toegestane waarde is time span. | No<br/>(de standaard waarde is **00:30:00** -30 minuten) |

>[!TIP]
>Als u JSON-documenten wilt importeren als-is, raadpleegt u de sectie [JSON-documenten importeren of exporteren](#import-and-export-json-documents) . Zie [schema toewijzing](#schema-mapping)voor informatie over het kopiëren van gegevens in tabel vorm.

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON-documenten importeren en exporteren

U kunt deze Azure Cosmos DB-connector gebruiken om eenvoudig:

* Kopieer documenten tussen twee Azure Cosmos DB verzamelingen.
* Importeer JSON-documenten uit verschillende bronnen naar Azure Cosmos DB, met inbegrip van MongoDB, Azure Blob Storage, Azure Data Lake Store en andere op bestanden gebaseerde archieven die door Azure Data Factory worden ondersteund.
* JSON-documenten exporteren vanuit een Azure Cosmos DB-verzameling naar verschillende op bestanden gebaseerde archieven.

Schema-neutraal kopiëren:

* Wanneer u het hulp programma Gegevens kopiëren gebruikt, selecteert u de optie **exporteren naar json-bestanden of Cosmos DB verzameling** .
* Wanneer u de functie voor het ontwerpen van activiteiten gebruikt, kiest u JSON-indeling met de bijbehorende bestands opslag voor de bron of sink.

## <a name="schema-mapping"></a>Schema toewijzing

Als u gegevens wilt kopiëren van de API van Azure Cosmos DB voor MongoDB naar tabellaire sink of omgekeerd, raadpleegt u [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

Om ervoor te zorgen dat u Cosmos DB met de juiste object-ID van de bron gegevens, bijvoorbeeld, een kolom ' id ' in SQL database tabel hebt en u de waarde wilt gebruiken als de document-ID in MongoDB voor INSERT/upsert, moet u de juiste schema toewijzing instellen volgens de definitie van de MongoDB Strict-modus ( `_id.$oid` ) als volgt: Cosmos db

![Toewijzings-ID in MongoDB-Sink](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Nadat de Kopieer activiteit is uitgevoerd, wordt de onderstaande BSON ObjectId gegenereerd in Sink:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
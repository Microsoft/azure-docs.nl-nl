---
title: Gegevens kopiëren van MongoDB
description: Meer informatie over het kopiëren van gegevens uit Mongo DB naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: 71096334f46531bba26f0ead66169340107627cf
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028689"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Gegevens kopiëren van MongoDB met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een MongoDB-data base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

>[!IMPORTANT]
>ADF release deze nieuwe versie van de MongoDB-connector, die betere ondersteuning voor systeem eigen MongoDB biedt. Raadpleeg het artikel [MongoDb connector (verouderd)](connector-mongodb-legacy.md) als u de vorige MongoDb-connector gebruikt in uw oplossing die wordt ondersteund als-is voor achterwaartse compatibiliteit.


## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de MongoDB-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze MongoDB-connector ondersteunt **met name versie 4,2**.


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor MongoDB-connector.


## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor MongoDB gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **MongoDbV2** |Ja |
| connectionString |Geef de MongoDB op connection string bijvoorbeeld `mongodb://[username:password@]host[:port][/[database][?options]]` . Raadpleeg de [hand leiding voor MongoDb op Connection String](https://docs.mongodb.com/manual/reference/connection-string/) voor meer informatie. <br/><br /> U kunt ook een connection string in Azure Key Vault plaatsen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. |Ja |
| database | De naam van de data base die u wilt openen. | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende eigenschappen worden ondersteund voor MongoDB-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MongoDbV2Collection** | Ja |
| collectionName |De naam van de verzameling in de MongoDB-data base. |Ja |

**Voorbeeld:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door MongoDB-bron worden ondersteund.

### <a name="mongodb-as-source"></a>MongoDB als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **MongoDbV2Source** | Ja |
| filter | Hiermee geeft u selectie filter op met behulp van query operators. Als u alle documenten in een verzameling wilt retour neren, laat u deze para meter weg of geeft u een leeg document ( {} ) door. | Nee |
| cursorMethods. project | Hiermee geeft u de velden op die moeten worden geretourneerd in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retour neren, laat u deze para meter weg. | Nee |
| cursorMethods. sort | Hiermee geeft u de volg orde waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nee |
| cursorMethods. Limit | Hiermee geeft u het maximum aantal documenten op dat door de server wordt geretourneerd. Raadpleeg [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nee |
| cursorMethods. Skip | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en waar MongoDB begint met het retour neren van resultaten. Raadpleeg [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nee |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden geretourneerd in elke batch van het antwoord van de MongoDB-instantie. In de meeste gevallen heeft het wijzigen van de Batch grootte geen invloed op de gebruiker of de toepassing. Cosmos DB limieten van elke batch kan niet groter zijn dan 40 MB. Dit is de som van de grootte van het batchSize aantal documenten, dus Verklein deze waarde als de grootte van het document groot is. | Nee<br/>(de standaard waarde is **100**) |

>[!TIP]
>ADF-ondersteuning die BSON-document verbruikt in de **strikte modus**. Zorg ervoor dat uw filter query in de strikte modus in plaats van in de shell modus is. Meer beschrijving vindt u in [MongoDb hand matig](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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


## <a name="export-json-documents-as-is"></a>JSON-documenten exporteren als-is

U kunt deze MongoDB-connector gebruiken om JSON-documenten te exporteren als-afkomstig van een MongoDB-verzameling naar verschillende op bestanden gebaseerde archieven of Azure Cosmos DB. Om een dergelijke schema-neutraal kopie te krijgen, slaat u de sectie ' Structure ' (ook wel *schema* genoemd) in de gegevensset en schema toewijzing in de Kopieer activiteit over.


## <a name="schema-mapping"></a>Schema toewijzing

Zie [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping)als u gegevens van MongoDb naar tabellaire Sink wilt kopiëren.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

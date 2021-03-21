---
title: Gegevens kopiëren van MongoDB Atlas
description: Meer informatie over het kopiëren van gegevens van MongoDB Atlas naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368792"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Gegevens uit MongoDB Atlas kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een MongoDB Atlas-Data Base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de MongoDB Atlas-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Met name deze MongoDB Atlas-connector biedt ondersteuning **voor versies van Maxi maal 4,2**.

## <a name="prerequisites"></a>Vereisten

Als u Azure Integration Runtime gebruikt voor kopiëren, moet u ervoor zorgen dat u de [Azure Integration runtime ip's](azure-integration-runtime-ip-addresses.md) van de efficiënte regio toevoegt aan de MongoDb Atlas IP-toegangs lijst.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor MongoDB Atlas connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor MongoDB Atlas-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **MongoDbAtlas** |Ja |
| connectionString |Geef de MongoDB Atlas op connection string bijvoorbeeld `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` . <br/><br /> U kunt ook een connection string in Azure Key Vault plaatsen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. |Ja |
| database | De naam van de data base die u wilt openen. | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende eigenschappen worden ondersteund voor MongoDB Atlas-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MongoDbAtlasCollection** | Ja |
| collectionName |De naam van de verzameling in de MongoDB Atlas-data base. |Ja |

**Voorbeeld:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de MongoDB Atlas-bron.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **MongoDbAtlasSource** | Ja |
| filter | Hiermee geeft u selectie filter op met behulp van query operators. Als u alle documenten in een verzameling wilt retour neren, laat u deze para meter weg of geeft u een leeg document ( {} ) door. | Nee |
| cursorMethods. project | Hiermee geeft u de velden op die moeten worden geretourneerd in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retour neren, laat u deze para meter weg. | Nee |
| cursorMethods. sort | Hiermee geeft u de volg orde waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nee |
| cursorMethods. Limit | Hiermee geeft u het maximum aantal documenten op dat door de server wordt geretourneerd. Raadpleeg [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nee |
| cursorMethods. Skip | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en waar MongoDB Atlas begint met het retour neren van resultaten. Raadpleeg [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nee |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden geretourneerd in elke batch van het antwoord van het MongoDB Atlas-exemplaar. In de meeste gevallen heeft het wijzigen van de Batch grootte geen invloed op de gebruiker of de toepassing. Cosmos DB limieten voor elke batch kan niet groter zijn dan 40MB. Dit is de som van de grootte van het batchSize aantal documenten. Verlaag deze waarde dus als de grootte van het document groot is. | Nee<br/>(de standaard waarde is **100**) |

>[!TIP]
>ADF-ondersteuning die BSON-document verbruikt in de **strikte modus**. Zorg ervoor dat uw filter query in de strikte modus in plaats van in de shell modus is. Meer beschrijving vindt u in [MongoDb hand matig](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

U kunt deze MongoDB Atlas-connector gebruiken om JSON-documenten te exporteren als-afkomstig van een MongoDB Atlas-verzameling naar verschillende archieven op basis van bestanden of Azure Cosmos DB. Om een dergelijke schema-neutraal kopie te krijgen, slaat u de sectie ' Structure ' (ook wel *schema* genoemd) in de gegevensset en schema toewijzing in de Kopieer activiteit over.

## <a name="schema-mapping"></a>Schema toewijzing

Zie [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping)voor informatie over het kopiëren van gegevens van MongoDb Atlas naar tabellaire sink.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

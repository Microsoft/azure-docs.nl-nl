---
title: Gegevens kopiëren naar zoek index
description: Meer informatie over het pushen of kopiëren van gegevens naar een Azure search-index met behulp van de Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597501"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Gegevens kopiëren naar een Azure Cognitive Search-index met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-search-connector.md)
> * [Huidige versie](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren naar Azure Cognitive Search index. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit elk ondersteund brongegevens archief kopiëren naar de zoek index. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Cognitive Search-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Cognitive Search-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureSearch** | Yes |
| url | URL voor de zoek service. | Yes |
| sleutel | De beheerders sleutel voor de zoek service. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

> [!IMPORTANT]
> Wanneer u gegevens uit een gegevens archief in de Cloud naar de zoek index kopieert, moet u in azure Cognitive Search gekoppelde service een Azure Integration Runtime met een expliciete regio in connactVia. Stel de regio in op de locatie waar uw zoek service zich bevindt. Meer informatie vindt u in [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Voorbeeld:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die door Azure Cognitive Search DataSet worden ondersteund.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens naar Azure Cognitive Search:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureSearchIndex** | Yes |
| indexName | De naam van de zoek index. Data Factory maakt de index niet. De index moet bestaan in azure Cognitive Search. | Ja |

**Voorbeeld:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Azure Cognitive Search Source worden ondersteund.

### <a name="azure-cognitive-search-as-sink"></a>Azure Cognitive Search als Sink

Als u gegevens wilt kopiëren naar Azure Cognitive Search, stelt u het bron type in de Kopieer activiteit in op **AzureSearchIndexSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **AzureSearchIndexSink** | Yes |
| writeBehavior | Hiermee geeft u op of u wilt samen voegen of vervangen wanneer een document al aanwezig is in de index. Zie de [eigenschap WriteBehavior](#writebehavior-property).<br/><br/>Toegestane waarden zijn: **samen voegen** (standaard) en **uploaden**. | No |
| writeBatchSize | Uploadt gegevens in de zoek index wanneer de buffer grootte writeBatchSize bereikt. Zie de [eigenschap WriteBatchSize](#writebatchsize-property) voor meer informatie.<br/><br/>Toegestane waarden zijn: integer 1 tot 1.000; de standaard waarde is 1000. | No |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

### <a name="writebehavior-property"></a>Eigenschap WriteBehavior

AzureSearchSink upsert bij het schrijven van gegevens. Met andere woorden, wanneer u een document schrijft en de document sleutel al in de zoek index bestaat, werkt Azure Cognitive Search het bestaande document bij in plaats van een conflict uitzondering uit te voeren.

De AzureSearchSink biedt de volgende twee upsert-gedragingen (met behulp van AzureSearch SDK):

- **Samen voegen**: alle kolommen in het nieuwe document combi neren met de bestaande. Voor kolommen met een null-waarde in het nieuwe document, blijft de waarde in de bestaande.
- **Uploaden**: het nieuwe document vervangt de bestaande. Voor kolommen die niet in het nieuwe document zijn opgegeven, wordt de waarde ingesteld op NULL, ongeacht of er een andere waarde dan Null is in het bestaande document of niet.

Het standaard gedrag is **samen voegen**.

### <a name="writebatchsize-property"></a>Eigenschap WriteBatchSize

Azure Cognitive Search service ondersteunt het schrijven van documenten als een batch. Een batch kan 1 tot 1.000 acties bevatten. Een actie behandelt één document om de upload/samenvoeg bewerking uit te voeren.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

In de volgende tabel wordt aangegeven of een Azure Cognitive Search-gegevens type wordt ondersteund.

| Azure Cognitive Search-gegevens type | Ondersteund in azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Tekenreeks | J |
| Int32 | J |
| Int64 | J |
| Dubbel | J |
| Booleaans | J |
| DataTimeOffset | J |
| Teken reeks matrix | N |
| GeographyPoint | N |

Momenteel worden andere gegevens typen, zoals complex type, niet ondersteund. Zie [ondersteunde gegevens typen (azure Cognitive Search)](/rest/api/searchservice/supported-data-types)voor een volledige lijst met ondersteunde gegevens typen van Azure Cognitive Search.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
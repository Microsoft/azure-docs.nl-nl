---
title: Gegevens kopiëren van/naar SAP-Cloud voor klant
description: Meer informatie over het kopiëren van gegevens uit de SAP-Cloud voor klant naar ondersteunde Sink-gegevens archieven (of) van ondersteunde brongegevens archieven naar SAP-Cloud voor klanten door gebruik te maken van Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: 4590129b8d0d7a63ef4291a023ea4d980c89368c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386642"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Gegevens kopiëren uit de SAP-Cloud voor de klant (C4C) met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van/naar SAP-Cloud voor klant (C4C). Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF in het scenario voor SAP-gegevens integratie met een gedetailleerde inleiding op elke SAP-connector, comparsion en richt lijnen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP-Cloud voor Customer connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de SAP-Cloud voor de klant kopiëren naar een ondersteunde Sink-gegevens opslag of gegevens kopiëren van elk ondersteund bron gegevens archief naar een SAP-Cloud voor de klant. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Met name Azure Data Factory maakt deze connector het mogelijk om gegevens te kopiëren van/naar SAP-Cloud voor klanten, waaronder de SAP-Cloud voor verkoop, SAP Cloud for service en SAP-Cloud voor sociale oplossingen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de SAP-Cloud voor de klant connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor SAP-Cloud voor door de klant gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomer**. | Yes |
| url | De URL van de SAP C4C OData-service. | Yes |
| gebruikersnaam | Geef de gebruikers naam op om verbinding te maken met de SAP-C4C. | Yes |
| wachtwoord | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No |

**Voorbeeld:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP-Cloud voor de gegevensset van de klant.

Als u gegevens wilt kopiëren uit de SAP-Cloud voor de klant, stelt u de eigenschap type van de gegevensset in op **SapCloudForCustomerResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapCloudForCustomerResource** |Yes |
| leertraject | Geef het pad op van de SAP C4C OData-entiteit. |Ja |

**Voorbeeld:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP-Cloud voor de klant bron.

### <a name="sap-c4c-as-source"></a>SAP C4C als bron

Als u gegevens wilt kopiëren uit de SAP-Cloud voor de klant, stelt u het bron type in de Kopieer activiteit in op **SapCloudForCustomerSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomerSource**  | Yes |
| query | Geef de aangepaste OData-query op om gegevens te lezen. | No |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. Als niet wordt opgegeven, is de standaard waarde **00:30:00** (30 minuten). | No |

Voorbeeld query om gegevens op te halen voor een specifieke dag: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP-C4C als Sink

Als u gegevens wilt kopiëren naar een SAP-Cloud voor de klant, stelt u het sink-type in de Kopieer activiteit in op **SapCloudForCustomerSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomerSink**  | Yes |
| writeBehavior | Het schrijf gedrag van de bewerking. Kan ' Insert ', ' update ' zijn. | Nee. Standaard invoegen. |
| writeBatchSize | De Batch grootte van de schrijf bewerking. De Batch grootte voor het verkrijgen van de beste prestaties kan verschillen voor de verschillende tabellen of servers. | Nee. Standaard 10. |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Toewijzing van gegevens type voor SAP-Cloud voor klant

Bij het kopiëren van gegevens uit de SAP-Cloud voor de klant, worden de volgende toewijzingen gebruikt vanuit de SAP-Cloud voor klant gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| SAP C4C OData-gegevens type | Data Factory-gegevens type interim |
|:--- |:--- |
| EDM. binary | Byte [] |
| Edm.Boolean | Booleaanse waarde |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimaal | Decimaal |
| Edm.Double | Dubbel |
| EDM. single | Enkelvoudig |
| EDM. GUID | Guid |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Tekenreeks |
| EDM. tijd | TimeSpan |
| Edm.DateTimeOffset | Date time offset |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

---
title: Gegevens kopiëren van Marketo met behulp van Azure Data Factory (preview)
description: Meer informatie over het kopiëren van gegevens uit Marketo naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: jingwang
ms.openlocfilehash: aae25536f0ac1d5d3b43b5173d3e5789cd66a774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385639"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Gegevens kopiëren van Marketo met behulp van Azure Data Factory (preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Marketo te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

> [!IMPORTANT]
> Deze connector is momenteel beschikbaar in preview. U kunt het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Marketo-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Marketo kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Marketo-exemplaar dat is geïntegreerd met externe CRM, wordt momenteel niet ondersteund.

>[!NOTE]
>Deze Marketo-connector is gebaseerd op de Marketo-REST API. Houd er rekening mee dat de Marketo [gelijktijdige aanvraag limiet](https://developers.marketo.com/rest-api/) aan service zijde heeft. Als u fouten krijgt met de melding ' fout bij het gebruik van REST API: de maximale frequentie limiet ' 100 ' is overschreden met in ' 20 ' seconden (606) ' of ' fout bij het gebruik van REST API: gelijktijdige toegangs limiet ' 10 ' bereikt (615) ', kunt u overwegen om de uitvoering van de gelijktijdige Kopieer activiteit te verminderen om het aantal aanvragen voor de service te verminderen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Marketo-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Marketo-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **marketo** | Ja |
| endpoint | Het eind punt van de Marketo-server. (bijvoorbeeld 123-ABC-321.mktorest.com)  | Ja |
| clientId | De client-id van uw Marketo-service.  | Ja |
| clientSecret | Het client geheim van uw Marketo-service. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Marketo-gegevensset.

Als u gegevens wilt kopiëren uit Marketo, stelt u de eigenschap type van de gegevensset in op **MarketoObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MarketoObject** | Ja |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Marketo-bron.

### <a name="marketo-as-source"></a>Marketo als bron

Als u gegevens wilt kopiëren uit Marketo, stelt u het bron type in de Kopieer activiteit in op **MarketoSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **MarketoSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Activitiy_Types"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

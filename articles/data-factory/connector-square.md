---
title: Gegevens kopiëren van vier kant (preview-versie)
description: Meer informatie over het kopiëren van gegevens van het kwadraat naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/03/2020
ms.openlocfilehash: ac10e42d338e0ddd44cb3c07709645a69653808d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384789"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Gegevens uit het vier kant kopiëren met behulp van Azure Data Factory (preview-versie)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een vier kant te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

> [!IMPORTANT]
> Deze connector is momenteel beschikbaar in preview. U kunt het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze vier Kante connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Square kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een vier Kante connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een vier Kante gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Square** | Yes |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met het kwadraat. | Yes |
| ***Onder `connectionProperties` :*** | | |
| host | De URL van het vier Kante exemplaar. (bijvoorbeeld mystore.mysquare.com)  | Yes |
| clientId | De client-ID die aan uw kwadraat toepassing is gekoppeld.  | Yes |
| clientSecret | Het client geheim dat aan uw kwadraat toepassing is gekoppeld. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| accessToken | Het toegangs token dat is verkregen van een vier kant. Verleent beperkte toegang tot een Square-account door een geverifieerde gebruiker om expliciete machtigingen te vragen. OAuth-toegangs tokens verlopen 30 dagen na uitgifte, maar vernieuwings tokens verlopen niet. Toegangs tokens kunnen worden vernieuwd op basis van het vernieuwings token.<br>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md).  | Yes |
| refreshToken | Het vernieuwings token dat is verkregen van een vier kant. Wordt gebruikt om nieuwe toegangs tokens te verkrijgen wanneer het huidige verloopt.<br>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | No |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | No |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | No |

Vier Kante ondersteuning voor twee typen toegangs token: **persoonlijk** en **OAuth**.

- Persoonlijke toegangs tokens worden gebruikt om onbeperkte Connect API-toegang tot resources in uw eigen vier kant account op te halen.
- OAuth-toegangs tokens worden gebruikt om geverifieerde en scoped Connect API-toegang te verkrijgen tot een Square-account. Gebruik deze wanneer uw app bronnen in andere vier Kante accounts opent namens de eigenaar van het account. OAuth-toegangs tokens kunnen ook worden gebruikt om toegang te krijgen tot resources in uw eigen vier Kante account.

In Data Factory is authenticatie via een persoonlijk toegangs token alleen vereist `accessToken` , terwijl verificatie via OAuth vereist `accessToken` en `refreshToken` . Meer informatie over het [ophalen van een](https://developer.squareup.com/docs/build-basics/access-tokens)toegangs token.

**Voorbeeld:**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "connectionProperties": {
                "host": "<e.g. mystore.mysquare.com>", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<clientSecret>"
                }, 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de vier Kante gegevensset.

Als u gegevens wilt kopiëren van een vier kant, stelt u de eigenschap type van de gegevensset in op **SquareObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SquareObject** | Yes |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de vier Kante bron.

### <a name="square-as-source"></a>Vier kant als bron

Als u gegevens wilt kopiëren van het kwadraat, stelt u het bron type in de Kopieer activiteit in op **SquareSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **SquareSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Business"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
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

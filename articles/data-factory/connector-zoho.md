---
title: Gegevens kopiëren van Zoho met behulp van Azure Data Factory (preview-versie)
description: Meer informatie over het kopiëren van gegevens uit Zoho naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jingwang
ms.openlocfilehash: e42638d484d2a71052c3a9410f73cbca9e038682
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366888"
---
# <a name="copy-data-from-zoho-using-azure-data-factory-preview"></a>Gegevens kopiëren van Zoho met behulp van Azure Data Factory (preview-versie)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Zoho te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

> [!IMPORTANT]
> Deze connector is momenteel beschikbaar in preview. U kunt het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Zoho-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)


U kunt gegevens van Zoho kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze connector ondersteunt verificatie van het Xero-toegangs token en OAuth 2,0-verificatie.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor Zoho-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Zoho gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Zoho** | Ja |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met Zoho. | Ja |
| ***Onder `connectionProperties` :*** | | |
| endpoint | Het eind punt van de Zoho-server ( `crm.zoho.com/crm/private` ). | Ja |
| authenticationType | Toegestane waarden zijn `OAuth_2.0` en `Access Token` . | Ja |
| clientId | De client-ID die is gekoppeld aan uw Zoho-toepassing. | Ja voor OAuth 2,0-verificatie | 
| clientSecrect | De clientsecret die is gekoppeld aan uw Zoho-toepassing. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja voor OAuth 2,0-verificatie | 
| refreshToken | Het OAuth 2,0-vernieuwings token dat is gekoppeld aan uw Zoho-toepassing, waarmee het toegangs token wordt vernieuwd wanneer het verloopt. Het vernieuwings token verloopt nooit. Als u een vernieuwings token wilt ophalen, moet u de `offline` access_type aanvragen, maar in [dit artikel](https://www.zoho.com/crm/developer/docs/api/auth-request.html)vindt u meer informatie. <br>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md).| Ja voor OAuth 2,0-verificatie |
| accessToken | Het toegangs token voor Zoho-verificatie. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | Nee |

**Voor beeld: OAuth 2,0-verificatie**

```json
{
    "name": "ZohoLinkedService",
    "properties": {
        "type": "Zoho",
        "typeProperties": {
            "connectionProperties": { 
                "authenticationType":"OAuth_2.0", 
                "endpoint": "crm.zoho.com/crm/private", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<client secret>"
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

**Voor beeld: toegangs token verificatie**

```json
{
    "name": "ZohoLinkedService",
    "properties": {
        "type": "Zoho",
        "typeProperties": {
            "connectionProperties": { 
                "authenticationType":"Access Token", 
                "endpoint": "crm.zoho.com/crm/private", 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Zoho-gegevensset.

Als u gegevens van Zoho wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **ZohoObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **ZohoObject** | Ja |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "ZohoDataset",
    "properties": {
        "type": "ZohoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Zoho linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Zoho-bron worden ondersteund.

### <a name="zoho-as-source"></a>Zoho als bron

Als u gegevens wilt kopiëren uit Zoho, stelt u het bron type in de Kopieer activiteit in op **ZohoSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **ZohoSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Accounts"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromZoho",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Zoho input dataset name>",
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
                "type": "ZohoSource",
                "query": "SELECT * FROM Accounts"
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

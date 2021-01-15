---
title: Gegevens kopiëren van QuickBooks Online met behulp van Azure Data Factory (preview)
description: Informatie over het kopiëren van gegevens van QuickBooks Online naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: ecdb0e55aa7127a373e63612908ed58109c1f8e2
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233163"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Gegevens kopiëren van QuickBooks Online met behulp van Azure Data Factory (preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit QuickBooks Online te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

> [!IMPORTANT]
> Deze connector is momenteel beschikbaar in preview. U kunt het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze QuickBooks-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van QuickBooks Online kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze connector ondersteunt QuickBooks OAuth 2,0-verificatie.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor QuickBooks-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde QuickBooks-Services:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **QuickBooks** | Yes |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met QuickBooks. | Yes |
| **_Onder `connectionProperties` :_* _ | | |
| endpoint | Het eind punt van de QuickBooks Online-server. (dat wil zeggen, quickbooks.api.intuit.com)  | Yes |
| companyId | De bedrijfs-ID van het QuickBooks-bedrijf dat moet worden geautoriseerd. Zie [Hoe kan ik mijn bedrijfs-id zoeken](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551)voor informatie over het vinden van de bedrijfs-id. | Yes |
| consumerKey | De client-ID van uw QuickBooks Online-toepassing voor OAuth 2,0-verificatie. Meer informatie [vindt u hier](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). | Yes |
| consumerSecret | Het client geheim van uw QuickBooks Online-toepassing voor OAuth 2,0-verificatie. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| refreshToken | Het OAuth 2,0-vernieuwings token dat is gekoppeld aan de QuickBooks-toepassing. Meer informatie [vindt u hier](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). Het token voor het vernieuwen van opmerkingen is na 180 dagen verlopen. De klant moet het vernieuwings token regel matig bijwerken. <br/>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md).| Yes |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | No |

_ *Voor beeld:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door QuickBooks-gegevensset.

Als u gegevens wilt kopiëren vanuit QuickBooks Online, stelt u de eigenschap type van de gegevensset in op **QuickBooksObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **QuickBooksObject** | Yes |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de QuickBooks-bron.

### <a name="quickbooks-as-source"></a>QuickBooks als bron

Als u gegevens wilt kopiëren vanuit QuickBooks Online, stelt u het bron type in de Kopieer activiteit in op **QuickBooksSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **QuickBooksSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Gegevens kopiëren van QuickBooks Desktop

Met de Kopieer activiteit in Azure Data Factory kunnen gegevens niet rechtstreeks vanuit QuickBooks Desktop worden gekopieerd. Als u gegevens wilt kopiëren van het bureau blad van QuickBooks, exporteert u uw QuickBooks-gegevens naar een CSV-bestand (Comma-Separated Values) en uploadt u het bestand naar Azure Blob Storage. Hier kunt u Data Factory gebruiken om de gegevens naar de Sink van uw keuze te kopiëren.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

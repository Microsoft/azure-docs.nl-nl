---
title: Gegevens kopiëren van concur met behulp van Azure Data Factory (preview-versie)
description: Meer informatie over het kopiëren van gegevens uit concur naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jingwang
ms.openlocfilehash: 974682aa9e9bebd6e2d17237decd8389174ff28d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383667"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Gegevens kopiëren van concur met behulp van Azure Data Factory (preview-versie)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit concur te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

> [!IMPORTANT]
> Deze connector is momenteel beschikbaar in preview. U kunt het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze concur-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van concur kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

> [!NOTE]
> Het partner account wordt momenteel niet ondersteund.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor concur-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor concur gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **concur** | Yes |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met concur. | Yes |
| ***Onder `connectionProperties` :*** | | |
| authenticationType | Toegestane waarden zijn `OAuth_2.0_Bearer` en `OAuth_2.0` (verouderd). De OAuth 2,0-verificatie optie werkt met de oude concur-API die sinds februari 2017 is afgeschaft. | Yes |
| host | Het eind punt van de concur-server, bijvoorbeeld `implementation.concursolutions.com` .  | Yes |
| baseUrl | De basis-URL van de autorisatie-URL van uw concur. | Ja voor `OAuth_2.0_Bearer` verificatie |
| clientId | De client-ID van de toepassing die is geleverd door concur-app-beheer.  | Yes |
| clientSecret | Het client geheim dat overeenkomt met de client-ID. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja voor `OAuth_2.0_Bearer` verificatie |
| gebruikersnaam | De gebruikers naam die u gebruikt voor toegang tot de concur-service. | Yes |
| wachtwoord | Het wacht woord dat overeenkomt met de gebruikers naam die u hebt opgegeven in het veld gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | No |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | No |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | No |

**Voorbeeld:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**Voor beeld (verouderd):**

Opmerking: het volgende is een verouderd gekoppeld service model zonder `connectionProperties` en verificatie te gebruiken `OAuth_2.0` .

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de concur-gegevensset.

Als u gegevens van concur wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **ConcurObject**. Er is geen aanvullende typespecifieke eigenschap in dit type gegevensset. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **ConcurObject** | Yes |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |


**Voorbeeld**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door concur-bron worden ondersteund.

### <a name="concursource-as-source"></a>ConcurSource als bron

Als u gegevens wilt kopiëren uit concur, stelt u het bron type in de Kopieer activiteit in op **ConcurSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **ConcurSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Opportunities where Id = xxx "`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
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

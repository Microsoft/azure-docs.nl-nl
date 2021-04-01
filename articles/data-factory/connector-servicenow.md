---
title: Gegevens kopiëren van ServiceNow
description: Meer informatie over het kopiëren van gegevens uit ServiceNow naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378448"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Gegevens kopiëren van ServiceNow met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit ServiceNow te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze ServiceNow-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van ServiceNow kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor ServiceNow-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor ServiceNow gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **ServiceNow** | Yes |
| endpoint | Het eind punt van de ServiceNow-server ( `http://<instance>.service-now.com` ).  | Yes |
| authenticationType | Het verificatie type dat moet worden gebruikt. <br/>Toegestane waarden zijn: **Basic**, **OAuth2** | Yes |
| gebruikersnaam | De gebruikers naam die wordt gebruikt om verbinding te maken met de ServiceNow-server voor Basic-en OAuth2-verificatie.  | Yes |
| wachtwoord | Het wacht woord dat overeenkomt met de gebruikers naam voor Basic-en OAuth2-verificatie. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| clientId | De client-ID voor OAuth2-verificatie.  | No |
| clientSecret | Het client geheim voor OAuth2-verificatie. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | No |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | No |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | No |

**Voorbeeld:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de ServiceNow-gegevensset.

Als u gegevens van ServiceNow wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **ServiceNowObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **ServiceNowObject** | Yes |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door ServiceNow-bron worden ondersteund.

### <a name="servicenow-as-source"></a>ServiceNow als bron

Als u gegevens wilt kopiëren uit ServiceNow, stelt u het bron type in de Kopieer activiteit in op **ServiceNowSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **ServiceNowSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Actual.alm_asset"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

Let op het volgende wanneer u het schema en de kolom voor ServiceNow in query opgeeft en **Raadpleeg de [prestatie tips](#performance-tips) bij het kopiëren van de prestatie implicaties**.

- **Schema:** Geef het schema op als `Actual` of `Display` in de ServiceNow-query, die u kunt bekijken als de para meter `sysparm_display_value` True of False bij het aanroepen van [ServiceNow-rest-api's](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolom:** de kolom naam voor de werkelijke waarde onder `Actual` schema is `[column name]_value` , terwijl de waarde weer geven onder `Display` schema is `[column name]_display_value` . Houd er rekening mee dat de naam van de kolom moet worden toegewezen aan het schema dat wordt gebruikt in de query.

**Voorbeeld query:** 
 `SELECT col_value FROM Actual.alm_asset` OF`SELECT col_display_value FROM Display.alm_asset`

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Tips voor prestaties

### <a name="schema-to-use"></a>Schema dat moet worden gebruikt

ServiceNow heeft twee verschillende schema's, een is **"werkelijk"** waarmee werkelijke gegevens worden geretourneerd. de andere waarde is **"display"** waarmee de weergave waarden van gegevens worden geretourneerd. 

Als u een filter in uw query hebt, gebruikt u ' werkelijk ' schema dat betere Kopieer prestaties heeft. Bij het uitvoeren van een query op een ' werkelijk ' schema, ServiceNow systeem eigen ondersteuning bij het ophalen van de gegevens om alleen de gefilterde resultatenset te retour neren, terwijl bij het uitvoeren van query's op het schema ' weer geven ' alle gegevens worden opgehaald en filter intern wordt toegepast.

### <a name="index"></a>Index

Met de ServiceNow-tabel index kunt u de prestaties van query's verbeteren door [een tabel index te maken](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

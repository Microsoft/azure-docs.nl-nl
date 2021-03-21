---
title: Gegevens kopiëren van Dynamics AX
description: Meer informatie over het kopiëren van gegevens van Dynamics AX naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 38ff77ad56f16fbd33b77021b18be77f6a153b3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380981"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Gegevens uit Dynamics AX kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit de Dynamics AX-bron te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Dynamics AX-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Dynamics AX kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Met name deze Dynamics AX-connector ondersteunt het kopiëren van gegevens van Dynamics AX met het **OData-protocol** met service- **Principal-verificatie**.

>[!TIP]
>U kunt deze connector ook gebruiken om gegevens te kopiëren uit **Dynamics 365-Financiën en-bewerkingen**. Raadpleeg de [OData-ondersteuning](/dynamics365/unified-operations/dev-itpro/data-entities/odata) en [verificatie methode](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)van Dynamics 365.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Dynamics AX-connector.

## <a name="prerequisites"></a>Vereisten

Voer de volgende stappen uit om Service-Principal-verificatie te gebruiken:

1. Registreer een toepassings entiteit in Azure Active Directory (Azure AD) door [uw toepassing te registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

2. Ga naar Dynamics AX en verleen deze service-principal de juiste machtigingen voor toegang tot uw Dynamics AX.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Dynamics AX-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **DynamicsAX**. |Ja |
| url | Het OData-eind punt van het exemplaar van Dynamics AX (of Dynamics 365 Finance and Operations). |Ja |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor autorisatie. Als uw Dynamics-URL bijvoorbeeld is `https://sampledynamics.sandbox.operations.dynamics.com/data/` , is de bijbehorende Aad-resource meestal `https://sampledynamics.sandbox.operations.dynamics.com` . | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt kiezen Azure Integration Runtime of een zelf-hostend Integration Runtime (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

In deze sectie vindt u een lijst met eigenschappen die door de Dynamics AX-gegevensset worden ondersteund.

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u de eigenschap **type** van de gegevensset in op **DynamicsAXResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **DynamicsAXResource**. | Ja |
| leertraject | Het pad naar de Dynamics AX OData-entiteit. | Ja |

**Voorbeeld**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de Dynamics AX-bron worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="dynamics-ax-as-source"></a>Dynamics AX als bron

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u het **bron** type in de Kopieer activiteit in op **DynamicsAXSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **DynamicsAXSource**. | Ja |
| query | OData-query opties voor het filteren van gegevens. Bijvoorbeeld: `"?$select=Name,Description&$top=5"`.<br/><br/>**Opmerking**: de connector kopieert gegevens van de gecombineerde URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` . Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | Nee |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. Als niet wordt opgegeven, is de standaard waarde **00:30:00** (30 minuten). | Nee |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
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

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
---
title: Gegevens kopiëren van Google AdWords
description: Meer informatie over het kopiëren van gegevens uit Google AdWords naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 8b3036f09e41b20bc3c190f06842acd817fcece6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380947"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Gegevens uit Google AdWords kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Google AdWords te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google AdWords-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)


U kunt gegevens uit Google AdWords kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Google AdWords-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Google AdWords-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **GoogleAdWords** | Ja |
| clientCustomerID | De klant-ID van de client van het AdWords-account waarvoor u rapport gegevens wilt ophalen.  | Ja |
| developerToken | Het ontwikkelaars token dat is gekoppeld aan het beheer account dat u gebruikt om toegang te verlenen tot de AdWords-API.  U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de ADF-Kopieer activiteit uit te voeren tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | Het OAuth 2,0-verificatie mechanisme dat wordt gebruikt voor verificatie. ServiceAuthentication kan alleen worden gebruikt op zelf-hostende IR. <br/>Toegestane waarden zijn: **ServiceAuthentication**, **UserAuthentication** | Ja |
| refreshToken | Het vernieuwings token dat is verkregen van Google voor het machtigen van toegang tot AdWords voor UserAuthentication. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de ADF-Kopieer activiteit uit te voeren tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Nee |
| clientId | De client-ID van de Google-toepassing die wordt gebruikt voor het verkrijgen van het vernieuwings token. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de ADF-Kopieer activiteit uit te voeren tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Nee |
| clientSecret | Het client geheim van de Google-toepassing die wordt gebruikt voor het verkrijgen van het vernieuwings token. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de ADF-Kopieer activiteit uit te voeren tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Nee |
| e-mail | De e-mail-ID van het service account die wordt gebruikt voor ServiceAuthentication en kan alleen worden gebruikt op zelf-hostende IR.  | Nee |
| keyFilePath | Het volledige pad naar het. p12-sleutel bestand dat wordt gebruikt voor verificatie van het e-mail adres van het service account en kan alleen worden gebruikt op zelf-hostende IR.  | Nee |
| trustedCertPath | Het volledige pad van het. pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken van verbinding via TLS. Deze eigenschap kan alleen worden ingesteld wanneer TLS op zelf-hostende IR wordt gebruikt. De standaard waarde is het cacerts. pem-bestand dat met de IR is geïnstalleerd.  | Nee |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken uit de systeem vertrouwens archief of vanuit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Google AdWords-gegevensset.

Als u gegevens wilt kopiëren uit Google AdWords, stelt u de eigenschap type van de gegevensset in op **GoogleAdWordsObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **GoogleAdWordsObject** | Ja |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Google AdWords-bron worden ondersteund.

### <a name="google-adwords-as-source"></a>Google AdWords als bron

Als u gegevens wilt kopiëren uit Google AdWords, stelt u het bron type in de Kopieer activiteit in op **GoogleAdWordsSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **GoogleAdWordsSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
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

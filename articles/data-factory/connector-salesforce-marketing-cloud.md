---
title: Gegevens kopiëren uit Sales Force marketing Cloud
description: Meer informatie over het kopiëren van gegevens uit Sales Force marketing Cloud naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369744"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Gegevens kopiëren uit Sales Force marketing Cloud met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Sales Force marketing Cloud te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Sales Force marketing-Cloud connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Sales Force marketing-Cloud kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

De Sales Force marketing-Cloud connector ondersteunt OAuth 2-verificatie en ondersteunt zowel oudere als uitgebreide pakket typen. De connector is gebaseerd op de [rest API Sales Force marketing Cloud](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Deze connector biedt geen ondersteuning voor het ophalen van aangepaste objecten of aangepaste gegevens extensies.

## <a name="getting-started"></a>Aan de slag

U kunt een pijp lijn maken met de Kopieer activiteit met behulp van .NET SDK, python SDK, Azure PowerShell, REST API of Azure Resource Manager sjabloon. Zie [zelf studie Kopieer activiteit](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Sales Force marketing Cloud connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor aan Sales Force marketing Cloud gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SalesforceMarketingCloud** | Yes |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met Sales Force marketing Cloud. | Yes |
| ***Onder `connectionProperties` :*** | | |
| authenticationType | Hiermee geeft u de verificatie methode op die moet worden gebruikt. Toegestane waarden zijn `Enhanced sts OAuth 2.0` of `OAuth_2.0` .<br><br>Het verouderde pakket Sales Force marketing Cloud wordt alleen ondersteund `OAuth_2.0` , terwijl er uitgebreide pakket behoeften nodig zijn `Enhanced sts OAuth 2.0` . <br>Sinds 1 augustus 2019 heeft Sales Force marketing Cloud de mogelijkheid om verouderde pakketten te maken, verwijderd. Alle nieuwe pakketten zijn uitgebreide pakketten. | Yes |
| host | Voor een verbeterd pakket moet de host uw [subdomein](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) zijn dat wordt vertegenwoordigd door een teken reeks van 28 tekens, te beginnen met de letters ' MC ', bijvoorbeeld `mc563885gzs27c5t9-63k636ttgm` . <br>Geef voor verouderd pakket op `www.exacttargetapis.com` . | Yes |
| clientId | De client-ID die is gekoppeld aan de Sales Force marketing Cloud-toepassing.  | Yes |
| clientSecret | Het client geheim dat is gekoppeld aan de Sales Force marketing Cloud-toepassing. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het geheim op te slaan in Azure Key Vault en de ADF Copy-activiteit van daaruit te halen bij het uitvoeren van een gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | No |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | No |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | No |

**Voor beeld: uitgebreide STS OAuth 2-verificatie gebruiken voor uitgebreid pakket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Voor beeld: OAuth 2-verificatie gebruiken voor verouderd pakket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Als u de gekoppelde service Sales Force marketing-Cloud met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u het nieuwe abonnement wilt gebruiken waarmee u een verbeterde pakket ondersteuning toevoegt.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Sales Force marketing Cloud DataSet.

Als u gegevens wilt kopiëren uit Sales Force marketing Cloud, stelt u de eigenschap type van de gegevensset in op **SalesforceMarketingCloudObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SalesforceMarketingCloudObject** | Yes |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Sales Force marketing Cloud-bron.

### <a name="salesforce-marketing-cloud-as-source"></a>Sales Force marketing Cloud als bron

Als u gegevens wilt kopiëren uit Sales Force marketing Cloud, stelt u het bron type in de Kopieer activiteit in op **SalesforceMarketingCloudSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **SalesforceMarketingCloudSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
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

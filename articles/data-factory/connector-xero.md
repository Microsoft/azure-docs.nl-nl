---
title: Gegevens kopiëren van Xero met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Xero naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jingwang
ms.openlocfilehash: 3f8c74f36c1c441e00b808954ce7f7710d3fbd52
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879962"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Gegevens kopiëren van Xero met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Xero te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Xero-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Xero kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze Xero-connector ondersteunt met name:

- OAuth 2,0 en OAuth 1,0-verificatie. Voor OAuth 1,0 biedt de connector ondersteuning voor Xero- [persoonlijke toepassingen](https://developer.xero.com/documentation/getting-started/getting-started-guide) , maar niet voor open bare toepassingen.
- Alle Xero-tabellen (API-eind punten), behalve ' rapporten '.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor Xero-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Xero gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Xero** | Ja |
| connectionProperties | Een groep eigenschappen die definieert hoe verbinding moet worden gemaakt met Xero. | Ja |
| **_Onder `connectionProperties` :_* _ | | |
| host | Het eind punt van de Xero-server ( `api.xero.com` ).  | Ja |
| authenticationType | Toegestane waarden zijn `OAuth_2.0` en `OAuth_1.0` . | Ja |
| consumerKey | Geef voor OAuth 2,0 de _ *client-id** op voor uw Xero-toepassing.<br>Geef voor OAuth 1,0 de consument sleutel op die is gekoppeld aan de Xero-toepassing.<br>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| privateKey | Geef voor OAuth 2,0 het **client geheim** op voor uw Xero-toepassing.<br>Voor OAuth 1,0 geeft u de persoonlijke sleutel op uit het. pem-bestand dat is gegenereerd voor uw persoonlijke Xero-toepassing. Zie [een openbaar/persoonlijk sleutel paar maken](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Opmerking voor **het genereren van privatekey. pem met numbits van 512** met `openssl genrsa -out privatekey.pem 512` , wordt 1024 niet ondersteund. Voeg alle tekst uit het. pem-bestand toe, inclusief de Unix-regel eindigt op (\n), zie voor beeld hieronder.<br/><br>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenantId | De Tenant-ID die is gekoppeld aan uw Xero-toepassing. Van toepassing op OAuth 2,0-verificatie.<br>Meer informatie over het ophalen van de Tenant-ID van [de tenants die u gemachtigd bent om toegang te krijgen](https://developer.xero.com/documentation/oauth2/auth-flow)tot de sectie. | Ja voor OAuth 2,0-verificatie |
| refreshToken | Van toepassing op OAuth 2,0-verificatie.<br/>Het OAuth 2,0-vernieuwings token is gekoppeld aan de Xero-toepassing en wordt gebruikt voor het vernieuwen van het toegangs token. het toegangs token verloopt na 30 minuten. Meer informatie over hoe de Xero-autorisatie stroom werkt en hoe u het vernieuwings token kunt ophalen uit [dit artikel](https://developer.xero.com/documentation/oauth2/auth-flow). Als u een vernieuwings token wilt ophalen, moet u het [offline_access bereik](https://developer.xero.com/documentation/oauth2/scopes)aanvragen. <br/>**Bekende beperking**: Opmerking Xero stelt het vernieuwings token opnieuw in nadat het is gebruikt voor het vernieuwen van het toegangs token. Voor een operationele werk belasting moet u voordat elke Kopieer activiteit wordt uitgevoerd, een geldig vernieuwings token instellen voor het gebruik van ADF.<br/>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja voor OAuth 2,0-verificatie |
| useEncryptedEndpoints | Hiermee geeft u op of de eind punten van de gegevens bron moeten worden versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee wordt aangegeven of de identiteit van de server moet worden gecontroleerd wanneer er verbinding wordt gemaakt via TLS. De standaardwaarde is waar.  | Nee |

**Voor beeld: OAuth 2,0-verificatie**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
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

**Voor beeld: OAuth 1,0-verificatie**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Voor beeld van een persoonlijke sleutel waarde:**

Voeg alle tekst uit het. pem-bestand toe, inclusief de Unix-regel eindigt op (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Xero-gegevensset.

Als u gegevens van Xero wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **XeroObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **XeroObject** | Ja |
| tableName | De naam van de tabel. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Xero-bron worden ondersteund.

### <a name="xero-as-source"></a>Xero als bron

Als u gegevens wilt kopiëren uit Xero, stelt u het bron type in de Kopieer activiteit in op **XeroSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **XeroSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Contacts"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Let op het volgende wanneer u de Xero-query opgeeft:

- Tabellen met complexe items worden op meerdere tabellen gesplitst. Bank transacties hebben bijvoorbeeld een complexe gegevens structuur ' regel items ', dus gegevens van Bank transacties worden toegewezen aan een tabel `Bank_Transaction` en `Bank_Transaction_Line_Items` , met `Bank_Transaction_ID` als refererende sleutel, om ze samen te koppelen.

- Xero-gegevens zijn beschikbaar via twee schema's: `Minimal` (standaard) en `Complete` . Het volledige schema bevat vereiste aanroep tabellen waarvoor extra gegevens (bijvoorbeeld ID-kolom) nodig zijn voordat de gewenste query wordt uitgevoerd.

De volgende tabellen hebben dezelfde informatie in het minimale en volledige schema. Gebruik Mini maal schema (standaard) om het aantal API-aanroepen te verminderen.

- Bank_Transactions
- Contact_Groups 
- Contactpersonen 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Facturen 
- Invoices_Credit_Notes
- Invoices_ voor uitbetalingen 
- Invoices_Overpayments 
- Manual_Journals 
- Overbetalingen 
- Overpayments_Allocations 
- Uitbetalingen 
- Prepayments_Allocations 
- Ontvangsten 
- Receipt_Validation_Errors 
- Tracking_Categories

In de volgende tabellen kunnen alleen query's met een volledig schema worden uitgevoerd:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ personen 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ betalingen 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met ondersteunde gegevens archieven door de Kopieer activiteit.

---
title: Gegevens kopiëren van Presto met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Presto naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 33e521d418c219be8eb85b79a0e07d999edb1b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374266"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Gegevens kopiëren van Presto met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Presto te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Presto-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Presto kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor Presto-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Presto gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Presto** | Yes |
| host | Het IP-adres of de hostnaam van de Presto-server. (bijvoorbeeld 192.168.222.160)  | Yes |
| serverVersion | De versie van de Presto-server. (bijvoorbeeld 0,148-t)  | Yes |
| catalogus | De context van de catalogus voor alle aanvragen bij de server.  | Yes |
| poort | De TCP-poort die de Presto-server gebruikt om te Luis teren naar client verbindingen. De standaard waarde is 8080.  | No |
| authenticationType | Het verificatie mechanisme dat wordt gebruikt om verbinding te maken met de Presto-server. <br/>Toegestane waarden zijn: **anoniem**, **LDAP** | Yes |
| gebruikersnaam | De gebruikers naam die wordt gebruikt om verbinding te maken met de Presto-server.  | No |
| wachtwoord | Het wacht woord dat overeenkomt met de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Hiermee geeft u op of de verbindingen met de server met behulp van TLS worden versleuteld. De standaardwaarde is false.  | No |
| trustedCertPath | Het volledige pad van het. pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken van verbinding via TLS. Deze eigenschap kan alleen worden ingesteld wanneer TLS op zelf-hostende IR wordt gebruikt. De standaard waarde is het cacerts. pem-bestand dat met de IR is geïnstalleerd.  | No |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken uit de systeem vertrouwens archief of vanuit een opgegeven PEM-bestand. De standaardwaarde is false.  | No |
| allowHostNameCNMismatch | Hiermee geeft u op of een door de certificerings instantie uitgegeven TLS/SSL-certificaat naam moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is false.  | No |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server mogen worden toegestaan. De standaardwaarde is false.  | No |
| timeZoneID | De lokale tijd zone die wordt gebruikt door de verbinding. Geldige waarden voor deze optie zijn opgegeven in de data base van de IANA-tijd zone. De standaard waarde is de systeem tijd zone.  | No |

**Voorbeeld:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Presto-gegevensset.

Als u gegevens van Presto wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **PrestoObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **PrestoObject** | Yes |
| schema | De naam van het schema. |Nee (als "query" in activiteit bron is opgegeven)  |
| tabel | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe werk belasting. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Presto-bron worden ondersteund.

### <a name="presto-as-source"></a>Presto als bron

Als u gegevens wilt kopiëren uit Presto, stelt u het bron type in de Kopieer activiteit in op **PrestoSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **PrestoSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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

---
title: Gegevens kopiëren van Phoenix met Azure Data Factory
description: Informatie over het kopiëren van gegevens van Phoenix naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: b0919e1da93f0cf0a5d27e541493b724d2fa5f0a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374317"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Gegevens kopiëren van Phoenix met Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van Phoenix te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Bredase connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Phoenix kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Bredase connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde services van Breda:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Phoenix** | Yes |
| host | Het IP-adres of de hostnaam van de Bredase server. (dat wil zeggen, 192.168.222.160)  | Yes |
| poort | De TCP-poort die de Phoenix-server gebruikt om te Luis teren naar client verbindingen. De standaard waarde is 8765. Als u verbinding maakt met Azure HDInsights, geeft u poort op als 443. | No |
| httpPath | De gedeeltelijke URL die overeenkomt met de Phoenix-server. (dat wil zeggen,/gateway/sandbox/Phoenix/Version). Geef op `/hbasephoenix0` of HDInsights-cluster wordt gebruikt.  | No |
| authenticationType | Het verificatie mechanisme dat wordt gebruikt om verbinding te maken met de Phoenix-server. <br/>Toegestane waarden zijn: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Yes |
| gebruikersnaam | De gebruikers naam die wordt gebruikt om verbinding te maken met de Bredase server.  | No |
| wachtwoord | Het wacht woord dat overeenkomt met de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Hiermee geeft u op of de verbindingen met de server met behulp van TLS worden versleuteld. De standaardwaarde is false.  | No |
| trustedCertPath | Het volledige pad van het. pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken van verbinding via TLS. Deze eigenschap kan alleen worden ingesteld wanneer TLS op zelf-hostende IR wordt gebruikt. De standaard waarde is het cacerts. pem-bestand dat met de IR is geïnstalleerd.  | No |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken uit de systeem vertrouwens archief of vanuit een opgegeven PEM-bestand. De standaardwaarde is false.  | No |
| allowHostNameCNMismatch | Hiermee geeft u op of een door de certificerings instantie uitgegeven TLS/SSL-certificaat naam moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is false.  | No |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server mogen worden toegestaan. De standaardwaarde is false.  | No |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

>[!NOTE]
>Als uw cluster geen plak sessie ondersteunt, bijvoorbeeld HDInsight, voegt u de knooppunt index expliciet toe aan het einde van de instelling van het HTTP-pad, bijvoorbeeld `/hbasephoenix0` in plaats van `/hbasephoenix` .

**Voorbeeld:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Phoenix-gegevensset.

Als u gegevens wilt kopiëren van Breda, stelt u de eigenschap type van de gegevensset in op **PhoenixObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **PhoenixObject** | Yes |
| schema | De naam van het schema. |Nee (als "query" in activiteit bron is opgegeven)  |
| tabel | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe werk belasting. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Phoenix-bron.

### <a name="phoenix-as-source"></a>Phoenix als bron

Als u gegevens wilt kopiëren van Breda, stelt u het bron type in de Kopieer activiteit in op **PhoenixSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **PhoenixSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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

---
title: Gegevens kopiëren van en naar ODBC-gegevens archieven met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van en naar ODBC-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 9b73e10b0ed539879e9a32d3961b6375828cc153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389617"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Gegevens kopiëren van en naar ODBC-gegevens archieven met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-odbc-connector.md)
> * [Huidige versie](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar een ODBC-gegevens archief. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze ODBC-Connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van een ODBC-bron naar een ondersteunde Sink-gegevens opslag kopiëren of vanuit elk ondersteund bron gegevens archief naar een ODBC-Sink kopiëren. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Met name deze ODBC-connector ondersteunt het kopiëren van gegevens van/naar **elk ODBC-compatibel gegevens archief** met behulp van **basis** -of **anonieme** verificatie. Er is een **64-bits ODBC-stuur programma** vereist. Voor ODBC-Sink ondersteunt ADF ODBC-versie 2,0-standaard.

## <a name="prerequisites"></a>Vereisten

Als u deze ODBC-Connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer het 64-bits ODBC-stuur programma voor het gegevens archief op de Integration Runtime computer.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor ODBC-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor ODBC-gekoppelde services:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **ODBC** | Ja |
| connectionString | De connection string het referentie gedeelte niet uitsluiten. U kunt de connection string met een patroon opgeven `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , of de systeem-DSN (gegevens bron naam) gebruiken die u hebt ingesteld op de Integration runtime machine met `"DSN=<name of the DSN on IR machine>;"` (u moet nog steeds het referentie deel opgeven in de gekoppelde service).<br>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie.| Yes |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met het ODBC-gegevens archief.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. | Yes |
| userName | Geef de gebruikers naam op als u basis verificatie gebruikt. | No |
| wachtwoord | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| referenties | Het deel van de toegangs referentie van de connection string dat is opgegeven in de eigenschaps waarde-indeling van het stuur programma. Bijvoorbeeld: `"RefreshToken=<secret refresh token>;"`. Dit veld markeren als SecureString. | No |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Yes |

**Voor beeld 1: basis verificatie gebruiken**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: anonieme verificatie gebruiken**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die door de ODBC-gegevensset worden ondersteund.

Als u gegevens wilt kopiëren van/naar een ODBC-compatibel gegevens archief, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **OdbcTable** | Yes |
| tableName | De naam van de tabel in het ODBC-gegevens archief. | Nee voor bron (als "query" in activiteits bron is opgegeven);<br/>Ja voor Sink |

**Voorbeeld**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die door de ODBC-bron worden ondersteund.

### <a name="odbc-as-source"></a>ODBC als bron

Als u gegevens wilt kopiëren vanuit een ODBC-compatibel gegevens archief, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **OdbcSource** | Yes |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

### <a name="odbc-as-sink"></a>ODBC als Sink

Als u gegevens wilt kopiëren naar een ODBC-compatibel gegevens archief, stelt u het sink-type in de Kopieer activiteit in op **OdbcSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op: **OdbcSink** | Yes |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br/>Toegestane waarden zijn: time span. Voor beeld: "00:30:00" (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt.<br/>Toegestane waarden zijn: geheel getal (aantal rijen). |Nee (standaard is 0-automatisch gedetecteerd) |
| preCopyScript |Geef een SQL-query voor de Kopieer activiteit op die moet worden uitgevoerd voordat u gegevens naar het gegevens archief in elke run schrijft. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. |No |

> [!NOTE]
> Als het ' writeBatchSize ' niet is ingesteld (automatisch gedetecteerd), detecteert de Kopieer activiteit eerst of het stuur programma batch bewerkingen ondersteunt, en stelt dit in 10000 op 1 als dit niet het geval is. Als u de waarde expliciet instelt op 0, wordt door de Kopieer activiteit de waarde nageleefd en mislukt de runtime als het stuur programma geen batch bewerkingen ondersteunt.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="troubleshoot-connectivity-issues"></a>Verbindingsproblemen oplossen

Gebruik het tabblad **Diagnostische gegevens** van **Integration Runtime Configuration Manager** om verbindings problemen op te lossen.

1. Start **Integration Runtime Configuration Manager**.
2. Schakel over naar het tabblad **Diagnostische gegevens** .
3. Selecteer in de sectie ' verbinding testen ' het **type** gegevens archief (gekoppelde service).
4. Geef de **Connection String** op die wordt gebruikt om verbinding te maken met het gegevens archief, kies de **verificatie** en voer de **gebruikers naam**, het **wacht woord** en/of de **referenties** in.
5. Klik op **verbinding testen** om de verbinding met het gegevens archief te testen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

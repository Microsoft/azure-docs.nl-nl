---
title: Gegevens kopiëren van een HTTP-bron met behulp van Azure Data Factory
description: Informatie over het kopiëren van gegevens uit een Cloud of een on-premises HTTP-bron naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 0462dac12d41fff667212902152b420d1460186d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383633"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Gegevens kopiëren van een HTTP-eind punt met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-http-connector.md)
> * [Huidige versie](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een HTTP-eind punt te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

Het verschil tussen deze HTTP-connector, de [rest-connector](connector-rest.md) en de [Web Table-connector](connector-web-table.md) zijn:

- **Rest-connector** biedt specifiek ondersteuning voor het kopiëren van gegevens uit rest-api's; 
- **Http-connector** is algemeen om gegevens op te halen uit een http-eind punt, bijvoorbeeld om het bestand te downloaden. Voordat REST-connector beschikbaar wordt, kunt u de HTTP-connector gebruiken om gegevens te kopiëren van de REST-API, die wordt ondersteund, maar minder functioneel is vergeleken met de REST-connector.
- Met **Web Table connector** wordt tabel inhoud geëxtraheerd van een HTML-webpagina.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze HTTP-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van een HTTP-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

U kunt deze HTTP-connector gebruiken voor het volgende:

- Gegevens ophalen van een HTTP/S-eind punt met behulp van de HTTP **Get** -of **post** -methoden.
- Gegevens ophalen met behulp van een van de volgende authenticaties: **Anonymous**, **Basic**, **Digest**, **Windows** of **ClientCertificate**.
- Kopieer het HTTP-antwoord alsof het is of Parset het met behulp van [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Als u een HTTP-aanvraag voor het ophalen van gegevens wilt testen voordat u de HTTP-connector in Data Factory configureert, kunt u meer informatie vinden over de API-specificatie voor vereisten voor koptekst en hoofd tekst. U kunt de hulpprogram ma's zoals postman of een webbrowser gebruiken om te valideren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de HTTP-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de HTTP-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **HttpServer**. | Yes |
| url | De basis-URL naar de webserver. | Yes |
| enableServerCertificateValidation | Geef op of de TLS/SSL-certificaat validatie van de server moet worden ingeschakeld wanneer u verbinding maakt met een HTTP-eind punt. Als uw HTTPS-server een zelfondertekend certificaat gebruikt, stelt u deze eigenschap in op **Onwaar**. | No<br /> (de standaard waarde is **True**) |
| authenticationType | Hiermee geeft u het verificatie type op. Toegestane waarden zijn **anoniem**, **basis**, **Digest**, **Windows** en **ClientCertificate**. <br><br> Zie de secties die volgen op deze tabel voor meer eigenschappen en JSON-voor beelden voor deze verificatie typen. | Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

### <a name="using-basic-digest-or-windows-authentication"></a>Basis verificatie, verificatie samenvatting of Windows-authenticatie gebruiken

Stel de eigenschap **authenticationType** in op **Basic**, **Digest** of **Windows**. Naast de algemene eigenschappen die in de voor gaande sectie worden beschreven, geeft u de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruikers naam die moet worden gebruikt voor toegang tot het HTTP-eind punt. | Yes |
| wachtwoord | Het wacht woord voor de gebruiker (de waarde van de **gebruikers naam** ). Markeer dit veld als **SecureString** -type om het veilig op te slaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Voorbeeld**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>Gebruik van ClientCertificate-verificatie

Als u ClientCertificate-verificatie wilt gebruiken, stelt u de eigenschap **authenticationType** in op **ClientCertificate**. Naast de algemene eigenschappen die in de voor gaande sectie worden beschreven, geeft u de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| embeddedCertData | Met base64 gecodeerde certificaat gegevens. | Geef **embeddedCertData** of **certThumbprint** op. |
| certThumbprint | De vinger afdruk van het certificaat dat is geïnstalleerd op uw zelf-hostende certificaat archief van Integration Runtime computer. Is alleen van toepassing wanneer het zelf-hostende type Integration Runtime is opgegeven in de eigenschap **connectVia** . | Geef **embeddedCertData** of **certThumbprint** op. |
| wachtwoord | Het wacht woord dat is gekoppeld aan het certificaat. Markeer dit veld als **SecureString** -type om het veilig op te slaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |

Als u **certThumbprint** gebruikt voor verificatie en het certificaat is geïnstalleerd in het persoonlijke archief van de lokale computer, verleent u lees machtigingen voor de zelf-hostende Integration runtime:

1. Open de micro soft Management Console (MMC). Voeg de module **certificaten** toe die gericht is op de **lokale computer**.
2. Vouw **certificaten**  >  **persoonlijk** uit en selecteer vervolgens **certificaten**.
3. Klik met de rechter muisknop op het certificaat in het persoonlijke archief en selecteer vervolgens **alle taken**  >  **persoonlijke sleutels beheren**.
3. Voeg op het tabblad **beveiliging** het gebruikers account toe waaronder de Integration runtime host-service (DIAHostService) wordt uitgevoerd, met lees toegang tot het certificaat.

**Voor beeld 1: certThumbprint gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: embeddedCertData gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HTTP onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap    | Beschrijving                                                  | Vereist |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | De eigenschap type onder `location` in DataSet moet worden ingesteld op **HttpServerLocation**. | Yes      |
| relativeUrl | Een relatieve URL naar de resource die de gegevens bevat. De HTTP-connector kopieert gegevens van de gecombineerde URL: `[URL specified in linked service][relative URL specified in dataset]` .   | No       |

> [!NOTE]
> De ondersteunde Payload-grootte van de HTTP-aanvraag is ongeveer 500 KB. Als de payload-grootte die u wilt door geven aan uw web-eind punt groter is dan 500 KB, kunt u de payload in kleinere segmenten batcheren.

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de HTTP-bron worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="http-as-source"></a>HTTP als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HTTP onder `storeSettings` instellingen in een op indeling gebaseerde kopie bron:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **HttpReadSettings**. | Yes      |
| requestMethod            | De HTTP-methode. <br>Toegestane waarden zijn **Get** (standaard) en **post**. | No       |
| addtionalHeaders         | Aanvullende HTTP-aanvraag headers.                             | No       |
| requestBody              | De hoofd tekst van de HTTP-aanvraag.                               | No       |
| httpRequestTimeout           | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. De standaard waarde is **00:01:40**. | No       |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | No       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
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

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **HttpFile**. | Yes |
| relativeUrl | Een relatieve URL naar de resource die de gegevens bevat. Als deze eigenschap niet is opgegeven, wordt alleen de URL gebruikt die in de definitie van de gekoppelde service is opgegeven. | No |
| requestMethod | De HTTP-methode. Toegestane waarden zijn **Get** (standaard) en **post**. | No |
| additionalHeaders | Aanvullende HTTP-aanvraag headers. | No |
| requestBody | De hoofd tekst van de HTTP-aanvraag. | No |
| indeling | Als u gegevens wilt ophalen uit het HTTP-eind punt zonder te parseren, en vervolgens de gegevens naar een op bestanden gebaseerde opslag kopie wilt kopiëren, slaat u de sectie **opmaak** in zowel de definitie van de invoer-als uitvoer gegevensset over.<br/><br/>Als u de inhoud van het HTTP-antwoord tijdens het kopiëren wilt parseren, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat** en **ParquetFormat**. Stel onder **indeling** de eigenschap **type** in op een van deze waarden. Zie [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, Orc- [indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en Parquet- [indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)voor meer informatie. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/><br/>Ondersteunde typen: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**.<br/>Ondersteunde niveaus:  **optimaal** en **snelst**. |No |

> [!NOTE]
> De ondersteunde Payload-grootte van de HTTP-aanvraag is ongeveer 500 KB. Als de payload-grootte die u wilt door geven aan uw web-eind punt groter is dan 500 KB, kunt u de payload in kleinere segmenten batcheren.

**Voor beeld 1: de Get-methode gebruiken (standaard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Voor beeld 2: de post-methode gebruiken**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **http**. | Yes |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. De standaard waarde is **00:01:40**.  | No |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.

---
title: Gegevens kopiëren van/naar Azure File Storage
description: Informatie over het kopiëren van gegevens van Azure File Storage naar ondersteunde Sink-gegevens archieven (of) van ondersteunde brongegevens archieven naar Azure File Storage met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 1046aee7eea406cce1141382294a70c7eb2f62d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597535"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Gegevens kopiëren vanuit of naar Azure File Storage met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens kopieert vanuit en naar Azure File Storage. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure File Storage-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

U kunt gegevens van Azure-File Storage kopiëren naar elk ondersteund Sink-gegevens archief of gegevens uit een ondersteund bron gegevens archief kopiëren naar Azure File Storage. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Deze Azure File Storage-connector ondersteunt met name:

- Kopiëren van bestanden met behulp van account sleutel of SAS-verificatie (Shared Access Signature).
- Kopiëren van bestanden als-is of parseren/genereren van bestanden met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure File Storage.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

Deze Azure File Storage-connector ondersteunt de volgende verificatie typen. Raadpleeg de bijbehorende gedeelten voor meer informatie.

- [Verificatie van account sleutel](#account-key-authentication)
- [Verificatie van de Shared Access-hand tekening](#shared-access-signature-authentication)

>[!NOTE]
> Als u Azure File Storage gekoppelde service met een [verouderd model](#legacy-model)gebruikt, waarbij de gebruikers interface van ADF die wordt weer gegeven als basis verificatie, nog steeds wordt ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe model te gebruiken. Het verouderde model brengt gegevens over van/naar opslag via SMB (Server Message Block), terwijl het nieuwe model gebruikmaakt van de opslag-SDK met een betere door voer. Als u een upgrade wilt uitvoeren, kunt u de gekoppelde service bewerken om de verificatie methode te wijzigen in ' account sleutel ' of ' SAS-URI '; Er is geen wijziging nodig voor de gegevensset-of kopieer activiteit.

### <a name="account-key-authentication"></a>Verificatie van account sleutel

Data Factory ondersteunt de volgende eigenschappen voor de sleutel verificatie van de Azure File Storage-account:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureFileStorage**. | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met Azure File Storage. <br/> U kunt ook de account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de Connection String halen. Zie voor meer informatie de volgende voor beelden en de [referenties van de winkel in azure Key Vault](store-credentials-in-key-vault.md) artikel. |Yes |
| Bestands share | Geef de bestands share op. | Yes |
| momentopname | Geef de datum van de [moment opname van de bestands share](../storage/files/storage-snapshots-files.md) op als u wilt kopiëren uit een moment opname. | No |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net;",
            "fileShare": "<file share name>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Sla de account sleutel op in Azure Key Vault**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "fileShare": "<file share name>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Verificatie van de Shared Access-hand tekening

Een SAS (een handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot resources in uw opslagaccount. U kunt een gedeelde hand tekening voor toegang gebruiken om een opgegeven periode een client met beperkte machtigingen te verlenen aan objecten in uw opslag account. Zie voor meer informatie over hand tekeningen voor gedeelde toegang, [Shared Access Signatures: inzicht in het model voor de Shared Access-hand tekening](../storage/common/storage-sas-overview.md).

Data Factory ondersteunt de volgende eigenschappen voor het gebruik van verificatie met een gedeelde toegangs handtekening:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureFileStorage**. | Ja |
| sasUri | Geef de URI van de Shared Access-hand tekening op voor de resources. <br/>Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory. U kunt ook de SAS-token in Azure Key Vault plaatsen om automatische rotatie te gebruiken en het token gedeelte te verwijderen. Voor meer informatie raadpleegt u de volgende voor beelden en [slaat u referenties op in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| Bestands share | Geef de bestands share op. | Yes |
| momentopname | Geef de datum van de [moment opname van de bestands share](../storage/files/storage-snapshots-files.md) op als u wilt kopiëren uit een moment opname. | No |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the resource e.g. https://<accountname>.file.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            },
            "fileShare": "<file share name>",
            "snapshot": "<snapshot version>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Sla de account sleutel op in Azure Key Vault**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.file.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="legacy-model"></a>Verouderd model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureFileStorage**. | Yes |
| host | Hiermee geeft u het Azure File Storage-eind punt op als: <br/>-Gebruikers interface gebruiken: opgeven `\\<storage name>.file.core.windows.net\<file service name>`<br/>-JSON gebruiken: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"` . | Yes |
| userid | Geef de gebruiker op om toegang te krijgen tot de Azure-File Storage als: <br/>-Gebruikers interface gebruiken: opgeven `AZURE\<storage name>`<br/>-JSON gebruiken: `"userid": "AZURE\\<storage name>"` . | Yes |
| wachtwoord | Geef de toegangs sleutel voor opslag op. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee voor bron, ja voor Sink |

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type onder `location` in DataSet moet worden ingesteld op **AzureFileStorageLocation**. | Yes      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u de instellingen voor de activiteit bron op. | No       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u Joker tekens wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de instellingen van de activiteit bron op. | No       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureFileStorageLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Azure File Storage bron en Sink.

### <a name="azure-file-storage-as-source"></a>Azure File Storage als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `storeSettings` instellingen in een op indeling gebaseerde kopie bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureFileStorageReadSettings**. | Yes                                           |
| ***Zoek de bestanden die moeten worden gekopieerd:*** |  |  |
| OPTIE 1: statisch pad<br> | Kopiëren uit de opgegeven map of het opgegeven bestandspad in de gegevensset. Als u alle bestanden uit een map wilt kopiëren, moet u ook opgeven `wildcardFileName` als `*` . |  |
| OPTIE 2: voor voegsel van bestand<br>-voor voegsel | Voor voegsel voor de bestands naam onder de opgegeven bestands share die is geconfigureerd in een gegevensset om bron bestanden te filteren. Bestanden waarvan de naam begint met `fileshare_in_linked_service/this_prefix` zijn geselecteerd. Er wordt gebruikgemaakt van het filter aan de service zijde voor Azure File Storage, dat betere prestaties biedt dan een filter voor joker tekens. Deze functie wordt niet ondersteund bij het gebruik van een [verouderd gekoppeld service model](#legacy-model). | No                                                          |
| OPTIE 3: Joker teken<br>- wildcardFolderPath | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | No                                            |
| OPTIE 3: Joker teken<br>- wildcardFileName | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruik `^` om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Yes |
| OPTIE 4: een lijst met bestanden<br>- fileListPath | Hiermee wordt aangegeven dat een opgegeven bestandenset moet worden gekopieerd. Wijs naar een tekst bestand met een lijst met bestanden die u wilt kopiëren, één bestand per regel, het relatieve pad naar het pad dat is geconfigureerd in de gegevensset.<br/>Wanneer u deze optie gebruikt, geeft u geen bestands naam op in DataSet. Meer voor beelden vindt u in [voor beelden van bestands lijsten](#file-list-examples). |No |
| ***Aanvullende instellingen:*** |  | |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. <br>Toegestane waarden zijn **True** (standaard) en **Onwaar**.<br>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . |No |
| deleteFilesAfterCompletion | Hiermee wordt aangegeven of de binaire bestanden uit het bron archief worden verwijderd nadat naar het doel archief is verplaatst. Het verwijderen van bestanden is per bestand, dus wanneer de Kopieer activiteit mislukt, ziet u dat er al een aantal bestanden naar het doel is gekopieerd en verwijderd uit de bron, terwijl andere nog steeds in het bron archief blijven staan. <br/>Deze eigenschap is alleen geldig in het scenario voor het kopiëren van binaire bestanden. De standaard waarde is False. |No |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. <br>De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd.<br/>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | No                                            |
| enablePartitionDiscovery | Geef voor bestanden die zijn gepartitioneerd op of de partities moeten worden geparseerd uit het bestandspad en voeg deze toe als aanvullende bron kolommen.<br/>Toegestane waarden zijn **False** (standaard) en **waar**. | No                                            |
| partitionRootPath | Wanneer partitie detectie is ingeschakeld, geeft u het absolute hoofdpad op om gepartitioneerde mappen te lezen als gegevens kolommen.<br/><br/>Als deze niet is opgegeven, wordt standaard<br/>-Als u het bestandspad in de gegevensset of lijst met bestanden op de bron gebruikt, is het basispad het pad dat is geconfigureerd in de gegevensset.<br/>-Wanneer u filter voor de map met Joker tekens gebruikt, is het pad van de partitie hoofdmap het pad vóór het eerste Joker teken.<br/><br/>Als u bijvoorbeeld het pad in gegevensset configureert als ' hoofdmap/map/jaar = 2020/maand = 08/dag = 27 ':<br/>-Als u basispad opgeeft als ' hoofdmap/map/jaar = 2020 ', worden met de Kopieer activiteit nog twee kolommen `month` en `day` met de waarde ' 08 ' en ' 27 ' gegenereerd, naast de kolommen in de bestanden.<br/>-Als het basispad niet is opgegeven, wordt er geen extra kolom gegenereerd. | No                                            |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No                                            |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "AzureFileStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-file-storage-as-sink"></a>Azure File Storage als Sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `storeSettings` instellingen in op indeling gebaseerde kopie-Sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureFileStorageWriteSettings**. | Yes      |
| copyBehavior             | Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron bestanden van een gegevens archief op basis van een bestand zijn.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders is het een automatisch gegenereerde bestands naam. | No       |
| maxConcurrentConnections | De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureFileStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursieve | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leeg, standaard instelling gebruiken) | onjuist | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (leeg, standaard instelling gebruiken) | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | onjuist | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Voor beelden van bestands lijst

In deze sectie wordt het resulterende gedrag van het gebruik van het pad naar een bestands lijst in de bron van de Kopieer activiteit beschreven.

Ervan uitgaande dat u de volgende structuur van de bronmap hebt en de bestanden vet wilt kopiëren:

| Voorbeeld bron structuur                                      | Inhoud in FileListToCopy.txt                             | ADF-configuratie                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| basis<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metagegevensarchiefmethode<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **In gegevensset:**<br>-Mappad: `root/FolderA`<br><br>**Bron van Kopieer activiteit:**<br>-Pad naar bestands lijst: `root/Metadata/FileListToCopy.txt` <br><br>Het pad naar de bestands lijst verwijst naar een tekst bestand in hetzelfde gegevens archief dat een lijst bevat met bestanden die u wilt kopiëren, één bestand per regel met het relatieve pad naar het pad dat is geconfigureerd in de gegevensset. |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior-voor beelden

In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van recursieve en copyBehavior waarden beschreven.

| recursieve | copyBehavior | Structuur van bronmap | Resulterende doel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + Bestand2 + File3 + File4 + inhoud van bestand 5 worden samengevoegd tot één bestand met automatisch gegenereerde bestands naam |
| onjuist |preserveHierarchy | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |flattenHierarchy | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;De inhoud van bestand1 + Bestand2 wordt samengevoegd met een bestand met automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor bestand1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Als u meer wilt weten over de eigenschappen, controleert u de [activiteit verwijderen](delete-activity.md)

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **file share** |Yes |
| folderPath | Pad naar de map. <br/><br/>Het Joker teken filter wordt ondersteund, toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Yes |
| fileName | De **naam of het Joker teken** voor het bestand (en) onder de opgegeven folderPath. Als u geen waarde opgeeft voor deze eigenschap, wijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor het filter zijn toegestane joker tekens: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"fileName": "*.csv"`<br/>-Voor beeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de bestands naam met het volgende patroon: "*gegevens. [ Run ID-GUID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]*', bijvoorbeeld ' Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz '; Als u vanuit tabellaire bron kopieert met behulp van de tabel naam in plaats van een query, is het naam patroon '*[tabel naam]. [ indeling]. [compressie indien geconfigureerd]*", bijvoorbeeld" MyTable.csv ". |No |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd.| No |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd.| No |
| indeling | Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en Parquet- [indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **optimaal** en **snelst**. |No |

>[!TIP]
>Als u alle bestanden in een map wilt kopiëren, geeft u alleen **FolderPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **FolderPath** op met een map en een **Bestands** naam met een bestand.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van de map en de **Bestands naam** met het Joker teken filter.

>[!NOTE]
>Als u de eigenschap File filter gebruikt voor het bestand, wordt deze nog steeds ondersteund als-is, terwijl u de nieuwe filter mogelijkheid wilt gebruiken die wordt toegevoegd aan "bestands naam" naar voren.

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **FileSystemSource** |Yes |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Opmerking Wanneer recursief is ingesteld op True en Sink is op bestanden gebaseerd archief, wordt lege map/submap niet gekopieerd/gemaakt bij sink.<br/>Toegestane waarden zijn: **True** (standaard), **False** | No |
| maxConcurrentConnections | De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Sink model voor verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op: **FileSystemSink** |Yes |
| copyBehavior | Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron bestanden van een gegevens archief op basis van een bestand zijn.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden van de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben een automatisch gegenereerde naam. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. anders is de bestands naam automatisch gegenereerd. | No |
| maxConcurrentConnections | De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
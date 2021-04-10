---
title: Gegevens kopiëren van de Amazon Simple Storage-service (S3)
description: Meer informatie over het kopiëren van gegevens van de Amazon Simple Storage-service (S3) naar ondersteunde Sink-gegevens archieven met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 03b0cd852f34e115cc5bbc60448e45fcbb680474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601210"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Gegevens kopiëren uit de Amazon Simple Storage-service met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
>
> * [Versie 1:](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Huidige versie](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens kopieert van de Amazon Simple Storage-service (Amazon S3). Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

>[!TIP]
>Zie [Azure Data Factory gebruiken om gegevens van Amazon S3 te migreren naar Azure Storage](data-migration-guidance-s3-azure-storage.md)voor meer informatie over het gegevens migratie scenario van amazon s3 naar Azure Storage.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Amazon S3-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name deze Amazon S3-connector ondersteunt het kopiëren van bestanden in de vorm van of het parseren van bestanden met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md). U kunt er ook voor kiezen om de [meta gegevens van een bestand te bewaren tijdens het kopiëren](#preserve-metadata-during-copy). De connector gebruikt [AWS Signature versie 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) om aanvragen voor S3 te verifiëren.

>[!TIP]
>U kunt deze Amazon S3-connector gebruiken om gegevens te kopiëren van *elke S3-compatibele opslag provider*, zoals [Google Cloud Storage](connector-google-cloud-storage.md). Geef de corresponderende service-URL op in de configuratie van de gekoppelde service.

## <a name="required-permissions"></a>Vereiste machtigingen

Als u gegevens wilt kopiëren uit Amazon S3, moet u ervoor zorgen dat u de volgende machtigingen hebt gekregen voor de operations van het Amazon S3-object: `s3:GetObject` en `s3:GetObjectVersion` .

Als u Data Factory gebruikers interface maakt, zijn er extra `s3:ListAllMyBuckets` en `s3:ListBucket` / `s3:GetBucketLocation` machtigingen vereist voor bewerkingen, zoals het testen van de verbinding met de gekoppelde service en het bladeren vanuit de hoofdmap. Als u deze machtigingen niet wilt verlenen, kunt u de opties verbinding met bestandspad testen of bladeren vanuit opgegeven pad selecteren in de gebruikers interface.

Zie [machtigingen opgeven in een beleid](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) op de AWS-site voor een volledige lijst met Amazon S3-machtigingen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Amazon S3.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde service van Amazon S3:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **AmazonS3**. | Yes |
| authenticationType | Geef het verificatie type op dat wordt gebruikt om verbinding te maken met Amazon S3. U kunt kiezen voor het gebruik van toegangs sleutels voor een account voor AWS identiteits-en toegangs beheer (IAM) of [tijdelijke beveiligings referenties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).<br>Toegestane waarden zijn: `AccessKey` (standaard) en `TemporarySecurityCredentials` . |No |
| accessKeyId | ID van de geheime toegangs sleutel. |Yes |
| secretAccessKey | De geheime toegangs sleutel zelf. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| sessionToken | Van toepassing bij gebruik van verificatie van [tijdelijke beveiligings referenties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) . Meer informatie over het [aanvragen van tijdelijke beveiligings referenties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) van AWS.<br>Opmerking AWS tijdelijke referentie verloopt tussen 15 minuten en 36 uur op basis van instellingen. Zorg ervoor dat uw referenties geldig zijn wanneer de activiteit wordt uitgevoerd, met name voor het uitvoeren van een operationele werk belasting, bijvoorbeeld, kunt u deze regel matig vernieuwen en opslaan in Azure Key Vault.<br>Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |No |
| serviceUrl | Geef het aangepaste S3-eind punt op als u gegevens kopieert van een andere S3-compatibele opslag provider dan de officiële service van Amazon S3. Als u bijvoorbeeld gegevens uit Google Cloud Storage wilt kopiëren, geeft u op `https://storage.googleapis.com` . | No |
| forcePathStyle | Hiermee wordt aangegeven of S3 [-paden](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) worden gebruikt in plaats van Virtual hosted-Style Access. Toegestane waarden zijn: **False** (standaard), **True**.<br>Als u verbinding maakt met een S3-compatibele opslag provider dan de officiële service van de Amazon S3, en dit gegevens archief vereist toegang tot een pad-stijl (bijvoorbeeld [Oracle-Cloud opslag](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)), stelt u deze eigenschap in op waar. Controleer de documentatie van elke gegevens opslag op als er wel of geen toegang tot het pad is vereist. |No |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of de zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als deze eigenschap niet is opgegeven, gebruikt de service de standaard Azure Integration runtime. |No |

>[!TIP]
>Geef de URL van de aangepaste S3-service op als u gegevens wilt kopiëren uit een S3-compatibele opslag, anders dan de officiële service van Amazon S3.

**Voor beeld: toegangs sleutel verificatie gebruiken**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: verificatie van tijdelijke beveiligings referenties gebruiken**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

De volgende eigenschappen worden ondersteund voor Amazon S3 onder `location` instellingen in een gegevensset op basis van een indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap **type** onder `location` in een gegevensset moet worden ingesteld op **AmazonS3Location**. | Yes      |
| Bucket | De naam van de S3-Bucket.                                          | Yes      |
| folderPath | Het pad naar de map onder de opgegeven Bucket. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op dat in de bron instellingen voor de activiteit. | No       |
| fileName   | De bestands naam onder de opgegeven Bucket en mappad. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op dat in de bron instellingen voor de activiteit. | No       |
| versie | De versie van het S3-object, als S3-versie beheer is ingeschakeld. Als deze niet is opgegeven, wordt de meest recente versie opgehaald. |No |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die door de Amazon S3-bron worden ondersteund.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 als bron type

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Amazon S3 onder `storeSettings` instellingen in een op indeling gebaseerde kopie bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | De eigenschap **type** onder `storeSettings` moet worden ingesteld op **AmazonS3ReadSettings**. | Yes                                                         |
| ***Zoek de bestanden die moeten worden gekopieerd:*** |  |  |
| OPTIE 1: statisch pad<br> | Kopiëren van de opgegeven Bucket of map/bestandspad die is opgegeven in de gegevensset. Als u alle bestanden van een Bucket of map wilt kopiëren, moet u ook opgeven `wildcardFileName` als `*` . |  |
| OPTIE 2: S3-voor voegsel<br>-voor voegsel | Voor voegsel voor de S3-sleutel naam onder de opgegeven Bucket die in een gegevensset is geconfigureerd voor het filteren van S3-bron bestanden. S3-sleutels waarvan de naam begint met `bucket_in_dataset/this_prefix` zijn geselecteerd. Er wordt gebruikgemaakt van S3's service-side filter, dat betere prestaties levert dan een filter voor joker tekens.<br/><br/>Als u een voor voegsel gebruikt en wilt kopiëren naar een op een bestand gebaseerde Sink met behoud van de hiërarchie, noteert u het pad na de laatste "/" in het voor voegsel blijven behouden. U hebt bijvoorbeeld  `bucket/folder/subfolder/file.txt` de bron en het voor voegsel geconfigureerd als `folder/sub` . vervolgens wordt het pad naar het bestand behouden `subfolder/file.txt` . | No |
| OPTIE 3: Joker teken<br>- wildcardFolderPath | Het mappad met Joker tekens onder de opgegeven Bucket die is geconfigureerd in een gegevensset om bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` dit om te escapen als uw mapnaam een Joker teken of escape tekens bevat in. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | No                                            |
| OPTIE 3: Joker teken<br>- wildcardFileName | De bestands naam met Joker tekens onder de opgegeven Bucket en mappad (of pad naar de map met Joker tekens) om bron bestanden te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` dit om te escapen als uw bestands naam een Joker teken of escape tekens bevat in.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Yes |
| OPTIE 4: een lijst met bestanden<br>- fileListPath | Hiermee wordt aangegeven dat een opgegeven bestandenset moet worden gekopieerd. Wijs naar een tekst bestand met een lijst met bestanden die u wilt kopiëren, één bestand per regel, het relatieve pad naar het pad dat is geconfigureerd in de gegevensset.<br/>Wanneer u deze optie gebruikt, geeft u geen bestands naam op in de gegevensset. Meer voor beelden vindt u in [voor beelden van bestands lijsten](#file-list-examples). |No |
| ***Aanvullende instellingen:*** |  | |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer **recursief** is ingesteld op **True** en de Sink een archief op basis van bestanden is, een lege map of submap niet wordt gekopieerd of gemaakt bij de sink. <br>Toegestane waarden zijn **True** (standaard) en **Onwaar**.<br>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . |No |
| deleteFilesAfterCompletion | Hiermee wordt aangegeven of de binaire bestanden uit het bron archief worden verwijderd nadat naar het doel archief is verplaatst. Het verwijderen van bestanden is per bestand, dus wanneer de Kopieer activiteit mislukt, ziet u dat er al een aantal bestanden naar het doel is gekopieerd en verwijderd uit de bron, terwijl andere nog steeds in het bron archief blijven staan. <br/>Deze eigenschap is alleen geldig in het scenario voor het kopiëren van binaire bestanden. De standaard waarde is False. |No |
| modifiedDatetimeStart    | Bestanden worden gefilterd op basis van het kenmerk: laatst gewijzigd. <br>De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op een UTC-tijd zone met de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen **Null** zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar is **Null**, worden de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk aan de datum/tijd-waarde, geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is **Null**, worden de bestanden waarvan het kenmerk laatst gewijzigd is kleiner dan de datum/tijd-waarde, geselecteerd.<br/>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | No                                                          |
| enablePartitionDiscovery | Geef voor bestanden die zijn gepartitioneerd op of de partities moeten worden geparseerd uit het bestandspad en voeg deze toe als aanvullende bron kolommen.<br/>Toegestane waarden zijn **False** (standaard) en **waar**. | No                                            |
| partitionRootPath | Wanneer partitie detectie is ingeschakeld, geeft u het absolute hoofdpad op om gepartitioneerde mappen te lezen als gegevens kolommen.<br/><br/>Als deze niet is opgegeven, wordt standaard<br/>-Als u het bestandspad in de gegevensset of lijst met bestanden op de bron gebruikt, is het basispad het pad dat is geconfigureerd in de gegevensset.<br/>-Wanneer u filter voor de map met Joker tekens gebruikt, is het pad van de partitie hoofdmap het pad vóór het eerste Joker teken.<br/>-Als u voor voegsel gebruikt, is het pad van de partitie hoofdmap een subpad voor het laatste/. <br/><br/>Als u bijvoorbeeld het pad in gegevensset configureert als ' hoofdmap/map/jaar = 2020/maand = 08/dag = 27 ':<br/>-Als u basispad opgeeft als ' hoofdmap/map/jaar = 2020 ', worden met de Kopieer activiteit nog twee kolommen `month` en `day` met de waarde ' 08 ' en ' 27 ' gegenereerd, naast de kolommen in de bestanden.<br/>-Als het basispad niet is opgegeven, wordt er geen extra kolom gegenereerd. | No                                            |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No                                                          |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| verzamelingen | sleutel | recursieve | De structuur van de bronmap en het filter resultaat (vetgedrukte bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| verzamelingen | `Folder*/*` | onjuist | verzamelingen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| verzamelingen | `Folder*/*` | true | verzamelingen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| verzamelingen | `Folder*/*.csv` | onjuist | verzamelingen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| verzamelingen | `Folder*/*.csv` | true | verzamelingen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Voor beelden van bestands lijst

In deze sectie wordt het resulterende gedrag van het gebruik van een bestands lijst pad in een bron van een Kopieer activiteit beschreven.

Stel dat u de volgende structuur van de bronmap hebt en de bestanden vet wilt kopiëren:

| Voorbeeld bron structuur                                      | Inhoud in FileListToCopy.txt                             | Data Factory configuratie                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| verzamelingen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metagegevensarchiefmethode<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **In gegevensset:**<br>Verzamelingen `bucket`<br>-Mappad: `FolderA`<br><br>**Bron van Kopieer activiteit:**<br>-Pad naar bestands lijst: `bucket/Metadata/FileListToCopy.txt` <br><br>Het pad naar de bestands lijst verwijst naar een tekst bestand in hetzelfde gegevens archief dat een lijst bevat met bestanden die u wilt kopiëren, één bestand per regel, met het relatieve pad naar het pad dat is geconfigureerd in de gegevensset. |

## <a name="preserve-metadata-during-copy"></a>Meta gegevens bewaren tijdens kopiëren

Wanneer u bestanden kopieert van Amazon S3 naar Azure Data Lake Storage Gen2 of Azure Blob-opslag, kunt u ervoor kiezen om de meta gegevens van het bestand te behouden, samen met de gegevens. Meer informatie over het [bewaren van meta gegevens](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Als u meer wilt weten over de eigenschappen, controleert u de [activiteit verwijderen](delete-activity.md).

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor achterwaartse compatibiliteit. We raden u aan het nieuwe model te gebruiken dat eerder is beschreven. De gebruikers interface voor het Data Factory-ontwerp is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **AmazonS3Object**. |Yes |
| Bucket | De naam van de S3-Bucket. Het Joker teken filter wordt niet ondersteund. |Ja voor de activiteit kopiëren of opzoeken, geen voor de activiteit GetMetadata |
| sleutel | De naam of het Joker teken filter van de S3-object sleutel onder de opgegeven Bucket. Is alleen van toepassing wanneer de eigenschap **prefix** niet is opgegeven. <br/><br/>Het Joker teken filter wordt ondersteund voor zowel het deel van de map als het bestands naam gedeelte. Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Voor beeld 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zie meer voor [beelden van mappen en bestands filters](#folder-and-file-filter-examples). Gebruik `^` dit om te escapen als uw daad werkelijke map of bestands naam een Joker teken of escape tekens bevat. |No |
| beleids | Voor voegsel voor de object sleutel S3. Objecten waarvan de sleutels beginnen met dit voor voegsel worden geselecteerd. Is alleen van toepassing als de **sleutel** eigenschap niet is opgegeven. |No |
| versie | De versie van het S3-object, als S3-versie beheer is ingeschakeld. Als er geen versie is opgegeven, wordt de meest recente versie opgehaald. |No |
| modifiedDatetimeStart | Bestanden worden gefilterd op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat het inschakelen van deze instelling van invloed is op de algehele prestaties van de verplaatsing van gegevens wanneer u grote hoeveel heden bestanden wilt filteren. <br/><br/> De eigenschappen kunnen **Null** zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar is **Null**, worden de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk aan de datum/tijd-waarde, geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, worden de bestanden waarvan het kenmerk laatst gewijzigd is kleiner dan de datum/tijd-waarde, geselecteerd.| No |
| modifiedDatetimeEnd | Bestanden worden gefilterd op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat het inschakelen van deze instelling van invloed is op de algehele prestaties van de verplaatsing van gegevens wanneer u grote hoeveel heden bestanden wilt filteren. <br/><br/> De eigenschappen kunnen **Null** zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar is **Null**, worden de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk aan de datum/tijd-waarde, geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is **Null**, worden de bestanden waarvan het kenmerk laatst gewijzigd is kleiner dan de datum/tijd-waarde, geselecteerd.| No |
| indeling | Als u bestanden wilt kopiëren als zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak over in de gegevensset voor invoer en uitvoer.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder **indeling** in op een van deze waarden. Zie de secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet-indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **gzip**, **Deflate**, **bzip2** en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst**. |No |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **Bucket** op voor de Bucket en het voor **voegsel** voor het deel van de map.
>
>Als u één bestand met een opgegeven naam wilt kopiëren, geeft u **Bucket** op voor de Bucket en de **sleutel** voor de map plus de bestands naam.
>
>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **Bucket** op voor de Bucket en de **sleutel** voor het deel van de map plus joker tekens.

**Voor beeld: voor voegsel gebruiken**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Voor beeld: sleutel en versie gebruiken (optioneel)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Verouderd bron model voor de Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **FileSystemSource**. |Yes |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer **recursief** is ingesteld op **True** en de Sink een archief op basis van bestanden is, er geen lege map of submap wordt gekopieerd of gemaakt bij de sink.<br/>Toegestane waarden zijn **True** (standaard) en **Onwaar**. | No |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die de Kopieer activiteit in azure Data Factory ondersteunt als bronnen en Sinks.

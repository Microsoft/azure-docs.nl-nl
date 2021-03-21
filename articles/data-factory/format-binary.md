---
title: Binaire indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met binaire indeling in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: cc5b54e99584b74b287fa66deba1694419b46b16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393680"
---
# <a name="binary-format-in-azure-data-factory"></a>Binaire indeling in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binaire indeling wordt ondersteund voor de volgende connectors [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

U kunt binaire gegevensset gebruiken in [Kopieer activiteit](copy-activity-overview.md), [GetMetadata-activiteit](control-flow-get-metadata-activity.md)of [Delete-activiteit](delete-activity.md). Wanneer u een binaire gegevensset gebruikt, parseert ADF geen bestands inhoud, maar behandelt deze niet. 

>[!NOTE]
>Wanneer u een binaire gegevensset in een Kopieer activiteit gebruikt, kunt u alleen kopiëren van een binaire gegevensset naar een binaire gegevensset.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de binaire gegevensset worden ondersteund.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **binary**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . **Zie de sectie Details in connector artikel-> eigenschappen van gegevensset**. | Ja      |
| compressie | Groep eigenschappen voor het configureren van bestands compressie. Configureer deze sectie als u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | Nee |
| type | De compressie-codec die wordt gebruikt om binaire bestanden te lezen/schrijven. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **tar** of **TarGzip**. <br>**Opmerking** wanneer u Kopieer activiteit gebruikt om **ZipDeflate** / **TarGzip** / **tar** -bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden standaard bestanden uitgepakt naar de map:. `<path specified in dataset>/<folder named as source compressed file>/` Gebruik de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` bron van de [Kopieer activiteit](#binary-as-source) om te bepalen of u de naam van de gecomprimeerde bestanden als mapstructuur wilt behouden.| Nee       |
| niveau | De compressie ratio. Toep assen wanneer gegevensset wordt gebruikt in Sink voor kopieer activiteit.<br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](/dotnet/api/system.io.compression.compressionlevel) voor meer informatie. | Nee       |

Hieronder ziet u een voor beeld van een binaire gegevensset op Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de binaire bron en Sink.

>[!NOTE]
>Wanneer u een binaire gegevensset in een Kopieer activiteit gebruikt, kunt u alleen kopiëren van een binaire gegevensset naar een binaire gegevensset.

### <a name="binary-as-source"></a>Binair als bron

De volgende eigenschappen worden ondersteund in de sectie ***\* bron \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **BinarySource**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **binaire Lees instellingen** . | Nee       |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

Ondersteunde **binaire Lees instellingen** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Het type formatSettings moet zijn ingesteld op **BinaryReadSettings**. | Ja      |
| compressionProperties | Een groep eigenschappen voor het decomprimeren van gegevens voor een bepaalde compressie-codec. | Nee       |
| preserveZipFileNameAsFolder<br>(*onder `compressionProperties` -> `type` als `ZipDeflateReadSettings`*) | Van toepassing wanneer de invoer-gegevensset is geconfigureerd met **ZipDeflate** -compressie. Hiermee wordt aangegeven of de naam van het bron-zip-bestand tijdens het kopiëren moet worden bewaard als mapstructuur.<br>-Als deze eigenschap is ingesteld op **True (standaard)**, wordt in Data Factory ongecomprimeerde bestanden naar `<path specified in dataset>/<folder named as source zip file>/` .<br>-Als deze eigenschap is ingesteld op **Onwaar**, wordt door Data Factory ongecomprimeerde bestanden rechtstreeks naar te schrijven `<path specified in dataset>` . Zorg ervoor dat u geen dubbele bestands namen in verschillende zip-bron bestanden hebt om te voor komen dat u racet of onverwacht gedrag.  | Nee |
| preserveCompressionFileNameAsFolder<br>(*onder `compressionProperties` -> `type` as `TarGZipReadSettings` of `TarReadSettings`*) | Van toepassing wanneer de invoer-gegevensset is geconfigureerd met **TarGzip** / **tar** -compressie. Hiermee wordt aangegeven of de gecomprimeerde bestands naam van de bron moet worden behouden als mapstructuur tijdens het kopiëren.<br>-Als deze eigenschap is ingesteld op **True (standaard)**, schrijft Data Factory gedecomprimeerde bestanden naar `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Als deze eigenschap is ingesteld op **Onwaar**, schrijft Data Factory de gedecomprimeerde bestanden rechtstreeks naar `<path specified in dataset>` . Zorg ervoor dat u geen dubbele bestands namen in verschillende bron bestanden hebt om te voor komen dat u racet of onverwacht gedrag. | Nee |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Binair als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\* sink \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **BinarySink**. | Ja      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
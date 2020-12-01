---
title: Excel-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met Excel-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: b1f95cf0a62aa68fe86f37cea137251553458a1d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348871"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de Excel-bestanden wilt parseren**. Azure Data Factory ondersteunt zowel ". xls" als ". xlsx".

Excel-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md). Het wordt ondersteund als bron, maar niet op sink. 

**Opmerking**: de indeling ". xls" wordt niet ondersteund bij het gebruik van [http](connector-http.md). 

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de Excel-gegevensset worden ondersteund.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Excel**.   | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . | Ja      |
| Ingestelde SheetName        | De naam van het Excel-werk blad om gegevens te lezen.                       | Ja      |
| bereik            | Het celbereik in het opgegeven werk blad om de selectieve gegevens te zoeken, bijvoorbeeld:<br>-Niet opgegeven: Hiermee wordt het hele werk blad gelezen als een tabel van de eerste niet-lege rij en kolom<br>- `A3`: leest een tabel die begint met de opgegeven cel, detecteert dynamisch alle rijen hieronder en alle kolommen aan de rechter kant.<br>- `A3:H5`: Hiermee wordt dit vaste bereik gelezen als een tabel<br>- `A3:A3`: Hiermee wordt deze enkele cel gelezen | Nee       |
| firstRowAsHeader | Hiermee wordt aangegeven of de eerste rij in het opgegeven werk blad of bereik moet worden behandeld als een header regel met de namen van kolommen.<br>Toegestane waarden zijn **True** en **False** (standaard). | Nee       |
| nullValue        | Hiermee wordt de teken reeks representatie van een null-waarde opgegeven. <br>De standaard waarde is een **lege teken reeks**. | Nee       |
| compressie | Groep eigenschappen voor het configureren van bestands compressie. Configureer deze sectie als u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | Nee |
| type<br/>(*onder `compression`*) | De compressie-codec die wordt gebruikt voor het lezen/schrijven van JSON-bestanden. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **TarGzip**, **tar**, **Snappy** of **LZ4**. De standaard waarde is niet gecomprimeerd.<br>**Houd er rekening mee** dat de Kopieer activiteit geen ondersteuning biedt voor "snappy" & "LZ4" en de toewijzing van gegevens stroom geen ondersteuning biedt voor "ZipDeflate", "TarGzip" en "tar".<br>**Opmerking** wanneer u Kopieer activiteit gebruikt om **ZipDeflate** -bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden bestanden uitgepakt naar de map: `<path specified in dataset>/<folder named as source zip file>/` . | Nee.  |
| niveau<br/>(*onder `compression`*) | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](/dotnet/api/system.io.compression.compressionlevel) voor meer informatie. | Nee       |

Hieronder ziet u een voor beeld van een Excel-gegevensset op Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door de Excel-bron worden ondersteund.

### <a name="excel-as-source"></a>Excel als bron 

De volgende eigenschappen worden ondersteund in de sectie Kopieer **activiteit \_ _ \* bron***.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **ExcelSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . | Nee       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

In het toewijzen van gegevens stromen kunt u Excel-indeling lezen in de volgende gegevens archieven: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). U kunt naar Excel-bestanden verwijzen met behulp van de Excel-gegevensset of een [inline-gegevensset](data-flow-source.md#inline-datasets)gebruiken.

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een Excel-bron. U kunt deze eigenschappen bewerken op het tabblad **bron opties** . Bij gebruik van een inline-gegevensset worden extra Bestands instellingen weer geven, die hetzelfde zijn als de eigenschappen die worden beschreven in de sectie [Eigenschappen van gegevensset](#dataset-properties) .

| Naam                      | Beschrijving                                                  | Vereist | Toegestane waarden                                            | Eigenschap gegevens stroom script         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Joker tekens           | Alle bestanden die overeenkomen met het pad naar Joker tekens worden verwerkt. Onderdrukt de map en het bestandspad die in de gegevensset zijn ingesteld. | nee       | Teken reeks []                                                  | wildcardPaths                     |
| Basispad voor partitie       | Voor bestands gegevens die zijn gepartitioneerd, kunt u een basispad opgeven om gepartitioneerde mappen als kolommen te kunnen lezen | nee       | Tekenreeks                                                    | partitionRootPath                 |
| Lijst met bestanden             | Hiermee wordt aangegeven of uw bron verwijst naar een tekst bestand met de bestanden die moeten worden verwerkt | nee       | `true` of `false`                                         | File List                          |
| Kolom voor het opslaan van de bestands naam | Een nieuwe kolom maken met de naam en het pad van het bron bestand       | nee       | Tekenreeks                                                    | rowUrlColumn                      |
| Na voltooiing          | De bestanden na de verwerking verwijderen of verplaatsen. Bestandspad wordt gestart vanuit de hoofdmap van de container | nee       | Verwijderen: `true` of `false` <br> Ga `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filteren op laatst gewijzigd   | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee       | Tijdstempel                                                 | modifiedAfter <br> modifiedBefore |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Bron voorbeeld

De onderstaande afbeelding is een voor beeld van een configuratie van een Excel-bron in het toewijzen van gegevens stromen met de gegevensset-modus.

![Excel-bron](media/data-flow/excel-source.png)

Het gekoppelde gegevensstroom script is:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Als u inline dataset gebruikt, ziet u de volgende bron opties in gegevens stroom toewijzen.

![Excel-bron inline-gegevensset](media/data-flow/excel-source-inline-dataset.png)

Het gekoppelde gegevensstroom script is:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
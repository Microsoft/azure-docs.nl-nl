---
title: Tekst indeling met scheidings tekens in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met gescheiden tekst indeling in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: jingwang
ms.openlocfilehash: 5b6367d2765277493ea34a4f7a23cae4b24c4dc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386591"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tekst indeling met scheidings tekens in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de gescheiden tekst bestanden wilt parseren of de gegevens naar tekst indeling met scheidings tekens wilt schrijven**. 

Tekst met scheidings tekens wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset met scheidings tekens.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **DelimitedText**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` .  | Ja      |
| columnDelimiter  | De teken (s) die worden gebruikt voor het scheiden van kolommen in een bestand. <br>De standaard waarde is **een `,` komma**. Wanneer het kolom scheidings teken is gedefinieerd als lege reeks, wat betekent dat er geen scheidings teken is, wordt de hele regel als één kolom beschouwd.<br>Kolom scheidings teken is op dit moment alleen toegestaan voor de toewijzing van gegevens stromen, maar niet voor het kopiëren van een activiteit.  | Nee       |
| rowDelimiter     | Het afzonderlijke teken of ' \r\n ' die wordt gebruikt om rijen in een bestand te scheiden. <br>De standaard waarde is een van de volgende waarden **voor lezen: ["\r\n", "\r", "\n"]** en **"\n" of "\r\n" bij** het toewijzen van gegevens stroom en de Kopieer activiteit respectievelijk. <br>Als het scheidings teken van de rij is ingesteld op geen scheidings teken (lege reeks), moet het kolom scheidings teken worden ingesteld als geen scheidings teken (lege teken reeks), wat betekent dat de gehele inhoud als één waarde wordt behandeld.<br>Op dit moment wordt het rijnummer als lege teken reeks alleen ondersteund voor het toewijzen van gegevens stroom, maar niet voor het kopiëren van de activiteit. | Nee       |
| quoteChar        | De kolom waarden voor één teken om te citeren als deze kolom scheidings tekens bevat. <br>De standaard waarde is **dubbele aanhalings tekens** `"` . <br>Als `quoteChar` is gedefinieerd als een lege teken reeks, betekent dit dat er geen aanhalings tekens en kolom waarde is opgenomen, en `escapeChar` wordt gebruikt om het scheidings teken van de kolom en zichzelf te verescapenen. | Nee       |
| escapeChar       | Het enkele teken om aanhalings tekens binnen een waarde in de offerte op te lossen.<br>De standaard waarde is **back `\` slash**. <br>Wanneer `escapeChar` is gedefinieerd als lege teken reeks, `quoteChar` moet de waarde ook worden ingesteld als een lege teken reeks, in dat geval dat alle kolom waarden geen scheidings tekens bevatten. | Nee       |
| firstRowAsHeader | Hiermee wordt aangegeven of de eerste rij moet worden behandeld/gemaakt als een kopregel met namen van kolommen.<br>Toegestane waarden zijn **True** en **False** (standaard).<br>Als de eerste rij als koptekst onwaar is, notitie UI-gegevens voorbeeld en de uitvoer van opzoek activiteiten automatisch genereren van kolom namen als Prop_ {n} (vanaf 0), is voor kopieer activiteit [expliciet toewijzing](copy-activity-schema-and-type-mapping.md#explicit-mapping) van bron aan Sink vereist en worden kolommen op rang telwoord gesorteerd (vanaf 1), en worden er kolommen met de naam weer gegeven als Column_ {n} (vanaf 1).  | Nee       |
| nullValue        | Hiermee wordt de teken reeks representatie van een null-waarde opgegeven. <br>De standaard waarde is een **lege teken reeks**. | Nee       |
| encodingName     | Het coderings type dat wordt gebruikt voor het lezen/schrijven van test bestanden. <br>Toegestane waarden zijn als volgt: ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' VS-ASCII ', ' UTF-7 ', ' BIG5 ', ' EUC-JP ', ' EUC-KR ', ' GB2312 "," GB18030 "," JOHAB "," SHIFT-JIS "," CP875 "," CP866 "," IBM00858 "," IBM037 "," IBM273 "," IBM437 "," IBM500 "," IBM737 "," IBM775 "," IBM850 "," IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 ', ' IBM863 ', ' IBM864 ', ' IBM865 ', ' IBM869 ', ' IBM870 ', ' IBM01140 ', ' IBM01141 ', ' IBM01142 ', ' IBM01143 ', ' IBM01144 ', IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," ISO-2022-JP "," ISO-2022-KR "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 " , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Opmerking toewijzing gegevens stroom ondersteunt geen UTF-7-code ring. | Nee       |
| compressionCodec | De compressie-codec die wordt gebruikt om tekst bestanden te lezen/schrijven. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **TarGzip**, **tar**, **Snappy** of **LZ4**. De standaard waarde is niet gecomprimeerd. <br>**Houd er rekening mee** dat de Kopieer activiteit geen ondersteuning biedt voor "snappy" & "LZ4" en de toewijzing van gegevens stroom geen ondersteuning biedt voor "ZipDeflate", "TarGzip" en "tar". <br>**Opmerking** wanneer u Kopieer activiteit gebruikt om **ZipDeflate** / **TarGzip** / **tar** -bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden standaard bestanden uitgepakt naar de map:. `<path specified in dataset>/<folder named as source compressed file>/` Gebruik de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` bron van de [Kopieer activiteit](#delimited-text-as-source) om te bepalen of u de naam van de gecomprimeerde bestanden als mapstructuur wilt behouden. | Nee       |
| compressionLevel | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](/dotnet/api/system.io.compression.compressionlevel) voor meer informatie. | Nee       |

Hieronder ziet u een voor beeld van een gegevensset met scheidings tekens op Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gescheiden tekst bron en Sink.

### <a name="delimited-text-as-source"></a>Tekst met scheidings tekens als bron 

De volgende eigenschappen worden ondersteund in de sectie ***\* bron \**** van de Kopieer activiteit.

| Eigenschap       | Beschrijving                                                  | Vereist |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **DelimitedTextSource**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **Lees instellingen voor tekst met scheidings tekens** . |  Nee       |
| storeSettings  | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . | Nee       |

Ondersteunde **instellingen voor het lezen van tekst met scheidings tekens** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Het type formatSettings moet zijn ingesteld op **DelimitedTextReadSettings**. | Ja      |
| skipLineCount | Hiermee wordt het aantal **niet-lege** rijen aangegeven dat moet worden overgeslagen bij het lezen van gegevens van invoer bestanden. <br>Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. | Nee       |
| compressionProperties | Een groep eigenschappen voor het decomprimeren van gegevens voor een bepaalde compressie-codec. | Nee       |
| preserveZipFileNameAsFolder<br>(*onder `compressionProperties` -> `type` als `ZipDeflateReadSettings`*) |  Van toepassing wanneer de invoer-gegevensset is geconfigureerd met **ZipDeflate** -compressie. Hiermee wordt aangegeven of de naam van het bron-zip-bestand tijdens het kopiëren moet worden bewaard als mapstructuur.<br>-Als deze eigenschap is ingesteld op **True (standaard)**, wordt in Data Factory ongecomprimeerde bestanden naar `<path specified in dataset>/<folder named as source zip file>/` .<br>-Als deze eigenschap is ingesteld op **Onwaar**, wordt door Data Factory ongecomprimeerde bestanden rechtstreeks naar te schrijven `<path specified in dataset>` . Zorg ervoor dat u geen dubbele bestands namen in verschillende zip-bron bestanden hebt om te voor komen dat u racet of onverwacht gedrag.  | Nee |
| preserveCompressionFileNameAsFolder<br>(*onder `compressionProperties` -> `type` as `TarGZipReadSettings` of `TarReadSettings`*)  | Van toepassing wanneer de invoer-gegevensset is geconfigureerd met **TarGzip** / **tar** -compressie. Hiermee wordt aangegeven of de gecomprimeerde bestands naam van de bron moet worden behouden als mapstructuur tijdens het kopiëren.<br>-Als deze eigenschap is ingesteld op **True (standaard)**, schrijft Data Factory gedecomprimeerde bestanden naar `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Als deze eigenschap is ingesteld op **Onwaar**, schrijft Data Factory de gedecomprimeerde bestanden rechtstreeks naar `<path specified in dataset>` . Zorg ervoor dat u geen dubbele bestands namen in verschillende bron bestanden hebt om te voor komen dat u racet of onverwacht gedrag. | Nee |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
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

### <a name="delimited-text-as-sink"></a>Tekst met scheidings tekens als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\* sink \**** van de Kopieer activiteit.

| Eigenschap       | Beschrijving                                                  | Vereist |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **DelimitedTextSink**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor het **schrijven van instellingen voor tekst met scheidings tekens** hieronder. |    Nee      |
| storeSettings  | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings` .  | Nee       |

Ondersteunde **instellingen voor het schrijven van tekst met scheidings tekens** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type formatSettings moet zijn ingesteld op **DelimitedTextWriteSettings**. | Ja                                                   |
| File Extension | De bestands extensie die wordt gebruikt voor het benoemen van de uitvoer bestanden, bijvoorbeeld, `.csv` `.txt` . Deze moet worden opgegeven wanneer de `fileName` niet is opgegeven in de DelimitedText-gegevensset. Als de bestands naam in de uitvoer gegevensset is geconfigureerd, wordt deze gebruikt als de naam van het sink-bestand en de instelling voor de bestands extensie wordt genegeerd.  | Ja als er geen bestands naam is opgegeven in de uitvoer gegevensset |
| maxRowsPerFile | Wanneer u gegevens naar een map schrijft, kunt u ervoor kiezen om naar meerdere bestanden te schrijven en het maximum aantal rijen per bestand op te geven.  | Nee |
| fileNamePrefix | Van toepassing wanneer `maxRowsPerFile` is geconfigureerd.<br> Geef het voor voegsel van de bestands naam op wanneer u gegevens naar meerdere bestanden schrijft, wat resulteert in dit patroon: `<fileNamePrefix>_00000.<fileExtension>` . Als u niets opgeeft, wordt het voor voegsel van de bestands naam automatisch gegenereerd. Deze eigenschap is niet van toepassing wanneer een bron archief of een [gegevens archief met partitie opties](copy-activity-performance-features.md)is gebaseerd op het bestand.  | Nee |

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Bij het toewijzen van gegevens stromen kunt u in de volgende gegevens archieven lezen en schrijven naar tekst indeling met scheidings tekens: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een tekst bron met scheidings tekens. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Joker tekens | Alle bestanden die overeenkomen met het pad naar Joker tekens worden verwerkt. Onderdrukt de map en het bestandspad die in de gegevensset zijn ingesteld. | nee | Teken reeks [] | wildcardPaths |
| Basispad voor partitie | Voor bestands gegevens die zijn gepartitioneerd, kunt u een basispad opgeven om gepartitioneerde mappen als kolommen te kunnen lezen | nee | Tekenreeks | partitionRootPath |
| Lijst met bestanden | Hiermee wordt aangegeven of uw bron verwijst naar een tekst bestand met de bestanden die moeten worden verwerkt | nee | `true` of `false` | File List |
| Meerregelige rijen | Bevat het bron bestand rijen die meerdere regels omspannen. Meerregelige waarden moeten tussen aanhalings tekens staan. | Nee `true` of `false` | multiLineRow |
| Kolom voor het opslaan van de bestands naam | Een nieuwe kolom maken met de naam en het pad van het bron bestand | nee | Tekenreeks | rowUrlColumn |
| Na voltooiing | De bestanden na de verwerking verwijderen of verplaatsen. Bestandspad wordt gestart vanuit de hoofdmap van de container | nee | Verwijderen: `true` of `false` <br> Ga `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filteren op laatst gewijzigd | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee | Tijdstempel | modifiedAfter <br> modifiedBefore |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Bron voorbeeld

De onderstaande afbeelding is een voor beeld van een tekst bron configuratie met scheidings tekens in het toewijzen van gegevens stromen.

![DelimitedText-bron](media/data-flow/delimited-text-source.png)

Het gekoppelde gegevensstroom script is:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Gegevens stroom bronnen bieden ondersteuning voor een beperkte set Linux-globbing die wordt ondersteund door Hadoop-bestands systemen

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een tekst filter met scheidings tekens. U kunt deze eigenschappen bewerken op het tabblad **instellingen** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| De map wissen | Als de doelmap vóór het schrijven is gewist | nee | `true` of `false` | afkappen |
| Optie Bestands naam | De naamgevings indeling van de gegevens die zijn geschreven. Standaard één bestand per partitie in de indeling `part-#####-tid-<guid>` | nee | Patroon: teken reeks <br> Per partitie: teken reeks [] <br> Als gegevens in kolom: teken reeks <br> Uitvoer naar één bestand: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Alle offertes | Alle waarden tussen aanhalings tekens plaatsen | nee | `true` of `false` | quoteAll |

### <a name="sink-example"></a>Sink-voor beeld

De onderstaande afbeelding is een voor beeld van een configuratie met scheidings tekens voor tekst in het toewijzen van gegevens stromen.

![DelimitedText-Sink](media/data-flow/delimited-text-sink.png)

Het gekoppelde gegevensstroom script is:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

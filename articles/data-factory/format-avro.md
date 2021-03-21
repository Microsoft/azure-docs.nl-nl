---
title: De indeling Avro in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de Avro-indeling in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392592"
---
# <a name="avro-format-in-azure-data-factory"></a>De indeling Avro in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de Avro-bestanden wilt parseren of de gegevens wilt schrijven naar de Avro-indeling**. 

De Avro-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Avro-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **Avro**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . **Zie de sectie Details in connector artikel-> eigenschappen van gegevensset**. | Ja      |
| avroCompressionCodec | De compressie-codec die moet worden gebruikt bij het schrijven naar Avro-bestanden. Bij het lezen van Avro-bestanden wordt de compressie-codec automatisch door Data Factory bepaald op basis van de meta gegevens van het bestand.<br>Ondersteunde typen zijn '**none**' (standaard), '**Deflate**', '**Snappy**'. Houd er rekening mee dat de activiteit die momenteel wordt gekopieerd, geen ondersteuning biedt voor Snappy wanneer Avro bestanden lezen/schrijven. | Nee       |

> [!NOTE]
> Spaties in kolom naam wordt niet ondersteund voor Avro-bestanden.

Hieronder ziet u een voor beeld van een AVRO-gegevensset op Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Avro-bron en Sink.

### <a name="avro-as-source"></a>AVRO als bron

De volgende eigenschappen worden ondersteund in de sectie ***\* bron \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="avro-as-sink"></a>AVRO als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\* sink \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSink**. | Ja      |
| formatSettings          | Een groep eigenschappen. Raadpleeg de tabel **Avro write Settings** hieronder.| Nee      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

Ondersteunde **Avro-schrijf instellingen** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type formatSettings moet zijn ingesteld op **AvroWriteSettings**. | Ja                                                   |
| maxRowsPerFile | Wanneer u gegevens naar een map schrijft, kunt u ervoor kiezen om naar meerdere bestanden te schrijven en het maximum aantal rijen per bestand op te geven.  | Nee |
| fileNamePrefix | Van toepassing wanneer `maxRowsPerFile` is geconfigureerd.<br> Geef het voor voegsel van de bestands naam op wanneer u gegevens naar meerdere bestanden schrijft, wat resulteert in dit patroon: `<fileNamePrefix>_00000.<fileExtension>` . Als u niets opgeeft, wordt het voor voegsel van de bestands naam automatisch gegenereerd. Deze eigenschap is niet van toepassing wanneer een bron archief of een [gegevens archief met partitie opties](copy-activity-performance-features.md)is gebaseerd op het bestand.  | Nee |

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

In het toewijzen van gegevens stromen kunt u lezen en schrijven naar Avro-indeling in de volgende gegevens archieven: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die door een AVRO-bron worden ondersteund. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Joker tekens | Alle bestanden die overeenkomen met het pad naar Joker tekens worden verwerkt. Onderdrukt de map en het bestandspad die in de gegevensset zijn ingesteld. | nee | Teken reeks [] | wildcardPaths |
| Basispad voor partitie | Voor bestands gegevens die zijn gepartitioneerd, kunt u een basispad opgeven om gepartitioneerde mappen als kolommen te kunnen lezen | nee | Tekenreeks | partitionRootPath |
| Lijst met bestanden | Hiermee wordt aangegeven of uw bron verwijst naar een tekst bestand met de bestanden die moeten worden verwerkt | nee | `true` of `false` | File List |
| Kolom voor het opslaan van de bestands naam | Een nieuwe kolom maken met de naam en het pad van het bron bestand | nee | Tekenreeks | rowUrlColumn |
| Na voltooiing | De bestanden na de verwerking verwijderen of verplaatsen. Bestandspad wordt gestart vanuit de hoofdmap van de container | nee | Verwijderen: `true` of `false` <br> Ga `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filteren op laatst gewijzigd | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee | Tijdstempel | modifiedAfter <br> modifiedBefore |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een AVRO-sink. U kunt deze eigenschappen bewerken op het tabblad **instellingen** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| De map wissen | Als de doelmap vóór het schrijven is gewist | nee | `true` of `false` | afkappen |
| Optie Bestands naam | De naamgevings indeling van de gegevens die zijn geschreven. Standaard één bestand per partitie in de indeling `part-#####-tid-<guid>` | nee | Patroon: teken reeks <br> Per partitie: teken reeks [] <br> Als gegevens in kolom: teken reeks <br> Uitvoer naar één bestand: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Alle offertes | Alle waarden tussen aanhalings tekens plaatsen | nee | `true` of `false` | quoteAll |

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

### <a name="copy-activity"></a>Kopieeractiviteit
Avro [complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast) in de Kopieer activiteit.

### <a name="data-flows"></a>Gegevensstromen
Wanneer u werkt met Avro-bestanden in gegevens stromen, kunt u complexe gegevens typen lezen en schrijven, maar u moet het fysieke schema eerst wissen uit de gegevensset. In gegevens stromen kunt u uw logische projectie instellen en kolommen afleiden die complexe structuren zijn en die velden vervolgens automatisch toewijzen aan een AVRO-bestand.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

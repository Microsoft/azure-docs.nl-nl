---
title: De indeling ORC in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de ORC-indeling in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 8973692b90cc9d6caa852616bf2962371d25abfa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386455"
---
# <a name="orc-format-in-azure-data-factory"></a>De indeling ORC in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de Orc-bestanden wilt parseren of de gegevens wilt schrijven naar de Orc-indeling**. 

De ORC-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de ORC-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **Orc**. | Yes      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . **Zie de sectie Details in connector artikel-> eigenschappen van gegevensset**. | Yes      |
| compressionCodec         | De compressie-codec die moet worden gebruikt bij het schrijven naar ORC-bestanden. Bij het lezen van ORC-bestanden, bepalen gegevens fabrieken de compressie-codec automatisch op basis van de meta gegevens van het bestand.<br>Ondersteunde typen zijn **geen**, **zlib**, **Snappy** (standaard) en **lzo**. Houd er rekening mee dat de activiteit die momenteel wordt gekopieerd, geen ondersteuning biedt voor LZO wanneer ORC bestanden lezen/schrijven. | No      |

Hieronder ziet u een voor beeld van een ORC-gegevensset op Azure Blob Storage:

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Houd rekening met de volgende punten:

* Complexe gegevens typen (zoals kaart, lijst, STRUCT) worden momenteel alleen ondersteund in gegevens stromen, niet in de Kopieer activiteit. Als u complexe typen in gegevens stromen wilt gebruiken, moet u het bestands schema niet importeren in de gegevensset, waardoor het schema leeg blijft in de gegevensset. Importeer vervolgens in de bron transformatie de projectie.
* Spaties in kolom naam wordt niet ondersteund.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de ORC-bron en Sink.

### <a name="orc-as-source"></a>ORC als bron

De volgende eigenschappen worden ondersteund in de sectie ***\* bron \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **OrcSource**. | Yes      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | No       |

### <a name="orc-as-sink"></a>ORC als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\* sink \**** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **OrcSink**. | Yes      |
| formatSettings | Een groep eigenschappen. Raadpleeg de tabel **Orc write Settings** hieronder. |    No      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | No       |

Ondersteunde **Orc-schrijf instellingen** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type formatSettings moet zijn ingesteld op **OrcWriteSettings**. | Yes                                                   |
| maxRowsPerFile | Wanneer u gegevens naar een map schrijft, kunt u ervoor kiezen om naar meerdere bestanden te schrijven en het maximum aantal rijen per bestand op te geven.  | No |
| fileNamePrefix | Van toepassing wanneer `maxRowsPerFile` is geconfigureerd.<br> Geef het voor voegsel van de bestands naam op wanneer u gegevens naar meerdere bestanden schrijft, wat resulteert in dit patroon: `<fileNamePrefix>_00000.<fileExtension>` . Als u niets opgeeft, wordt het voor voegsel van de bestands naam automatisch gegenereerd. Deze eigenschap is niet van toepassing wanneer een bron archief of een [gegevens archief met partitie opties](copy-activity-performance-features.md)is gebaseerd op het bestand.  | No |

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

In het toewijzen van gegevens stromen kunt u lezen en schrijven naar ORC-indeling in de volgende gegevens archieven: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

U kunt verwijzen naar ORC-bestanden met behulp van de ORC-gegevensset of met behulp van een [inline dataset](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die door een ORC-bron worden ondersteund. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

Bij gebruik van een inline-gegevensset worden extra Bestands instellingen weer geven, die hetzelfde zijn als de eigenschappen die worden beschreven in de sectie [Eigenschappen van gegevensset](#dataset-properties) .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `orc` | ja | `orc` | indeling |
| Joker tekens | Alle bestanden die overeenkomen met het pad naar Joker tekens worden verwerkt. Onderdrukt de map en het bestandspad die in de gegevensset zijn ingesteld. | nee | Teken reeks [] | wildcardPaths |
| Basispad voor partitie | Voor bestands gegevens die zijn gepartitioneerd, kunt u een basispad opgeven om gepartitioneerde mappen als kolommen te kunnen lezen | nee | Tekenreeks | partitionRootPath |
| Lijst met bestanden | Hiermee wordt aangegeven of uw bron verwijst naar een tekst bestand met de bestanden die moeten worden verwerkt | nee | `true` of `false` | File List |
| Kolom voor het opslaan van de bestands naam | Een nieuwe kolom maken met de naam en het pad van het bron bestand | nee | Tekenreeks | rowUrlColumn |
| Na voltooiing | De bestanden na de verwerking verwijderen of verplaatsen. Bestandspad wordt gestart vanuit de hoofdmap van de container | nee | Verwijderen: `true` of `false` <br> Ga `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filteren op laatst gewijzigd | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee | Tijdstempel | modifiedAfter <br> modifiedBefore |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Bron voorbeeld

Het gekoppelde gegevensstroom script van een ORC-bron configuratie is:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een ORC-sink. U kunt deze eigenschappen bewerken op het tabblad **instellingen** .

Bij gebruik van een inline-gegevensset worden extra Bestands instellingen weer geven, die hetzelfde zijn als de eigenschappen die worden beschreven in de sectie [Eigenschappen van gegevensset](#dataset-properties) .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `orc` | ja | `orc` | indeling |
| De map wissen | Als de doelmap vóór het schrijven is gewist | nee | `true` of `false` | afkappen |
| Optie Bestands naam | De naamgevings indeling van de gegevens die zijn geschreven. Standaard één bestand per partitie in de indeling `part-#####-tid-<guid>` | nee | Patroon: teken reeks <br> Per partitie: teken reeks [] <br> Als gegevens in kolom: teken reeks <br> Uitvoer naar één bestand: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Sink-voor beeld

Het gekoppelde gegevensstroom script van een ORC-Sink-configuratie is:

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime gebruiken

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u het pakket **64-bits jre 8 (Java Runtime Environment) of openjdk** en **micro soft Visual C++ 2010 Redistributable** installeren op uw **IR-computer**. Raadpleeg de volgende alinea met meer informatie.

Voor een kopie die wordt uitgevoerd op een zelf-hostende IR met ORC-serialisatie/deserialisatie, zoekt ADF de Java-runtime door het REGI ster *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* voor jre te controleren, indien niet gevonden, ter controle van de systeem variabele *`JAVA_HOME`* voor openjdk.

- **Jre gebruiken**: de 64-bits IR vereist een 64-bits jre. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verpak de jvm.dll met alle andere vereiste assembly's van OpenJDK op een zelf-hostende IR-computer en stel de systeem omgevings variabele in JAVA_HOME dienovereenkomstig in.
- **Het herdistribueerbare pakket Visual c++ 2010 installeren: het** herdistribueerbare pakket visual c++ 2010 wordt niet geïnstalleerd met de zelf-HOSTende IR-installaties. U kunt deze [hier](https://www.microsoft.com/download/details.aspx?id=14632)vinden.

> [!TIP]
> Als u gegevens kopieert naar/van ORC-indeling met behulp van zelf-hostende Integration Runtime en de fout melding ' er is een fout opgetreden bij het aanroepen van Java, bericht: **Java. lang. OutOfMemoryError: Java-heap-ruimte**', kunt u een omgevings variabele toevoegen aan `_JAVA_OPTIONS` de computer die als host fungeert voor de zelf-hostende IR om een dergelijke kopie te maken,

![JVM-Heap-grootte instellen op zelf-hostende IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voor beeld: Stel variabele `_JAVA_OPTIONS` met waarde in `-Xms256m -Xmx16g` . Met de vlag `Xms` wordt de eerste geheugen toewijzings groep opgegeven voor een Java Virtual Machine (JVM), terwijl `Xmx` de maximale geheugen toewijzings groep wordt opgegeven. Dit betekent dat JVM wordt gestart met `Xms` de hoeveelheid geheugen en dat er een maximum hoeveelheid geheugen kan worden gebruikt `Xmx` . ADF gebruikt standaard min 64 MB en Max 1G.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

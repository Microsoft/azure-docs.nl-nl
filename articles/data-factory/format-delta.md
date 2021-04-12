---
title: Delta-indeling in Azure Data Factory
description: Gegevens transformeren en verplaatsen vanuit een Delta-Lake met de Delta-indeling
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: weetok
ms.openlocfilehash: 6d9d2b0d185750cf8ed8192661f28a2b82d88b78
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222535"
---
# <a name="delta-format-in-azure-data-factory"></a>Delta-indeling in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt uitgelegd hoe u gegevens kopieert van en naar een Delta Lake dat is opgeslagen in [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md) of [Azure Blob Storage](connector-azure-blob-storage.md) met behulp van de Delta-indeling. Deze connector is beschikbaar als een [inline-gegevensset](data-flow-source.md#inline-datasets) in het toewijzen van gegevens stromen als bron en een sink.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Deze connector is beschikbaar als een [inline-gegevensset](data-flow-source.md#inline-datasets) in het toewijzen van gegevens stromen als bron en een sink.

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een Delta bron. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `delta` | ja | `delta` | indeling |
| Bestandssysteem | De container/het bestands systeem van de Delta Lake | ja | Tekenreeks | System |
| Mappad | De rechtstreekse van de Delta Lake | ja | Tekenreeks | folderPath |
| Compressie type | Het compressie type van de Delta tabel | nee | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Compressie niveau | Kies of de compressie zo snel mogelijk wordt voltooid of dat het resulterende bestand optimaal moet worden gecomprimeerd. | vereist als `compressedType` is opgegeven. | `Optimal` of `Fastest` | compressionLevel |
| Tijd reis | Kies of u een query wilt uitvoeren op een oudere moment opname van een Delta tabel | nee | Query op tijds tempel: tijds tempel <br> Query op versie: geheel getal | timestampAsOf <br> versionAsOf |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Schema importeren

Delta is alleen beschikbaar als een inline-gegevensset en heeft standaard geen gekoppeld schema. Als u kolom meta gegevens wilt ophalen, klikt u op de knop **schema importeren** op het tabblad **projectie** . Hiermee kunt u verwijzen naar de kolom namen en gegevens typen die door de verzameling zijn opgegeven. Als u het schema wilt importeren, moet een foutopsporingssessie voor [gegevens stromen](concepts-data-flow-debug-mode.md) actief zijn en moet u een bestaand CDM-entiteits definitie bestand hebben om naar te verwijzen.
 

### <a name="delta-source-script-example"></a>Voor beeld van Delta bron script

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een Delta-sink. U kunt deze eigenschappen bewerken op het tabblad **instellingen** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `delta` | ja | `delta` | indeling |
| Bestandssysteem | De container/het bestands systeem van de Delta Lake | ja | Tekenreeks | System |
| Mappad | De rechtstreekse van de Delta Lake | ja | Tekenreeks | folderPath |
| Compressie type | Het compressie type van de Delta tabel | nee | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Compressie niveau | Kies of de compressie zo snel mogelijk wordt voltooid of dat het resulterende bestand optimaal moet worden gecomprimeerd. | vereist als `compressedType` is opgegeven. | `Optimal` of `Fastest` | compressionLevel |
| Vacuum | Geef de drempel waarde voor bewaren op in uren voor oudere versies van de tabel. Een waarde van 0 of minder standaard ingesteld op 30 dagen | ja | Geheel getal | vacuüm |
| Update methode | Opgeven welke bijwerk bewerkingen zijn toegestaan op Delta Lake. Voor-methoden die niet worden ingevoegd, is een voor gaande trans formatie van rijen vereist voor het markeren van een rij. | ja | `true` of `false` | verwijderd <br> invoegen <br> bij te werken <br> samenvoegen |
| Geoptimaliseerde schrijf bewerkingen | Zorg voor een hogere door Voer voor schrijf bewerkingen via het optimaliseren van interne wille keurige volg orde in Spark-uitvoerende bedrijven. Als gevolg hiervan kunt u minder partities en bestanden met een grotere grootte opmerken | nee | `true` of `false` | optimizedWrite: True |
| Automatisch comprimeren | Nadat een schrijf bewerking is voltooid, voert Spark automatisch de ```OPTIMIZE``` opdracht uit om de gegevens opnieuw in te delen, waardoor er meer partities zijn, indien nodig, voor betere Lees prestaties in de toekomst | nee | `true` of `false` |    autocompact: waar |

### <a name="delta-sink-script-example"></a>Script voor beeld van Delta-Sink

Het gekoppelde gegevensstroom script is:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Bekende beperkingen

Bij het schrijven naar een Delta-sink is er sprake van een bekende beperking waarbij het aantal rijen dat is geschreven niet wordt geretourneerd in de uitvoer van de bewaking.

## <a name="next-steps"></a>Volgende stappen

* Maak een [bron transformatie](data-flow-source.md) in de toewijzing van gegevens stroom.
* Maak een [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.
* Maak een [ALTER Row trans formatie](data-flow-alter-row.md) om rijen te markeren als insert, update, upsert of DELETE.

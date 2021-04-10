---
title: Kopieer activiteit in Azure Data Factory
description: Meer informatie over de Kopieer activiteit in Azure Data Factory. U kunt dit gebruiken om gegevens van een ondersteund brongegevens archief te kopiëren naar een ondersteund Sink-gegevens archief.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387900"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopieer activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-data-movement-activities.md)
> * [Huidige versie](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In Azure Data Factory kunt u de Kopieer activiteit gebruiken om gegevens te kopiëren tussen gegevens archieven die zich lokaal en in de cloud bevinden. Nadat u de gegevens hebt gekopieerd, kunt u andere activiteiten gebruiken om deze verder te transformeren en te analyseren. U kunt ook de Kopieer activiteit gebruiken om trans formatie-en analyse resultaten te publiceren voor business intelligence (BI) en het gebruik van toepassingen.

![De rol van de Kopieer activiteit](media/copy-activity-overview/copy-activity.png)

De Kopieer activiteit wordt uitgevoerd op een [Integration runtime](concepts-integration-runtime.md). U kunt verschillende typen Integration Runtimes voor verschillende scenario's voor het kopiëren van gegevens gebruiken:

* Bij het kopiëren van gegevens tussen twee gegevens archieven die openbaar toegankelijk zijn via internet vanuit elk IP-adres, kunt u de Azure Integration runtime gebruiken voor de Kopieer activiteit. Deze Integration runtime is veilig, betrouwbaar, schaalbaar en [wereld wijd beschikbaar](concepts-integration-runtime.md#integration-runtime-location).
* Wanneer u gegevens kopieert van en naar gegevens archieven die zich on-premises of in een netwerk met toegangs beheer (bijvoorbeeld een virtueel netwerk van Azure) bevinden, moet u een zelf-hostende Integration runtime instellen.

Een Integration runtime moet worden gekoppeld aan elke bron-en Sink-gegevens opslag. Zie [bepalen welke IR u moet gebruiken](concepts-integration-runtime.md#determining-which-ir-to-use)voor meer informatie over hoe de Kopieer activiteit bepaalt welke Integration runtime moet worden gebruikt.

Als u gegevens wilt kopiëren van een bron naar een sink, voert de service die de Kopieer activiteit uitvoert de volgende stappen uit:

1. Hiermee worden gegevens uit een brongegevens archief gelezen.
2. Voert serialisatie/deserialisatie, compressie/decompressie, kolom toewijzing, enzovoort uit. Deze bewerkingen worden uitgevoerd op basis van de configuratie van de invoer gegevensset, de uitvoer gegevensset en de Kopieer activiteit.
3. Schrijft gegevens naar het gegevens archief van de Sink/bestemming.

![Overzicht van kopieeractiviteiten](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevens archieven en-indelingen

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

U kunt de Kopieer activiteit gebruiken om bestanden te kopiëren tussen twee op bestanden gebaseerde gegevens archieven. in dat geval worden de gegevens efficiënt gekopieerd zonder serialisatie of deserialisatie. Daarnaast kunt u ook bestanden van een bepaalde indeling parseren of genereren. u kunt bijvoorbeeld het volgende doen:

* Gegevens uit een SQL Server-Data Base kopiëren en schrijven naar Azure Data Lake Storage Gen2 in de Parquet-indeling.
* Kopieer bestanden in de tekst indeling (CSV) van een on-premises bestands systeem en schrijf naar Azure Blob-opslag in de Avro-indeling.
* Kopieer gezipte bestanden van een on-premises bestands systeem, Decomprimeer ze op de vlucht en schrijf uitgepakte bestanden naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de indeling van de tekst in het type gzip (gecomprimeerd) in de vorm van Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten waarvoor serialisatie/deserialisatie of compressie/decompressie nodig is.

## <a name="supported-regions"></a>Ondersteunde regio’s

De service die de Kopieer activiteit mogelijk maakt, is wereld wijd beschikbaar in de regio's en geografies die worden vermeld in [Azure Integration runtime-locaties](concepts-integration-runtime.md#integration-runtime-location). De wereld wijd beschik bare topologie zorgt voor een efficiënte verplaatsing van gegevens die doorgaans interregionale hops voor komt. Zie [producten per regio](https://azure.microsoft.com/regions/#services) om de beschik baarheid van Data Factory en het verplaatsen van gegevens in een specifieke regio te controleren.

## <a name="configuration"></a>Configuratie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Als u de Kopieer activiteit in Azure Data Factory wilt gebruiken, moet u het volgende doen:

1. **Gekoppelde services maken voor de brongegevens opslag en het sink-gegevens archief.** De lijst met ondersteunde connectors vindt u in de sectie [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats) van dit artikel. Raadpleeg de sectie ' eigenschappen van gekoppelde service ' van het connector artikel voor informatie over de configuratie en ondersteunde eigenschappen. 
2. **Gegevens sets maken voor de bron en Sink.** Raadpleeg de sectie eigenschappen van gegevensset van de bron-en Sink-connector artikelen voor informatie over de configuratie en ondersteunde eigenschappen.
3. **Maak een pijp lijn met de Kopieer activiteit.** In de volgende sectie vindt u een voor beeld.

### <a name="syntax"></a>Syntax

De volgende sjabloon van een Kopieer activiteit bevat een volledige lijst met ondersteunde eigenschappen. Geef de waarden op die passen bij uw scenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Syntaxis Details

| Eigenschap | Beschrijving | Vereist? |
|:--- |:--- |:--- |
| type | Stel voor een Kopieer activiteit in op `Copy` | Yes |
| invoer | Geef de gegevensset op die u hebt gemaakt die verwijst naar de bron gegevens. De Kopieer activiteit ondersteunt slechts één invoer. | Yes |
| uitvoer | Geef de gegevensset op die u hebt gemaakt die verwijst naar de Sink-gegevens. De Kopieer activiteit ondersteunt slechts één uitvoer. | Yes |
| typeProperties | Geef eigenschappen op voor het configureren van de Kopieer activiteit. | Yes |
| source | Geef het type Kopieer bron en de bijbehorende eigenschappen op om gegevens op te halen.<br/>Zie voor meer informatie de sectie ' eigenschappen van de Kopieer activiteit ' in het connector artikel dat wordt vermeld in [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats). | Yes |
| tenen | Geef het sink-type voor het kopiëren en de bijbehorende eigenschappen op voor het schrijven van gegevens.<br/>Zie voor meer informatie de sectie ' eigenschappen van de Kopieer activiteit ' in het connector artikel dat wordt vermeld in [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats). | Yes |
| vertalen | Geef expliciete kolom toewijzingen op van de bron naar de sink. Deze eigenschap is van toepassing wanneer het standaard gedrag voor kopiëren niet aan uw behoeften voldoet.<br/>Zie [schema toewijzing in de Kopieer activiteit](copy-activity-schema-and-type-mapping.md)voor meer informatie. | No |
| dataIntegrationUnits | Geef een meting op die staat voor de hoeveelheid kracht die de [Azure Integration runtime](concepts-integration-runtime.md) gebruikt voor het kopiëren van gegevens. Deze eenheden waren voorheen bekend als DMU (Cloud data units). <br/>Zie [gegevens integratie-eenheden](copy-activity-performance-features.md#data-integration-units)voor meer informatie. | No |
| parallelCopies | Geef de parallelle factor op die de Kopieer activiteit moet gebruiken bij het lezen van gegevens van de bron en het schrijven van gegevens naar de sink.<br/>Zie [parallelle kopie](copy-activity-performance-features.md#parallel-copy)voor meer informatie. | No |
| doordat | Opgeven of meta gegevens/Acl's tijdens het kopiëren van de gegevens moeten worden bewaard. <br/>Zie [meta gegevens behouden](copy-activity-preserve-metadata.md)voor meer informatie. |No |
| enableStaging<br/>stagingSettings | Geef op of de tussenliggende gegevens in Blob Storage moeten worden gestage in plaats van gegevens rechtstreeks van bron naar sink te kopiëren.<br/>Zie voor meer informatie over nuttige scenario's en configuratie Details [gefaseerde kopie](copy-activity-performance-features.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Kies hoe u incompatibele rijen wilt verwerken wanneer u gegevens kopieert van bron naar sink.<br/>Zie [fout tolerantie](copy-activity-fault-tolerance.md)voor meer informatie. | No |

## <a name="monitoring"></a>Bewaking

U kunt het uitvoeren van de Kopieer activiteit in de Azure Data Factory zowel visueel als programmatisch controleren. Zie [copy-activiteit controleren](copy-activity-monitoring.md)voor meer informatie.

## <a name="incremental-copy"></a>Incrementele kopie

Data Factory kunt u de Delta gegevens van een brongegevens archief stapsgewijs naar een Sink-gegevens archief kopiëren. Zie [zelf studie: gegevens stapsgewijs kopiëren](tutorial-incremental-copy-overview.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

In de ervaring voor het controleren van de [Kopieer activiteit](copy-activity-monitoring.md) worden de prestatie statistieken voor de Kopieer bewerking voor elk van de uitgevoerde activiteiten weer gegeven. In de [hand leiding Kopieer activiteit prestaties en schaal baarheid](copy-activity-performance.md) worden de belangrijkste factoren beschreven die van invloed zijn op de prestaties van het verplaatsen van gegevens via de Kopieer activiteit in azure Data Factory. Het bevat ook een lijst met de prestatie waarden die tijdens het testen zijn waargenomen en bespreekt hoe u de prestaties van de Kopieer activiteit kunt optimaliseren.

## <a name="resume-from-last-failed-run"></a>Hervatten vanuit de laatste uitvoering is mislukt

De Kopieer activiteit ondersteunt de laatste mislukte uitvoering wanneer u grote hoeveel heden bestanden kopieert, met een binaire indeling tussen archieven op basis van bestanden en ervoor kiest om de map/bestands hiërarchie te bewaren van bron naar sink, bijvoorbeeld om gegevens te migreren van Amazon S3 naar Azure Data Lake Storage Gen2. Dit is van toepassing op de volgende op bestanden gebaseerde connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)en [SFTP](connector-sftp.md).

U kunt op de volgende twee manieren gebruikmaken van de bewerking voor het hervatten van de Kopieer activiteit:

- **Nieuwe poging op activiteit niveau:** U kunt het aantal pogingen voor de Kopieer activiteit instellen. Als het uitvoeren van de uitvoering van de pijp lijn mislukt, wordt de volgende automatische nieuwe poging gestart vanaf het fout punt van de laatste proef versie.
- **Opnieuw uitvoeren vanaf mislukte activiteit:** Nadat de uitvoering van de pijp lijn is voltooid, kunt u ook een opnieuw uitvoeren starten vanuit de mislukte activiteit in de weer gave van de ADF-gebruikers interface of via een programma. Als de mislukte activiteit een Kopieer activiteit is, wordt de pijp lijn niet alleen opnieuw op basis van deze activiteit uitgevoerd, maar wordt ook hervat vanaf het fout punt van de vorige uitvoering.

    ![Kopiëren hervatten](media/copy-activity-overview/resume-copy.png)

Enkele punten om te noteren:

- Hervatten vindt plaats op bestands niveau. Als de Kopieer activiteit mislukt bij het kopiëren van een bestand, wordt dit specifieke bestand opnieuw gekopieerd.
- Als u wilt door gaan met werken, wijzigt u de instellingen voor de Kopieer activiteit niet tussen de herstarts.
- Wanneer u gegevens van Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 en Google Cloud Storage kopieert, kan de Kopieer activiteit uit een wille keurig aantal gekopieerde bestanden worden hervat. Hoewel de rest van op bestanden gebaseerde connectors als bron is, ondersteunt de huidige Kopieer activiteit het hervatten van een beperkt aantal bestanden, meestal ten opzichte van tien duizenden en varieert afhankelijk van de lengte van de bestands paden. bestanden die groter zijn dan dit aantal, worden opnieuw gekopieerd tijdens het opnieuw uitvoeren.

Voor andere scenario's dan het kopiëren van een binair bestand begint de Kopieer activiteit opnieuw vanaf het begin.

## <a name="preserve-metadata-along-with-data"></a>Meta gegevens in combi natie met gegevens behouden

Bij het kopiëren van gegevens van bron naar sink, in scenario's zoals data Lake Migration, kunt u ook ervoor kiezen de meta gegevens en Acl's samen met gegevens te behouden met Kopieer activiteit. Zie [meta gegevens behouden](copy-activity-preserve-metadata.md) voor meer informatie.

## <a name="schema-and-data-type-mapping"></a>Toewijzing van schema en gegevens type

Zie [schema en gegevens type toewijzing](copy-activity-schema-and-type-mapping.md) voor informatie over hoe de Kopieer activiteit uw bron gegevens aan uw Sink koppelt.

## <a name="add-additional-columns-during-copy"></a>Aanvullende kolommen toevoegen tijdens kopiëren

Naast het kopiëren van gegevens uit de brongegevens opslag naar sink, kunt u ook configureren om extra gegevens kolommen toe te voegen, samen met de sink. Bijvoorbeeld:

- Bij kopiëren van bron op basis van een bestand slaat u het relatieve bestandspad op als een extra kolom om op te sporen van welk bestand de gegevens afkomstig zijn.
- Dupliceer de opgegeven bron kolom als een andere kolom. 
- Voeg een kolom met een ADF-expressie toe om ADF-systeem variabelen als pijplijn naam/pijplijn-ID te koppelen, of sla andere dynamische waarden op van de uitvoer van de upstream-activiteit.
- Voeg een kolom toe met een statische waarde om te voldoen aan de behoeften van het stroomafwaartse verbruik.

U vindt de volgende configuratie op het tabblad Bron van Kopieer activiteit. U kunt deze extra kolommen ook toewijzen in [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping) voor kopieer activiteiten zoals gebruikelijk door gebruik te maken van uw gedefinieerde kolom namen. 

![Aanvullende kolommen toevoegen in de Kopieer activiteit](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Deze functie werkt met het meest recente gegevensset-model. Als deze optie niet wordt weer geven in de gebruikers interface, kunt u een nieuwe gegevensset maken.

Als u het programma wilt configureren, voegt u de eigenschap toe aan de `additionalColumns` bron van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| additionalColumns | Voeg aanvullende gegevens kolommen toe om te kopiëren naar sink.<br><br>Elk object onder de `additionalColumns` matrix vertegenwoordigt een extra kolom. `name`Hiermee wordt de naam van de kolom gedefinieerd en `value` wordt de waarde van de gegevens van die kolom aangegeven.<br><br>Toegestane gegevens waarden zijn:<br>- **`$$FILEPATH`** -een gereserveerde variabele geeft aan dat het relatieve pad van de bron bestanden moet worden opgeslagen in het mappad dat is opgegeven in de gegevensset. Toep assen op de bron op basis van een bestand.<br>- **`$$COLUMN:<source_column_name>`** -een gereserveerde variabelen patroon geeft aan dat de opgegeven bron kolom moet worden gedupliceerd als een andere kolom<br>- **Expressie**<br>- **Statische waarde** | No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Sink-tabellen automatisch maken

Wanneer u gegevens naar SQL database/Azure Synapse Analytics kopieert en de doel tabel niet bestaat, ondersteunt de Kopieer activiteit automatisch het maken op basis van de bron gegevens. Het helpt u snel aan de slag te gaan met het laden van de gegevens en het evalueren van SQL database/Azure Synapse Analytics. Na het opnemen van de gegevens kunt u het sink-tabel schema controleren en aanpassen op basis van uw behoeften.

Deze functie wordt ondersteund bij het kopiëren van gegevens uit een bron in de volgende Sink-gegevens opslag. U kunt de optie vinden in de *gebruikers interface van ADF-ontwerpen* – > *Kopieer activiteit Sink* – > *tabel optie* : > *tabel voor automatisch maken* of via `tableOption` eigenschap in de nettolading van de Kopieer activiteit.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Beheerde instantie Azure SQL Database](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Sink-tabellen maken](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Fouttolerantie

Standaard stopt de Kopieer activiteit met het kopiëren van gegevens en wordt een fout geretourneerd wanneer brongegevens rijen niet compatibel zijn met Sink-gegevens rijen. Als u de kopie wilt laten slagen, kunt u de Kopieer activiteit configureren om de niet-compatibele rijen over te slaan en te registreren en alleen de compatibele gegevens te kopiëren. Zie [fout tolerantie voor kopieer activiteiten](copy-activity-fault-tolerance.md) voor meer informatie.

## <a name="data-consistency-verification"></a>Verificatie van gegevensconsistentie

Wanneer u gegevens van de bron naar het doel archief verplaatst, biedt Azure Data Factory Kopieer activiteit een optie waarmee u aanvullende gegevens consistentie verificatie kunt uitvoeren om ervoor te zorgen dat de gegevens niet alleen worden gekopieerd van de bron naar het doel archief, maar ook zijn geverifieerd om consistent te zijn tussen de bron-en doel opslag. Zodra er inconsistente bestanden zijn gevonden tijdens het verplaatsen van gegevens, kunt u de Kopieer activiteit afbreken of door gaan met het kopiëren van de rest door de instelling fout tolerantie in te scha kelen om inconsistente bestanden over te slaan. U kunt de overgeslagen bestands namen ophalen door de instelling voor sessie logboek in de Kopieer activiteit in te scha kelen. Zie [verificatie van gegevens consistentie in de Kopieer activiteit](copy-activity-data-consistency.md) voor meer informatie.

## <a name="session-log"></a>Sessie logboek
U kunt de gekopieerde bestands namen registreren, zodat u er zeker van kunt zijn dat de gegevens niet alleen worden gekopieerd van de bron naar het doel archief, maar ook consistent zijn tussen het bron-en doel Archief door de sessie logboeken van de Kopieer activiteit te bekijken. Zie [sessie logboek in Kopieer activiteit](copy-activity-log.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende Quick starts, zelf studies en voor beelden:

- [Gegevens kopiëren van de ene locatie naar een andere locatie in hetzelfde Azure Blob Storage-account](quickstart-create-data-factory-dot-net.md)
- [Gegevens kopiëren van Azure Blob-opslag naar Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Gegevens van een SQL Server Data Base naar Azure kopiëren](tutorial-hybrid-copy-powershell.md)

---
title: Gegevenssets
description: Meer informatie over gegevens sets in Data Factory. Gegevens sets vertegenwoordigen invoer/uitvoer-data.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 167fb0014f2f0a9e7a2530fe276289f94347146e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785781"
---
# <a name="datasets-in-azure-data-factory"></a>Gegevenssets in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-create-datasets.md)
> * [Huidige versie](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


In dit artikel wordt beschreven welke gegevens sets zijn, hoe ze worden gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory pijp lijnen.

Als u geen ervaring hebt met Data Factory, raadpleegt u [Introduction to Azure Data Factory](introduction.md) voor een overzicht.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijp lijn** is een logische groep **activiteiten** die samen een taak uitvoeren. De activiteiten in een pijplijn bepalen acties die moeten worden uitgevoerd op uw gegevens. Nu is een **gegevensset** een benoemde weer gave van gegevens waarmee u de gegevens die u in uw **activiteiten** wilt gebruiken als invoer en uitvoer, eenvoudigweg wijst of ernaar verwijst. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Een Azure Blob-gegevensset benoemt bijvoorbeeld de blobcontainer en -map in de Blob-opslag van waaruit de activiteit de gegevens moet lezen.

Voordat u een gegevensset maakt, moet u een [**gekoppelde service**](concepts-linked-services.md) maken om uw gegevens archief te koppelen aan de Data Factory. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. U kunt het op deze manier nadenken. de gegevensset vertegenwoordigt de structuur van de gegevens in de gekoppelde gegevens archieven en de gekoppelde service definieert de verbinding met de gegevens bron. Een Azure Storage gekoppelde service koppelt bijvoorbeeld een opslag account aan de data factory. Een Azure Blob-gegevensset vertegenwoordigt de BLOB-container en de map binnen die Azure Storage account dat de invoer-blobs bevat die moeten worden verwerkt.

Hier volgt een voorbeeld scenario. Als u gegevens wilt kopiëren van Blob-opslag naar een SQL Database, maakt u twee gekoppelde services: Azure Blob Storage en Azure SQL Database. Maak vervolgens twee gegevens sets: tekst gegevensset met scheidings tekens (die verwijst naar de gekoppelde Azure Blob Storage-service, ervan uitgaande dat u tekst bestanden hebt als bron) en gegevensset voor Azure SQL-tabel (die verwijst naar de Azure SQL Database gekoppelde service). De Azure Blob Storage-en Azure SQL Database gekoppelde services bevatten verbindings reeksen die Data Factory in runtime gebruikt om respectievelijk verbinding te maken met uw Azure Storage en Azure SQL Database. De gegevensset met scheidings tekens specificeert de BLOB-container en BLOB-map die de invoer-blobs in uw Blob-opslag bevat, samen met instellingen voor de indeling. De gegevensset van de Azure SQL-tabel geeft de SQL-tabel in de SQL Database aan waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram ziet u de relaties tussen pijp lijn, activiteit, gegevensset en gekoppelde service in Data Factory:

![Relatie tussen pijp lijn, activiteit, gegevensset, gekoppelde services](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON van gegevensset
Een gegevensset in Data Factory wordt gedefinieerd in de volgende JSON-indeling:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

Eigenschap | Beschrijving | Vereist |
-------- | ----------- | -------- |
naam | De naam van de gegevensset. Zie [Azure Data Factory naamgevings regels](naming-rules.md). |  Yes |
type | Het type van de gegevensset. Geef een van de typen op die worden ondersteund door Data Factory (bijvoorbeeld: DelimitedText, AzureSqlTable). <br/><br/>Zie [type gegevensset](#dataset-type)voor meer informatie. | Yes |
schema | Het schema van de gegevensset geeft het fysieke gegevens type en de vorm aan. | No |
typeProperties | De type-eigenschappen verschillen voor elk type. Zie [type gegevensset](#dataset-type)voor meer informatie over de ondersteunde typen en hun eigenschappen. | Yes |

Wanneer u het schema van de gegevensset importeert, selecteert u de knop **schema importeren** en kiest u importeren uit de bron of van een lokaal bestand. In de meeste gevallen importeert u het schema rechtstreeks vanuit de bron. Maar als u al een lokaal schema bestand (een Parquet-bestand of CSV met headers) hebt, kunt u Data Factory door sturen om het schema op dat bestand te baseren.

In de Kopieer activiteit worden gegevens sets gebruikt in de bron-en Sink-bewerking. Het schema dat in de gegevensset is gedefinieerd, is optioneel als verwijzing. Als u een kolom-of veld toewijzing tussen de bron-en Sink wilt Toep assen, raadpleegt u [schema-en type toewijzing](copy-activity-schema-and-type-mapping.md).

In gegevens stroom worden gegevens sets gebruikt in bron-en Sink-trans formaties. In de gegevens sets worden de basis gegevens schema's gedefinieerd. Als uw gegevens geen schema hebben, kunt u schema-drift gebruiken voor uw bron en Sink. Meta gegevens uit de gegevens sets worden weer gegeven in de bron transformatie als bron projectie. De projectie in de bron transformatie vertegenwoordigt de gegevens stroom gegevens met gedefinieerde namen en typen.

## <a name="dataset-type"></a>Type gegevensset

Azure Data Factory ondersteunt veel verschillende typen gegevens sets, afhankelijk van de gegevensopslag plaatsen die u gebruikt. De lijst met gegevens archieven die worden ondersteund door Data Factory, vindt u in het [overzichts artikel van connectors](connector-overview.md) . Klik op een gegevens Archief voor informatie over het maken van een gekoppelde service en een gegevensset hiervoor.

Voor een gegevensset met scheidings tekens is het type gegevensset bijvoorbeeld ingesteld op **DelimitedText** , zoals wordt weer gegeven in de volgende JSON-voor beeld:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Gegevenssets maken
U kunt gegevens sets maken met behulp van een van deze hulpprogram ma's of Sdk's: [.net API](quickstart-create-data-factory-dot-net.md), [power shell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager sjabloon en Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Huidige versie versus gegevens sets van versie 1

Hier volgen enkele verschillen tussen Data Factory en Data Factory gegevens sets van versie 1:

- De eigenschap external wordt niet ondersteund in de huidige versie. Het wordt vervangen door een [trigger](concepts-pipeline-execution-triggers.md).
- De eigenschappen beleid en beschik baarheid worden niet ondersteund in de huidige versie. De begin tijd voor een pijp lijn is afhankelijk van [Triggers](concepts-pipeline-execution-triggers.md).
- Bereik gegevens sets (gegevens sets die zijn gedefinieerd in een pijp lijn) worden niet ondersteund in de huidige versie.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende zelf studie voor stapsgewijze instructies voor het maken van pijp lijnen en gegevens sets met behulp van een van deze hulpprogram ma's of Sdk's.

- [Snelstartgids: een gegevensfactory maken met .NET](quickstart-create-data-factory-dot-net.md)
- [Snelstartgids: een data factory maken met Power shell](quickstart-create-data-factory-powershell.md)
- [Snelstartgids: een data factory maken met behulp van REST API](quickstart-create-data-factory-rest-api.md)
- [Snelstartgids: een data factory maken met behulp van Azure Portal](quickstart-create-data-factory-portal.md)

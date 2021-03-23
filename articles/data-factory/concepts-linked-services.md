---
title: Gekoppelde services in Azure Data Factory
description: Meer informatie over gekoppelde services in Data Factory. Gekoppelde services koppelen reken-en gegevens archieven aan data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: aaa690a4205951bd251a5230721e34fcb960a3b1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782738"
---
# <a name="linked-services-in-azure-data-factory"></a>Gekoppelde services in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-create-datasets.md)
> * [Huidige versie](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven wat gekoppelde services zijn, hoe ze worden gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory pijp lijnen.

Als u niet bekend bent met Data Factory, raadpleegt u [Introduction to Azure Data Factory](introduction.md) voor een overzicht.

## <a name="overview"></a>Overzicht

Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijp lijn** is een logische groep **activiteiten** die samen een taak uitvoeren. De activiteiten in een pijplijn bepalen acties die moeten worden uitgevoerd op uw gegevens. U kunt bijvoorbeeld een Kopieer activiteit gebruiken om gegevens te kopiëren van SQL Server naar Azure Blob-opslag. Vervolgens kunt u een Hive-activiteit gebruiken waarmee een Hive-script op een Azure HDInsight-cluster wordt uitgevoerd om gegevens uit de Blob-opslag te verwerken om uitvoer gegevens te produceren. Ten slotte kunt u een tweede Kopieer activiteit gebruiken om de uitvoer gegevens te kopiëren naar Azure Synapse Analytics, op het punt waar business intelligence (BI) Reporting-oplossingen zijn gebouwd. Voor meer informatie over pijp lijnen en activiteiten raadpleegt u [pijp lijnen en activiteiten](concepts-pipelines-activities.md) in azure Data Factory.

Nu is een **gegevensset** een benoemde weer gave van gegevens waarmee u de gegevens die u in uw **activiteiten** wilt gebruiken als invoer en uitvoer, eenvoudigweg wijst of ernaar verwijst.

Voordat u een gegevensset maakt, moet u een **gekoppelde service** maken om uw gegevens archief te koppelen aan de Data Factory. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. U kunt het op deze manier nadenken. de gegevensset vertegenwoordigt de structuur van de gegevens in de gekoppelde gegevens archieven en de gekoppelde service definieert de verbinding met de gegevens bron. Een Azure Storage gekoppelde service koppelt bijvoorbeeld een opslag account aan de data factory. Een Azure Blob-gegevensset vertegenwoordigt de BLOB-container en de map binnen die Azure Storage account dat de invoer-blobs bevat die moeten worden verwerkt.

Hier volgt een voorbeeld scenario. Als u gegevens wilt kopiëren van Blob-opslag naar een SQL Database, maakt u twee gekoppelde services: Azure Storage en Azure SQL Database. Maak vervolgens twee gegevens sets: Azure Blob-gegevensset (die verwijst naar de Azure Storage gekoppelde service) en de gegevensset van de Azure SQL-tabel (die verwijst naar de Azure SQL Database gekoppelde service). De Azure Storage-en Azure SQL Database gekoppelde services bevatten verbindings reeksen die Data Factory in runtime gebruiken om respectievelijk verbinding te maken met uw Azure Storage en Azure SQL Database. De Azure Blob-gegevensset bevat de BLOB en BLOB-map die de invoer-blobs in uw Blob-opslag bevat. De gegevensset van de Azure SQL-tabel geeft de SQL-tabel in de SQL Database aan waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram ziet u de relaties tussen pijp lijn, activiteit, gegevensset en gekoppelde service in Data Factory:

![Relatie tussen pijp lijn, activiteit, gegevensset, gekoppelde services](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON van gekoppelde service

Een gekoppelde service in Data Factory wordt als volgt gedefinieerd in JSON-indeling:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

Eigenschap | Beschrijving | Vereist |
-------- | ----------- | -------- |
naam | De naam van de gekoppelde service. Zie [Azure Data Factory naamgevings regels](naming-rules.md). |  Ja |
type | Het type van de gekoppelde service. Bijvoorbeeld: AzureBlobStorage (Data Store) of AzureBatch (Compute). Zie de beschrijving voor typeProperties. | Ja |
typeProperties | De type-eigenschappen verschillen voor elke gegevens opslag of compute. <br/><br/> Zie het [overzichts](copy-activity-overview.md#supported-data-stores-and-formats) artikel van de connector voor de ondersteunde typen gegevens opslag en de type-eigenschappen ervan. Navigeer naar het gegevens archief connector artikel voor meer informatie over type-eigenschappen die specifiek zijn voor een gegevens archief. <br/><br/> Zie [gekoppelde services berekenen](compute-linked-services.md)voor de ondersteunde reken typen en de bijbehorende type-eigenschappen. | Ja |
connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee

## <a name="linked-service-example"></a>Voor beeld van gekoppelde service

De volgende gekoppelde service is een gekoppelde Azure Blob Storage-service. U ziet dat het type is ingesteld op Azure Blob-opslag. De type-eigenschappen voor de gekoppelde Azure Blob Storage-service omvatten een connection string. De Data Factory-service gebruikt deze connection string om tijdens de uitvoering verbinding te maken met het gegevens archief.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Gekoppelde services maken

Gekoppelde services kunnen worden gemaakt in de Azure Data Factory UX via de [beheer hub](author-management-hub.md) en alle activiteiten, gegevens sets of data stromen die ernaar verwijzen.

U kunt gekoppelde services maken met behulp van een van deze hulpprogram ma's of Sdk's: [.net API](quickstart-create-data-factory-dot-net.md), [power shell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager sjabloon en Azure Portal.


## <a name="data-store-linked-services"></a>Gekoppelde services voor gegevens opslag

De lijst met gegevens archieven die worden ondersteund door Data Factory, vindt u in het [overzichts artikel van connectors](copy-activity-overview.md#supported-data-stores-and-formats) . Klik op een gegevens Archief voor meer informatie over de ondersteunde verbindings eigenschappen.

## <a name="compute-linked-services"></a>Gekoppelde services berekenen

Referentie [berekenings omgevingen](compute-linked-services.md) die worden ondersteund voor meer informatie over de verschillende reken omgevingen waarmee u verbinding kunt maken vanuit uw Data Factory en van de verschillende configuraties.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende zelf studie voor stapsgewijze instructies voor het maken van pijp lijnen en gegevens sets met behulp van een van deze hulpprogram ma's of Sdk's.

- [Snelstartgids: een gegevensfactory maken met .NET](quickstart-create-data-factory-dot-net.md)
- [Snelstartgids: een data factory maken met Power shell](quickstart-create-data-factory-powershell.md)
- [Snelstartgids: een data factory maken met behulp van REST API](quickstart-create-data-factory-rest-api.md)
- [Snelstartgids: een data factory maken met behulp van Azure Portal](quickstart-create-data-factory-portal.md)

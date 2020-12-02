---
title: Wrangling-gegevens stromen in Azure Data Factory
description: Een overzicht van de wrangling-gegevens stromen in Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 607ed6167ec761a6bf380a2cc420eb72f808f0a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494865"
---
# <a name="what-are-wrangling-data-flows"></a>Wat zijn wrangling-gegevens stromen?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Organisaties moeten gegevens voorbereiding en wrangling voor nauw keurige analyse van complexe gegevens die steeds elke dag blijven groeien. Het voorbereiden van gegevens is vereist zodat organisaties de gegevens in verschillende bedrijfs processen kunnen gebruiken en de tijd tot waarde kan verlagen.

Met Wrangling-gegevens stromen in Azure Data Factory kunt u de gegevens voorbereiding voor code gratis uitvoeren op de Cloud schaal. Wrangling-gegevens stromen kunnen worden geïntegreerd met [Power query online](/power-query/) en er zijn Power query M-functies beschikbaar voor Data Factory gebruikers.

Wrangling-gegevens stroom vertaalt M die is gegenereerd door Power Query online Mashup-Editor in Spark-code voor het uitvoeren van Cloud schalen.

Wrangling-gegevens stromen zijn vooral nuttig voor gegevens technici of ' burger data Integrators '.

> [!NOTE]
> Wrangling-gegevens stroom is momenteel beschikbaar in de open bare preview

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snel interactieve gegevens verkennen en voorbereiden

Meerdere gegevens technici en burger data integrators kunnen gegevens sets interactief verkennen en voorbereiden op Cloud schaal. Dankzij de toename van het volume, de verscheidenheid en de snelheid van gegevens op het meren deel, hebben gebruikers een efficiënte manier nodig om gegevens sets te verkennen en voor te bereiden. U moet bijvoorbeeld een gegevensset maken die alle demografische gegevens van de klant voor nieuwe klanten sinds 2017 bevat. U bent niet toegewezen aan een bekend doel. U kunt gegevens sets verkennen, wrangling en bereidt om te voldoen aan een vereiste voordat u deze in het Lake publiceert. Wrangling-gegevens stromen worden vaak gebruikt voor minder formele analyse scenario's. De bereid-gegevens sets kunnen worden gebruikt voor het uitvoeren van trans formaties en machine learning bewerkingen stroomafwaarts.

### <a name="code-free-agile-data-preparation"></a>Code-gratis flexibele gegevens voorbereiding

Burger data integrators best Eden meer dan 60% van de tijd om gegevens te zoeken en voor te bereiden. Ze zijn op zoek naar een gratis manier om de operationele productiviteit te verbeteren. Met burger data integrators kunnen gegevens worden verrijkt, gevormd en gepubliceerd met behulp van bekende hulpprogram ma's, zoals Power Query online, op een schaal bare manier, waardoor hun productiviteit aanzienlijk wordt verbeterd. Met Wrangling data flow in Azure Data Factory kunt u met de vertrouwde Power Query online Mashup-Editor voor burger data integrators snel fouten corrigeren, gegevens normaliseren en gegevens van hoge kwaliteit produceren ter ondersteuning van zakelijke beslissingen.

### <a name="data-validation"></a>Gegevensvalidatie

Scan uw gegevens visueel op een code-Free manier om uitschieters en afwijkingen te verwijderen en deze aan een vorm voor snelle analyse te voldoen.

## <a name="supported-sources"></a>Ondersteunde bronnen

| Connector | Gegevensindeling | Verificatietype |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Account sleutel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Service-principal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Account sleutel, Service-Principal |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-verificatie |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-verificatie |

## <a name="the-mashup-editor"></a>De Mashup-Editor

Wanneer u een wrangling-gegevens stroom maakt, worden alle bron gegevens sets gegevensset-query's en worden deze in de map **ADFResource** geplaatst. Standaard wordt de UserQuery naar de eerste gegevensset-query verwijzen. Alle trans formaties moeten worden uitgevoerd op de UserQuery als wijzigingen in de query van de gegevensset niet worden ondersteund en blijven niet behouden. Het wijzigen van de naam, het toevoegen en verwijderen van query's wordt momenteel niet ondersteund.

![Wrangling](media/wrangling-data-flow/editor.png)

Momenteel niet alle Power Query M-functies worden ondersteund voor gegevens wrangling ondanks dat deze beschikbaar zijn tijdens het ontwerpen. Tijdens het maken van uw wrangling-gegevens stromen wordt u met het volgende fout bericht gevraagd als een functie niet wordt ondersteund:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Zie [wrangling data flow functions](wrangling-data-flow-functions.md)(Engelstalig) voor meer informatie over ondersteunde trans formaties.

Momenteel wordt de wrangling-gegevens stroom alleen ondersteund voor het schrijven naar één sink.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een wrangling-gegevens stroom](wrangling-data-flow-tutorial.md).
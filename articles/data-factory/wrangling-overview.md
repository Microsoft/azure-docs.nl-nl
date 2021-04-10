---
title: Gegevens wrangling in Azure Data Factory
description: Een overzicht van de gegevens Wrangling in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738166"
---
# <a name="what-is-data-wrangling"></a>Wat is gegevens wrangling?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Organisaties moeten hun essentiële Bedrijfs gegevens voor de voor bereiding van gegevens en wrangling kunnen verkennen om nauw keurige analyse van complexe gegevens te kunnen leveren die elke dag blijft groeien. Het voorbereiden van gegevens is vereist zodat organisaties de gegevens in verschillende bedrijfs processen kunnen gebruiken en de tijd tot waarde kan verlagen.

Data Factory biedt iteratieve code ring van gegevens op de Cloud met behulp van Power Query. Data Factory integreert met [Power query online](/power-query/) en maakt Power query M-functies beschikbaar als pijplijn activiteit.

Data Factory vertaalt M dat door de Power Query online Mashup-Editor is gegenereerd, in Spark-code voor het uitvoeren van Cloud schalen door M te vertalen in Azure Data Factory gegevens stromen. Wrangling gegevens met Power Query en gegevens stromen zijn vooral nuttig voor gegevens technici of ' burger data Integrators '.

> [!NOTE]
> De Power Query activiteit in Azure Data Factory is momenteel beschikbaar in de open bare preview

## <a name="use-cases"></a>Gebruiksvoorbeelden

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snel interactieve gegevens verkennen en voorbereiden

Meerdere gegevens technici en burger data integrators kunnen gegevens sets interactief verkennen en voorbereiden op Cloud schaal. Dankzij de toename van het volume, de verscheidenheid en de snelheid van gegevens op het meren deel, hebben gebruikers een efficiënte manier nodig om gegevens sets te verkennen en voor te bereiden. U moet bijvoorbeeld een gegevensset maken die alle demografische gegevens van de klant voor nieuwe klanten sinds 2017 bevat. U bent niet toegewezen aan een bekend doel. U kunt gegevens sets verkennen, wrangling en bereidt om te voldoen aan een vereiste voordat u deze in het Lake publiceert. Wrangling wordt vaak gebruikt voor minder formele analyse scenario's. De bereid-gegevens sets kunnen worden gebruikt voor het uitvoeren van trans formaties en machine learning bewerkingen stroomafwaarts.

### <a name="code-free-agile-data-preparation"></a>Code-gratis flexibele gegevens voorbereiding

Burger data integrators best Eden meer dan 60% van de tijd om gegevens te zoeken en voor te bereiden. Ze zijn op zoek naar een gratis manier om de operationele productiviteit te verbeteren. Met burger data integrators kunnen gegevens worden verrijkt, gevormd en gepubliceerd met behulp van bekende hulpprogram ma's, zoals Power Query online, op een schaal bare manier, waardoor hun productiviteit aanzienlijk wordt verbeterd. Wrangling in Azure Data Factory maakt de vertrouwde Power Query online Mashup-Editor de mogelijkheid om burger data integrators snel fouten op te lossen, gegevens te standaardiseren en gegevens van hoge kwaliteit te maken ter ondersteuning van zakelijke beslissingen.

### <a name="data-validation-and-exploration"></a>Gegevens validatie en-exploratie

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

Wanneer u een Power Query activiteit maakt, worden alle bron gegevens sets gegevensset-query's en worden deze in de map **ADFResource** geplaatst. Standaard wordt de UserQuery naar de eerste gegevensset-query verwijzen. Alle trans formaties moeten worden uitgevoerd op de UserQuery als wijzigingen in de query van de gegevensset niet worden ondersteund en blijven niet behouden. Het wijzigen van de naam, het toevoegen en verwijderen van query's wordt momenteel niet ondersteund.

![Wrangling](media/wrangling-data-flow/editor.png)

Momenteel niet alle Power Query M-functies worden ondersteund voor gegevens wrangling ondanks dat deze beschikbaar zijn tijdens het ontwerpen. Tijdens het maken van uw Power Query-activiteiten wordt u met het volgende fout bericht gevraagd als een functie niet wordt ondersteund:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Zie [Data wrangling functions](wrangling-functions.md)(Engelstalig) voor meer informatie over ondersteunde trans formaties.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een gegevens wrangling Power query mix verder](wrangling-tutorial.md).

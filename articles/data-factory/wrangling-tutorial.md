---
title: Aan de slag met wrangling data flow in Azure Data Factory
description: Een zelf studie over het voorbereiden van gegevens in Azure Data Factory met behulp van wrangling-gegevens stroom
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f32739b06920f6b20dc87b8e1fbd2884c323a859
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633920"
---
# <a name="prepare-data-with-data-wrangling"></a>Gegevens voorbereiden met data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Met data wrangling in data factory kunt u interactieve Power Query mix verder-UPS systeem eigen maken in ADF en vervolgens deze op schaal uitvoeren binnen een ADF-pijp lijn.

> [!NOTE]
> Power Query acitivty in ADF is momenteel beschik bare in open bare preview

## <a name="create-a-power-query-activity"></a>Een Power Query activiteit maken

Er zijn twee manieren om een Power Query te maken in Azure Data Factory. U kunt ook op het plus-pictogram klikken en **gegevens stroom** selecteren in het deel venster Factory resources.

> [!NOTE]
> Voorheen bevindt de functie data wrangling zich in de werk stroom van de gegevens stroom. Nu maakt u uw gegevens wrangling mix verder-up van ```New > Power query```

![Scherm afbeelding met Power Query in het deel venster Factory resources.](media/data-flow/power-query-wrangling.png)

De andere methode bevindt zich in het deel venster activiteiten van het pijp lijn-canvas. Open de **Power query** accordeon en sleep de **Power query** activiteit op het canvas.

![Scherm afbeelding die de Wrangling-gegevens stroom optie markeert.](media/data-flow/power-query-activity.png)

## <a name="author-a-wrangling-data-flow"></a>Een wrangling-gegevens stroom schrijven

Voeg een **bron gegevensset** toe voor uw Power query mix verder. U kunt ofwel een bestaande gegevensset kiezen of een nieuwe maken. U kunt ook een Sink-gegevensset selecteren. U kunt een of meer bron gegevens sets kiezen, maar er is op dit moment slechts één sink toegestaan. Het kiezen van een Sink-gegevensset is optioneel, maar er is ten minste één bron-gegevensset vereist.

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Klik op **maken** om de Power query online Mashup-Editor te openen.

![Scherm opname van de knop maken waarmee de Power Query online Mashup-Editor wordt geopend.](media/wrangling-data-flow/tutorial5.png)

Maak uw wrangling-gegevens stroom met behulp van code-Free Data Preparation. Zie [transformatie functies](wrangling-functions.md)voor een lijst met beschik bare functies. Met ADF wordt het ```M``` script omgezet in een script voor gegevens stroom, zodat u uw Power query op schaal kunt uitvoeren met behulp van de ADF-gegevens stroom Spark-omgeving.

![Scherm opname van het proces voor het ontwerpen van uw wrangling-gegevens stroom.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Een wrangling-gegevens stroom uitvoeren en controleren

Klik op **debug** in het canvas op de pijp lijn om het uitvoeren van een pijp lijn fout opsporing uit te voeren voor een wrangling-gegevens stroom. Zodra u de gegevens stroom hebt gepubliceerd, voert de **trigger nu** een uitvoering op aanvraag uit van de laatste gepubliceerde pijp lijn. Wrangling-gegevens stromen kunnen worden gepland met alle bestaande Azure Data Factory triggers.

![Scherm afbeelding die laat zien hoe u een wrangling-gegevens stroom kunt toevoegen.](media/wrangling-data-flow/tutorial3.png)

Ga naar het tabblad **monitor** om de uitvoer te visualiseren van een triggered wrangling-activiteit voor het uitvoeren van gegevens stromen.

![Scherm opname van de uitvoer van een triggered wrangling-activiteit voor het uitvoeren van gegevens stromen.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een toewijzings gegevens stroom](tutorial-data-flow.md).

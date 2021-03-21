---
title: Power Query activiteit in Azure Data Factory
description: Meer informatie over het gebruik van de Power Query activiteit voor gegevens wrangling-functies in een Data Factory-pijp lijn
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385452"
---
# <a name="power-query-activity-in-data-factory"></a>Power query-activiteit in data factory

Met de Power Query-activiteit kunt u Power Query mix verder-ups bouwen en uitvoeren om gegevens wrangling op schaal uit te voeren in een Data Factory-pijp lijn. U kunt een nieuw Power Query mix verder maken op basis van de menu optie nieuwe resources of door een stroom activiteit toe te voegen aan de pijp lijn.

![Scherm afbeelding met Power Query in het deel venster Factory resources.](media/data-flow/power-query-wrangling.png)

Voorheen werden gegevens wrangling in Azure Data Factory gemaakt met de menu optie gegevens stroom. Dit is gewijzigd in ontwerpen vanuit een nieuwe Power Query-activiteit. U kunt rechtstreeks in de Power Query mix verder-up-editor werken om interactieve gegevens te verkennen en vervolgens uw werk op te slaan. Zodra u klaar bent, kunt u uw Power Query activiteiten nemen en toevoegen aan een pijp lijn. Azure Data Factory schaalt deze automatisch en operationeel maken uw gegevens wrangling met behulp van de data stroom Spark-omgeving van Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Vertaling naar gegevens stroom script

Om de schaal te verg Roten met uw Power Query-activiteit, Azure Data Factory uw ```M``` script vertalen in een gegevensstroom script, zodat u uw Power query op schaal kunt uitvoeren met behulp van de data flow Spark-omgeving van Azure Data Factory. Maak uw wrangling-gegevens stroom met behulp van code-Free Data Preparation. Zie [transformatie functies](wrangling-functions.md)voor een lijst met beschik bare functies.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de concepten van data wrangling met behulp [van Power query in azure Data Factory](wrangling-tutorial.md)

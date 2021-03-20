---
title: 'Kolom transformatie selecteren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de transformatie module select columns in Azure Machine Learning Designer kunt gebruiken om een SELECT trans formatie uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420713"
---
# <a name="select-columns-transform"></a>Kolomtransformatie selecteren

In dit artikel wordt beschreven hoe u de module column Transform SELECT in Azure Machine Learning Designer kunt gebruiken. Het doel van de transformatie module select columns is ervoor te zorgen dat een voorspel bare, consistente set kolommen wordt gebruikt in downstream-machine learning bewerkingen.

Deze module is handig voor taken zoals scores, waarvoor specifieke kolommen zijn vereist. Wijzigingen in de beschik bare kolommen kunnen de pijp lijn verstoren of de resultaten wijzigen.

U kunt kolommen transformeren selecteren gebruiken om een set kolommen te maken en op te slaan. Vervolgens gebruikt u de module [trans formatie Toep](apply-transformation.md) assen om deze selecties toe te passen op nieuwe gegevens.

## <a name="how-to-use-select-columns-transform"></a>De optie kolommen transformeren gebruiken

In dit scenario wordt ervan uitgegaan dat u de functie selectie wilt gebruiken om een dynamische set kolommen te genereren die wordt gebruikt voor het trainen van een model. Om ervoor te zorgen dat de kolom selecties hetzelfde zijn voor het Score proces, gebruikt u de transformatie module select columns om de kolom selecties vast te leggen en ze elders in de pijp lijn toe te passen.

1. Voeg een invoer-gegevensset toe aan uw pijp lijn in de ontwerp functie.

2. Een exemplaar van de [functie selectie op basis van filters](filter-based-feature-selection.md)toevoegen.

3. Verbind de modules en configureer de module functie selectie om automatisch een aantal beste functies in de invoer-gegevensset te vinden.

4. Een exemplaar van [Train model](train-model.md) toevoegen en de uitvoer van [functie selectie op basis van filters](filter-based-feature-selection.md) gebruiken als invoer voor de training.

    > [!IMPORTANT]
    > Omdat de prioriteit van de functie is gebaseerd op de waarden in de kolom, kunt u niet van tevoren weten welke kolommen beschikbaar kunnen zijn voor het trainen van het [model](train-model.md).  
5. Koppel een instantie van de transformatie module select columns. 

    Met deze stap wordt een kolom selectie gegenereerd als een trans formatie die kan worden opgeslagen of toegepast op andere gegevens sets. Met deze stap zorgt u ervoor dat de kolommen die zijn geïdentificeerd in functie selectie, worden opgeslagen voor andere modules die u opnieuw kunt gebruiken.

6. Voeg de module [score model](score-model.md) toe. 

   *Verbind de invoer gegevensset niet.* Voeg in plaats daarvan de module [trans formatie Toep assen](apply-transformation.md) toe en verbind de uitvoer van de functie selectie transformatie.

   De pijplijn structuur moet er als volgt uitzien:

   > [!div class="mx-imgBorder"]
   > ![Voorbeeld pijplijn](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > Het is niet mogelijk om op [filters gebaseerde functie selectie](filter-based-feature-selection.md) toe te passen op de Score gegevensset en dezelfde resultaten te verkrijgen. Omdat de functie selectie is gebaseerd op waarden, kan er een andere set kolommen worden gekozen, waardoor de Score bewerking mislukt.
    
7. Verzend de pijp lijn.

Dit proces voor het opslaan en vervolgens Toep assen van een kolom selectie zorgt ervoor dat hetzelfde gegevens schema beschikbaar is voor training en scores.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

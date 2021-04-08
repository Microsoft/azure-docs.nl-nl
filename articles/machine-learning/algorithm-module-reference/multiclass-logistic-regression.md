---
title: 'Multi klasse logistiek regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module logistiek-regressie (Multi Class) in Azure Machine Learning Designer om meerdere waarden te voors pellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420730"
---
# <a name="multiclass-logistic-regression-module"></a>Module logistiek-regressie voor multi klasse

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Met deze module kunt u een logistiek regressie model maken dat kan worden gebruikt om meerdere waarden te voors pellen.

Classificatie met behulp van logistiek regressie is een geclassificeerde leer methode en vereist daarom een gegevensset met een label. U traint het model door het model en de gelabelde gegevensset op te geven als invoer voor een module, zoals [Train model](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor nieuwe invoer voorbeelden te voors pellen.

Azure Machine Learning biedt ook een [logistiek regressie module van twee klassen](./two-class-logistic-regression.md) , die geschikt is voor de classificatie van binaire of dichotomous variabelen.

## <a name="about-multiclass-logistic-regression"></a>Over een logistiek regressie voor multi klasse

Logistiek regressie is een bekende methode in statistieken die wordt gebruikt om de kans op een resultaat te voors pellen en populair is voor classificatie taken. Het algoritme voor spelt de kans op een gebeurtenis door gegevens aan een logistiek functie toe te passen. 

In een logistiek-regressie met meerdere klassen kan de classificatie worden gebruikt voor het voors pellen van meervoudige resultaten.

## <a name="configure-a-multiclass-logistic-regression"></a>Een logistiek-regressie met meer klasse configureren

1. Voeg de module **logistiek-regressie** (Multi Class) toe aan de pijp lijn.

2. Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    + **Eén para meter**: gebruik deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op.

    + **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.  

3. **Optimalisatie tolerantie**, geeft u de drempel waarde voor optimalisatie convergentie op. Als de verbetering tussen herhalingen kleiner is dan de drempel waarde, wordt het algoritme gestopt en wordt het huidige model geretourneerd.

4. **L1 regularisatie Weight**, **L2-regularisatie gewicht**: Typ een waarde die moet worden gebruikt voor de regularisatie-para meters L1 en L2. Een andere waarde dan nul wordt aanbevolen voor beide.

    Regularisatie is een methode voor het voor komen van overmontage door het bestraffen van modellen met extreme coëfficiënt waarden. Regularisatie werkt door de sanctie toe te voegen die is gekoppeld aan coëfficiënt waarden voor de fout van de hypo these. Een nauw keurig model met extreme coëfficiënt waarden wordt meer bestraft, maar een minder nauw keurig model met meer conservatieve waarden zou minder worden bestraft.

     L1 en L2-regularisatie hebben verschillende effecten en worden gebruikt. L1 kan worden toegepast op sparse modellen, wat handig is bij het werken met zeer dimensionale gegevens. In tegens telling tot L2-regularisatie is de voor keur voor gegevens die niet sparse zijn.  Dit algoritme ondersteunt een lineaire combi natie van L1-en L2-regularisatie-waarden: dat wil zeggen, als `x = L1` en `y = L2` , `ax + by = c` de lineaire reeks van de regularisatie-voor waarden definieert.

     Er zijn verschillende lineaire combi Naties van L1-en L2-termen ontwikkeld voor logistieke regressie modellen, zoals [elastische net regularisatie](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Wille keurig getal Seed**: Typ een geheel getal dat moet worden gebruikt als seed voor het algoritme als u wilt dat de resultaten herhaaldelijk worden uitgevoerd. Anders wordt een waarde van de systeem klok gebruikt als seed, waardoor er iets andere resultaten kunnen worden geproduceerd in uitvoeringen van dezelfde pijp lijn.

8. Verbind een gegevensset met een label en Train het model:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.

9. Verzend de pijp lijn.



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
---
title: 'Two-Class gemiddelde Perceptron: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Two-Class Gem Perceptron module in de ontwerp functie om een binaire classificatie te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421138"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class gemiddelde Perceptron-module

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een machine learning model te maken op basis van het gemiddelde Perceptron-algoritme.  
  
Dit classificatie algoritme is een leer methode met toezicht en vereist een *gecodeerde gegevensset*, die een kolom Label bevat. U kunt het model trainen door het model en de gecodeerde gegevensset op te geven als invoer voor het [trainen van modellen](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden te voors pellen voor de nieuwe invoer voorbeelden.  

### <a name="about-averaged-perceptron-models"></a>Over gemiddelde Perceptron-modellen

De *methode gemiddeld Perceptron* is een vroege en eenvoudige versie van een Neural-netwerk. In deze benadering worden de invoer ingedeeld in verschillende mogelijke uitvoer op basis van een lineaire functie en vervolgens gecombineerd met een set gewichten die zijn afgeleid van de functie Vector, dus de naam ' Perceptron '.

De eenvoudiger Perceptron modellen zijn geschikt voor het leren van lineaire opvallende patronen, terwijl Neural netwerken (met name Neural Networks) een complexere klassen grenzen kunnen vormen. Perceptrons zijn echter sneller en omdat de cases serieel worden verwerkt, kan Perceptrons worden gebruikt met doorlopende training.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Two-Class gemiddelde Perceptron configureren

1.  Voeg de **gemiddelde Perceptron-module met twee klassen** toe aan de pijp lijn.  

2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: als u weet hoe u het model wilt configureren, geeft u een specifieke set waarden als argumenten op.

    -   **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.  
  
3.  Geef voor het **leer tempo** een waarde op voor het *leer tempo*. De waarden voor het leer tempo bepalen de grootte van de stap die wordt gebruikt in stochastische verloop Daal telkens wanneer het model wordt getest en gecorrigeerd.
  
     Door het aantal kleiner te maken, test u het model vaker, met het risico dat u mogelijk vastloopt in een lokaal vaste waarde. Door de stap groter te maken, kunt u sneller convergeren, tegen het risico van een overschrijding van de echte minima.
  
4.  Voor het **maximum aantal herhalingen** typt u het aantal keren dat het algoritme de trainings gegevens moet onderzoeken.  
  
     Als u een vroege regel matig stopt, levert dat betere generalisatie. Het verhogen van het aantal iteraties verbetert de fitting, tegen het risico van overmontage.
  
5.  Voor **wille keurig getal zaad** typt u optioneel een geheel getal dat moet worden gebruikt als seed. Het gebruik van een Seed wordt aanbevolen als u de reproduceer baarheid van de pijp lijn in uitvoeringen wilt garanderen.  
  
1.  Verbind een trainings gegevensset en Train het model:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.




## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
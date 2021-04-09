---
title: 'Two-Class logistiek regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Two-Class logistiek-regressie module in Azure Machine Learning om een binaire classificatie te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012643"
---
# <a name="two-class-logistic-regression-module"></a>Module logistiek regressie Two-Class

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Met deze module kunt u een logistiek regressie model maken dat kan worden gebruikt om twee (en slechts twee) resultaten te voors pellen. 

Logistiek regressie is een goed bekende statistische techniek die wordt gebruikt voor het model leren van veel problemen. Dit algoritme is een *gesupere leer* methode;  Daarom moet u een gegevensset opgeven die al de resultaten bevat om het model te trainen.  

### <a name="about-logistic-regression"></a>Over logistieke regressie  

Logistiek regressie is een bekende methode in statistieken die wordt gebruikt om de kans op een resultaat te voors pellen en is met name populair bij classificatie taken. Het algoritme voor spelt de kans op een gebeurtenis door gegevens aan een logistiek functie toe te passen.
  
In deze module is het classificatie algoritme geoptimaliseerd voor dichotomous of binaire variabelen. Als u meerdere resultaten wilt classificeren, gebruikt u de module [logistiek-regressie](./multiclass-logistic-regression.md) (multi klasse).

##  <a name="how-to-configure"></a>Configureren  

Als u dit model wilt trainen, moet u een gegevensset opgeven die een kolom van het type label of klasse bevat. Omdat deze module is bedoeld voor problemen met twee klassen, moet de kolom Label of klasse precies twee waarden bevatten. 

De kolom Label kan bijvoorbeeld [gestemd] zijn met mogelijke waarden ' Yes ' of ' no '. Het kan ook [Credit Risk] zijn, met mogelijke waarden van ' hoog ' of ' laag '. 
  
1.  Voeg de module **logistiek-regressie met twee klassen** toe aan uw pijp lijn.  
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.  

    -   **Parameter bereik**: als u niet zeker weet wat de beste para meters zijn, kunt u de optimale para meters vinden met behulp van de Hyper parameters-module voor het [afstemmen van modellen](tune-model-hyperparameters.md) . U geeft een aantal waarden op, en de trainer herhaalt meerdere combi Naties van de instellingen om de combi natie van waarden te bepalen die het beste resultaat oplevert.
  
3.  Geef bij **optimalisatie tolerantie** een drempel waarde op die moet worden gebruikt bij het optimaliseren van het model. Als de verbetering tussen herhalingen onder de opgegeven drempel waarde valt, wordt het algoritme gezien als geconvergeerd op een oplossing en stopt de training.  
  
4.  Typ voor **L1 regularisatie Weight** en **L2 regularisatie Weight** een waarde om te gebruiken voor de regularisatie-para meters L1 en L2. Een andere waarde dan nul wordt aanbevolen voor beide.  
     *Regularisatie* is een methode voor het voor komen van overmontage door het bestraffen van modellen met extreme coëfficiënt waarden. Regularisatie werkt door de sanctie toe te voegen die is gekoppeld aan coëfficiënt waarden voor de fout van de hypo these. Een nauw keurig model met extreme coëfficiënt waarden zou dus meer worden bestraft, maar een minder nauw keurig model met meer conservatieve waarden zou minder worden bestraft.  
  
     L1 en L2-regularisatie hebben verschillende effecten en worden gebruikt.  
  
    -   L1 kan worden toegepast op sparse modellen, wat handig is bij het werken met zeer dimensionale gegevens.  
  
    -   In tegens telling tot L2-regularisatie is de voor keur voor gegevens die niet sparse zijn.  
  
     Dit algoritme ondersteunt een lineaire combi natie van L1-en L2-regularisatie-waarden: dat wil zeggen, als <code>x = L1</code> en en <code>y = L2</code> vervolgens <code>ax + by = c</code> de lineaire lijn van de regularisatie-voor waarden definieert.  
  
    > [!NOTE]
    >  Wilt u meer informatie over L1 en L2-regularisatie? In het volgende artikel wordt uitgelegd hoe L1 en L2 regularisatie verschillend zijn en hoe deze van invloed zijn op model fitting, met code voorbeelden voor logistiek-regressie en Neural-netwerk modellen:  [L1 en L2 regularisatie voor machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Er zijn verschillende lineaire combi Naties van L1-en L2-termen ontwikkeld voor logistieke regressie modellen: bijvoorbeeld [elastische net regularisatie](https://wikipedia.org/wiki/Elastic_net_regularization). U wordt aangeraden om te verwijzen naar deze combi Naties om een lineaire combi natie te definiëren die effectief is in uw model.
      
5.  Geef voor de **geheugen grootte voor l-BFGS** de hoeveelheid geheugen op die moet worden gebruikt voor de optimalisatie van *l-BFGS* .  
  
     L-BFGS staat voor ' Limited Memory Broyden-Fletcher-Goldfarb-Shanno '. Het is een optimalisatie algoritme dat populair is voor de schatting van de para meters. Met deze para meter wordt het aantal vroegere posities en verlopen aangegeven dat moet worden opgeslagen voor de berekening van de volgende stap.  
  
     Deze optimalisatie parameter beperkt de hoeveelheid geheugen die wordt gebruikt voor het berekenen van de volgende stap en richting. Wanneer u minder geheugen opgeeft, is de training sneller, maar minder nauw keurig.  
  
6.  Voor **wille keurig getal zaad** typt u een geheel getal. Het definiëren van een Seed-waarde is belang rijk als u wilt dat de resultaten reproduceerbaar zijn op meerdere uitvoeringen van dezelfde pijp lijn.  
  
  
8. Voeg een gegevensset met een label toe aan de pijp lijn en Train het model:

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
  
## <a name="results"></a>Resultaten

Nadat de training is voltooid:
 
  
+ Als u voor spellingen wilt doen op nieuwe gegevens, gebruikt u het getrainde model en de nieuwe gegevens als invoer voor de module [score model](./score-model.md) . 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
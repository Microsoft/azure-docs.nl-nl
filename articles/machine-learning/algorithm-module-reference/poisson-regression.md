---
title: 'Poisson-regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de Poisson-regressie module in Azure Machine Learning Designer kunt gebruiken om een Poisson-regressie model te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420696"
---
# <a name="poisson-regression"></a>Regressie Poisson

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een Poisson-regressie model in een pijp lijn te maken. Poisson-regressie is bedoeld voor het voors pellen van numerieke waarden, meestal aantal. Daarom moet u deze module gebruiken om het regressie model alleen te maken als de waarden die u wilt voors pellen, voldoen aan de volgende voor waarden:

- De variabele Response heeft een [Poisson-verdeling](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Aantallen kunnen niet negatief zijn. De methode werkt niet goed als u deze wilt gebruiken met negatieve labels.

- Een Poisson-verdeling is een discrete distributie; Daarom is het niet zinvol om deze methode te gebruiken met niet-geheel getal.

> [!TIP]
> Als uw doel geen aantal is, is Poisson-regressie waarschijnlijk geen geschikte methode. Probeer [andere regressie modules in de ontwerp functie](./module-reference.md#machine-learning-algorithms). 

Nadat u de regressie methode hebt ingesteld, moet u het model trainen met een gegevensset met voor beelden van de waarde die u wilt voors pellen. Het getrainde model kan vervolgens worden gebruikt voor het maken van voor spellingen.

## <a name="more-about-poisson-regression"></a>Meer informatie over Poisson-regressie

Poisson-regressie is een speciaal type regressie analyse dat doorgaans wordt gebruikt voor het aantal modellen. Bijvoorbeeld, Poisson-regressie is handig in de volgende scenario's:

- Model leren van het aantal koelingen dat is gekoppeld aan vliegtuig vluchten

- Schatting van het aantal nood aanroepen van de service tijdens een gebeurtenis

- Projecteren van het aantal vragen van klanten na een promotie

- Nood tabellen maken

Omdat de reactie variabele een Poisson-verdeling heeft, maakt het model verschillende hypo thesen over de gegevens en de kans-distributie ervan dan bijvoorbeeld een regressie van Mini maal kwadraten. Daarom moeten Poisson-modellen anders worden geïnterpreteerd dan andere regressie modellen.

## <a name="how-to-configure-poisson-regression"></a>Poisson-regressie configureren

1. Voeg de module **Poisson regressie** toe aan uw pijp lijn in Designer. U kunt deze module vinden onder **machine learning-algoritmen** in de categorie **regressie** .

2. Voeg een gegevensset toe die de opleidings gegevens van het juiste type bevat. 

    We raden u aan [Normal data](normalize-data.md) gebruiken om de invoer gegevensset te normaliseren voordat u deze gebruikt om de regressor hierop te trainen.

3. Geef in het rechterdeel venster van de **Poisson-regressie** -module op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    - **Eén para meter**: als u weet hoe u het model wilt configureren, geeft u een specifieke set waarden als argumenten op.
  
    - **Parameter bereik**: als u niet zeker weet wat de beste para meters zijn, kunt u een para meter opruimen met behulp van de Hyper parameters-module [model](tune-model-hyperparameters.md) . De trainer herhaalt over meerdere waarden die u opgeeft om de optimale configuratie te vinden.
  
4. **Optimalisatie tolerantie**: Typ een waarde die het tolerantie-interval tijdens de optimalisatie definieert. Hoe lager de waarde, hoe langzamer en nauw keuriger de fitting.

5. **L1-regularisatie gewicht** en **L2-regularisatie gewicht**: Typ de waarden die moeten worden gebruikt voor L1 en L2-regularisatie. *Regularisatie* voegt beperkingen toe aan het algoritme met betrekking tot aspecten van het model die onafhankelijk zijn van de trainings gegevens. Regularisatie wordt vaak gebruikt om overmontage te voor komen. 

    - L1 regularisatie is handig als het doel een model moet hebben dat zo sparst mogelijk is.

        L1 regularisatie wordt gedaan door het gewicht van L1 aan de hand van de verlies expressie af te trekken dat de cursist probeert te minimaliseren. De L1-norm is een goede benadering van de N0-norm. Dit is het aantal niet-nul coördinaten.

    - Met L2-regularisatie wordt voor komen dat één coördinaat in de gewichts vector te veel in omvang groeit. L2-regularisatie is handig als het doel een model met kleine totale gewichten moet hebben.

    In deze module kunt u een combi natie van L1 en L2-regularizations Toep assen. Door de combi natie van L1 en L2-regularisatie kunt u een sanctie opleggen voor de grootte van de parameter waarden. De cursist probeert de sanctie te minimaliseren, in een afweging waarbij het verlies wordt geminimaliseerd.

    Voor een goede bespreking van L1 en L2-regularisatie raadpleegt u [L1 en L2-regularisatie voor machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Geheugen grootte voor L-BFGS**: Geef de hoeveelheid geheugen op die moet worden gereserveerd voor model fitting en-optimalisatie.

     L-BFGS is een specifieke methode voor optimalisatie, gebaseerd op het algoritme Broyden – Fletcher – Goldfarb – Shanno (BFGS). De methode maakt gebruik van een beperkte hoeveelheid geheugen (L) om de richting van de volgende stap te berekenen.

     Door deze para meter te wijzigen, kunt u het aantal achterstallige posities en verlopen die voor de berekening van de volgende stap zijn opgeslagen, beïnvloeden.

7. Verbind de trainings-gegevensset en het niet-getrainde model met een van de trainings modules: 

    - Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .

    - Als u de **modus trainer maken** instelt op het **parameter bereik**, gebruikt u de Hyper parameters-module voor het [afstemmen van modellen](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de eerste waarde in de lijst met para meters gebruikt.
    > 
    > - Als u één set parameter waarden doorgeeft aan de Hyper parameters-module voor het [Tune-model](tune-model-hyperparameters.md) , worden de waarden genegeerd en worden de standaard waarden gebruikt voor de cursist als er een reeks instellingen voor elke para meter wordt verwacht.
    > 
    > - Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.

8.  Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u de module training en gaat u naar uitvoer en het tabblad **Logboeken** in het rechter paneel. Klik op het pictogram **gegevensset registreren**.  U kunt het opgeslagen model vinden als een module in de boom structuur van de module. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
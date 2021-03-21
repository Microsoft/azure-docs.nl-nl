---
title: 'Lineaire regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Linear Regression in Azure Machine Learning om een lineair regressie model te maken voor gebruik in een pijp lijn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93323660"
---
# <a name="linear-regression-module"></a>Lineaire regressie module
In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een lineair regressie model te maken voor gebruik in een pijp lijn.  Lineaire regressie pogingen om een lineaire relatie tot stand te brengen tussen een of meer onafhankelijke variabelen en een numeriek resultaat of een afhankelijke variabele. 

Met deze module kunt u een lineaire regressie methode definiëren en vervolgens een model trainen met een gegevensset met een label. Het getrainde model kan vervolgens worden gebruikt voor het maken van voor spellingen.

## <a name="about-linear-regression"></a>Over lineaire regressie

Lineaire regressie is een gemeen schappelijke statistische methode, die in machine learning is aangenomen en uitgebreid met veel nieuwe methoden voor het aanpassen van de lijn en het meten van de fout. Eenvoudig gezegd, regressie verwijst naar de voor spelling van een numeriek doel. Lineaire regressie is nog steeds een goede keuze als u een eenvoudig model voor een eenvoudige, voorspellende taak wilt. Lineaire regressie kan ook worden gebruikt voor zeer dimensionale, sparse gegevens sets die geen complexiteit hebben.

Azure Machine Learning ondersteunt diverse regressie modellen, naast lineaire regressie. De term ' regressie ' kan echter soepel worden geïnterpreteerd en bepaalde typen regressies die worden geboden in andere hulpprogram ma's, worden niet ondersteund.

+ Het klassieke regressie probleem omvat één onafhankelijke variabele en een afhankelijke variabele. Dit heet *eenvoudige regressie*.  Deze module biedt ondersteuning voor eenvoudige regressie.

+ *Meerdere lineaire regressie* omvat twee of meer onafhankelijke variabelen die bijdragen aan één afhankelijke variabele. Problemen waarbij meerdere invoer wordt gebruikt om een enkele numerieke uitkomst te voors pellen, ook wel *multidimensionale lineaire regressie* genoemd.

    Deze problemen kunnen worden opgelost met de module **Linear Regression** , evenals de meeste andere regressie modules.

+ *Regressie met meerdere labels* is de taak voor het voors pellen van meerdere afhankelijke variabelen binnen één model. Zo kan in een logistiek-regressie met meerdere labels een voor beeld worden toegewezen aan meerdere labels. (Dit wijkt af van de taak voor het voors pellen van meerdere niveaus binnen één klassen variabele.)

    Dit type regressie wordt niet ondersteund in Azure Machine Learning. Als u meerdere variabelen wilt voors pellen, maakt u een afzonderlijke cursist voor elke uitvoer die u wilt voors pellen.

Voor jaren statistici zijn steeds meer geavanceerde methoden ontwikkeld voor regressie. Dit geldt ook voor lineaire regressie. Deze module ondersteunt twee methoden om een fout te meten en te voldoen aan de regressie regel: normale, minimale kwadraten methode en daal van kleur overgang.

- **Gradient Daal** is een methode die de fout waarde bij elke stap van het model trainings proces minimaliseert. Er zijn veel variaties in Daal en de optimalisatie voor verschillende leer problemen is uitgebreid bestudeerd. Als u deze optie voor de **oplossings methode** kiest, kunt u een aantal para meters instellen om de stap grootte, het leer tempo, enzovoort te bepalen. Deze optie biedt ook ondersteuning voor het gebruik van een geïntegreerde parameter opruiming.

- **Normale minimale kwadraten** is een van de meest gebruikte technieken in lineaire regressie. Bijvoorbeeld, minste kwadraten is de methode die wordt gebruikt in Analysis ToolPak voor micro soft Excel.

    Normale minimale kwadraten verwijzen naar de functie verlies, waarbij fout wordt berekend als de som van het kwadraat van de afstand van de werkelijke waarde tot de voorspelde regel, en past het model aan door de gekwadrateerde fout te minimaliseren. Bij deze methode wordt uitgegaan van een sterke lineaire relatie tussen de invoer en de afhankelijke variabele.

## <a name="configure-linear-regression"></a>Lineaire regressie configureren

Deze module ondersteunt twee methoden voor het aanpassen van een regressie model met verschillende opties:

+ [Een regressie model passend maken met behulp van normale, minimale kwadraten](#create-a-regression-model-using-ordinary-least-squares)

    Voor kleine gegevens sets kunt u het beste normale minimale kwadraten selecteren. Dit moet vergelijk bare resultaten opleveren voor Excel.
    
+ [Een regressie model maken met online verloop Daal](#create-a-regression-model-using-online-gradient-descent)

    Gradient Daal is een betere verlies functie voor modellen die ingewik kelder zijn of die te weinig trainings gegevens hebben, gezien het aantal variabelen.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Een regressie model maken met behulp van normale, minimale kwadraten

1. Voeg de module **Linear regressie model** toe aan uw pijp lijn in de ontwerp functie.

    U kunt deze module vinden in de categorie **machine learning** . Vouw **Initialiseer model** uit, vouw **regressie** uit en sleep de module **Linear regressie model** naar uw pijp lijn.

2. Selecteer in het deel venster **Eigenschappen** in de vervolg keuzelijst **oplossings methode** de optie **normale kleinste kwadraten**. Met deze optie geeft u de reken methode op die wordt gebruikt om de regressie regel te vinden.

3. In **L2-regularisatie gewicht** typt u de waarde die moet worden gebruikt als gewicht voor de L2-regularisatie. U kunt het beste een andere waarde dan nul gebruiken om te voor komen dat deze wordt overpast.

     Zie dit artikel: [L1 en L2 regularisatie voor machine learning voor](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning) meer informatie over hoe regularisatie van invloed is op model fittings.

4. Selecteer de optie, **Voeg interceptie term** toe als u de term voor het snij punt wilt weer geven.

    Schakel deze optie uit als u de regressie formule niet hoeft te controleren.

5. Voor **wille keurig getal Seed** kunt u optioneel een waarde typen om de wille keurige generator te seeden die door het model wordt gebruikt.

    Het gebruik van een Seed-waarde is handig als u dezelfde resultaten wilt behouden in verschillende uitvoeringen van dezelfde pijp lijn. Anders wordt de standaard instelling gebruikt voor het gebruik van een waarde uit de systeem klok.


7. Voeg de module [Train model](./train-model.md) toe aan uw pijp lijn en Verbind een gegevensset met een label.

8. Verzend de pijp lijn.

### <a name="results-for-ordinary-least-squares-model"></a>Resultaten voor model met de normale kleinste kwadraten

Nadat de training is voltooid:


+ Als u voor spellingen wilt doen, verbindt u het getrainde model met de module [score model](./score-model.md) , samen met een gegevensset met nieuwe waarden. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Een regressie model maken met online verloop Daal

1. Voeg de module **Linear regressie model** toe aan uw pijp lijn in de ontwerp functie.

    U kunt deze module vinden in de categorie **machine learning** . Vouw **initialiseren model** uit, vouw **regressie** uit en sleep de module **Linear regressie model** naar uw pijp lijn

2. Kies in het deel venster **Eigenschappen** in de vervolg keuzelijst **oplossings methode** de optie **online verloop Daal** als de reken methode die wordt gebruikt om de regressie lijn te vinden.

3. Voor de **modus trainer maken** geeft u aan of u het model wilt trainen met een vooraf gedefinieerde set para meters of als u het model wilt optimaliseren met behulp van een parameter sweep.

    + **Eén para meter**: als u weet hoe u het lineaire regressie netwerk wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.
    
    + **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.  

   
4. Voor het **leer tempo** geeft u het eerste leer tempo op voor de stochastische Daal Optimizer van een kleur overgang.

5. Voor het **aantal trainings-epochen** typt u een waarde die aangeeft hoe vaak de algoritme voor beelden moet herhalen. Voor gegevens sets met een klein aantal voor beelden moet dit aantal groot zijn om convergentie te bereiken.

6. **Functies normaliseren**: als u de numerieke gegevens die worden gebruikt voor het trainen van het model al hebt genormaliseerd, kunt u deze optie deselecteren. Standaard normaleert de module alle numerieke invoer naar een bereik tussen 0 en 1.

    > [!NOTE]
    > 
    > Vergeet niet om dezelfde normalisatie methode toe te passen op nieuwe gegevens die worden gebruikt voor een score.

7. In **L2-regularisatie gewicht** typt u de waarde die moet worden gebruikt als gewicht voor de L2-regularisatie. U kunt het beste een andere waarde dan nul gebruiken om te voor komen dat deze wordt overpast.

    Zie dit artikel: [L1 en L2 regularisatie voor machine learning voor](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning) meer informatie over hoe regularisatie van invloed is op model fittings.


9. Selecteer de optie, **Verminder het leer** tempo als u wilt dat het leer tempo afneemt als de voortgang van herhalingen.  

10. Voor **wille keurig getal Seed** kunt u optioneel een waarde typen om de wille keurige generator te seeden die door het model wordt gebruikt. Het gebruik van een Seed-waarde is handig als u dezelfde resultaten wilt behouden in verschillende uitvoeringen van dezelfde pijp lijn.


12. Het model trainen:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.

13. Verzend de pijp lijn.

### <a name="results-for-online-gradient-descent"></a>Resultaten voor online verloop Daal

Nadat de training is voltooid:

+ Als u voor spellingen wilt doen, verbindt u het getrainde model met de module [score model](./score-model.md) , samen met nieuwe invoer gegevens.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
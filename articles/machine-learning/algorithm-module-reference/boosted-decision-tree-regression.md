---
title: 'Regressie van de beslissings structuur verbeterd: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met behulp van de versterkte regressie module voor de beslissings structuur in Azure Machine Learning een ensemble van regressie structuren kunt maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375326"
---
# <a name="boosted-decision-tree-regression-module"></a>Regressie module voor versterkte beslissings structuur

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een ensemble van regressie structuren te maken met behulp van Boosting. *Versterking* houdt in dat elke boom structuur afhankelijk is van eerdere structuren. Het algoritme leert u door het aanpassen van de rest van de bomen die erop werden gevolgd. Zo kunt u in een beslissings structuur een ensemble verhogen om de nauw keurigheid van een klein risico van minder behoefte aan te nemen.  

Deze module is gebaseerd op LightGBM-algoritme.
  
Deze regressie methode is een *gelabelde* leer methode en vereist daarom een gegevensset met de naam. De label kolom moet numerieke waarden bevatten.  

> [!NOTE]
> Gebruik deze module alleen met gegevens sets die numerieke variabelen gebruiken.  

Nadat u het model hebt gedefinieerd, traint u het met het [Train model](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Meer informatie over versterkte regressie structuren  

Versterking is een van de klassieke methoden voor het maken van ensemble-modellen, samen met behulp van de inbagging, wille keurige forests enzovoort.  In Azure Machine Learning gebruiken versterkte beslissings structuren een efficiënte implementatie van het Boost-algoritme voor de bloedkleur overgang. Verbetering van de kleur overgang is een machine learning techniek voor regressie problemen. Hiermee wordt elke regressie structuur op een stapsgewijse manier gebouwd, waarbij een vooraf gedefinieerde verlies functie wordt gebruikt om de fout in elke stap te meten en in de volgende weer te corrigeren. Daarom is het Voorspellings model eigenlijk een ensemble van zwakkere Voorspellings modellen.  
  
Bij regressie problemen bouwt het versterking van een reeks structuren op een stapsgewijse manier en selecteert de optimale boom structuur met een wille keurige differentiatie functie.  
  
Zie de volgende artikelen voor meer informatie:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    In dit Wikipedia-artikel over verloop versterking wordt een achtergrond op gestimuleerde structuren geboden. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Micro soft Research: van RankNet tot LambdaRank naar LambdaMART: een overzicht. Door J.C. Burges.

De methode voor het verhogen van de kleur overgang kan ook worden gebruikt voor classificatie problemen door deze te verminderen tot regressie met een geschikte verlies functie. Zie voor meer informatie over de implementatie van de versterkte bomen voor classificatie taken, [twee klasse boosted beslissings structuur](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Verbetering van de regressie beslissings structuur configureren

1.  Voeg de **Uitgeboostte beslissings structuur** module toe aan uw pijp lijn. U kunt deze module vinden onder **machine learning**, **initialiseren**, onder de categorie **regressie** . 
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op. 
     
    -   **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.    
   
  
3. **Maximum aantal Leaves per boom structuur**: Hiermee geeft u het maximum aantal Terminal knooppunten (bladeren) op dat in een wille keurige structuur kan worden gemaakt.  

    Door deze waarde te verg Roten, kunt u de grootte van de structuur verg Roten, waardoor het risico op overschrijding en een langere tijd beter is.  

4. **Minimum aantal steek proeven per blad knooppunt**: geeft het minimum aantal cases aan dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.

    Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de opleidings gegevens ten minste 5 gevallen bevatten die aan dezelfde voor waarden voldoen.

5. **Leer tempo**: Typ een getal tussen 0 en 1 dat de stap grootte tijdens het leren definieert. Het leer tempo bepaalt hoe snel of traag de kenniser convergeert met de optimale oplossing. Als de grootte van de stap te groot is, kunt u de optimale oplossing overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing.

6. **Aantal geconstrueerde structuren**: hier wordt het totale aantal beslissings structuren aangegeven dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar de trainings tijd wordt verhoogd.

    Als u de waarde instelt op 1, Er wordt echter slechts één structuur geproduceerd (de boom structuur met de initiële set para meters) en er worden geen verdere herhalingen uitgevoerd.

7. **Wille keurig getal Seed**: Typ een optioneel niet-negatief geheel getal dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.

    Standaard is de wille keurige Seed ingesteld op 0, wat betekent dat de aanvankelijke Seed-waarde wordt opgehaald uit de systeem klok.
  

9. Het model trainen:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.
    

10. Verzend de pijp lijn.  
  
## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u het model voor scores wilt gebruiken, verbindt u [Train model](train-model.md) to [score model](./score-model.md)om waarden te voors pellen voor nieuwe invoer voorbeelden.

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van het **getrainde model** en klikt u op pictogram **gegevensset registreren** . De kopie van het getrainde model wordt als een module in de module structuur opgeslagen en wordt niet bijgewerkt op opeenvolgende uitvoeringen van de pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

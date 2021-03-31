---
title: 'Two-Class versterkte beslissings structuur: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Two-Class versterkte beslissings structuur module in de ontwerp functie om een binaire classificatie te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 810878956a89b6e7400be99692cf43925d1cd134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375907"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class versterkte beslissings structuur module

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een machine learning model te maken dat is gebaseerd op het boosted Decision Trees-algoritme. 

Een versterkte beslissings structuur is een ensemble leer methode waarbij de tweede structuur de fouten van de eerste structuur corrigeert, de derde structuur corrigeert voor de fouten van de eerste en tweede bomen, enzovoort. Voor spellingen zijn gebaseerd op de hele ensemble van structuren, samen met de voor spelling.
  
Over het algemeen is het zo dat gestimuleerde beslissings structuren optimaal worden geconfigureerd, waarmee u de beste prestaties kunt verkrijgen voor een groot aantal machine learning taken. Ze zijn echter ook een van de meer geheugenintensieve leerers, en de huidige implementatie houdt alles in het geheugen. Daarom kan een verbeterd beslissings structuur model mogelijk niet de grote gegevens sets verwerken die sommige lineaire leerers kunnen verwerken.

Deze module is gebaseerd op LightGBM-algoritme.

## <a name="how-to-configure"></a>Configureren

Deze module maakt een niet-getraind classificatie model. Omdat classificatie een leer methode met toezicht is, hebt u een *gecodeerde gegevensset* nodig die een kolom Label bevat met een waarde voor alle rijen.

U kunt dit type model trainen met [Train model](././train-model.md). 

1.  Voeg in Azure Machine Learning de **Uitgeboostte beslissings structuur** module toe aan uw pijp lijn.
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.
  
    + **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.
  
    + **Parameter bereik**: als u niet zeker weet wat de beste para meters zijn, kunt u de optimale para meters vinden met behulp van de Hyper parameters-module voor het [afstemmen van modellen](tune-model-hyperparameters.md) . U geeft een aantal waarden op, en de trainer herhaalt meerdere combi Naties van de instellingen om de combi natie van waarden te bepalen die het beste resultaat oplevert.
  
3.  Voor het **maximum aantal Leaves per structuur** geeft u het maximum aantal Terminal knooppunten (bladeren) op dat in een wille keurige structuur kan worden gemaakt.
  
     Door deze waarde te verg Roten, kunt u de grootte van de structuur verg Roten, waardoor het risico op overschrijding en een langere tijd beter is.
  
4.  Geef voor het **minimum aantal steek proeven per blad knooppunt** het aantal cases op dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.  
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.
  
5.  Voor het **leer tempo** typt u een getal tussen 0 en 1 dat de stap grootte definieert tijdens het leren.  
  
     Het leer tempo bepaalt hoe snel of traag de kenniser convergeert met de optimale oplossing. Als de grootte van de stap te groot is, is het mogelijk dat u de optimale oplossing kunt overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing.
  
6.  Geef voor **aantal geconstrueerde structuren** het totale aantal beslissings structuren op dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar wordt de trainings tijd verhoogd.
  
     Als u de waarde instelt op 1, wordt er slechts één boom structuur geproduceerd (de boom structuur met de initiële set para meters). er worden geen verdere herhalingen uitgevoerd.
  
7.  Voor **wille keurig getal zaad** typt u optioneel een niet-negatief geheel getal dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.  
  
     De wille keurige Seed wordt standaard ingesteld op 0, wat betekent dat de aanvankelijke Seed-waarde wordt opgehaald uit de systeem klok.  Opeenvolgende uitvoeringen met een wille keurige Seed kan verschillende resultaten hebben.
  

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
   
## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van de module **Train model** . Selecteer het pictogram **gegevensset registreren** om het model als een herbruikbare module op te slaan.

+ Als u het model voor scores wilt gebruiken, voegt u de module **score model** toe aan een pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
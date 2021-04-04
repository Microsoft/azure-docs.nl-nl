---
title: 'Two-Class decision-forest: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Two-Class decision-forest in Azure Machine Learning om een machine learning model te maken op basis van het algoritme voor de beslissings bossen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: c4c303a7640454ba0cb6622b21fd161354266068
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375921"
---
# <a name="two-class-decision-forest-module"></a>Forest-module Two-Class beslissing

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een machine learning model te maken op basis van het algoritme voor de beslissings bossen.  

Beslissings bossen zijn snelle, onder Super visie gemodelleerde ensemble-modellen. Deze module is een goede keuze als u een doel wilt voors pellen met Maxi maal twee resultaten. 

## <a name="understanding-decision-forests"></a>Informatie over beslissings bossen

Deze beslissings forest-algoritme is een ensemble-leer methode die is bedoeld voor classificatie taken. Ensemble-methoden zijn gebaseerd op het algemene principe dat in plaats van op één model moet worden gebruikgemaakt, u krijgt betere resultaten en een meer gegeneraliseerde model door meerdere gerelateerde modellen te maken en deze op een of andere manier te combi neren. In het algemeen bieden ensemble-modellen betere dekking en nauw keurigheid dan enkelvoudige beslissings structuren. 

Er zijn veel manieren om afzonderlijke modellen te maken en deze te combi neren in een ensemble. Deze specifieke implementatie van een besluit forest werkt door meerdere beslissings structuren te bouwen en vervolgens te **stemmen** op de meest populaire uitvoer klasse. Stemmen is een van de betere bekende methoden voor het genereren van resultaten in een ensembles model. 

+ Er worden veel afzonderlijke classificatie structuren gemaakt met behulp van de volledige gegevensset, maar verschillende (meestal wille keurig) begin punten. Dit wijkt af van de benadering van een wille keurige forest, waarbij de afzonderlijke beslissings structuren mogelijk alleen een wille keurig deel van de gegevens of functies gebruiken.
+ Elke structuur in de structuur van het beslissings forest voert een niet-genormaliseerd frequentie histogram van labels uit. 
+ Het aggregatie proces berekent deze histogrammen en normaliseert het resultaat om de ' kansen ' voor elk label op te halen. 
+ De structuren die een hoog Voorspellings betrouwbaarheids niveau hebben, hebben een groter gewicht in de definitieve beslissing van de ensemble.

Beslissings structuren in het algemeen hebben veel voor delen voor classificatie taken:
  
- Ze kunnen niet-lineaire beslissings grenzen vastleggen.
- U kunt veel gegevens trainen en voors pellen, omdat ze efficiënt zijn in berekeningen en geheugen gebruik.
- De functie selectie is geïntegreerd in de trainings-en classificatie processen.  
- Structuren kunnen ruis gegevens en veel functies bevatten.  
- Dit zijn niet-parametrische modellen, wat betekent dat ze gegevens kunnen verwerken met verschillende distributies. 

Eenvoudige beslissings structuren kunnen echter overfit aan gegevens en zijn minder generaliseer dan een boom ensemble.

Zie [Decision bossen](https://go.microsoft.com/fwlink/?LinkId=403677)voor meer informatie.  

## <a name="how-to-configure"></a>Configureren
  
1.  Voeg de module **beslissings forest met twee klassen** toe aan uw pijp lijn in azure machine learning en open het deel venster **Eigenschappen** van de module. 

    U kunt de module vinden onder **machine learning**. Vouw **initialiseren** en vervolgens **classificatie** uit.  
  
2.  Kies voor **resampling methode** de methode die is gebruikt voor het maken van de afzonderlijke structuren.  U kunt **kiezen uit een** of meer van de **replicatie**.  
  
    -   **Overbaggen**: de functie voor het *verzamelen van de Boots trap* In deze methode wordt elke structuur op een nieuw voor beeld gekweekt, gemaakt door de oorspronkelijke gegevensset wille keurig te bemonsteren, totdat u een gegevensset hebt die de oorspronkelijke grootte heeft.  
  
         De uitvoer van de modellen worden gecombineerd door middel van *stemmen*. Dit is een vorm van aggregatie. Elke structuur in een forest met classificatie beslissingen voert een niet-genormaliseerd frequentie histogram van labels uit. De aggregatie is het opsommen van deze histogrammen en normaliseren om de ' kansen ' voor elk label op te halen. Op deze manier hebben de structuren met een hoog Voorspellings betrouwbaarheids niveau een groter gewicht in de definitieve beslissing van de ensemble.  
  
         Zie de Wikipedia-vermelding voor Boots trap aggregatie voor meer informatie.  
  
    -   **Repliceren**: in replicatie wordt elke structuur getraind op exact dezelfde invoer gegevens. De bepaling waarvan het gesplitste predicaat voor elk structuur knooppunt wordt gebruikt, blijft wille keurig en de structuur is divers.   
  
3.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.

    -   **Parameter bereik**: als u niet zeker weet wat de beste para meters zijn, kunt u de optimale para meters vinden met behulp van de Hyper parameters-module voor het [afstemmen van modellen](tune-model-hyperparameters.md) . U geeft een aantal waarden op, en de trainer herhaalt meerdere combi Naties van de instellingen om de combi natie van waarden te bepalen die het beste resultaat oplevert.
  
4.  Voor het **aantal beslissings structuren** typt u het maximum aantal beslissings structuren dat kan worden gemaakt in de ensemble. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar de trainings tijd wordt verhoogd.  
  
    > [!NOTE]
    >  Als u de waarde instelt op 1. Er kan echter maar één structuur worden geproduceerd (de boom structuur met de initiële set para meters) en er worden geen verdere herhalingen uitgevoerd.
  
5.  Voor **de maximale diepte van de beslissings structuren** typt u een getal om de maximale diepte van een beslissings structuur te beperken. Het verg Roten van de diepte van de structuur kan nauw keuriger zijn, met het risico van een aantal verdeelde en verhoogde opleidings tijd.
  
  
7.  Geef voor het **minimum aantal steek proeven per blad knooppunt** het minimale aantal cases op dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.  
  
8.  Selecteer de optie **onbekende waarden toestaan voor categorische functies** om een groep te maken voor onbekende waarden in de trainings-of validatie sets. Het model is mogelijk minder nauw keurig voor bekende waarden, maar het kan betere voor spellingen bieden voor nieuwe (onbekende) waarden. 

     Als u deze optie uitschakelt, kan het model alleen de waarden accepteren die deel uitmaken van de trainings gegevens.
  
9. Voeg een gegevensset met een label toe en Train het model:

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
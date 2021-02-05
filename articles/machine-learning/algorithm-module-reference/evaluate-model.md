---
title: 'Model evalueren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module evaluate model in Azure Machine Learning om de nauw keurigheid van een getraind model te meten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 39bdf9cb0c97e19a67b23046c6f06b60daa30147
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584937"
---
# <a name="evaluate-model-module"></a>Model module evalueren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om de nauw keurigheid van een getraind model te meten. U geeft een gegevensset op die uit een model gegenereerde scores bevat en de module **Evaluate model** berekent een aantal metrische gegevens over de evaluatie van de industrie standaard.
  
 De metrische gegevens die door het **Evalueer model** worden geretourneerd, zijn afhankelijk van het type model dat u evalueert:  
  
-   **Classificatie modellen**    
-   **Regressie modellen**  
-   **Cluster modellen**  


> [!TIP]
> Als u geen ervaring hebt met het model, kunt u het beste de video serie door Dr. Stephen Elston als onderdeel van de [machine learning cursus](/archive/blogs/machinelearning/new-edx-course-data-science-machine-learning-essentials) van EdX. 


## <a name="how-to-use-evaluate-model"></a>Het gebruik van een evalueren model
1. Verbind de Score van de **gescoorde gegevensset** van het [score model](./score-model.md) of de uitvoer van de gegevensset van het resultaat van de gegevens van het type [toewijzen aan clusters](./assign-data-to-clusters.md) aan de linkerkant invoer poort van het **Evalueer model**. 
    > [!NOTE] 
    > Als u modules als ' select columns in Dataset ' wilt gebruiken om een deel van een invoer gegevensset te selecteren, moet u ervoor zorgen dat de werkelijke Label kolom (gebruikt in de training), de kolom ' gescoorde kansen ' en de kolom ' gescoorde labels ' bestaan voor het berekenen van metrische gegevens zoals AUC, nauw keurigheid voor binaire classificatie/anomalie detectie.
    > Kolom werkelijk label, de kolom scoored labels bestaat voor het berekenen van metrische gegevens voor classificatie en regressie met meerdere klassen.
    > De kolom Assignments, columns DistancesToClusterCenter. X ' (X is massa middelpunt index, variërend van 0,..., aantal centroids-1) bevindt zich voor het berekenen van metrische gegevens voor clustering.

    > [!IMPORTANT]
    > + Als u de resultaten wilt evalueren, moet de uitvoer gegevensset specifieke namen van de Score kolom bevatten die voldoen aan de vereisten voor de module voor het evalueren van modellen.
    > + De `Labels` kolom wordt als werkelijke labels beschouwd.
    > + Voor de regressie taak moet de te evalueren gegevensset één kolom hebben met de naam `Regression Scored Labels` , die de gescoorde labels vertegenwoordigt.
    > + Voor de binaire classificatie taak moet de te evalueren gegevensset twee kolommen hebben, met de naam `Binary Class Scored Labels` , `Binary Class Scored Probabilities` die de gescoorde labels en de waarschijnlijkheid vertegenwoordigen.
    > + Voor een taak met meerdere classificaties moet de te evalueren gegevensset één kolom hebben met de naam `Multi Class Scored Labels` , die de gescoorde labels vertegenwoordigt.
    > Als de uitvoer van de upstream-module deze kolommen niet bevat, moet u wijzigingen aanbrengen in overeenstemming met de bovenstaande vereisten.

2. Beschrijving Verbind de Score van de **gescoorde gegevensset** van het [score model](./score-model.md) of het resultaat van de gegevensset-uitvoer van de gegevens voor het tweede model aan de **juiste** invoer poort van het **Evalueer model**. U kunt de resultaten van twee verschillende modellen op dezelfde gegevens eenvoudig vergelijken. De twee invoer algoritmen moeten hetzelfde algoritme type zijn. U kunt ook scores van twee verschillende uitvoeringen vergelijken met dezelfde gegevens met verschillende para meters.

    > [!NOTE]
    > Algoritme type verwijst naar ' Two-class classificatie ', ' multi-class classificatie ', ' regressie ', ' clustering ' onder ' Machine Learning-algoritmen '. 

3. Verzend de pijp lijn om de evaluatie scores te genereren.

## <a name="results"></a>Resultaten

Nadat u het **model evalueren** hebt uitgevoerd, selecteert u de module om het navigatie paneel voor het **model evalueren** te openen aan de rechter kant.  Kies vervolgens het tabblad **uitvoer en logboeken** en op dat tabblad bevat de sectie **gegevens uitvoer** meerdere pictogrammen. Het pictogram **visualiseren** bevat een pictogram van een staaf diagram en is een eerste manier om de resultaten weer te geven.

Voor binaire classificatie kunt u, nadat u op het pictogram **visualiseren** hebt geklikt, de binaire Verwar ring visualiseren.
Voor multi-classificatie kunt u het teken bestand met de Verwar ring matrix vinden op het tabblad **uitvoer en logboeken** , zoals hieronder:
> [!div class="mx-imgBorder"]
> ![Voor beeld van geüploade afbeelding](media/module/multi-class-confusion-matrix.png)

Als u gegevens sets verbindt met zowel de invoer van een **Evalueer model**, bevatten de resultaten metrische gegevens voor beide sets of beide modellen.
Het model of de gegevens die aan de linker poort zijn gekoppeld, worden eerst in het rapport weer gegeven, gevolgd door de metrieken voor de gegevensset of het model dat is gekoppeld aan de juiste poort.  

De volgende afbeelding vertegenwoordigt bijvoorbeeld een vergelijking van de resultaten van twee cluster modellen die zijn gebouwd op dezelfde gegevens, maar met verschillende para meters.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Omdat dit een cluster model is, zijn de evaluatie resultaten anders dan wanneer u scores van twee regressie modellen of twee classificatie modellen hebt vergeleken. De gehele presentatie is echter hetzelfde. 

## <a name="metrics"></a>Metrische gegevens

In deze sectie worden de metrische gegevens die worden geretourneerd voor de specifieke typen modellen beschreven die worden ondersteund voor gebruik met het **Evaluate-model**:

+ [classificatie modellen](#metrics-for-classification-models)
+ [regressie modellen](#metrics-for-regression-models)
+ [cluster modellen](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metrische gegevens voor classificatie modellen


De volgende metrische gegevens worden gerapporteerd bij het evalueren van binaire classificatie modellen.
  
-   **Nauw keurigheid** meet de goed keuring van een classificatie model als het aandeel van de werkelijke resultaten tot het totale aantal cases.  
  
-   **Nauw keurigheid** is het aandeel van de werkelijke resultaten ten opzichte van alle positieve resultaten. Precisie = TP/(TP + FP)  
  
-   **Intrekken** is de Fractie van het totale aantal relevante instanties dat daad werkelijk is opgehaald. Intrekken = TP/(TP + FN)  
  
-   De **F1-Score** wordt berekend als het gewogen gemiddelde van de precisie en intrekken tussen 0 en 1, waarbij de ideale waarde voor de F1-score 1 is.  
  
-   **AUC** meet het gebied onder de curve die is getekend met echte positieven op de y-as en fout-positieven op de x-as. Deze meet waarde is nuttig omdat deze een enkel getal bevat waarmee u modellen van verschillende typen kunt vergelijken. AUC is classificatie-Threshold-invariant. De oplossing meet de kwaliteit van de voor spellingen van het model, ongeacht welke classificatie drempel is gekozen.


### <a name="metrics-for-regression-models"></a>Metrische gegevens voor regressie modellen
 
De metrische gegevens die voor regressie modellen zijn geretourneerd, zijn ontworpen om de hoeveelheid fout te schatten.  Een model wordt gezien als een goede grootte van de gegevens als het verschil tussen de waargenomen en voorspelde waarden klein is. Gezien het patroon van de verschillen (het verschil tussen een voorspeld punt en de bijbehorende werkelijke waarde), kan u echter een heleboel informatie over mogelijke afwijking in het model vertellen.  
  
 De volgende metrische gegevens worden gerapporteerd voor het evalueren van regressie modellen.
  
- **Gemiddelde absolute fout (Mae)** meet hoe dicht de voor spellingen zijn voor de werkelijke resultaten. een lagere score is dus beter.  
  
- **Root mean error (RMSE)** maakt een enkele waarde die de fout in het model samenvat. Door het verschil te squaringen, wordt het verschil tussen de voor spelling en de onderdictie van de metriek genegeerd.  
  
- **Relatieve absolute fout (Rae)** is het relatieve absolute verschil tussen de verwachte en werkelijke waarden; relatief omdat het gemiddelde verschil wordt gedeeld door het reken kundige gemiddelde.  
  
- Met **relatieve kwadratische fout (RSE)** wordt het totale kwadraat van de voorspelde waarden op dezelfde manier genormaliseerd door te delen door de totale kwadraat fout van de werkelijke waarden.  
  

  
- De **determinatie coëfficiënt**, vaak R <sup>2</sup>genoemd, vertegenwoordigt de voorspellende kracht van het model als een waarde tussen 0 en 1. Nul betekent dat het model wille keurig is (er wordt niets uitgelegd). 1 betekent dat er een perfecte aanpassing is. Wees echter voorzichtig bij het interpreteren van R<sup>2</sup> -waarden, omdat lage waarden volledig normaal kunnen zijn en hoge waarden kunnen worden verdacht.

###  <a name="metrics-for-clustering-models"></a>Metrische gegevens voor cluster modellen

Omdat de cluster modellen in veel opzichten aanzienlijk verschillen van classificatie-en regressie modellen, wordt met het evalueren van het [model](evaluate-model.md) ook een andere set statistieken voor cluster modellen geretourneerd.  
  
 De statistieken die worden geretourneerd voor een cluster model beschrijven het aantal gegevens punten dat aan elk cluster is toegewezen, de hoeveelheid schei ding tussen clusters en de nauw keurigheid van de gegevens punten binnen elk cluster.  
  
 De statistieken voor het cluster model worden gemiddeld over de hele gegevensset, met extra rijen met de statistieken per cluster.  
  
De volgende metrische gegevens worden gerapporteerd voor het evalueren van cluster modellen.
    
-   De scores in de kolom, **gemiddelde afstand tot ander midden**, geven aan hoe dicht, op gemiddeld elk punt in het cluster naar de centroids van alle andere clusters gaat.   

-   De scores in de kolom, **gemiddelde afstand tot cluster centrum**, vertegenwoordigen de dichtheid van alle punten in een cluster naar de massa middelpunt van dat cluster.  
  
-   In de kolom **aantal punten** ziet u hoeveel gegevens punten zijn toegewezen aan elk cluster, samen met het totale aantal gegevens punten in een cluster.  
  
     Als het aantal gegevens punten dat aan clusters is toegewezen kleiner is dan het totale aantal beschik bare gegevens punten, betekent dit dat de gegevens punten niet aan een cluster kunnen worden toegewezen.  
  
-   De scores in de kolom, de **maximale afstand tot het cluster centrum** vertegenwoordigen het maximum van de afstand tussen de afzonderlijke punten en de massa middelpunt van het cluster van dat punt.  
  
     Als dit aantal hoog is, kan dit betekenen dat het cluster veel wordt verspreid. U moet deze statistiek samen met de **gemiddelde afstand tot cluster centrum** door nemen om de verspreiding van het cluster te bepalen.   

-   De **gecombineerde evaluatie** score onder aan elk gedeelte van de resultaten bevat een lijst met de gemiddelde scores voor de clusters die in dat specifieke model zijn gemaakt.  
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
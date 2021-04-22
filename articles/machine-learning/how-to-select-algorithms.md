---
title: Een algoritme voor machine learning selecteren
titleSuffix: Azure Machine Learning
description: Het selecteren Azure Machine Learning algoritmen voor leren onder supervisie en zonder supervisie in clustering-, classificatie- of regressieexperimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: a4a8ea704ab6e304f1d4465befed96e40ea55987
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884674"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Algoritmen selecteren voor Azure Machine Learning

Een veelvoorkomende vraag is: 'Welk machine learning algoritme moet ik gebruiken?' Het algoritme dat u selecteert, is voornamelijk afhankelijk van twee verschillende aspecten van uw data science-scenario:

 - **Wat wilt u doen met uw gegevens?** Met name wat is de zakelijke vraag die u wilt beantwoorden door te leren van uw eerdere gegevens?

 - **Wat zijn de vereisten van uw data science-scenario?** Wat is met name de nauwkeurigheid, trainingstijd, lineariteit, het aantal parameters en het aantal functies dat door uw oplossing wordt ondersteund?

 ![Overwegingen voor het kiezen van algoritmen: Wat wilt u weten? Wat zijn de scenariovereisten?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Bedrijfsscenario's en Machine Learning Algorithm Cheat Sheet

Het [Azure Machine Learning Algorithm Cheat Sheet](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri) helpt u bij de eerste overweging: Wat wilt u doen met uw **gegevens?** Zoek op Machine Learning Algorithm Cheat Sheet naar de taak die u wilt uitvoeren en zoek vervolgens een [Azure Machine Learning](./concept-designer.md?WT.mc_id=docs-article-lazzeri) designer-algoritme voor de predictive analytics oplossing. 

Machine Learning designer biedt een uitgebreide portfolio met algoritmen, zoals beslissings-forest met meerdere klasses, aanbevelingssystemen, [neurale netwerkregressie,](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) [multiclass neurale](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri)netwerken en [K-Means-clustering.](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) [](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) [](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri) Elk algoritme is ontworpen om een ander type probleem machine learning aanpakken. Zie het [Machine Learning designer-algoritme](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) en moduleverwijzing voor een volledige lijst, samen met documentatie over hoe elk algoritme werkt en hoe u parameters kunt afstemmen om het algoritme te optimaliseren.

> [!NOTE]
> Als u het cheatsheet voor machine learning-algoritme wilt downloaden, gaat u naar Cheatsheet voor [Azure Machine Learning-algoritme.](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)
> 
> 

Naast de richtlijnen in het Azure Machine Learning Algorithm Cheat Sheet, moet u rekening houden met andere vereisten bij het kiezen van een machine learning algoritme voor uw oplossing. Hieronder vindt u aanvullende factoren om rekening mee te houden, zoals de nauwkeurigheid, trainingtijd, lineariteit, het aantal parameters en het aantal functies.

## <a name="comparison-of-machine-learning-algorithms"></a>Vergelijking van machine learning algoritmen

Sommige leeralgoritmen doen bepaalde veronderstellingen over de structuur van de gegevens of de gewenste resultaten. Als u er een kunt vinden die aan uw behoeften voldoet, kunt u er nuttigere resultaten, nauwkeurigere voorspellingen of snellere trainingstijden mee krijgen.

De volgende tabel bevat een overzicht van enkele van de belangrijkste kenmerken van algoritmen uit de classificatie-, regressie- en clusteringfamilies:

| **Algoritme** | **Nauwkeurigheid** | **Trainingstijd** | **Lineariteit** | **Parameters** | **Opmerkingen** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificatiefamilie** | | | | | |
| [Logistieke regressie met twee klassen](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Goed  |Snel |Yes |4 | |
| [Beslissings forest met twee klassen](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |No |5 |Geeft tragere scoretijden weer. Stel voor om niet te werken One-vs-All Multiclass, vanwege tragere scoretijden die worden veroorzaakt door een vergrendeling van de vergrendeling van de structuur bij het verzamelen van structuurvoorspellingen |
| [Beslissingsstructuur met twee klassen boosted](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |No |6 |Grote geheugen-footprint |
| [Neuraal netwerk met twee klassen](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |Goed |Matig |No |8 | |
| [Gemiddelde perceptron met twee klassen](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |Goed |Matig |Yes |4 | |
| [Ondersteuningsvectormachine met twee klassen](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |Goed |Snel |Yes |5 |Goed voor grote functiesets |
| [Logistieke regressie met meerdere klasses](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Goed |Snel |Yes |4 | |
| [Beslissings-forest met meerdere klasse](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |No |5 |Geeft tragere scoretijden weer |
| [Beslissingsstructuur met meerdere klasse-boosts](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |No |6 | Verbetert de nauwkeurigheid met een klein risico op minder dekking |
| [Neuraal netwerk met meerdere klasses](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |Goed |Matig |No |8 | |
| [Een-versus-alle multiklasse](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Eigenschappen bekijken van de geselecteerde methode met twee klassen |
| **Regressiefamilie** | | | | | |
| [Lineaire regressie](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |Goed |Snel |Yes |4 | |
| [Regressie van beslissings forest](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|Uitstekend |Matig |No |5 | |
| [Boosted Decision Tree Regression](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |No |6 |Grote geheugenvoetafdruk |
| [Regressie van neurale netwerken](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |Goed |Matig |No |8 | |
| **Clustering-familie** | | | | | |
| [K-means-clustering](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |Uitstekend |Matig |Yes |8 |Een clusteralgoritme |

## <a name="requirements-for-a-data-science-scenario"></a>Vereisten voor een data science-scenario

Zodra u weet wat u met uw gegevens wilt doen, moet u aanvullende vereisten voor uw oplossing bepalen. 

Maak keuzes en mogelijke afwegingen voor de volgende vereisten:

- Nauwkeurigheid
- Trainingstijd
- Lineariteit
- Aantal parameters
- Aantal functies

## <a name="accuracy"></a>Nauwkeurigheid

Nauwkeurigheid in machine learning meet de effectiviteit van een model als het aandeel van werkelijke resultaten in het totale aantal gevallen. In Machine Learning ontwerpfunctie berekent [de module Evaluate Model](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) een set metrische evaluatiegegevens die voldoen aan de industriestandaard. U kunt deze module gebruiken om de nauwkeurigheid van een getraind model te meten.

Het is niet altijd nodig om het meest nauwkeurige antwoord te krijgen. Soms is een benadering voldoende, afhankelijk van waarvoor u deze wilt gebruiken. Als dat het geval is, kunt u de verwerkingstijd mogelijk aanzienlijk bekorten door bij benadering methoden te gebruiken. Bij benaderingsmethoden wordt overfitting ook vaak voorkomen.

Er zijn drie manieren om de module Evaluate Model te gebruiken:

- Scores genereren voor uw trainingsgegevens om het model te evalueren
- Scores genereren voor het model, maar deze scores vergelijken met scores voor een gereserveerde testset
- Scores vergelijken voor twee verschillende, maar gerelateerde modellen, met behulp van dezelfde set gegevens

Zie evaluate Model module (Model evalueren) voor een volledige lijst met metrische gegevens en benaderingen die u kunt gebruiken om de nauwkeurigheid van machine learning modellen [te evalueren.](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri)

## <a name="training-time"></a>Trainingstijd

Bij leren onder supervisie betekent training het gebruik van historische gegevens om een machine learning model te bouwen dat fouten minimaliseert. Het aantal minuten of uren dat nodig is om een model te trainen, varieert aanzienlijk tussen algoritmen. De training tijd is vaak nauw gekoppeld aan nauwkeurigheid; De ene gaat meestal samen met de andere. 

Bovendien zijn sommige algoritmen gevoeliger voor het aantal gegevenspunten dan andere. U kunt een specifiek algoritme kiezen omdat u een tijdslimiet hebt, met name wanneer de gegevensset groot is.

In Machine Learning designer is het maken en gebruiken van een machine learning-model doorgaans een proces met drie stappen:

1.  Configureer een model door een bepaald type algoritme te kiezen en vervolgens de parameters of hyperparameters te definiëren. 

2.  Geef een gegevensset op die is gelabeld en gegevens bevat die compatibel zijn met het algoritme. Verbind zowel de gegevens als het model met [de module Train Model](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri).

3.  Nadat de training is voltooid, gebruikt u het getrainde model met een van de [scoremodules om](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) voorspellingen te doen over nieuwe gegevens.

## <a name="linearity"></a>Lineariteit

Lineariteit in statistieken en machine learning betekent dat er een lineaire relatie is tussen een variabele en een constante in uw gegevensset. Bij lineaire classificatiealgoritmen wordt er bijvoorbeeld van uit gegaan dat klassen kunnen worden gescheiden door een rechte lijn (of de hogere dimensionale analog).

Veel van machine learning algoritmen maken gebruik van lineariteit. Deze Azure Machine Learning in de ontwerpfunctie: 

- [Logistieke regressie met meerdere klasses](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Logistieke regressie met twee klassen](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Support Vector Machines](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

Bij algoritmen voor lineaire regressie wordt ervan uitgegaan dat gegevenstrends een rechte lijn volgen. Deze veronderstelling is niet slecht voor sommige problemen, maar voor andere vermindert dit de nauwkeurigheid. Ondanks hun nadelen zijn lineaire algoritmen populair als een eerste strategie. Ze zijn vaak algoritmisch eenvoudig en snel om te trainen.

![Niet-lijnvormige klassegrens](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Niet-lineaire klassegrens** _: _Relying een lineair classificatiealgoritme zou resulteren in lage nauwkeurigheid.*

![Gegevens met een niet-lijnvormige trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Gegevens met een niet-lineaire trend** _: _Using lineaire regressiemethode zou veel grotere fouten genereren dan nodig is.*

## <a name="number-of-parameters"></a>Aantal parameters

Parameters zijn de knoppen die een data scientist moet gebruiken bij het instellen van een algoritme. Dit zijn getallen die van invloed zijn op het gedrag van het algoritme, zoals fouttolerantie of het aantal iteraties, of opties tussen varianten van het gedrag van het algoritme. De trainingtijd en nauwkeurigheid van het algoritme kunnen soms gevoelig zijn voor het verkrijgen van de juiste instellingen. Algoritmen met een groot aantal parameters vereisen doorgaans de meeste trial-and-error om een goede combinatie te vinden.

Er is ook de [module Tune Model Hyperparameters](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) in Machine Learning Designer: Het doel van deze module is het bepalen van de optimale hyperparameters voor een machine learning-model. De module bouwt en test meerdere modellen met behulp van verschillende combinaties van instellingen. Het vergelijkt metrische gegevens over alle modellen om de combinaties van instellingen op te halen. 

Hoewel dit een uitstekende manier is om ervoor te zorgen dat u de parameterruimte hebt overspant, neemt de tijd die nodig is om een model te trainen exponentieel toe met het aantal parameters. Het omgekeerde is dat het hebben van veel parameters meestal aangeeft dat een algoritme meer flexibiliteit heeft. Het kan vaak een zeer goede nauwkeurigheid bereiken, mits u de juiste combinatie van parameterinstellingen kunt vinden.

## <a name="number-of-features"></a>Aantal functies

In machine learning is een functie een meetbare variabele van het verschijnsel dat u probeert te analyseren. Voor bepaalde typen gegevens kan het aantal functies erg groot zijn vergeleken met het aantal gegevenspunten. Dit is vaak het geval bij genetische gegevens of tekstgegevens. 

Een groot aantal functies kan een aantal leeralgoritmen in de weg zitten, waardoor de training niet haalbaar lang wordt. [Support Vector Machines](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) zijn met name geschikt voor scenario's met een groot aantal functies. Daarom zijn ze in veel toepassingen gebruikt, van het ophalen van gegevens tot tekst- en afbeeldingsclassificatie. Support Vector Machines kunnen worden gebruikt voor zowel classificatie- als regressietaken.

Functieselectie verwijst naar het proces van het toepassen van statistische tests op invoer, op basis van een opgegeven uitvoer. Het doel is om te bepalen welke kolommen beter voorspellend zijn voor de uitvoer. De [module Filter Based Feature Selection](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) in Machine Learning designer biedt meerdere functieselectiealgoritmen waar u uit kunt kiezen. De module bevat correlatiemethoden zoals Pearson-correlatie en chi-kwadraatwaarden.

U kunt ook de module [Belang van permutatiefunctie](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) gebruiken om een set belangrijke scores voor functies voor uw gegevensset te berekenen. U kunt deze scores vervolgens gebruiken om u te helpen bepalen welke functies het beste in een model moeten worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

 - [Meer informatie over Azure Machine Learning designer](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Voor beschrijvingen van alle machine learning algoritmen die beschikbaar zijn in Azure Machine Learning Designer, Machine Learning [designer-algoritme](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) en moduleverwijzing
 - Als u de relatie tussen deep learning, machine learning en AI wilt verkennen, gaat u naar [Deep Learning versus Machine Learning](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)
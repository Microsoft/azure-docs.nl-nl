---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de One-vs-All Multiclass-module in Azure Machine Learning Designer voor het maken van een ensemble van binaire classificatie modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 4dfe284a00052cbd1915d62355e1d7772f3712ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94591866"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

In dit artikel wordt beschreven hoe u de module One-vs-All Multiclass gebruikt in Azure Machine Learning Designer. Het doel is om een classificatie model te maken dat meerdere klassen kan voors pellen met behulp van de *eenrichtings* benadering.

Deze module is handig voor het maken van modellen die drie of meer mogelijke resultaten voors pellen, wanneer het resultaat afhankelijk is van continue of categorische prediction-variabelen. Met deze methode kunt u ook binaire classificatie methoden gebruiken voor problemen waarvoor meerdere uitvoer klassen zijn vereist.

### <a name="more-about-one-versus-all-models"></a>Meer informatie over één versus alle modellen

Sommige classificatie algoritmen maken het gebruik van meer dan twee klassen mogelijk per ontwerp. Andere beperken de mogelijke resultaten voor een van twee waarden (een binair of een model met twee klassen). Maar zelfs binaire classificatie algoritmen kunnen worden aangepast voor classificatie taken met meerdere klassen via verschillende strategieën. 

Met deze module wordt de methode één-op-alle toegepast, waarbij een binair model voor elk van de meerdere uitvoer klassen wordt gemaakt. In de module wordt elk van deze binaire modellen voor de afzonderlijke klassen geëvalueerd op basis van de aanvulling (alle andere klassen in het model) alsof het een probleem is met de binaire classificatie. Naast de verwerkings efficiëntie (alleen `n_classes` classificaties zijn nodig), is een voor deel van deze benadering de interpretiteit. Omdat elke klasse wordt vertegenwoordigd door één en één classificatie, is het mogelijk om kennis te krijgen over de-klasse door de bijbehorende classificatie te controleren. Dit is de meest gebruikte strategie voor de classificatie van verschillende klassen en is een billijke standaard optie. De module voert vervolgens voor spellingen uit door deze binaire classificaties uit te voeren en de voor spelling te kiezen met de hoogste betrouwbaarheids Score. 

In essentie maakt de module een ensemble van afzonderlijke modellen en voegt de resultaten vervolgens samen om één model te maken waarmee alle klassen worden voor speld. Een binaire classificatie kan worden gebruikt als basis voor een model dat overal en op alle is gebaseerd.  

Stel bijvoorbeeld dat u een [twee klasse ondersteunings vector computer](two-class-support-vector-machine.md) model configureert en als invoer voor de module One-vs-all Multiclass levert. De module maakt met twee klassen ondersteuning vector machine modellen voor alle leden van de klasse output. Vervolgens wordt de methode één-op-alle toegepast om de resultaten voor alle klassen te combi neren.  

De module maakt gebruik van OneVsRestClassifier van sklearn en u vindt [hier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html)meer informatie.

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>De classificatie van de One-vs-All Multiclass configureren  

Deze module maakt een ensemble van binaire classificatie modellen om meerdere klassen te analyseren. Als u deze module wilt gebruiken, moet u eerst een *binair classificatie* model configureren en trainen. 

U verbindt het binaire model met de module One-vs-All Multiclass. Vervolgens traint u de ensemble van modellen met behulp van [Train model](train-model.md) met een gelabelde trainings gegevensset.

Wanneer u de modellen combineert, maakt One-vs-All Multiclass meerdere binaire classificatie modellen, optimaliseert de algoritme voor elke klasse en voegt de modellen vervolgens samen. De module voert deze taken uit, ook al heeft de gegevensset van de training mogelijk meerdere klassen waarden.

1. Voeg de module One-vs-All Multiclass toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning-Initialize**, in de categorie **classificatie** .

   De classificatie van de One-vs-All Multiclass heeft geen Configureer bare para meters. Aanpassingen moeten worden uitgevoerd in het binaire classificatie model dat wordt opgegeven als invoer.

2. Voeg een binair classificatie model toe aan de pijp lijn en configureer dat model. U kunt bijvoorbeeld [twee klassen Support Vector machine](two-class-support-vector-machine.md) of een [Geboostte beslissings structuur van twee](two-class-boosted-decision-tree.md)klassen gebruiken.

3. Voeg de module [Train model](train-model.md) toe aan de pijp lijn. Verbind de niet-uitgetrainde classificatie die de uitvoer van One-vs-All Multiclass is.

4. Verbind op de andere invoer van het [trein model](train-model.md)een gelabelde trainings gegevensset met meerdere klassen waarden.

5. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid, kunt u het model gebruiken om voor spellingen voor multi klassen te maken.

U kunt ook de niet-getrainde classificatie door geven aan [kruislings valideren model](cross-validate-model.md) voor kruis validatie tegen een gelabelde validatie gegevensset.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

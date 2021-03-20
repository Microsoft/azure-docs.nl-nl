---
title: Een-VS-één-klasse
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de een-VS-één-module met meerdere klassen in Azure Machine Learning om een classificatie model met meerdere klassen te maken op basis van een ensemble van binaire classificatie modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592903"
---
# <a name="one-vs-one-multiclass"></a>Een-VS-één-klasse

In dit artikel wordt beschreven hoe u de een-VS-één-module met meerdere klassen gebruikt in Azure Machine Learning Designer. Het doel is om een classificatie model te maken dat meerdere klassen kan voors pellen met behulp van de *eenrichtings* benadering.

Deze module is handig voor het maken van modellen die drie of meer mogelijke resultaten voors pellen, wanneer het resultaat afhankelijk is van continue of categorische prediction-variabelen. Met deze methode kunt u ook binaire classificatie methoden gebruiken voor problemen waarvoor meerdere uitvoer klassen zijn vereist.

### <a name="more-about-one-versus-one-models"></a>Meer informatie over een-versus-één model

Sommige classificatie algoritmen maken het gebruik van meer dan twee klassen mogelijk per ontwerp. Andere beperken de mogelijke resultaten voor een van twee waarden (een binair of een model met twee klassen). Maar zelfs binaire classificatie algoritmen kunnen worden aangepast voor classificatie taken met meerdere klassen via verschillende strategieën. 

Met deze module wordt de één-op-een-methode geïmplementeerd, waarin een binair model per klasse-paar wordt gemaakt. Op het moment van de voor spelling is de klasse geselecteerd die de meeste stemmen heeft ontvangen. Omdat `n_classes * (n_classes - 1) / 2` de classificaties vereist zijn, is deze methode meestal trager dan één-op-alle, vanwege de complexiteit van de O (n_classes ^ 2). Deze methode kan echter nuttig zijn voor algoritmen als kernel-algoritmen die niet goed kunnen worden geschaald met `n_samples` . Dit komt doordat elk afzonderlijk leer probleem slechts een kleine subset van de gegevens omvat, terwijl de volledige gegevensset, met één voor alle, wordt gebruikt `n_classes` .

In essentie maakt de module een ensemble van afzonderlijke modellen en voegt de resultaten vervolgens samen om één model te maken waarmee alle klassen worden voor speld. Een binaire classificatie kan worden gebruikt als basis voor een model met één versus één.  

Stel bijvoorbeeld dat u een [twee klasse ondersteunings vector computer](two-class-support-vector-machine.md) model configureert en als invoer voor de module met één-VS-één-klasse. De module maakt met twee klassen ondersteuning vector machine modellen voor alle leden van de klasse output. Vervolgens wordt de één-op-één-methode toegepast om de resultaten voor alle klassen te combi neren.  

De module maakt gebruik van OneVsOneClassifier van sklearn en u vindt [hier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)meer informatie.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>De één-VS-een classificatie met meerdere klassen configureren  

Deze module maakt een ensemble van binaire classificatie modellen om meerdere klassen te analyseren. Als u deze module wilt gebruiken, moet u eerst een *binair classificatie* model configureren en trainen. 

U verbindt het binaire model met de multi klassen module. Vervolgens traint u de ensemble van modellen met behulp van [Train model](train-model.md) met een gelabelde trainings gegevensset.

Wanneer u de modellen combineert, worden met één VS-één klasse meerdere binaire classificatie modellen gemaakt, wordt het algoritme voor elke klasse geoptimaliseerd en worden de modellen vervolgens samengevoegd. De module voert deze taken uit, ook al heeft de gegevensset van de training mogelijk meerdere klassen waarden.

1. Voeg de module met één VS en één Multi klasse toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning-Initialize**, in de categorie **classificatie** .

   De één-VS-één classificatie met meerdere klassen heeft geen eigen para meters. Aanpassingen moeten worden uitgevoerd in het binaire classificatie model dat wordt opgegeven als invoer.

2. Voeg een binair classificatie model toe aan de pijp lijn en configureer dat model. U kunt bijvoorbeeld [twee klassen Support Vector machine](two-class-support-vector-machine.md) of een [Geboostte beslissings structuur van twee](two-class-boosted-decision-tree.md)klassen gebruiken.

3. Voeg de module [Train model](train-model.md) toe aan de pijp lijn. Verbind de niet-getrainde classificatie die de uitvoer is van een-VS-één-klasse.

4. Verbind op de andere invoer van het [trein model](train-model.md)een gelabelde trainings gegevensset met meerdere klassen waarden.

5. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid, kunt u het model gebruiken om voor spellingen voor multi klassen te maken.

U kunt ook de niet-getrainde classificatie door geven aan [kruislings valideren model](cross-validate-model.md) voor kruis validatie tegen een gelabelde validatie gegevensset.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

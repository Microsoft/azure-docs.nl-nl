---
title: Machine Learning-algoritme Cheat Sheet-Designer
titleSuffix: Azure Machine Learning
description: Een afdruk bare Machine Learning-algoritme voor het Cheat-werk blad helpt u bij het kiezen van het juiste algoritme voor uw voorspellende model in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367053"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Machine Learnings Cheat-algoritme voor Azure Machine Learning Designer

Het **Cheat-venster van Azure machine learning-algoritme** helpt u bij het kiezen van het juiste algoritme van de ontwerp functie voor een Predictive Analytics model.

Azure Machine Learning heeft een grote bibliotheek met algoritmen van de *-**classificatie** _, _*_Aanbevolen systemen_*_, _*_clustering_*_, _*_afwijkings detectie_*_, _*_regressie_*_ en _ *_tekst analyse_**-families. Elk is ontworpen om een ander type machine learning probleem op te lossen.

Zie [algoritmen selecteren](how-to-select-algorithms.md) voor meer informatie.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Downloaden: Machine Learning-algoritme Cheat-werk blad

**Down load het Cheat-blad hier: [machine learning-algoritme Cheat-blad (279 x.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Cheat-werk blad Machine Learning-algoritme: meer informatie over het kiezen van een Machine Learning algoritme.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Down load en druk het Machine Learning-algoritme blad Cheat in de formaat van tabloid om het handig te houden en hulp te krijgen bij het kiezen van een algoritme.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Het Cheat-venster van Machine Learning Algorithm gebruiken

De suggesties die in dit Cheat-algoritme worden aangeboden, zijn geschatte regels van de miniatuur. Sommige kunnen worden gebogen en andere kunnen worden flagrantly geschonden. Dit Cheat-venster is bedoeld om een begin punt te suggereren. Zorg dat u geen hoofd-naar-head-concurrentie kunt uitvoeren tussen de verschillende algoritmen van uw gegevens. Er is eenvoudigweg geen vervanging voor het goed keuren van de principes van elk algoritme en het systeem dat uw gegevens heeft gegenereerd.

Elke machine learning-algoritme heeft een eigen stijl of inductieve afwijking. Voor een specifiek probleem zijn er mogelijk verschillende algoritmen geschikt en is het mogelijk dat een algoritme beter past dan andere. Maar het is niet altijd mogelijk om vooraf te weten wat het beste past. In dergelijke gevallen worden verschillende algoritmen samen in het Cheat-venster weer gegeven. Een geschikte strategie is om één algoritme te proberen, en als de resultaten nog niet bevredigend zijn, probeert u de andere. 

Voor meer informatie over de algoritmen in Azure Machine Learning Designer gaat u naar de naslag informatie over [algoritmen en modules](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Soorten machine learning

Er zijn drie hoofd categorieën van machine learning: *leren*, zonder *toezicht* en leer *leren*.

### <a name="supervised-learning"></a>Leren met Super visie

In het onderwijs met Super visie is elk gegevens punt gelabeld of gekoppeld aan een categorie of waarde van belang. Een voor beeld van een categorische-label is het toewijzen van een afbeelding als een ' kat ' of ' hond '. Een voor beeld van een waarde label is de verkoop prijs die is gekoppeld aan een gebruikte auto. Het doel van het leren van een super visie is het bestuderen van een groot aantal voor beelden zoals deze en vervolgens om voor spellingen over toekomstige gegevens punten te kunnen maken. U kunt bijvoorbeeld nieuwe Foto's identificeren met het juiste dier of nauw keurige verkoop prijzen toewijzen aan andere gebruikte auto's. Dit is een populair en nuttig type machine learning.

### <a name="unsupervised-learning"></a>Leren zonder toezicht

Er zijn geen labels aan gegevens punten gekoppeld aan het leren zonder toezicht. In plaats daarvan kunt u de gegevens op een bepaalde manier organiseren of de structuur ervan beschrijven. Met niet-super visie worden gegevens in clusters gegroepeerde, omdat K-wil zeggen, of op verschillende manieren om complexe gegevens te bekijken, zodat deze eenvoudiger worden weer gegeven.

### <a name="reinforcement-learning"></a>Bekrachtigend leren

In het versterken van learning wordt de algoritme gekozen om een actie te kiezen als reactie op elk gegevens punt. Het is een algemene benadering in Robotics, waarbij de set Lees bewerkingen op één punt in de tijd een gegevens punt is, en het algoritme de volgende actie van de robot moet kiezen. Het is ook natuurlijk geschikt voor Internet of Things toepassingen. Het leer algoritme heeft ook een korte tijd een belonings signaal ontvangen dat aangeeft hoe goed de beslissing was. Op basis van dit signaal wijzigt het algoritme de strategie om de hoogste beloning te kunnen halen. 

## <a name="next-steps"></a>Volgende stappen

* Meer richt lijnen voor [het selecteren van algoritmen](how-to-select-algorithms.md) weer geven

* [Meer informatie over studio in azure machine learning en de Azure Portal](overview-what-is-azure-ml.md).

* [Zelf studie: een Voorspellings model bouwen in azure machine learning Designer](tutorial-designer-automobile-price-train-score.md).

* [Meer informatie over uitgebreid leren versus machine learning](concept-deep-learning-vs-machine-learning.md).

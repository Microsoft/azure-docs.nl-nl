---
title: 'Score model: module verwijzing'
titleSuffix: Azure Machine Learning
description: Leer hoe u de module score model in Azure Machine Learning kunt gebruiken om voor spellingen te genereren met behulp van een getrainde classificatie of een regressie model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 67d17af4f615907ca50b27ce8fa26973e5869608
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314248"
---
# <a name="score-model"></a>Score Model

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om voor spellingen te genereren met behulp van een getrainde classificatie of een regressie model.

## <a name="how-to-use"></a>Gebruik

1. Voeg de module **score model** toe aan de pijp lijn.

2. Koppel een getraind model en een gegevensset die nieuwe invoer gegevens bevat. 

    De gegevens moeten een indeling hebben die compatibel is met het type getrainde model dat u gebruikt. Het schema van de invoer gegevensset moet ook algemeen overeenkomen met het schema van de gegevens die worden gebruikt om het model te trainen.

3. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat u een set scores hebt gegenereerd met het [score model](./score-model.md):

+ Als u een set metrische gegevens wilt genereren die worden gebruikt voor het evalueren van de nauw keurigheid van het model (prestaties), kunt u de gescoorde gegevensset verbinden om het [model te evalueren](./evaluate-model.md). 
+ Klik met de rechter muisknop op de module en selecteer **visualiseren** om een voor beeld van de resultaten weer te geven.
<!-- + To Save the results to a dataset. -->

De Score of voorspelde waarde kan in veel verschillende indelingen worden opgegeven, afhankelijk van het model en uw invoer gegevens:

- Voor classificatie modellen voert het [score model](./score-model.md) een voorspelde waarde uit voor de klasse, evenals de waarschijnlijkheid van de voorspelde waarde.
- Voor regressie modellen genereert het [score model](./score-model.md) alleen de voorspelde numerieke waarde.


## <a name="publish-scores-as-a-web-service"></a>Scores publiceren als een webservice

Een gemeen schappelijk gebruik van scores is het retour neren van de uitvoer als onderdeel van een voorspellende webservice. Zie [deze zelf studie](../tutorial-designer-automobile-price-deploy.md) voor meer informatie over het implementeren van een real-time-eind punt op basis van een pijp lijn in azure machine learning Designer.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
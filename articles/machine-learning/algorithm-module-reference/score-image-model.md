---
title: De module Score image model gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de module Score image model in Azure Machine Learning kunt gebruiken om voor spellingen te genereren met behulp van een getraind afbeeldings model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324878"
---
# <a name="score-image-model"></a>Afbeeldingsmodel voor score

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om voor spellingen te genereren met behulp van een getraind installatie kopie model op gegevens van de invoer afbeelding.

## <a name="how-to-configure-score-image-model"></a>Score image model configureren

1. Voeg de module **Score image model** toe aan de pijp lijn.

2. Koppel een getraind afbeeldings model en een gegevensset met gegevens over de invoer afbeelding. 

    De gegevens moeten van het type ImageDirectory zijn. Raadpleeg [Convert to image directory](convert-to-image-directory.md) module voor meer informatie over het ophalen van een map met installatie kopieën. Het schema van de invoer gegevensset moet ook algemeen overeenkomen met het schema van de gegevens die worden gebruikt om het model te trainen.

3. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat u een set scores hebt gegenereerd met een [Score afbeeldings model](score-image-model.md), kunt u deze module en de gescoorde gegevensset verbinden om het [model te evalueren](evaluate-model.md), om een set metrische gegevens te genereren die worden gebruikt voor het evalueren van de nauw keurigheid van het model (prestaties). 

### <a name="publish-scores-as-a-web-service"></a>Scores publiceren als een webservice

Een gemeen schappelijk gebruik van scores is het retour neren van de uitvoer als onderdeel van een voorspellende webservice. Zie [deze zelf studie](../tutorial-designer-automobile-price-deploy.md) voor meer informatie over het implementeren van een real-time-eind punt op basis van een pijp lijn in azure machine learning Designer.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
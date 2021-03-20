---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de DenseNet-module in Azure Machine Learning Designer voor het maken van een afbeeldings classificatie model met behulp van de DenseNet-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d21c7443f6b30d0b7d6e8295c0c9b060a80d9a62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421325"
---
# <a name="densenet"></a>DenseNet

In dit artikel wordt beschreven hoe u de **DenseNet** -module in azure machine learning Designer kunt gebruiken om een classificatie model voor installatie kopieën te maken met behulp van de DenseNet-algoritme.  

Deze classificatie-algoritme is een gesupere leer methode en vereist een map met een gelabelde afbeelding. 

> [!NOTE]
> Deze module biedt geen ondersteuning voor een gelabelde gegevensset die is gegenereerd op basis van *gegevens labeling* in Studio, maar ondersteunt alleen gelabelde map met installatie kopieën die is gegenereerd van [converteren naar afbeelding Directory](convert-to-image-directory.md) module. 

U kunt het model trainen door het model en de map met de gelabelde afbeelding op te geven als invoer voor het [trainen van Pytorch-model](train-pytorch-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoer voorbeelden te voors pellen met behulp van [Score afbeeldings model](score-image-model.md).

### <a name="more-about-densenet"></a>Meer informatie over DenseNet

Zie voor meer informatie over DenseNet het document Research, [dense-verbonden Convolutional-netwerken](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>DenseNet configureren

1.  Voeg de module **DenseNet** toe aan uw pijp lijn in de ontwerp functie.  

2.  Geef bij **model naam** de naam op van een bepaalde DenseNet-structuur en u kunt kiezen uit de ondersteunde DenseNet: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Voor vooraf **getrainde** geeft u op of u een voor ImageNet wilt gebruiken. Als deze is geselecteerd, kunt u het model op basis van het geselecteerde vooraf getrainde model verfijnen. Als u dit selectie vakje uitschakelt, kunt u helemaal zelf trainen.

4.  Geef voor **geheugen efficiënt** op of controle punten moeten worden gebruikt. Dit is veel meer geheugen-efficiënt, maar langzamer. Zie voor meer informatie het document Research, [geheugen-efficiënte implementatie van DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Verbind de uitvoer van de module **DenseNet** -module, training en validatie kopie gegevensset naar het [Model Train Pytorch](train-pytorch-model.md). 

6. Verzend de pijp lijn.


## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid, kunt u het model voor scores gebruiken door het model van [Train Pytorch](train-pytorch-model.md) te verbinden met een [Score image model](score-image-model.md)om waarden voor nieuwe invoer voorbeelden te voors pellen.

## <a name="technical-notes"></a>Technische opmerkingen  

###  <a name="module-parameters"></a>Module parameters  

| Name             | Bereik | Type    | Standaard     | Beschrijving                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Modelnaam       | Alle   | Modus    | densenet201 | Naam van een bepaalde DenseNet-structuur     |
| Voortraind       | Alle   | Boolean-waarde | True        | Of u een vooraf getrainde model wilt gebruiken op ImageNet |
| Geheugen efficiënt | Alle   | Booleaans | Niet waar       | Of controle punten moeten worden gebruikt, wat veel geheugen efficiënt maar langzamer is |

###  <a name="output"></a>Uitvoer  

| Naam            | Type                    | Beschrijving                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Niet-traind model | UntrainedModelDirectory | Een niet-traind DenseNet-model dat kan worden verbonden met Train Pytorch model. |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

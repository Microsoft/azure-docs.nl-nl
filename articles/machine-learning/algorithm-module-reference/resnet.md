---
title: ResNet
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een afbeeldings classificatie model in Azure Machine Learning Designer met behulp van de ResNet-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 88a820d0f1fa9515b4f2992a8305a2d1065e0987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421206"
---
# <a name="resnet"></a>ResNet

In dit artikel wordt beschreven hoe u de **ResNet** -module in azure machine learning Designer gebruikt om een classificatie model voor installatie kopieën te maken met behulp van de ResNet-algoritme.  

Deze classificatie-algoritme is een geclassificeerde leer methode en vereist een gegevensset met een label. 
> [!NOTE]
> Deze module biedt geen ondersteuning voor een gelabelde gegevensset die is gegenereerd op basis van *gegevens labeling* in Studio, maar ondersteunt alleen gelabelde map met installatie kopieën die is gegenereerd van [converteren naar afbeelding Directory](convert-to-image-directory.md) module. 

U kunt het model trainen door een model en een gelabelde map met installatie kopieën op te geven als invoer voor het [trainen van Pytorch-model](train-pytorch-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoer voorbeelden te voors pellen met behulp van [Score afbeeldings model](score-image-model.md).

### <a name="more-about-resnet"></a>Meer informatie over ResNet

Raadpleeg [dit artikel](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) voor meer informatie over ResNet.

## <a name="how-to-configure-resnet"></a>ResNet configureren

1.  Voeg de module **ResNet** toe aan uw pijp lijn in de ontwerp functie.  

2.  Geef bij **model naam** een naam op van een bepaalde ResNet-structuur en u kunt kiezen uit de ondersteunde ResNet: ' resnet18 ', ' resnet34 ', ' resnet50 ', ' resnet101 ', ' resnet152 ', ' resnet152 ', ' resnext50 \_ 32x4d ', ' resnext101 \_ 32x8d ', ' wide_resnet50 \_ 2 ', ' wide_resnet101 \_ 2 '.

3.  Voor vooraf **getrainde** geeft u op of u een voor ImageNet wilt gebruiken. Als deze is geselecteerd, kunt u het model op basis van het geselecteerde vooraf getrainde model verfijnen. Als u dit selectie vakje uitschakelt, kunt u helemaal zelf trainen.

4.  Verbind de uitvoer van de **DenseNet** -module, de trainings-en validatie afbeelding gegevensset-module naar het [Train Pytorch-model](train-pytorch-model.md). 

5. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid, kunt u het model voor scores gebruiken door het model van [Train Pytorch](train-pytorch-model.md) te verbinden met een [Score image model](score-image-model.md)om waarden voor nieuwe invoer voorbeelden te voors pellen.

## <a name="technical-notes"></a>Technische opmerkingen  

###  <a name="module-parameters"></a>Module parameters  

| Name       | Bereik | Type    | Standaard           | Beschrijving                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Modelnaam | Alle   | Modus    | resnext101 \_ 32x8d | Naam van een bepaalde ResNet-structuur       |
| Voortraind | Alle   | Boolean-waarde | True              | Of u een vooraf getrainde model wilt gebruiken op ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Uitvoer  

| Naam            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Niet-traind model | UntrainedModelDirectory | Een niet-traind ResNet-model dat kan worden verbonden met Train Pytorch model. |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
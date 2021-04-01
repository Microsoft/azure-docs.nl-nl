---
title: Pytorch-model trainen
titleSuffix: Azure Machine Learning
description: Gebruik de module Train Pytorch models in Azure Machine Learning Designer om modellen helemaal opnieuw te leren of bestaande modellen te verfijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420662"
---
# <a name="train-pytorch-model"></a>Pytorch-model trainen

In dit artikel wordt beschreven hoe u de **Pytorch-model module Train** in azure machine learning Designer kunt gebruiken om Pytorch-modellen zoals DenseNet te trainen. De training vindt plaats nadat u een model hebt gedefinieerd en de para meters hebt ingesteld en dat gelabelde gegevens vereist. 

## <a name="how-to-use-train-pytorch-model"></a>Het Pytorch-model van Train gebruiken 

1. Voeg de [DenseNet](densenet.md) -module of [ResNet](resnet.md) toe aan het ontwerp van de pijp lijn in de ontwerp functie.

2. Voeg de module **Train Pytorch model** toe aan de pijp lijn. U kunt deze module vinden onder de categorie **model training** . Vouw **Train** uit en sleep de module **Train Pytorch model** naar uw pijp lijn.

   > [!NOTE]
   > De **Pytorch-model** module voor Train kan beter worden uitgevoerd op het **GPU** -type Compute voor grote gegevensset, anders mislukt de pijp lijn. U kunt Compute voor een specifieke module selecteren in het rechterdeel venster van de module door het **gebruik van andere compute target** in te stellen.

3.  Voeg aan de linkerkant een niet-traind model toe. Koppel de trainings gegevensset en de validatie gegevensset aan de middelste en rechter invoer van het **trein Pytorch model**.

    Voor een niet-traind model moet het een pytorch-model zijn zoals DenseNet; anders wordt een ' InvalidModelDirectoryError ' gegenereerd.

    De trainings gegevensset voor gegevensset moet een map met een gelabelde afbeelding zijn. Raadpleeg voor meer informatie over het ophalen van een gelabelde map met afbeeldingen **converteren naar afbeelding** . Als u geen label krijgt, wordt een ' NotLabeledDatasetError ' gegenereerd.

    De gegevensset en validatie gegevensset van de training hebben dezelfde label categorieën, anders wordt er een InvalidDatasetError gegenereerd.

4.  Voor **epoches** geeft u op hoeveel epochen u wilt trainen. De hele gegevensset wordt standaard 5 in elke epoche herhaald.

5.  Geef bij **Batch grootte** op hoeveel instanties in een batch moeten worden getraind, standaard 16.

6.  Geef voor het **leer tempo** een waarde op voor het *leer tempo*. De leer frequentie waarden bepalen de grootte van de stap die wordt gebruikt in optimalisatie, zoals SGD telkens wanneer het model wordt getest en gecorrigeerd.

    Door het aantal kleiner te maken, test u het model vaker, met het risico dat u mogelijk vastloopt in een lokaal vaste waarde. Door de stap groter te maken, kunt u sneller convergeren, tegen het risico van een overschrijding van de echte minima. standaard 0,001.

7.  Voor **wille keurig zaad** typt u optioneel een geheel getal dat moet worden gebruikt als seed. Het gebruik van een Seed wordt aanbevolen als u de reproduceer baarheid van het experiment over uitvoeringen wilt garanderen.

8.  Voor **geduld**, geeft u op hoeveel epoche er moet worden getraind als het validatie verlies niet opeenvolgend afneemt. standaard 3.

9.  Verzend de pijp lijn. Als uw gegevensset een grotere grootte heeft, duurt het enige tijd en wordt de GPU-berekening aanbevolen.

## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid, kunt u het model voor scores gebruiken door het model van [Train Pytorch](train-pytorch-model.md) te verbinden met een [Score image model](score-image-model.md)om waarden voor nieuwe invoer voorbeelden te voors pellen.

## <a name="technical-notes"></a>Technische opmerkingen
###  <a name="expected-inputs"></a>Verwachte invoer  

| Naam               | Type                    | Description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Niet-traind model    | UntrainedModelDirectory | Niet-traind model, vereisen pytorch         |
| Trainings gegevensset   | ImageDirectory          | Trainings gegevensset                         |
| Validatie gegevensset | ImageDirectory          | Validatie gegevensset voor evaluatie elke epoche |

###  <a name="module-parameters"></a>Module parameters  

| Name          | Bereik            | Type    | Standaard | Beschrijving                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epoches        | >0               | Geheel getal | 5       | Selecteer de kolom die de kolom Label of het resultaat bevat |
| Batchgrootte    | >0               | Geheel getal | 16      | Het aantal exemplaren dat in een batch moet worden getraind   |
| Leersnelheid | >= double. Letter | Float   | 0,001   | Het eerste leer tempo voor de stochastische Gradient Daal Optimizer. |
| Wille keurige Seed   | Alle              | Geheel getal | 1       | Het Seed voor de generator voor wille keurige getallen die wordt gebruikt door het model. |
| Geduld      | >0               | Geheel getal | 3       | Het aantal epochen naar een vroege stop-training   |

###  <a name="outputs"></a>Uitvoerwaarden  

| Naam          | Type           | Description   |
| ------------- | -------------- | ------------- |
| Getraind model | ModelDirectory | Getraind model |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 




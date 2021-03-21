---
title: Wat is gedistribueerde training?
titleSuffix: Azure Machine Learning
description: Meer informatie over het type gedistribueerde training dat Azure Machine Learning ondersteunt en de open source framework-integraties die beschikbaar zijn voor gedistribueerde trainingen.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498962"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Gedistribueerde training met Azure Machine Learning

In dit artikel vindt u meer informatie over gedistribueerde trainingen en hoe Azure Machine Learning deze ondersteunt voor diepe leer modellen. 

In de gedistribueerde training wordt de werk belasting voor het trainen van een model opgesplitst en gedeeld met meerdere mini processors, die werk knooppunten worden genoemd. Deze worker-knoop punten werken parallel met het versnellen van model training. Gedistribueerde training kan worden gebruikt voor traditionele ML-modellen, maar is beter geschikt voor reken-en tijdrovende taken, zoals [diepe](concept-deep-learning-vs-machine-learning.md) training voor het trainen van diepe Neural-netwerken. 

## <a name="deep-learning-and-distributed-training"></a>Diep gaande lessen en gedistribueerde trainingen 

Er zijn twee hoofd typen gedistribueerde training: [gegevens parallellisme](#data-parallelism) en [model parallellisme](#model-parallelism). Voor gedistribueerde training over diepe leer modellen ondersteunt de [Azure machine learning SDK in python](/python/api/overview/azure/ml/intro) integraties met populaire frameworks, PyTorch en tensor flow. Beide Frameworks gebruiken gegevens parallellisme voor gedistribueerde trainingen en kunnen gebruikmaken van [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) voor het optimaliseren van reken snelheden. 

* [Gedistribueerde training met PyTorch](how-to-train-pytorch.md#distributed-training)

* [Gedistribueerde training met tensor flow](how-to-train-tensorflow.md#distributed-training)

Voor ML-modellen waarvoor geen gedistribueerde training nodig is, raadpleegt u [modellen met Azure machine learning](concept-train-machine-learning-model.md#python-sdk) voor de verschillende manieren om modellen te trainen met behulp van de PYTHON-SDK.

## <a name="data-parallelism"></a>Gegevens parallellisme

Gegevens parallellisme is de eenvoudigste manier om de twee gedistribueerde trainings benaderingen te implementeren en is voldoende voor de meeste gebruiks scenario's.

In deze benadering worden de gegevens onderverdeeld in partities, waarbij het aantal partities gelijk is aan het totale aantal beschik bare knoop punten in het berekenings cluster. Het model wordt in elk van deze worker-knoop punten gekopieerd en elke werk nemer werkt op een eigen subset van de gegevens. Houd er rekening mee dat elk knoop punt de capaciteit moet hebben voor de ondersteuning van het model dat wordt getraind. het model moet volledig op elk knoop punt passen. Het volgende diagram biedt een visuele demonstratie van deze benadering.

![Data-parallellisme-concept diagram](./media/concept-distributed-training/distributed-training.svg)

Elk knoop punt berekent onafhankelijk de fouten tussen de voor spellingen van de trainings voorbeelden en de gelabelde uitvoer. Elk knoop punt werkt op zijn beurt het model bij op basis van de fouten en moet alle wijzigingen door geven aan de andere knoop punten om de bijbehorende modellen bij te werken. Dit betekent dat de worker-knoop punten de model parameters, of verlopen, aan het einde van de batch berekening moeten synchroniseren om ervoor te zorgen dat ze een consistent model trainen. 

## <a name="model-parallelism"></a>Model parallellisme

In model parallellisme, ook wel netwerk parallellisme genoemd, wordt het model gesegmenteerd in verschillende onderdelen die gelijktijdig kunnen worden uitgevoerd op verschillende knoop punten, en elk van beide worden uitgevoerd op dezelfde gegevens. De schaal baarheid van deze methode is afhankelijk van de mate van taak parallel Lise ring van het algoritme en is complexer voor implementatie dan gegevens parallellisme. 

In model parallelie hoeven werk knooppunten alleen de gedeelde para meters te synchroniseren, meestal één keer voor elke stap voorwaarts of achterwaarts door geven. Grotere modellen zijn ook niet relevant omdat elk knoop punt wordt uitgevoerd op een subsectie van het model in dezelfde trainings gegevens.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [gebruik van Compute-doelen voor model training](how-to-set-up-training-targets.md) met de PYTHON-SDK.
* Zie het [scenario referentie architectuur](/azure/architecture/reference-architectures/ai/training-deep-learning)voor een technisch voor beeld.
* [Train ml-modellen met tensor flow](how-to-train-tensorflow.md).
* [Train ml-modellen met PyTorch](how-to-train-pytorch.md).
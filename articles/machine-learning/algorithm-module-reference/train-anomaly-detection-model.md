---
title: 'Model voor afwijkings detectie trainen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module voor het detecteren van detectie van Train-modellen voor het maken van een getraind anomalie detectie model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905726"
---
# <a name="train-anomaly-detection-model-module"></a>Model module voor detectie van anomalieën trainen

In dit artikel wordt beschreven hoe u de model module voor het detecteren van de Train-detectie in Azure Machine Learning Designer kunt gebruiken om een getraind anomalie detectie model te maken.

De module gebruikt de invoer van een set para meters voor een anomalie detectie model en een niet-gelabelde gegevensset. Er wordt een getraind anomalie detectie model geretourneerd, samen met een set labels voor de trainings gegevens.  

Zie voor meer informatie over de algoritmen voor anomalie detectie die zijn opgenomen in de ontwerp functie voor [anomalie detectie op basis van PCA](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Het detectie model voor het afwijkings proces van Train configureren 

1.  Voeg de module **afwijkingen detectie model** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **anomalie detectie** .

2. Verbind een van de modules die zijn ontworpen voor detectie van anomalieën, zoals [op PCA gebaseerde anomalie detectie](pca-based-anomaly-detection.md).

    Andere typen modellen worden niet ondersteund. Wanneer u de pijp lijn uitvoert, wordt de volgende fout weer geven: alle modellen moeten hetzelfde informatie type hebben.  

3.  Configureer de module anomalie detectie door de kolom label te kiezen en andere para meters in te stellen die specifiek zijn voor het algoritme.  

4.  Voeg een trainings gegevensset toe aan de rechter invoer van het **detectie model** van de Train.  

5.  Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u de para meters van het model wilt weer geven, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**. 

+ Als u voor spellingen wilt maken, gebruikt u de module [score model](score-model.md) met nieuwe invoer gegevens.

+ Selecteer de module om een moment opname van het getrainde model op te slaan. Selecteer vervolgens het pictogram **gegevensset registreren** onder het tabblad **uitvoer en logboeken** in het rechterdeel venster.   

 
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [uitzonde ringen en fout codes voor de ontwerp functie](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerp modules.
'
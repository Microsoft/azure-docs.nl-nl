---
title: 'Train clustering model: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module clustering model leren in Azure Machine Learning voor het trainen van cluster modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: ea6673a04bf9f5f568c660658e51036f2d2712e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654727"
---
# <a name="train-clustering-model"></a>Clustermodel trainen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een cluster model te trainen.

De module heeft een niet-getraind cluster model dat u al hebt geconfigureerd met de [K-betekent clustering](k-means-clustering.md) module en traint het model met behulp van een gelabelde of niet-gelabelde gegevensset. De module maakt zowel een getraind model dat u voor de voor spelling kunt gebruiken als voor elk geval een set cluster toewijzingen in de trainings gegevens.

> [!NOTE]
> Een cluster model kan niet worden getraind met behulp van de [Train model](train-model.md) module, de algemene module voor het trainen van machine learning modellen. Dat komt omdat [Train model](train-model.md) alleen werkt met Super visie-algoritmen. Met K-betekent dat en andere cluster algoritmen onbewaakt leren toestaan, wat inhoudt dat het algoritme kan leren van niet-gelabelde gegevens.  
  
## <a name="how-to-use-train-clustering-model"></a>Het gebruik van Train clustering model  

1.  Voeg de module **clustering model trainen** toe aan uw pijp lijn in de ontwerp functie. U kunt de module vinden onder **machine learning-modules** in de categorie **trein** .  
  
2. Voeg de [cluster module K-betekent](k-means-clustering.md) toe, of een andere aangepaste module die een compatibel cluster model maakt en stel de para meters van het cluster model in.  
    
3.  Een trainings gegevensset koppelen aan de rechter invoer van het **cluster model voor Train**.
  
5.  Selecteer in **kolom set** de kolommen uit de gegevensset die u wilt gebruiken voor het maken van clusters. Zorg ervoor dat u kolommen selecteert die goede functies maken: Vermijd het gebruik van Id's of andere kolommen met unieke waarden of kolommen met dezelfde waarden.

    Als er een label beschikbaar is, kunt u het gebruiken als een functie of dit laten.  
  
6. Selecteer de optie, **Schakel het selectie vakje toevoegen of alleen uitschakelen voor resultaat** in als u de trainings gegevens samen met het nieuwe cluster label wilt uitvoeren.

    Als u deze optie uitschakelt, worden alleen de cluster toewijzingen uitgevoerd. 

7. Verzend de pijp lijn, of klik op de module **clustering model trainen** en selecteer **geselecteerde uitvoeren**.  
  
### <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van de module **Train model** . Selecteer het pictogram **gegevensset registreren** om het model als een herbruikbare module op te slaan.

+ Als u scores wilt genereren op basis van het model, gebruikt u [gegevens toewijzen aan clusters](assign-data-to-clusters.md).

> [!NOTE]
> Als u het getrainde model in de ontwerp functie moet implementeren, moet u ervoor zorgen dat het [toewijzen van gegevens aan clusters](assign-data-to-clusters.md) in plaats van het **score model** is verbonden met de invoer van de module voor webservice-uitvoer in de pipeline voor het afnemen van [webservices](web-service-input-output.md) .

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
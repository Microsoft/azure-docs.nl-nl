---
title: Afbeeldingstransformatie toepassen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module afbeeldings transformatie Toep assen om een afbeeldings transformatie toe te passen op een afbeeldings Directory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898905"
---
# <a name="apply-image-transformation"></a>Afbeeldingstransformatie toepassen 

In dit artikel wordt beschreven hoe u de module installatie kopie Toep assen in Azure Machine Learning Designer kunt gebruiken om een map met een invoer installatie kopie te wijzigen op basis van een eerder opgegeven afbeeldings transformatie.  

U moet een init- [installatie kopie transformatie](init-image-transformation.md) module verbinden om de trans formatie op te geven. vervolgens kunt u deze trans formatie Toep assen op de invoer afbeelding van de module installatie kopie Toep assen.

## <a name="how-to-use-apply-image-transformation"></a>Afbeeldings transformatie Toep assen gebruiken  

1. Voeg de module voor het **transformeren van installatie kopieën** toe aan de pijp lijn. U kunt deze module vinden onder de categorie *gegevens transformatie van computer vision/afbeelding* . 

2. Koppel de uitvoer van de **trans formatie** van de init-afbeelding aan de linkerkant van de **afbeeldings transformatie Toep assen**.

     > [!NOTE]
     > Alleen afbeeldings transformatie die is gegenereerd door de initialisatie van de [init image](init-image-transformation.md) -module, wordt geaccepteerd voor deze module. Voor een ander type trans formatie moet u verbinding maken met [trans formatie Toep assen](apply-transformation.md), anders wordt InvalidTransformationDirectoryError gegenereerd.


3. Verbind de map met de installatie kopie die u wilt transformeren.

4. Voor de **modus** geeft u op voor welk doel u invoer transformatie gebruikt: ' for training ' of ' for deinterferentie '. 

   Als u **voor training** selecteert, wordt alle trans formatie toegepast die u opgeeft in de trans formatie van init image.

   Als u kiest **voor** inschakeling, wordt trans formatie, zoals het wille keurig maken van nieuwe steek proeven, uitgesloten voordat ze worden toegepast. Dit komt omdat transformatie bewerkingen om nieuwe steek proeven wille keurig als ' wille keurige horizon taal spie gelen ' te maken, worden gebruikt voor de uitbrei ding van gegevens in de training, die moet worden verwijderd in afleiding, omdat er voor een nauw keurige voor spelling en evaluatie van inactiviteit steek proeven moeten worden verholpen.

   > [!NOTE]
   > Trans formaties die worden uitgesloten in de modus **voor deinterferentie** zijn: wille keurig aangepast gewas, wille keurige bijsnijden, wille keurige horizontale spie gelen, wille keurige verticale spiegel, wille keurige weer gave, wille keurige affiniteit, wille keurige grijs waarde, wille keurig verwijderen.

5. Als u een afbeeldings transformatie wilt Toep assen op een nieuwe map met installatie kopieën, verzendt u de pijp lijn.  

### <a name="module-parameters"></a>Module parameters

| Name | Bereik | Type | Standaard                   | Beschrijving                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Modus | Alle   | Modus | (Verplicht te geven gebruiker) | Voor welk doel gebruikt u invoer transformatie. U moet wille keurige transformatie bewerkingen uitsluiten, maar ze in de training houden |

### <a name="expected-inputs"></a>Verwachte invoer  

| Naam                       | Type                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Trans formatie van invoer afbeelding | TransformationDirectory | Trans formatie van invoer afbeelding        |
| Map met invoer afbeeldingen      | ImageDirectory          | Afbeeldings Directory die moet worden getransformeerd |

### <a name="outputs"></a>Uitvoerwaarden  

| Naam                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Uitvoer afbeelding map | ImageDirectory | Uitvoer afbeelding map |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

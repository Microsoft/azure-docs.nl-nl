---
title: Merkdetectie - Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt een gespecialiseerde modus voor objectdetectie besproken; merk- en/of logodetectie met behulp van Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 5892fc0bbbd07690ff010e8e1212a914733cbb18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778981"
---
# <a name="detect-popular-brands-in-images"></a>Populaire merken in afbeeldingen detecteren

Merkdetectie is een gespecialiseerde modus voor [objectdetectie](concept-object-detection.md) die gebruikmaakt van een database met duizenden globale logo's om commerciële merken in afbeeldingen of video's te identificeren. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media.

De Computer Vision-service detecteert of er merklogo's in een bepaalde afbeelding staan; Zo ja, dan worden de merknaam, een betrouwbaarheidsscore en de coördinaten van een begrensingsvak rond het logo weergegeven.

De ingebouwde logodatabase omvat populaire merken in consumentenelek electronica, kleding en meer. Als u ontdekt dat het merk dat u zoekt niet wordt gedetecteerd door de Computer Vision-service, kunt u beter uw eigen logodetector maken en trainen met behulp van [de Custom Vision-service.](../custom-vision-service/index.yml)

## <a name="brand-detection-example"></a>Voorbeeld van merkdetectie

De volgende JSON-antwoorden illustreren wat Computer Vision retourneert bij het detecteren van merken in de voorbeeldafbeeldingen.

![Een rood shirt met een Microsoft-label en logo erop](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

In sommige gevallen haalt de merkdetector zowel de logoafbeelding als de ged maken merknaam op als twee afzonderlijke logo's.

![Een grijze auto met een Microsoft-label en logo erop](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>De API gebruiken

De merkdetectiefunctie maakt deel uit van de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Brands` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"brands"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
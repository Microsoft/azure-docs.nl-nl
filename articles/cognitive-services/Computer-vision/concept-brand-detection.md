---
title: Merk detectie-Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt een gespecialiseerde modus voor object detectie beschreven. merk-en/of logo detectie met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 46cead6f7b9362dc9b48fda15c0d04b13c2f989c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013656"
---
# <a name="detect-popular-brands-in-images"></a>Populaire merken detecteren in installatie kopieën

Merk detectie is een gespecialiseerde modus van [object detectie](concept-object-detection.md) die gebruikmaakt van een Data Base met duizenden wereld wijde logo's waarmee commerciële merken in afbeeldingen of video worden geïdentificeerd. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media.

De Computer Vision-service detecteert of er merk logo's zijn in een bepaalde afbeelding. Als dit het geval is, wordt de merk naam, een betrouwbaarheids Score en de coördinaten van een selectie kader rond het logo geretourneerd.

De ingebouwde logo database heeft betrekking op populaire merken in consumenten elektronica, kleren en meer. Als u merkt dat het merk dat u zoekt niet wordt gedetecteerd door de Computer Vision-service, kunt u beter uw eigen logo detector maken en trainen met behulp van de [Custom Vision](../custom-vision-service/index.yml) -service.

## <a name="brand-detection-example"></a>Voor beeld van merk detectie

De volgende JSON-antwoorden laten zien wat Computer Vision retourneert bij het detecteren van Brands in de voorbeeld afbeeldingen.

![Een rode shirt met een micro soft-label en-logo](./Images/red-shirt-logo.jpg)

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

In sommige gevallen haalt de merk detector zowel de logo afbeelding als de stijl van de stijlvol merk op als twee afzonderlijke logo's.

![Een grijze Sweatshirt met een micro soft-label en-logo](./Images/gray-shirt-logo.jpg)

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

De functie voor merk detectie maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Brands` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"brands"` sectie.

* [Snelstartgids: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quick Start: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
---
title: Beschrijvingen van afbeeldingen - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de afbeeldingsbeschrijvingsfunctie van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c517fa98bfc17d4702a51d4990e860b2ed7aaefd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778927"
---
# <a name="describe-images-with-human-readable-language"></a>Afbeeldingen beschrijven met een door mensen leesbare taal

Computer Vision kunt een afbeelding analyseren en een door mensen leesbare zin genereren die de inhoud ervan beschrijft. Het algoritme retourneert in feite verschillende beschrijvingen op basis van verschillende visuele kenmerken en elke beschrijving krijgt een betrouwbaarheidsscore. De uiteindelijke uitvoer is een lijst met beschrijvingen die zijn geordend van hoogste naar laagste betrouwbaarheid.

## <a name="image-description-example"></a>Voorbeeld van een beschrijving van de afbeelding

Het volgende JSON-antwoord illustreert wat Computer Vision retourneert bij het beschrijven van de voorbeeldafbeelding op basis van de visuele kenmerken.

![Een zwart-witfoto van gebouwen in New York](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie beschrijving van de afbeelding maakt deel uit van [de Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Description` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"description"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de gerelateerde concepten van het [taggen van afbeeldingen](concept-tagging-images.md) [en het categoriseren van afbeeldingen.](concept-categorizing-images.md)

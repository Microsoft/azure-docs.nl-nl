---
title: Beschrijvingen van installatie kopieën-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie beschrijving van afbeelding van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4fd328dad3544697ba1f4896f7383be857b097a5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530660"
---
# <a name="describe-images-with-human-readable-language"></a>Afbeeldingen beschrijven met een Human-Lees bare taal

Computer Vision kunt een afbeelding analyseren en een door de mens lees bare zin genereren waarin de inhoud wordt beschreven. Het algoritme retourneert een aantal beschrijvingen op basis van verschillende visuele functies en elke beschrijving krijgt een betrouwbaarheids Score. De uiteindelijke uitvoer is een lijst met beschrijvingen die zijn besteld van hoogste naar laagste betrouw baarheid.

## <a name="image-description-example"></a>Voor beeld van installatie kopie beschrijving

In het volgende JSON-antwoord ziet u wat Computer Vision retourneert bij het beschrijven van de voorbeeld afbeelding op basis van de visuele functies.

![Een zwarte en witte afbeelding van gebouwen in Manhattan](./Images/bw_buildings.png)

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

De functie beschrijving van afbeelding maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Description` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"description"` sectie.

* [Snelstartgids: Computer Vision REST API of client bibliotheken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verwante concepten voor het [labelen van afbeeldingen](concept-tagging-images.md) en het [categoriseren van afbeeldingen](concept-categorizing-images.md).

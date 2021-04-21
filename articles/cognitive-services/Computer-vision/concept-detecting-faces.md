---
title: Gezichtsdetectie - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de gezichtsdetectiefunctie van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778837"
---
# <a name="face-detection-with-computer-vision"></a>Gezichtsdetectie met Computer Vision

Computer Vision kunt menselijke gezichten in een afbeelding detecteren en de leeftijd, het geslacht en de rechthoek voor elk gedetecteerd gezicht genereren. 

> [!NOTE]
> Deze functie wordt ook aangeboden door de Azure [Face-service.](../face/index.yml) Zie dit alternatief voor gedetailleerdere gezichtsanalyse, waaronder gezichtsidentificatie en houdingsdetectie. 

## <a name="face-detection-examples"></a>Voorbeelden van gezichtsdetectie

In het volgende voorbeeld ziet u het JSON-antwoord dat door de Computer Vision voor een afbeelding met één menselijk gezicht.

![Vision-analyse Vrouw op dak-gezicht](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

In het volgende voorbeeld ziet u het JSON-antwoord dat wordt geretourneerd voor een afbeelding met meerdere menselijke gezichten.

![Vision: gezichtsfoto's van familie analyseren](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie gezichtsdetectie maakt deel uit van [de Analyze Image-API.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Faces` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"faces"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

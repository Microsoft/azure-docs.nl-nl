---
title: Detectie van afbeeldingstype - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie detectie van afbeeldingstype van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dc24788ddd21ca2b7df1f9f92238c776dee33016
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778873"
---
# <a name="detecting-image-types-with-computer-vision"></a>Afbeeldingstypen detecteren met Computer Vision

Met de [Analyze Image-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Computer Vision het inhoudstype van afbeeldingen analyseren, waarmee wordt aangegeven of een afbeelding een illustratie of een lijntekening is.

## <a name="detecting-clip-art"></a>Illustratie detecteren

Computer Vision analyseert een afbeelding en analyseert de kans dat de afbeelding een illustratie wordt op een schaal van 0 tot 3, zoals beschreven in de volgende tabel.

| Waarde | Betekenis |
|-------|---------|
| 0 | Non-clip-art (geen illustratie) |
| 1 | Dubbelzinnig |
| 2 | Normale illustratie |
| 3 | Goede illustratie |

### <a name="clip-art-detection-examples"></a>Voorbeelden van illustratiedetectie

De volgende JSON-antwoorden laten zien wat Computer Vision retourneert wanneer de kans wordt beoordeeld dat de voorbeeldafbeeldingen illustratief zijn.

![Een illustratieafbeelding van een stuk kaas](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Een blauw huis en de voorste erf](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Lijntekeningen detecteren

Computer Vision een afbeelding analyseert en een Booleaanse waarde retourneert die aangeeft of de afbeelding een lijntekening is.

### <a name="line-drawing-detection-examples"></a>Voorbeelden van lijntekeningdetectie

De volgende JSON-antwoorden laten zien wat Computer Vision retourneert wanneer wordt aangegeven of de voorbeeldafbeeldingen lijntekeningen zijn.

![Een lijntekeningafbeelding van een man](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Een witte bloem met een groene achtergrond](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie detectie van afbeeldingstype maakt deel uit van [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `ImageType` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"imageType"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

---
title: Detectie van kleurenschema's - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van het kleurenschema in afbeeldingen met behulp van Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b113c424851065e244c3943bf4a4816bae9bba8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778909"
---
# <a name="detect-color-schemes-in-images"></a>Kleurenschema's in afbeeldingen detecteren

Computer Vision analyseert de kleuren in een afbeelding om drie verschillende kenmerken te bieden: de dominante voorgrondkleur, de dominante achtergrondkleur en de set dominante kleuren voor de afbeelding als geheel. Geretourneerde kleuren horen bij de set: zwart, blauw, grijs, groen, oranje, roze, paars, rood, teal, wit en geel. 

Computer Vision extraheert ook een accentkleur, die de meest opvallende kleur in de afbeelding vertegenwoordigt, op basis van een combinatie van dominante kleuren en verzadiging. De accentkleur wordt geretourneerd als een hexadecimale HTML-kleurcode. 

Computer Vision retourneert ook een Booleaanse waarde die aangeeft of een afbeelding zwart-wit is.

## <a name="color-scheme-detection-examples"></a>Voorbeelden van detectie van kleurenschema's

Het volgende voorbeeld illustreert het JSON-antwoord dat door de Computer Vision bij het detecteren van het kleurenschema van de voorbeeldafbeelding. In dit geval is de voorbeeldafbeelding geen zwart-witafbeelding, maar zijn de dominante voor- en achtergrondkleuren zwart en zijn de dominante kleuren voor de afbeelding als geheel zwart-wit.

![Buitenser mountain bij vervalsen, met de mand van een persoon](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Voorbeelden van dominante kleuren

In de volgende tabel ziet u de geretourneerde voorgrond-, achtergrond- en afbeeldingskleuren voor elke voorbeeldafbeelding.

| Installatiekopie | Dominante kleuren |
|-------|-----------------|
|![Een witte bloem met een groene achtergrond](./Images/flower.png)| Voorgrond: Zwart<br/>Achtergrond: Wit<br/>Kleuren: Zwart, Wit, Groen|
![Een train die door een station loopt](./Images/train_station.png) | Voorgrond: Zwart<br/>Achtergrond: Zwart<br/>Kleuren: Zwart |

### <a name="accent-color-examples"></a>Voorbeelden van accentkleur

 In de volgende tabel ziet u de geretourneerde accentkleur, als een hexadecimale HTML-kleurwaarde, voor elke voorbeeldafbeelding.

| Installatiekopie | Accentkleur |
|-------|--------------|
|![Een persoon die op een bergsteen staat bij een vervalsen](./Images/mountain_vista.png) | #BB6D10 |
|![Een witte bloem met een groene achtergrond](./Images/flower.png) | #C6A205 |
|![Een train die wordt uitgevoerd via een station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Voorbeelden van & van zwart-witdetectie

In de volgende tabel Computer Vision de zwart-wit-evaluatie in de voorbeeldafbeeldingen.

| Installatiekopie | Zwart & wit? |
|-------|----------------|
|![Een zwart-witfoto van gebouwen in](./Images/bw_buildings.png) | true |
|![Een blauw huis en de voorste erf](./Images/house_yard.png) | onjuist |

## <a name="use-the-api"></a>De API gebruiken

De functie voor detectie van kleurenschema's maakt deel uit [van Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Color` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"color"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

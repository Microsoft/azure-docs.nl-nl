---
title: Detectie van kleuren schema-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van het kleuren schema in afbeeldingen met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e7774d2cd100931f92ff80066ebea4463c6f65c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532649"
---
# <a name="detect-color-schemes-in-images"></a>Kleuren schema's in afbeeldingen detecteren

Computer Vision analyseert de kleuren in een afbeelding om drie verschillende kenmerken te bieden: de dominante voorgrond kleur, de dominante achtergrond kleur en de set dominante kleuren voor de afbeelding als geheel. De geretourneerde kleuren behoren tot de set: zwart, blauw, bruin, grijs, groen, oranje, roze, paars, rood, groen blauw, wit en geel. 

Computer Vision haalt ook een accent kleur op die de meest heldere kleur in de afbeelding vertegenwoordigt, op basis van een combi natie van dominante kleuren en verzadiging. De accent kleur wordt geretourneerd als een hexadecimale HTML-kleur code. 

Computer Vision retourneert ook een Booleaanse waarde die aangeeft of een afbeelding in zwart-wit is.

## <a name="color-scheme-detection-examples"></a>Voor beelden van kleuren schema's detectie

In het volgende voor beeld ziet u de JSON-reactie die wordt geretourneerd door Computer Vision bij het detecteren van het kleuren schema van de voorbeeld afbeelding. In dit geval is de afbeelding niet zwart en wit, maar de dominante voor-en achtergrond kleuren zijn zwart en de dominante kleuren voor de afbeelding als geheel zijn zwart en wit.

![Buiten bergen in Zons ondergang met de silhouet van een persoon](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Voor beelden van dominante kleuren

In de volgende tabel ziet u de resulterende voor-, achtergrond-en afbeeldings kleuren voor elke voorbeeld afbeelding.

| Installatiekopie | Dominante kleuren |
|-------|-----------------|
|![Een witte bloem met een groene achtergrond](./Images/flower.png)| Voor grond: zwart<br/>Achtergrond: wit<br/>Kleuren: zwart, wit, groen|
![Een trein die via een station wordt uitgevoerd](./Images/train_station.png) | Voor grond: zwart<br/>Achtergrond: zwart<br/>Kleuren: zwart |

### <a name="accent-color-examples"></a>Accent kleur-voor beelden

 In de volgende tabel wordt de geretourneerde accent kleur weer gegeven als een hexadecimale HTML-kleur waarde voor elke voorbeeld afbeelding.

| Installatiekopie | Accentkleur |
|-------|--------------|
|![Een persoon in een Zons ondergang van een Mountain Rock](./Images/mountain_vista.png) | #BB6D10 |
|![Een witte bloem met een groene achtergrond](./Images/flower.png) | #C6A205 |
|![Een trein die via een station wordt uitgevoerd](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Voor beelden van zwarte & wit-detectie

In de volgende tabel ziet u de zwarte en witte evaluatie van Computer Vision in de voorbeeld afbeeldingen.

| Installatiekopie | Zwart & wit? |
|-------|----------------|
|![Een zwarte en witte afbeelding van gebouwen in Manhattan](./Images/bw_buildings.png) | true |
|![Een blauw huis en de voor tuin](./Images/house_yard.png) | onjuist |

## <a name="use-the-api"></a>De API gebruiken

De functie voor het detecteren van het kleuren schema maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Color` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"color"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

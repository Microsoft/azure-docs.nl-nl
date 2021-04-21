---
title: Objectdetectie - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de objectdetectiefunctie van de Computer Vision API - gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a705a4134ec22d1cb14406cab4491f2af9177b48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767993"
---
# <a name="detect-common-objects-in-images"></a>Algemene objecten in afbeeldingen detecteren

Objectdetectie is vergelijkbaar met [taggen,](concept-tagging-images.md)maar de API retourneert de coördinaten van het begrenzeggingsvak (in pixels) voor elk gevonden object. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U kunt deze functionaliteit gebruiken om de relaties tussen de objecten in een afbeelding te verwerken. U kunt hiermee ook bepalen of er meerdere exemplaren van dezelfde tag in een afbeelding zijn.

De Detect-API past tags toe op basis van de objecten of levende dingen die in de afbeelding zijn geïdentificeerd. Er is momenteel geen formele relatie tussen de taxonomie voor taggen en de taxonomie voor objectdetectie. Op conceptueel niveau vindt de Detect-API alleen objecten en levende dingen, terwijl de Tag-API ook contextuele termen zoals 'indoor' kan bevatten, die niet kunnen worden gelokaliseerd met begrenzendvakken.

## <a name="object-detection-example"></a>Voorbeeld van objectdetectie

Het volgende JSON-antwoord illustreert wat Computer Vision retourneert bij het detecteren van objecten in de voorbeeldafbeelding.

![Een vrouw die een Microsoft Surface-apparaat in een keuken gebruikt](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Beperkingen

Het is belangrijk om rekening te houden met de beperkingen van objectdetectie, zodat u de effecten van fout-negatieven (gemiste objecten) en beperkte details kunt voorkomen of beperken.

* Objecten worden doorgaans niet gedetecteerd als ze klein zijn (minder dan 5% van de afbeelding).
* Objecten worden doorgaans niet gedetecteerd als ze dicht bij elkaar zijn gerangschikt (bijvoorbeeld een stapel borden).
* Objecten worden niet gedifferentieerd op basis van merk- of productnamen (bijvoorbeeld verschillende soorten ijsjes op een winkelschap). U kunt echter merkinformatie verkrijgen uit een afbeelding met behulp van de [functie Merkdetectie.](concept-brand-detection.md)

## <a name="use-the-api"></a>De API gebruiken

De functie objectdetectie maakt deel uit van [de Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Objects` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"objects"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

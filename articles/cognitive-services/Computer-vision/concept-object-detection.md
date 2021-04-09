---
title: Object detectie-Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor object detectie van de Computer Vision-API-gebruik en-limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96533839"
---
# <a name="detect-common-objects-in-images"></a>Algemene objecten detecteren in installatie kopieën

Object detectie is vergelijkbaar met [labelen](concept-tagging-images.md), maar de API retourneert de coördinaten van het selectie kader (in pixels) voor elk gevonden object. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U kunt deze functie gebruiken om de relaties tussen de objecten in een installatie kopie te verwerken. U kunt hiermee ook bepalen of er meerdere exemplaren van dezelfde tag in een afbeelding zijn.

De detectie-API past tags toe op basis van de objecten of de levens punten die in de installatie kopie zijn geïdentificeerd. Er is momenteel geen formele relatie tussen de labels taxonomie en de object detectie-taxonomie. Op een conceptueel niveau zoekt de detectie-API alleen objecten en levende dingen, terwijl de tag-API ook contextuele termen zoals ' binnen ' kan bevatten, die niet kan worden gelokaliseerd met omsluitende vakken.

## <a name="object-detection-example"></a>Voor beeld van object detectie

In het volgende JSON-antwoord ziet u wat Computer Vision als resultaat geeft bij het detecteren van objecten in de voorbeeld afbeelding.

![Een vrouw die gebruikmaakt van een micro soft Surface-apparaat in een keuken](./Images/windows-kitchen.jpg)

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

Het is belang rijk dat u de beperkingen van object detectie noteert, zodat u de effecten van valse negatieven (gemiste objecten) en beperkte Details kunt voor komen of beperken.

* Objecten worden over het algemeen niet gedetecteerd als ze klein zijn (minder dan 5% van de afbeelding).
* Objecten worden over het algemeen niet gedetecteerd als ze samen worden gerangschikt (bijvoorbeeld een stapel platen).
* Objecten worden niet onderscheiden door merk-of product namen (bijvoorbeeld verschillende soorten fris drank op een Store-plank). U kunt echter merk gegevens ophalen van een installatie kopie met behulp van de [merk detectie](concept-brand-detection.md) functie.

## <a name="use-the-api"></a>De API gebruiken

De functie object detectie maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Objects` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"objects"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

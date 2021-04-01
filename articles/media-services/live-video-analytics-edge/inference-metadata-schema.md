---
title: Deductiemetagegevensschema - Azure
description: In dit artikel leert u over het deductiemetagegevensschema.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019491"
---
# <a name="inference-metadata-schema"></a>Schema voor interferentie van metagegevens 

Elk Deductie-object, of het nu een HTTP-contract of een gRPC-contract gebruikt, volgt het objectmodel dat in dit onderwerp wordt beschreven.

## <a name="object-model"></a>Objectmodel

![Objectmodel](./media/inference-metadata-schema/object-model.png)
 
|Typedefinitie|Beschrijving|
|---|---|
|Label|Tag of label die aan het resultaat is gekoppeld. Naast de tag krijgt u zelfs de betrouwbaarheidswaarde die aan de tag is gekoppeld.|
|Kenmerk|Extra kenmerken die aan het resultaat zijn gekoppeld. U kunt nieuwe kenmerken toevoegen die u samen met de betrouwbaarheidswaarde uit de deductie-engine ophaalt.|
|Kenmerkenlijst|Lijst met optionele kenmerken.|
|Rechthoek|Rechthoekige regio relatief aan de afbeelding in de linkerbovenhoek. De vereiste eigenschappen zijn ‘lengte’, ‘breedte’, ‘hoogte’ en ‘afstand van de bovenrand tot de oorsprong’.|
|Classificatie|Label van classificatie die vaak voor het hele voorbeeld geldt. Met behulp van een ‘tag’ kunt u het resultaat classificeren.|
|Entiteit|Entiteit die in het voorbeeld is gedetecteerd of geïdentificeerd. Wanneer de deductie-engine een entiteit detecteert, krijgt deze een ‘tag’, extra kenmerken die zijn gededuceerd, en worden de coördinaten van een rechthoekig kader rondom de gevonden entiteit geretourneerd.|
|Gebeurtenis|Gebeurtenis die in het voorbeeld is gedetecteerd. Wanneer een gebeurtenis in het voorbeeld wordt gedetecteerd, worden de naam van de gebeurtenis en de gebeurtenisspecifieke eigenschappen geretourneerd.|
|Beweging|Beweging die in het voorbeeld is gedetecteerd. Wanneer beweging in het voorbeeld wordt gedetecteerd, worden de coördinaten van een rechthoekig kader waar de beweging is gedetecteerd, geretourneerd.|
|Tekst|Tekst die aan het voorbeeld is gekoppeld, samen met de start- en eindtijdstempel van de tekst, wordt geretourneerd.|
|Anders|Retourneert andere algemene nettoladinginformatie.|

Het onderstaande voorbeeld bevat één gebeurtenis met een aantal ondersteunde deductietypen:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Volgende stappen

- [gRPC-gegevenscontract](./grpc-extension-protocol.md)
- [HTTP-gegevenscontract](./http-extension-protocol.md)
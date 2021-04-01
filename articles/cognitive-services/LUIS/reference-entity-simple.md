---
title: Type eenvoudige entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Een eenvoudige entiteit beschrijft één concept van de machine learning-context. Voeg een woordgroepen lijst toe wanneer u een eenvoudige entiteit gebruikt om de resultaten te verbeteren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: 384d3df2de551e7c79f13a0fe47ffb26c7825f1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91539282"
---
# <a name="simple-entity"></a>Eenvoudige entiteit

Een eenvoudige entiteit is een generieke entiteit die één concept beschrijft en wordt geleerd van de computer Learning-context. Omdat eenvoudige entiteiten doorgaans namen zijn zoals bedrijfs namen, product namen of andere categorieën namen, voegt u een [woordgroepen lijst](luis-concept-feature.md) toe wanneer u een eenvoudige entiteit gebruikt om het signaal van de gebruikte namen te verhogen.

**De entiteit is goed geschikt wanneer:**

* De gegevens zijn niet consistent opgemaakt, maar aangeven hetzelfde.

![eenvoudige entiteit](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Voor beeld JSON

`Bob Jones wants 3 meatball pho`

In de vorige utterance `Bob Jones` is het label een eenvoudige `Customer` entiteit.

De gegevens die door het eind punt worden geretourneerd, bevatten de naam van de entiteit, de gedetecteerde tekst van de utterance, de locatie van de gedetecteerde tekst en de Score:

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Dit is de JSON als deze `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Gegevens object|Entiteitsnaam|Waarde|
|--|--|--|
|Eenvoudige entiteit|`Customer`|`bob jones`|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Syntaxis van het patroon leren](reference-pattern-syntax.md)
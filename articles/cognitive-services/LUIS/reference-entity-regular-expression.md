---
title: Entiteits type van reguliere expressie-LUIS
description: Een reguliere expressie is het meest geschikt voor onbewerkte utterance-tekst. Case wordt genegeerd en de cultuur variant wordt genegeerd.  Reguliere expressie-overeenkomsten worden toegepast na wijzigingen in de spelling controle op het teken niveau, niet op het token niveau.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025222"
---
# <a name="regular-expression-entity"></a>Een entiteit in de vorm van een reguliere expressie

Een reguliere expressie-entiteit extraheert een entiteit op basis van een reguliere expressie patroon dat u opgeeft.

Een reguliere expressie is het meest geschikt voor onbewerkte utterance-tekst. Case wordt genegeerd en de cultuur variant wordt genegeerd.  Reguliere expressie-overeenkomsten worden toegepast na wijzigingen in de spelling controle op het teken niveau, niet op het token niveau. Als de reguliere expressie te complex is, zoals het gebruik van een groot aantal haken, kunt u de expressie niet toevoegen aan het model. Maakt gebruik van een deel, maar niet alle [.net regex](/dotnet/standard/base-types/regular-expressions) -bibliotheek.

**De entiteit is goed geschikt wanneer:**

* De gegevens zijn consistent opgemaakt met elke variatie die ook consistent is.
* De reguliere expressie heeft niet meer dan 2 geneste niveaus nodig.

![Een entiteit in de vorm van een reguliere expressie](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Gebruiks overwegingen

Reguliere expressies kunnen overeenkomen met meer dan verwacht. Een voor beeld hiervan is een numerieke woord overeenkomst zoals `one` en `two` . Een voor beeld is de volgende regex die overeenkomt met het nummer `one` en andere getallen:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Deze regex-expressie komt ook overeen met een wille keurig woord dat met deze getallen eindigt, zoals `phone` . Als u problemen wilt oplossen, moet u ervoor zorgen dat de regex-overeenkomsten rekening houden met woord grenzen. De regex voor het gebruik van woord grenzen voor dit voor beeld wordt gebruikt in de volgende regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Voor beeld JSON

Als de `kb[0-9]{6}` definitie van de reguliere expressie-entiteit wordt gebruikt, is de volgende JSON-reactie een voor beeld van een utterance met de geretourneerde reguliere expressie-entiteiten voor de query:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)


Dit is de JSON als deze `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Dit is de JSON als deze `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over entiteiten:

* [Concepten](luis-concept-entity-types.md)
* [Het maken van](luis-how-to-add-entities.md)
---
title: Entiteits type van lijst-LUIS
description: Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor lijst entiteiten. Gebruik de functie aanbevolen om suggesties voor nieuwe woorden op basis van de huidige lijst te bekijken.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608269"
---
# <a name="list-entity"></a>Lijstentiteit

Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor lijst entiteiten. Gebruik de functie **Aanbevolen** om suggesties voor nieuwe woorden op basis van de huidige lijst te bekijken. Als er meer dan één lijst entiteit met dezelfde waarde is, wordt elke entiteit geretourneerd in de eindpunt query.

Een lijst entiteit is niet door de machine geleerd. Het is een exacte tekst overeenkomst. LUIS markeert elke overeenkomst met een item in een lijst als een entiteit in het antwoord.

**De entiteit is goed geschikt wanneer de tekst gegevens:**

* Zijn een bekende set.
* Verandert niet vaak. Als u de lijst vaak wilt wijzigen of de lijst zelf wilt uitbreiden, is een eenvoudige entiteit met een woordgroepen lijst een betere keuze.
* De set maximale [begrenzingen](luis-limits.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een hoofdletter gevoelige overeenkomst met een synoniem of canonieke naam. LUIS gebruikt de lijst niet verder dan de overeenkomst. Fuzzy matching, ontleding, meervouden en andere variaties worden niet opgelost met een lijst entiteit. Hiervoor kunt u overwegen een [patroon](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

![entiteit weer geven](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Voor beeld. json om in lijst entiteit te importeren

  U kunt waarden importeren in een bestaande lijst entiteit met behulp van de volgende JSON-indeling:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Stel dat de app een lijst heeft met de naam `Cities` , waarmee u namen van steden kunt variëren, waaronder de plaats van de lucht haven (zeekaas), luchthaven code (zee), post code (98101) en telefoon netnummer (206).

|Lijst item|Item synoniemen|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

In de vorige utterance wordt het woord `paris` toegewezen aan het item Parijs als onderdeel van de `Cities` entiteit lijst. De lijst entiteit komt overeen met de genormaliseerde naam van het item en de synoniemen van het item.

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Dit is de JSON als deze `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Dit is de JSON als deze `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Entiteit weer geven|`Cities`|`paris`|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over entiteiten:

* [Concepten](luis-concept-entity-types.md)
* [Het maken van](luis-how-to-add-entities.md)

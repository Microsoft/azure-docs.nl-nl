---
title: Percentage vooraf samengestelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat percentage vooraf opgebouwde entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541693"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentage vooraf samengestelde entiteit voor een LUIS-app
Percentage nummers kunnen worden weer gegeven als breuken, `3 1/2` of als percentage `2%` . Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een percentage toe te voegen aan de toepassings intentie. Het percentage entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typen percentage
Percentage wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) github

## <a name="resolution-for-prebuilt-percentage-entity"></a>Oplossing voor vooraf samengestelde percentage entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld ziet u de resolutie van de **ingebouwde percent** -entiteit.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [rang telwoord](luis-reference-prebuilt-ordinal.md), het [aantal](luis-reference-prebuilt-number.md)en de [Tempe ratuur](luis-reference-prebuilt-temperature.md) .

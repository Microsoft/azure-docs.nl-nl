---
title: Vooraf gemaakte telefoon nummers-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgebouwde telefoon nummer entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 598ecaddbab3b70297a460521c7ec3386b390a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535386"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Op telefoon nummer vooraf gebouwde entiteit voor een LUIS-app
De `phonenumber` entiteit extraheert diverse telefoon nummers, inclusief land code. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen aan de toepassing toe te voegen. De `phonenumber` entiteit wordt alleen ondersteund in `en-us` cultuur.

## <a name="types-of-a-phone-number"></a>Typen van een telefoon nummer
`Phonenumber` wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) github

## <a name="resolution-for-this-prebuilt-entity"></a>Oplossing voor deze vooraf gebouwde entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

In het volgende voor beeld ziet u de oplossing van de **ingebouwde entiteit. phoneNumber** .

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de eenheden [percentage](luis-reference-prebuilt-percentage.md), [aantal](luis-reference-prebuilt-number.md)en [Tempe ratuur](luis-reference-prebuilt-temperature.md) .

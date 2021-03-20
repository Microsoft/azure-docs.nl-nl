---
title: URL-preconstrueerde entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat URL-vooraf opgebouwde entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535250"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL preconstrueerde entiteit voor een LUIS-app
URL-entiteit extraheert Url's met domein namen of IP-adressen. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met Url's aan de toepassing toe te voegen. URL-entiteit wordt alleen ondersteund in `en-us` cultuur.

## <a name="types-of-urls"></a>Typen Url's
URL wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) github

## <a name="resolution-for-prebuilt-url-entity"></a>Oplossing voor preingebouwde URL-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

In het volgende voor beeld ziet u de oplossing van het https://www.luis.ai is een fantastische voor beeld van de cognitieve services van kunst matige intelligentie

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [rang telwoord](luis-reference-prebuilt-ordinal.md), het [aantal](luis-reference-prebuilt-number.md)en de [Tempe ratuur](luis-reference-prebuilt-temperature.md) .

---
title: Naslag informatie voor LUIS-vooraf gemaakte entiteiten
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte e-mail gegevens van de entiteit in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 806357670bead54658f0b501ca20473293275d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91533363"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail vooraf gebouwde entiteit voor een LUIS-app
E-mail extractie bevat het volledige e-mail adres van een utterance. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een e-mail bericht aan de toepassings intentie toe te voegen. E-mail entiteit wordt `en-us` alleen in cultuur ondersteund.

## <a name="resolution-for-prebuilt-email"></a>Oplossing voor vooraf gegenereerde e-mail

De volgende entiteits objecten worden geretourneerd voor de query:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
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

In het volgende voor beeld ziet u de oplossing van de **Builtin.email** -entiteit.

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [aantal](luis-reference-prebuilt-number.md), het [rang telwoord](luis-reference-prebuilt-ordinal.md)en het [percentage](luis-reference-prebuilt-percentage.md).

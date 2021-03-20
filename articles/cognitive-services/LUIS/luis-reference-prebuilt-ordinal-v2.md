---
title: Rang telwoord v2 vooraf gebouwde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte nummer v2-entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541931"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Rang telwoord v2 vooraf gebouwde entiteit voor een LUIS-app
Ordinal v2-nummer breidt [rang telwoord](luis-reference-prebuilt-ordinal.md) uit om relatieve verwijzingen te bieden, zoals `next` , `last` , en `previous` . Deze worden niet geëxtraheerd met behulp van de vooraf gebouwde ordinale entiteit.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Oplossing voor vooraf ontwikkelde rang telwoord v2-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

In het volgende voor beeld ziet u de oplossing van de **ingebouwde entiteit. ordinalV2** .

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage](luis-reference-prebuilt-percentage.md), het [telefoon nummer](luis-reference-prebuilt-phonenumber.md)en de [temperatuur](luis-reference-prebuilt-temperature.md) entiteiten.

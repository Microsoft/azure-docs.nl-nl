---
title: De vooraf samengestelde entiteit van de persoon-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgebouwde entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535420"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>De vooraf samengestelde entiteit van de persoon voor een LUIS-app
De entiteit van de vooraf gedefinieerde persoons naam detecteert namen van personen. Omdat deze entiteit al is getraind, hoeft u geen voorbeeld uitingen toe te voegen die zijn toegevoegd aan de toepassings intentie. entiteit voor persoons wordt ondersteund in de Engelse en Chinese [cult uren](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Omzetting voor entiteit persoonnaam

De volgende entiteits objecten worden geretourneerd voor de query:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)


De volgende JSON is met de `verbose` para meter ingesteld op `false` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is met de `verbose` para meter ingesteld op `true` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld wordt de oplossing van de entiteit **Builtin. persoonnaam** weer gegeven.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de entiteiten [e-mail](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md)en [rang telwoord](luis-reference-prebuilt-ordinal.md) .

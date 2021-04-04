---
title: Patroon. elk entiteits type-LUIS
titleSuffix: Azure Cognitive Services
description: Patroon. any is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloon van een patroon utterance om te markeren waar de entiteit begint en eindigt.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91542203"
---
# <a name="patternany-entity"></a>Pattern.any-entiteit

Patroon. any is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloon van een patroon utterance om te markeren waar de entiteit begint en eindigt.  

Patroon. alle entiteiten moeten worden gemarkeerd in de voor beelden van [patroon](luis-how-to-model-intent-pattern.md) sjablonen, niet de intentie gebruikers voorbeelden.

**De entiteit is goed geschikt wanneer:**

* Het einde van de entiteit kan worden verward met de resterende tekst van de utterance.

## <a name="usage"></a>Gebruik

Aan de hand van een client toepassing die zoekt naar boeken op basis van de titel, het patroon. alle extraheert de volledige titel. Een sjabloon utterance het gebruik van een patroon. elke voor deze zoek opdracht in het boek is `Was {BookTitle} written by an American this year[?]` .

In de volgende tabel heeft elke rij twee versies van de utterance. De bovenste utterance is hoe LUIS aanvankelijk de utterance ziet. Het is niet duidelijk waar de titel van het boek begint en eindigt. De onderste utterance maakt gebruik van een patroon. een wille keurige entiteit om het begin en het einde van de entiteit te markeren.

|Utterance met entiteit in vet|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **de man die de vrouw heeft geduurd voor een hoed en andere klinische verschijnselen die zijn** geschreven met een Amerikaans dit jaar?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Is de **halve slaap stand in kikker Pajamas** geschreven door een Amerikaans dit jaar?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Was **de specifieke verdriet van citroen taart: een nieuwe** geschreven door een Amerikaans, dit jaar?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Is **er een Wocket in mijn Pocket!** geschreven door een Amerikaans dit jaar?|
||



## <a name="example-json"></a>Voor beeld JSON

Laten we nu eens naar deze query kijken:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Met de naam van het Inge sloten formulier dat moet worden geëxtraheerd als een patroon. alle:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Dit is de JSON als deze `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Dit is de JSON als deze `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

In deze [zelf studie](luis-tutorial-pattern.md)gebruikt u het **patroon. elke** entiteit voor het extra heren van gegevens uit uitingen waarbij de uitingen goed zijn opgemaakt en waarbij het einde van de gegevens gemakkelijk kan worden verward met de resterende woorden van de utterance.

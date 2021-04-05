---
title: Gegevens wijziging-LUIS
description: Meer informatie over hoe gegevens kunnen worden gewijzigd voordat voor spellingen in Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026007"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Utterance-gegevens wijzigen vóór of tijdens de voor spelling
LUIS biedt manieren om de utterance voor of tijdens de voor spelling te bewerken. Dit zijn onder andere het corrigeren van de [Spelling](luis-tutorial-bing-spellcheck.md)en het oplossen van problemen met de tijd zone voor vooraf ontwikkelde [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Spel fouten in utterance corrigeren


### <a name="v3-runtime"></a>V3-runtime

Tekst voorverwerken voor spelling correcties voordat u de utterance naar LUIS verzendt. Gebruik voor beeld-uitingen met de juiste spelling om ervoor te zorgen dat u de juiste voor spellingen krijgt.

Gebruik [Bing spellingcontrole](../bing-spell-check/overview.md) om tekst te corrigeren voordat u deze naar Luis verzendt.

### <a name="prior-to-v3-runtime"></a>Voorafgaand aan de V3-runtime

LUIS maakt gebruik van [Bing spellingcontrole-API V7](../Bing-Spell-Check/overview.md) om spel fouten in de utterance te corrigeren. LUIS moet voldoen aan de sleutel die aan deze service is gekoppeld. Maak de sleutel en voeg vervolgens de sleutel toe als een query reeks parameter op het [eind punt](https://go.microsoft.com/fwlink/?linkid=2092356).

Voor het eind punt zijn twee para meters vereist voor het werken met spelling correcties:

|Param|Waarde|
|--|--|
|`spellCheck`|booleaans|
|`bing-spell-check-subscription-key`|[Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -eindpunt sleutel|

Wanneer [Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) een fout detecteert, worden de oorspronkelijke utterance en de gecorrigeerde utterance met de voor spellingen van het eind punt geretourneerd.

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lijst met toegestane woorden
De Bing spell check-API die in LUIS wordt gebruikt, biedt geen ondersteuning voor een lijst met woorden die moeten worden genegeerd tijdens de wijziging van de spelling controle. Als u een lijst met woorden of acroniemen wilt toestaan, verwerkt u de utterance in de client toepassing voordat u de utterance naar LUIS verzendt voor het voors pellen van de intentie.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Tijd zone van vooraf gemaakte datetimeV2-entiteit wijzigen
Wanneer een LUIS-app gebruikmaakt van de vooraf gemaakte [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteit, kan een datum/tijd-waarde worden geretourneerd in het Voorspellings antwoord. De tijd zone van de aanvraag wordt gebruikt om te bepalen of de juiste datum/tijd moet worden geretourneerd. Als de aanvraag afkomstig is van een bot of een andere gecentraliseerde toepassing voordat LUIS wordt opgehaald, moet u de LUIS van de tijd zone corrigeren.

### <a name="v3-prediction-api-to-alter-timezone"></a>V3 Voorspellings-API voor het wijzigen van de tijd zone

In v3 bepaalt de tijds `datetimeReference` interval-offset. Meer informatie over de voor [spellingen van v3](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 Voorspellings-API voor het wijzigen van de tijd zone
De tijd zone wordt gecorrigeerd door de tijd zone van de gebruiker toe te voegen aan het eind punt met de `timezoneOffset` para meter op basis van de API-versie. De waarde van de para meter moet het positieve of negatieve getal zijn, in minuten, voor het wijzigen van de tijd.

#### <a name="v2-prediction-daylight-savings-example"></a>Voor beeld van een zomer-en winter tijd voor v2
Als u de geretourneerde vooraf gemaakte datetimeV2 wilt aanpassen aan zomer tijd, moet u de query reeks-para meter met een +/-waarde in minuten gebruiken voor de [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356) query.

60 minuten toevoegen:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 minuten verwijderen:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2-voor spelling C#-code bepaalt de juiste waarde van para meter

De volgende C#-code maakt gebruik van de methode [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) van de klasse [time zone info](/dotnet/api/system.timezoneinfo) om de juiste offset waarde te bepalen op basis van de systeem tijd:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Spel fouten corrigeren met deze zelf studie](luis-tutorial-bing-spellcheck.md)
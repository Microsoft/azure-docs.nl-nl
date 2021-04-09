---
title: Gegevens extractie-LUIS
description: Gegevens uit utterance-tekst ophalen met intents en entiteiten. Meer informatie over wat voor soort gegevens kan worden geëxtraheerd uit Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025990"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Gegevens uit utterance-tekst ophalen met intents en entiteiten
LUIS biedt u de mogelijkheid om gegevens op te halen uit de uitingen van de natuurlijke taal van een gebruiker. De gegevens worden geëxtraheerd op een manier die kan worden gebruikt door een programma, toepassing of chat-bot om actie te ondernemen. In de volgende secties vindt u informatie over de gegevens die worden geretourneerd door intenties en entiteiten met voor beelden van JSON.

De hardste gegevens die u wilt extra heren, zijn de gegevens van de machine learning, omdat deze niet exact overeenkomen met de tekst. Het ophalen van gegevens van de machine learning- [entiteiten](luis-concept-entity-types.md) moet deel uitmaken van de [ontwerp cyclus](luis-concept-app-iteration.md) totdat u zeker weet dat u de verwachte gegevens ontvangt.

## <a name="data-location-and-key-usage"></a>Gegevens locatie en sleutel gebruik
LUIS extraheert gegevens van de utterance van de gebruiker op het gepubliceerde [eind punt](luis-glossary.md#endpoint). De **HTTPS-aanvraag** (post of Get) bevat de utterance en enkele optionele configuraties zoals staging of productie omgevingen.

**V2-aanvraag voor Voorspellings eindpunt**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3-Voorspellings eindpunt aanvraag**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

De `appID` is beschikbaar op de pagina **instellingen** van uw Luis-app, evenals een deel van de URL (na `/apps/` ) wanneer u die Luis-app bewerkt. De `subscription-key` is de eindpunt sleutel die wordt gebruikt voor het uitvoeren van query's op uw app. Hoewel u uw gratis versie van authoring/starter kunt gebruiken terwijl u LUIS Learning, is het belang rijk dat u de eindpunt sleutel wijzigt in een sleutel die het [verwachte Luis gebruik](luis-limits.md#key-limits)ondersteunt. De `timezoneOffset` eenheid is minuten.

Het **https-antwoord** bevat alle intentie-en entiteits gegevens Luis kunnen bepalen op basis van het huidige gepubliceerde model van het faserings-of productie-eind punt. De eind punt-URL is te vinden op de [Luis](luis-reference-regions.md) -website, in de sectie **beheren** op de pagina **sleutels en eind punten** .

## <a name="data-from-intents"></a>Gegevens uit de intenties
De primaire gegevens zijn de naam van het bovenste score **doel**. Het eindpunt antwoord is:

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

|Gegevens object|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|
|Intentie|Tekenreeks|topScoringIntent. intentie|"GetStoreInfo"|

Als uw chatbot-of LUIS-app een beslissing neemt op basis van meer dan één intentie Score, kunt u alle uitstaande scores voor intenties retour neren.


#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Stel de query string-para meter in `verbose=true` . Het eindpunt antwoord is:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Stel de query string-para meter in `show-all-intents=true` . Het eindpunt antwoord is:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

De intenties worden besteld van hoogste naar laagste score.

|Gegevens object|Gegevenstype|Locatie van gegevens|Waarde|Score|
|--|--|--|--|:--|
|Intentie|Tekenreeks|intents [0]. intentie|"GetStoreInfo"|0,984749258|
|Intentie|Tekenreeks|intentie [1]. intentie|Geen|0,0168218873|

Als u vooraf gemaakte domeinen toevoegt, geeft de naam van het doel het domein aan, zoals of, evenals `Utilties` `Communication` de bedoeling:

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

|Domain|Gegevens object|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|--|
|Hulpprogramma's|Intentie|Tekenreeks|intents [0]. intentie|"<b>Hulpprogram ma's</b>. ShowNext"|
|Communicatie|Intentie|Tekenreeks|intentie [1]. intentie|<b>Communicatie</b>. StartOver"|
||Intentie|Tekenreeks|intentie [2]. intentie|Geen|


## <a name="data-from-entities"></a>Gegevens van entiteiten
De meeste chat bots en toepassingen hebben meer dan de naam van het doel nodig. Deze aanvullende, optionele gegevens zijn afkomstig van entiteiten die zijn gedetecteerd in de utterance. Elk type entiteit retourneert verschillende informatie over de overeenkomst.

Eén woord of zinsdeel in een utterance kan overeenkomen met meer dan één entiteit. In dat geval wordt elke overeenkomende entiteit geretourneerd met de bijbehorende Score.

Alle entiteiten worden geretourneerd in de matrix **entiteiten** van de reactie van het eind punt

## <a name="tokenized-entity-returned"></a>Tokend geretourneerde entiteit

Controleer de [token ondersteuning](luis-language-support.md#tokenization) in Luis.


## <a name="prebuilt-entity-data"></a>Vooraf samengestelde entiteits gegevens
[Vooraf gemaakte](luis-concept-entity-types.md) entiteiten worden gedetecteerd op basis van een reguliere expressie die overeenkomt met het open-source [recognizers-tekst](https://github.com/Microsoft/Recognizers-Text) project. Vooraf gemaakte entiteiten worden geretourneerd in de matrix entities en gebruiken de type naam voorafgegaan door `builtin::` .

## <a name="list-entity-data"></a>Entiteits gegevens weer geven

[Lijst entiteiten](reference-entity-list.md) vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor lijst entiteiten. Gebruik de functie **Aanbevolen** om suggesties voor nieuwe woorden op basis van de huidige lijst te bekijken. Als er meer dan één lijst entiteit met dezelfde waarde is, wordt elke entiteit geretourneerd in de eindpunt query.

## <a name="regular-expression-entity-data"></a>Entiteits gegevens voor reguliere expressies

Een [reguliere expressie-entiteit](reference-entity-regular-expression.md) extraheert een entiteit op basis van een reguliere expressie die u opgeeft.

## <a name="extracting-names"></a>Namen extra heren
Het ophalen van namen van een utterance is moeilijk omdat een naam bijna elke combi natie van letters en woorden kan zijn. Afhankelijk van het type naam dat u uitpakt, hebt u verschillende opties. De volgende suggesties zijn geen regels, maar ook meer richt lijnen.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Preconstrueerde persoons-en GeographyV2-entiteiten toevoegen

De entiteiten [persoons](luis-reference-prebuilt-person.md) -en [GeographyV2](luis-reference-prebuilt-geographyV2.md) zijn beschikbaar in sommige [taal culturen](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Namen van personen

De naam van een persoon kan een geringe indeling hebben, afhankelijk van de taal en de cultuur. Gebruik een vooraf gedefinieerde entiteit **[persoon](luis-reference-prebuilt-person.md)** of een **[eenvoudige entiteit](luis-concept-entity-types.md)** met rollen van de voor-en achternaam.

Als u de eenvoudige entiteit gebruikt, moet u voor beelden opgeven die gebruikmaken van de voor-en achternaam in verschillende delen van de utterance, in uitingen met verschillende lengten en uitingen voor alle intenten, inclusief de geen intentie. [Bekijk](./luis-how-to-review-endpoint-utterances.md) de eind punten uitingen regel matig om namen te labelen die niet correct zijn voor speld.

### <a name="names-of-places"></a>Namen van locaties

Locatie namen worden ingesteld en bekend, zoals steden, regio's, provincies, provincies en landen/regio's. Gebruik de vooraf samengestelde entiteit **[geographyV2](luis-reference-prebuilt-geographyv2.md)** om locatie gegevens te extra heren.

### <a name="new-and-emerging-names"></a>Nieuwe en opkomende namen

Sommige apps moeten nieuwe en opkomende namen kunnen vinden, zoals producten of bedrijven. Deze typen namen zijn het lastigste type gegevens extractie. Begin met een **[eenvoudige entiteit](luis-concept-entity-types.md#simple-entity)** en voeg een [woordgroepen lijst](luis-concept-feature.md)toe. [Bekijk](./luis-how-to-review-endpoint-utterances.md) de eind punten uitingen regel matig om namen te labelen die niet correct zijn voor speld.

## <a name="patternany-entity-data"></a>Patroon. alle entiteits gegevens

[Patroon. any](reference-entity-pattern-any.md) is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloon van een patroon utterance om te markeren waar de entiteit begint en eindigt. De entiteit die in het patroon wordt gebruikt, moet worden gevonden om het patroon te kunnen Toep assen.

## <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentiment-analyse is geconfigureerd tijdens het [publiceren](luis-how-to-publish-app.md#sentiment-analysis), omvat het JSON-antwoord Luis sentiment analyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](../text-analytics/index.yml) -documentatie.

## <a name="key-phrase-extraction-entity-data"></a>Entiteits gegevens voor de extractie van sleutel zinnen
De [sleutel frase extractie-entiteit](luis-reference-prebuilt-keyphrase.md) retourneert sleutel zinnen in de utterance, die door [Text Analytics](../text-analytics/index.yml)worden gegeven.

## <a name="data-matching-multiple-entities"></a>Gegevens die overeenkomen met meerdere entiteiten

LUIS retourneert alle entiteiten die zijn gedetecteerd in de utterance. Als gevolg hiervan moet uw chat-bot mogelijk een beslissing nemen op basis van de resultaten.

## <a name="data-matching-multiple-list-entities"></a>Gegevens die overeenkomen met meerdere lijst entiteiten

Als een woord of woord groep overeenkomt met meer dan één lijst entiteit, retourneert de eindpunt query elke lijst entiteit.

Voor de query `when is the best time to go to red rock?` , en de app heeft het woord `red` in meer dan één lijst, herkent Luis alle entiteiten en retourneert een matrix van entiteiten als onderdeel van het JSON-eindpunt antwoord.

## <a name="next-steps"></a>Volgende stappen

Zie [entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw Luis-app.

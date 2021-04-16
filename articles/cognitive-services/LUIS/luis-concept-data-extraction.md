---
title: Gegevensextractie - LUIS
description: Extraheert gegevens uit utterancetekst met intenties en entiteiten. Ontdek wat voor soort gegevens kunnen worden geëxtraheerd uit Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500138"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Gegevens extraheren uit utterancetekst met intenties en entiteiten
LUIS biedt u de mogelijkheid om informatie op te halen uit uitingen in natuurlijke taal van een gebruiker. De informatie wordt geëxtraheerd op een manier die kan worden gebruikt door een programma, toepassing of chatbot om actie te ondernemen. In de volgende secties leert u welke gegevens worden geretourneerd uit intenties en entiteiten met voorbeelden van JSON.

De lastigste gegevens om te extraheren zijn de machine learning-gegevens, omdat het geen exacte tekstmatch is. Gegevensextractie van de [](luis-concept-entity-types.md) machine learning-entiteiten moet deel uitmaken van de [ontwerpcyclus](luis-concept-app-iteration.md) totdat u zeker weet dat u de gegevens ontvangt die u verwacht.

## <a name="data-location-and-key-usage"></a>Gegevenslocatie en sleutelgebruik
LUIS extraheert gegevens uit de uiting van de gebruiker op het [gepubliceerde eindpunt](luis-glossary.md#endpoint). De **HTTPS-aanvraag** (POST of GET) bevat de utterance en een aantal optionele configuraties, zoals faserings- of productieomgevingen.

**Aanvraag voor V2-voorspellings-eindpunt**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3-voorspellings-eindpuntaanvraag**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

De is beschikbaar op de pagina Instellingen van uw LUIS-app en als onderdeel van de URL (na ) wanneer u `appID` die  `/apps/` LUIS-app bewerkt. De `subscription-key` is de eindpuntsleutel die wordt gebruikt voor het uitvoeren van query's op uw app. Hoewel u tijdens het leren van LUIS uw gratis ontwerp-/startersleutel kunt gebruiken, is het belangrijk om de eindpuntsleutel te wijzigen in een sleutel die uw verwachte [LUIS-gebruik ondersteunt.](luis-limits.md#key-limits) De `timezoneOffset` eenheid is minuten.

Het **HTTPS-antwoord** bevat alle intentie- en entiteitsinformatie die LUIS kan bepalen op basis van het huidige gepubliceerde model van het faserings- of productie-eindpunt. De eindpunt-URL vindt u op de [website van LUIS,](luis-reference-regions.md) in de **sectie Beheren,** op de **pagina Sleutels en** eindpunten.

## <a name="data-from-intents"></a>Gegevens van intenties
De primaire gegevens zijn de naam van de **best scorende intentie.** Het antwoord op het eindpunt is:

#### <a name="v2-prediction-endpoint-response"></a>[Antwoord van V2-voorspellings-eindpunt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Antwoord van V3-voorspellings-eindpunt](#tab/V3)

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

|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|
|Intentie|Tekenreeks|topScoringIntent.intent|"GetStoreInfo"|

Als uw chatbot of LUIS-app een beslissing neemt op basis van meer dan één intentiescore, retourneert u de scores van alle intenties.


#### <a name="v2-prediction-endpoint-response"></a>[Antwoord van V2-voorspellings-eindpunt](#tab/V2)

Stel de queryreeksparameter in, `verbose=true` . Het antwoord op het eindpunt is:

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

#### <a name="v3-prediction-endpoint-response"></a>[Antwoord van V3-voorspellings-eindpunt](#tab/V3)

Stel de parameter querystring in, `show-all-intents=true` . Het antwoord op het eindpunt is:

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

De intenties worden geordend van de hoogste naar de laagste score.

|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|Score|
|--|--|--|--|:--|
|Intentie|Tekenreeks|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intentie|Tekenreeks|intents [1].intent|'Geen'|0.0168218873|

Als u vooraf gebouwde domeinen toevoegt, geeft de naam van de intentie het domein aan, zoals `Utilties` `Communication` of, evenals de intentie:

#### <a name="v2-prediction-endpoint-response"></a>[Antwoord van V2-voorspellings-eindpunt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Antwoord van V3-voorspellings-eindpunt](#tab/V3)

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

|Domain|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|--|
|Hulpprogramma's|Intentie|Tekenreeks|intents[0].intent|"<b>Hulpprogramma's</b>. ShowNext"|
|Communicatie|Intentie|Tekenreeks|intents [1].intent|<b>Communicatie</b>. StartOver'|
||Intentie|Tekenreeks|intents [2].intent|'Geen'|


## <a name="data-from-entities"></a>Gegevens van entiteiten
De meeste chatbots en -toepassingen hebben meer nodig dan de naam van de intentie. Deze aanvullende, optionele gegevens zijn afkomstig van entiteiten die in de utterance zijn ontdekt. Elk type entiteit retourneert verschillende informatie over de overeenkomst.

Eén woord of woordgroep in een utterance kan overeenkomen met meer dan één entiteit. In dat geval wordt elke overeenkomende entiteit geretourneerd met de bijbehorende score.

Alle entiteiten worden geretourneerd in de **entiteiten-matrix** van het antwoord van het eindpunt

## <a name="tokenized-entity-returned"></a>Tokenized entiteit geretourneerd

Controleer de [tokenondersteuning](luis-language-support.md#tokenization) in LUIS.


## <a name="prebuilt-entity-data"></a>Vooraf gebouwde entiteitsgegevens
[Vooraf gebouwde](luis-concept-entity-types.md) entiteiten worden ontdekt op basis van een reguliere expressiematch met behulp van het [opensource-project Recognizers-Text.](https://github.com/Microsoft/Recognizers-Text) Vooraf gebouwde entiteiten worden geretourneerd in de entiteiten-matrix en gebruiken de typenaam met het voorvoegsel `builtin::` .

## <a name="list-entity-data"></a>Entiteitsgegevens opslijst

[Lijstentiteiten](reference-entity-list.md) vertegenwoordigen een vaste, gesloten set gerelateerde woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor lijstentiteiten. Gebruik de **functie** Aanbevelen om suggesties te zien voor nieuwe woorden op basis van de huidige lijst. Als er meer dan één lijstentiteit met dezelfde waarde is, wordt elke entiteit geretourneerd in de eindpuntquery.

## <a name="regular-expression-entity-data"></a>Entiteitsgegevens van reguliere expressie

Een [entiteit in de reguliere expressie](reference-entity-regular-expression.md) extraheert een entiteit op basis van een reguliere expressie die u op geeft.

## <a name="extracting-names"></a>Namen extraheren
Het is lastig om namen op te vragen uit een utterance, omdat een naam vrijwel elke combinatie van letters en woorden kan zijn. Afhankelijk van het type naam dat u uitpakken, hebt u verschillende opties. De volgende suggesties zijn geen regels, maar meer richtlijnen.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Vooraf gebouwde entiteiten PersonName en GeographyV2 toevoegen

[PersonName-](luis-reference-prebuilt-person.md) [en GeographyV2-entiteiten](luis-reference-prebuilt-geographyV2.md) zijn beschikbaar in sommige [taalcultuuren.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Namen van personen

De naam van personen kan enigszins worden opgemaakt, afhankelijk van de taal en cultuur. Gebruik een vooraf gebouwde **[entiteit personName](luis-reference-prebuilt-person.md)** of een **[eenvoudige entiteit](luis-concept-entity-types.md)** met de rollen voor- en achternaam.

Als u de entiteit Simple gebruikt, moet u voorbeelden geven die de voor- en achternaam in verschillende delen van de utterance gebruiken, in utterances met verschillende lengten en utterances voor alle intenties, inclusief de intentie None. [Controleer](./luis-how-to-review-endpoint-utterances.md) regelmatig eindpunt-utterances om namen te labelen die niet correct zijn voorspeld.

### <a name="names-of-places"></a>Namen van plaatsen

Locatienamen zijn ingesteld en bekend, zoals steden, regio's, staten, provincies en landen/regio's. Gebruik de vooraf gebouwde entiteit **[geographyV2 om](luis-reference-prebuilt-geographyv2.md)** locatiegegevens te extraheren.

### <a name="new-and-emerging-names"></a>Nieuwe en opkomende namen

Sommige apps moeten nieuwe en opkomende namen kunnen vinden, zoals producten of bedrijven. Deze typen namen zijn het moeilijkst type gegevensextractie. Begin met een **[entiteit Simple en](luis-concept-entity-types.md)** voeg een [frasenlijst toe.](luis-concept-feature.md) [Controleer](./luis-how-to-review-endpoint-utterances.md) regelmatig eindpunt-utterances om namen te labelen die niet correct zijn voorspeld.

## <a name="patternany-entity-data"></a>Pattern.any-entiteitsgegevens

[Pattern.any](reference-entity-pattern-any.md) is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloonuiting van een patroon om te markeren waar de entiteit begint en eindigt. De entiteit die in het patroon wordt gebruikt, moet worden gevonden om het patroon toe te passen.

## <a name="sentiment-analysis"></a>Sentimentanalyse
Als Sentimentanalyse is geconfigureerd tijdens het [publiceren,](luis-how-to-publish-app.md#sentiment-analysis)bevat het JSON-antwoord van LUIS sentimentanalyse. Meer informatie over sentimentanalyse in de [](../text-analytics/index.yml) Text Analytics documentatie.

## <a name="key-phrase-extraction-entity-data"></a>Entiteitsgegevens voor sleuteltermextractie
De [entiteit sleuteltermextractie](luis-reference-prebuilt-keyphrase.md) retourneert sleuteltermen in de utterance, geleverd door [Text Analytics](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Gegevens die overeenkomen met meerdere entiteiten

LUIS retourneert alle entiteiten die in de utterance zijn ontdekt. Als gevolg hiervan moet uw chatbot mogelijk een beslissing nemen op basis van de resultaten.

## <a name="data-matching-multiple-list-entities"></a>Gegevens die overeenkomen met meerdere lijstentiteiten

Als een woord of woordgroep overeenkomt met meer dan één lijstentiteit, retourneert de eindpuntquery elke List-entiteit.

Voor de query , en de app heeft het woord in meer dan één lijst, herkent LUIS alle entiteiten en retourneert een matrix met entiteiten als onderdeel van het `when is the best time to go to red rock?` `red` JSON-eindpuntreactie.

## <a name="next-steps"></a>Volgende stappen

Zie [Entiteiten toevoegen voor](luis-how-to-add-entities.md) meer informatie over het toevoegen van entiteiten aan uw LUIS-app.

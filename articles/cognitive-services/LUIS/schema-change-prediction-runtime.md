---
title: App uitbreiden tijdens runtime-LUIS
description: Meer informatie over het uitbreiden van een al gepubliceerd Voorspellings eindpunt om nieuwe informatie door te geven.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953018"
---
# <a name="extend-app-at-prediction-runtime"></a>App uitbreiden bij Voorspellings runtime

Het schema van de app (modellen en functies) wordt getraind en gepubliceerd naar het Voorspellings eindpunt. Dit gepubliceerde model wordt gebruikt in de Voorspellings runtime. U kunt nieuwe informatie, samen met de utterance van de gebruiker, door geven aan de Voorspellings runtime om de voor spelling te verg Roten.

Twee wijzigingen in het Voorspellings-runtime schema zijn:
* [Externe entiteiten](#external-entities)
* [Dynamische lijsten](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externe entiteiten

Externe entiteiten bieden uw LUIS-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. Zo kunt u uw eigen afzonderlijke en aangepaste entiteits-extracten gebruiken voordat u query's naar uw Voorspellings eindpunt verzendt. Omdat dit op het query Voorspellings eindpunt wordt gedaan, hoeft u uw model niet opnieuw te trainen en te publiceren.

De client toepassing levert een eigen entiteits extractie door het beheren van entiteits overeenkomsten en het bepalen van de locatie binnen de utterance van die overeenkomende entiteit en vervolgens die informatie met de aanvraag te verzenden.

Externe entiteiten zijn het mechanisme voor het uitbreiden van elk type entiteit, terwijl ze nog steeds worden gebruikt als signalen voor andere modellen.

Dit is handig voor een entiteit met gegevens die alleen beschikbaar zijn voor de runtime van de Voorspellings functie voor query's. Voor beelden van dit type gegevens zijn voortdurend veranderende gegevens of specifieke per gebruiker. U kunt een LUIS-contact persoon uitbreiden met externe informatie uit de lijst met contact personen van een gebruiker.

Externe entiteiten maken deel uit van de V3-ontwerp-API. Meer informatie over het [migreren](luis-migration-api-v3.md) naar deze versie.

### <a name="entity-already-exists-in-app"></a>De entiteit bestaat al in de app

De waarde van `entityName` voor de externe entiteit, door gegeven in de hoofd tekst van het eindpunt verzoek, moet al aanwezig zijn in de getrainde en gepubliceerde app op het moment dat de aanvraag wordt ingediend. Het entiteits type is niet van belang, alle typen worden ondersteund.

### <a name="first-turn-in-conversation"></a>Eerst gesprek inschakelen

Bekijk een eerste utterance in een chat-bot waarbij een gebruiker de volgende onvolledige gegevens invoert:

`Send Hazem a new message`

De aanvraag van de chat-bot naar LUIS kan informatie geven in de hoofd tekst `Hazem` , zodat deze direct overeenkomt met de contact personen van de gebruiker.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Het Voorspellings antwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

### <a name="second-turn-in-conversation"></a>Tweede gesprek inschakelen

De volgende gebruiker utterance in de chat-bot maakt gebruik van een meer vague-term:

`Send him a calendar reminder for the party.`

In deze conversatie wordt het utterance gebruikt `him` als een verwijzing naar `Hazem` . De bot van de conversatie in de hoofd tekst kan worden toegewezen `him` aan de entiteit waarde die is geëxtraheerd uit de eerste utterance, `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Het Voorspellings antwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

### <a name="override-existing-model-predictions"></a>Bestaande model voorspellingen onderdrukken

De `preferExternalEntities` eigenschap Options geeft aan dat als de gebruiker een externe entiteit verzendt die overlapt met een voorspelde entiteit met dezelfde naam, Luis kiest voor de entiteit die is door gegeven of de entiteit die in het model is opgenomen.

Denk bijvoorbeeld aan de query `today I'm free` . LUIS detecteert `today` als een datetimeV2 met het volgende antwoord:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Als de gebruiker de externe entiteit verzendt:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Als `preferExternalEntities` is ingesteld op `false` , retourneert Luis een antwoord alsof de externe entiteit niet is verzonden.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Als `preferExternalEntities` is ingesteld op `true` , Luis retourneert een antwoord met de volgende instellingen:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Oplossing

De _optionele_ `resolution` eigenschap retourneert in het Voorspellings antwoord, zodat u de meta gegevens kunt door geven die aan de externe entiteit zijn gekoppeld. Vervolgens ontvangt u deze in het antwoord.

Het belangrijkste doel is om vooraf gemaakte entiteiten uit te breiden, maar dit is niet beperkt tot dat entiteits type.

De `resolution` eigenschap kan een getal, een teken reeks, een object of een matrix zijn:

* Filiaal
* {"tekst": "waarde"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamische lijsten

Met dynamische lijsten kunt u een bestaande getrainde en gepubliceerde lijst entiteit uitbreiden, al in de LUIS-app.

Gebruik deze functie wanneer de waarden van de lijst entiteit periodiek moeten worden gewijzigd. Met deze functie kunt u een al getrainde en gepubliceerde lijst entiteit uitbreiden:

* Op het moment van de aanvraag voor het uitvoeren van een query Voorspellings eindpunt.
* Voor een enkele aanvraag.

De lijst entiteit kan leeg zijn in de LUIS-app, maar deze moet bestaan. De entiteit lijst in de LUIS-app is niet gewijzigd, maar de Voorspellings mogelijkheid op het eind punt wordt uitgebreid tot twee lijsten met ongeveer 1.000 items.

### <a name="dynamic-list-json-request-body"></a>Hoofd tekst JSON-aanvraag van dynamische lijst

Verzend in de volgende JSON-hoofd tekst om een nieuwe sublijst met synoniemen toe te voegen aan de lijst, en voor te voors pellen dat de lijst entiteit voor de tekst wordt weer geven, `LUIS` met de aanvraag voor de voor `POST` Spelling van query's:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

De Voorspellings reactie omvat die lijst entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

## <a name="next-steps"></a>Volgende stappen

* [Voorspellingsscore](luis-concept-prediction-score.md)
* [API v3-wijzigingen ontwerpen](luis-migration-api-v3.md)

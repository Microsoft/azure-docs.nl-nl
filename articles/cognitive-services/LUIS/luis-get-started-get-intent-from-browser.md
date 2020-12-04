---
title: Query's uitvoeren voor voorspellingen met behulp van een browser - LUIS
description: In dit artikel gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst in een browser.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 6aa681c8ea60cb57ac3e6d143c60cc46bc057719
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436909"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Query's uitvoeren op de voorspellingsruntime met gebruikerstekst

Om te begrijpen wat een LUIS-voorspellingseindpunt retourneert, kunt u het resultaat van een voorspelling in een webbrowser bekijken.

## <a name="prerequisites"></a>Vereisten

Als u een query wilt uitvoeren op een openbare app, hebt u het volgende nodig:

* Resourcegegevens van Language Understanding (LUIS):
    * **De voorspellingssleutel**, die kan worden verkregen via de [LUIS-portal](https://www.luis.ai/). Als u nog geen abonnement hebt om een sleutel te maken, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/cognitive-services).
    * **Subdomein voor voorspellingseindpunt**: het subdomein is ook de **naam** van uw LUIS-resource.
* Een LUIS-app-id: gebruik de openbare IoT-app-id van `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. De gebruikersquery die in de code van de quickstart wordt gebruikt, is specifiek voor die app.

## <a name="use-the-browser-to-see-predictions"></a>De browser gebruiken om voorspellingen te bekijken

1. Open een webbrowser.
1. Gebruik de volledige URL's hieronder, waarbij u `YOUR-KEY` vervangt door uw eigen LUIS-voorspellingssleutel. De aanvragen zijn GET-aanvragen en bevatten de autorisatie, met uw LUIS-voorspellingssleutel, als een queryreeksparameter.

    #### <a name="v3-prediction-request"></a>[V3-voorspellingsaanvraag](#tab/V3-1-1)


    De indeling van de V3-URL voor een **GET**-eindpuntaanvraag (via sites) is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2-voorspellingsaanvraag](#tab/V2-1-2)

    De indeling van de V2-URL voor een **GET**-eindpuntaanvraag is:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Plak de URL in een browservenster en druk op Enter. In de browser wordt een JSON-resultaat weergegeven dat aangeeft dat LUIS de intent `HomeAutomation.TurnOn` als de belangrijkste intent en de entiteit `HomeAutomation.Operation` met de waarde `on` detecteert.

    #### <a name="v3-prediction-response"></a>[V3-voorspellingsantwoord](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2-voorspellingsantwoord](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Als u alle intenties wilt zien, voegt u de juiste queryreeksparameter toe.

    #### <a name="v3-prediction-endpoint"></a>[V3-voorspellingseindpunt](#tab/V3-3-1)

    Voeg `show-all-intents=true` toe aan het einde van de queryreeks om **alle intenties weer te geven**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2-voorspellingseindpunt](#tab/V2)

    Voeg `verbose=true` toe aan het einde van de queryreeks om **alle intenties weer te geven**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:
* [V3-voorspellingseindpunt](luis-migration-api-v3.md)
* [Aangepaste subdomeinen](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Een app in de LUIS-portal maken](get-started-portal-build-app.md)

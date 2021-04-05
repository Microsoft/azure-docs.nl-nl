---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386596"
---
Deze snelstart op basis van Postman begeleidt u bij het ophalen van een antwoord uit een knowledge base.

## <a name="prerequisites"></a>Vereisten

* Meest recente [**Postman**](https://www.getpostman.com/).
* U moet het volgende hebben
    * Een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)
    * Een getrainde en gepubliceerde [knowledge base met vragen en antwoorden](../Quickstarts/add-question-metadata-portal.md), gebouwd op basis van de vorige quickstart, is geconfigureerd met metagegevens en weergepraat.

> [!NOTE]
> Wanneer u klaar bent voor het genereren van een antwoord op een vraag uit uw knowledge base, moet u uw knowledge base [trainen](../Quickstarts/create-publish-knowledge-base.md#save-and-train) en [publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base). Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Het tabblad **Postman** toont de instellingen die vereist zijn voor het genereren van een antwoord.

## <a name="set-up-postman-for-requests"></a>Postman voor aanvragen instellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Deze quickstart maakt gebruik van dezelfde instellingen voor de Postman-aanvraag **POST** en configureert vervolgens naar de hoofdtekst van de JSON die wordt verzonden naar de service op basis van waarvoor u een query wilt uitvoeren.

Gebruik deze procedure voor het configureren van Postman en lees elke volgende sectie om de JSON van de hoofdtekst te configureren.

1. Selecteer op de pagina **Instellingen** van de knowledge base het tabblad **Postman** om de configuratie te zien die wordt gebruikt voor het genereren van een antwoord uit de knowledge base. Kopieer de volgende informatie om te gebruiken in Postman.

    |Naam|Instelling|Doel en waarde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Dit is de HTTP-methode en route voor de URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Dit is de host van de URL. Voeg de Host en Post-waarden toe om de volledige generateAnswer-URL op te halen.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|De waarde van de header om uw aanvraag in Azure te autoriseren. |
    |`Content-type`|`application/json`|De headerwaarde van uw inhoud.|
    ||`{"question":"<Your question>"}`|De hoofdtekst van de POST-aanvraag als een JSON-object. Deze waarde wordt in elk van de volgende secties gewijzigd, afhankelijk van de bedoeling van de query.|

1. Open Postman en maak een nieuwe basis **POST**-aanvraag met uw gepubliceerde knowledge base-instellingen. Wijzig in de volgende secties de JSON POST-hoofdtekst om de query te wijzigen in uw knowledge base.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Deze quickstart maakt gebruik van dezelfde instellingen voor de Postman-aanvraag **POST** en configureert vervolgens naar de hoofdtekst van de JSON die wordt verzonden naar de service op basis van waarvoor u een query wilt uitvoeren.

Gebruik deze procedure voor het configureren van Postman en lees elke volgende sectie om de JSON van de hoofdtekst te configureren.

1. Selecteer op de pagina **Instellingen** van de knowledge base het tabblad **Postman** om de configuratie te zien die wordt gebruikt voor het genereren van een antwoord uit de knowledge base. Kopieer de volgende informatie om te gebruiken in Postman.

    |Naam|Instelling|Doel en waarde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Dit is de HTTP-methode en route voor de URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Dit is de host van de URL. Voeg de Host en Post-waarden toe om de volledige generateAnswer-URL op te halen.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|De waarde van de header om uw aanvraag te autoriseren. |
    |`Content-type`|`application/json`|De headerwaarde van uw inhoud.|
    ||`{"question":"<Your question>"}`|De hoofdtekst van de POST-aanvraag als een JSON-object. Deze waarde wordt in elk van de volgende secties gewijzigd, afhankelijk van de bedoeling van de query.|

1. Open Postman en maak een nieuwe basis **POST**-aanvraag met uw gepubliceerde knowledge base-instellingen. Wijzig in de volgende secties de JSON POST-hoofdtekst om de query te wijzigen in uw knowledge base.
---

## <a name="use-metadata-to-filter-answer"></a>Metagegevens gebruiken om antwoord te filteren

In een vorige quickstart zijn metagegevens toegevoegd aan twee QnA-paren om onderscheid te maken tussen twee verschillende vragen. Voeg de metagegevens toe aan de query om het filter te beperken tot alleen het relevante QnA-paar.

1. Wijzig in Postman alleen de query JSON door de eigenschap `strictFilters` toe te voegen met de combinatie van naam/waarde van `service:qna_maker`. De JSON van de hoofdtekst moet het volgende zijn:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    De vraag is slechts één woord, `size`, waarmee een van de twee paren vragen en antwoorden kan worden geretourneerd. De matrix `strictFilters` vertelt u de respons om alleen de `qna_maker`-antwoorden te verminderen.

1. Het respons bevat alleen het antwoord dat voldoet aan de filtercriteria.

    Het volgende antwoord is opgemaakt voor betere leesbaarheid:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Als er een vraag-en-antwoordpaar is dat niet voldoet aan de zoekterm maar wel aan het filter voldoet, wordt het niet geretourneerd. In plaats daarvan wordt het algemene antwoord `No good match found in KB.` geretourneerd.

## <a name="use-debug-query-property"></a>Query-eigenschap fouten opsporen gebruiken

> [!NOTE]
>Het gebruik van de eigenschap fouten opsporen voor een afhankelijkheid wordt niet aanbevolen. Deze eigenschap is toegevoegd om het productteam te helpen bij het oplossen van problemen.

Informatie over foutopsporing geeft u inzicht in hoe het geretourneerde antwoord is vastgesteld. Hoewel het handig is, is het niet nodig. Als u een antwoord met foutopsporingsinformatie wilt genereren, voegt u de eigenschap `debug` toe:

1. Wijzig in Postman alleen de JSON van de hoofdtekst door de eigenschap `debug` toe te voegen. De JSON moet het volgende zijn:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. De respons bevat de relevante informatie over het antwoord. In de volgende JSON-uitvoer zijn een aantal foutopsporingsgegevens vervangen door het weglatingsteken.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Test-knowledge base gebruiken

Als u een antwoord wilt ontvangen van de test-knowledge base, gebruikt u de hoofdteksteigenschap `isTest`.

Wijzig in Postman alleen de JSON van de hoofdtekst door de eigenschap `isTest` toe te voegen. De JSON moet het volgende zijn:

```json
{
    'question':'size',
    'isTest': true
}
```

In de JSON-respons wordt hetzelfde schema gebruikt als voor de gepubliceerde knowledge base-query.

> [!NOTE]
> Als de test en de gepubliceerde knowledge bases exact hetzelfde zijn, kan er nog een kleine variatie optreden omdat de testindex wordt gedeeld tussen alle knowledge bases in de resource.

## <a name="query-for-a-chit-chat-answer"></a>Zoeken naar een heen- en weergepraatantwoord

1. Wijzig in Postman alleen de JSON van de hoofdtekst in een instructie voor het beëindigen van een gesprek van de gebruiker. De JSON moet het volgende zijn:

    ```json
    {
        'question':'thank you'
    }
    ```

1. De respons bevat de score en het antwoord.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Omdat de vraag `Thank you` precies overeenkwam met een heen- en weergepraatvraag, is QnA Maker volledig zeker met de score van 100. QnA Maker heeft ook alle gerelateerde vragen geretourneerd, evenals de metagegevenseigenschap met de informatie over de tag met heen- en weergepraatmetagegevens.

## <a name="use-threshold-and-default-answer"></a>Drempel en standaardantwoord gebruiken

U kunt een minimale drempelwaarde voor het antwoord aanvragen. Als niet wordt voldaan aan de drempelwaarde, wordt het standaardantwoord geretourneerd.

1. Wijzig in Postman alleen de JSON van de hoofdtekst in een instructie voor het beëindigen van een gesprek van de gebruiker. De JSON moet het volgende zijn:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    De knowledge base mag dat antwoord niet vinden omdat de score van de vraag 71% is en het standaardantwoord dat u hebt ingevoerd tijdens het maken van de Knowledge Base, in plaats daarvan wordt geretourneerd.

    Het geretourneerde JSON-response, inclusief de score en het antwoord is:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker heeft een score geretourneerd van `0`, wat geen betrouwbaarheid betekent. Ook wordt het standaardantwoord geretourneerd.

1. Wijzig de drempelwaarde in 60% en vraag de query opnieuw aan:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    De geretourneerde JSON heeft het antwoord gevonden.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
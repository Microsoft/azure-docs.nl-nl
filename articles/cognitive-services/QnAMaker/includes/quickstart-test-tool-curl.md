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
ms.openlocfilehash: cbabfc1ae0c54c4ef20d3c689506e486bf4b6a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386647"
---
Deze snelstart op basis van cURL begeleidt u bij het ophalen van een antwoord uit een knowledge base.

## <a name="prerequisites"></a>Vereisten

* Nieuwste [**cURL**](https://curl.haxx.se/).
* U moet het volgende hebben
    * Een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)
    * Een getrainde en gepubliceerde knowledge base met vragen en antwoorden, van de vorige [quickstart](../Quickstarts/add-question-metadata-portal.md), geconfigureerd met metagegevens en weergepraat.

> [!NOTE]
> Wanneer u klaar bent voor het genereren van een antwoord op een vraag uit uw knowledge base, moet u uw knowledge base [trainen](../Quickstarts/create-publish-knowledge-base.md#save-and-train) en [publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base). Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Het tabblad **cURL** toont de instellingen die vereist zijn voor het genereren van een antwoord uit hulpprogramma voor opdrachtregels.

## <a name="use-metadata-to-filter-answer"></a>Metagegevens gebruiken om antwoord te filteren

Gebruik de knowledge base van de vorige quickstart om een antwoord te zoeken op basis van metagegevens.

1. Selecteer op de pagina **Instellingen** van de knowledge base het tabblad **cURL** om een voorbeeld van een cURL-opdracht te zien die wordt gebruikt voor het genereren van een antwoord uit de knowledge base.
1. Kopieer de opdracht naar een bewerkbare omgeving (zoals een tekstbestand), zodat u de opdracht kunt bewerken. Bewerk de waarde van de vraag als volgt zodat de metagegevens van `service:qna_maker` worden gebruikt als een filter voor de QnA-paren.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
    
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H   "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    ---

    De vraag is slechts één woord, `size`, waarmee een van de twee QnA-paren kan worden geretourneerd. De matrix `strictFilters` vertelt u de respons om alleen de `qna_maker`-antwoorden te verminderen.

1. Het respons bevat alleen het antwoord dat voldoet aan de filtercriteria. De volgende cURL-respons is opgemaakt voor betere leesbaarheid:

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

```json
Debug: {Enable:true}
```

1. Bewerk de cURL-opdracht zodat u de eigenschap debug kunt gebruiken om meer informatie weer te geven.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    ---

1. De respons bevat de relevante informatie over het antwoord. In de volgende JSON-uitvoer zijn een aantal foutopsporingsgegevens vervangen door het weglatingsteken om het korter te houden.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
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

De eigenschap is een booleaanse waarde.

```json
isTest:true
```

De cURL-opdracht ziet er als volgt uit:
# <a name="qna-maker-ga"></a>[QnA Maker GA](#tab/v1)
```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
```bash
curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

---
In de JSON-respons wordt hetzelfde schema gebruikt als voor de gepubliceerde knowledge base-query.

> [!NOTE]
> Als de test en de gepubliceerde knowledge bases exact hetzelfde zijn, kan er nog een kleine variatie optreden omdat de testindex wordt gedeeld tussen alle knowledge bases in de resource.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>cURL gebruiken om te zoeken naar een heen- en weergepraatantwoord

1. Gebruik in de voor cURL ingeschakelde terminal een botinstructie van de gebruiker die het gesprek beëindigt, zoals `Thank you` als de vraag. Er zijn geen andere eigenschappen om in te stellen.
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    ---

1. Voer de cURL-opdracht uit en ontvang het JSON-antwoord inclusief de score en het antwoord.

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

1. Voeg de eigenschap `threshold` toe om te vragen om een antwoord op `size` met een drempel van 80% of hoger. De knowledge base mag dat antwoord niet vinden omdat de score van de vraag 71% is. Het resultaat retourneert het standaardantwoord dat u hebt gegeven tijdens het maken van de knowledge base.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    ---

1. Voer de cURL-opdracht uit en ontvang het JSON-antwoord.

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
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    ---

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
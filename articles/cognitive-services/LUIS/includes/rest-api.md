---
title: REST API-opnamebestand
description: opnamebestand
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/30/2020
ms.topic: include
ms.openlocfilehash: ac5d4ce3080bad36b0f68289bee32910bdc58837
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941513"
---
Door deze quickstart te volgen, voert u drie opeenvolgende REST-aanroepen uit.

- Eerst uploadt u een batch van voorbeelduitingen die wordt gebruikt om het model van de Pizza-app te trainen met de aanroep [REST Batch labels toevoegen](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09).
- Vervolgens start u een trainingssessie voor de Pizza-app met behulp van de aanroep [REST Toepassingsversie trainen](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45).
- Ten slotte haalt u de status van de trainingssessie op voor de Pizza-app, met behulp van de aanroep [REST Status versietraining ophalen](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46).

[Referentiedocumentatie](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Vereisten

* Een gratis [LUIS](https://www.luis.ai)-account.
* Een teksteditor zoals [Visual Studio Code](https://code.visualstudio.com/).
* Het opdrachtregelprogramma cURL. Het programma cURL is al geïnstalleerd op macOS, de meeste Linux-distributies en Windows 10 build 1803 en hoger.

    Als u een cURL moet installeren, kunt u dit downloaden op de [Downloadpagina voor cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Pizza-app maken

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]


## <a name="create-a-json-file-to-train-the-pizza-app"></a>Een JSON-bestand maken om de Pizza-app te trainen

Om een JSON-bestand met drie voorbeelduitingen wilt maken, slaat u de volgende JSON-gegevens op in een bestand met de naam `ExampleUtterances.JSON`:

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

De JSON met voorbeelduitingen volgt een specifieke indeling.

Het veld `text` bevat de tekst van de voorbeeldutterance. Het veld `intentName` moet overeenkomen met de naam van een bestaande intentie in de LUIS-app. Het veld `entityLabels` is vereist. Als u niet alle entiteiten een label wilt geven, geeft u een lege matrix op.

Als de entityLabels-matrix niet leeg is, moeten `startCharIndex` en `endCharIndex` de entiteit markeren waarnaar wordt verwezen in het veld `entityName`. De index is gebaseerd op nul. Als u het label begint of beëindigt met een spatie in de tekst, mislukt de API-aanroep om de uitingen toe te voegen.

## <a name="add-example-utterances"></a>Voorbeelden van utterances toevoegen

1. Om de batch met voorbeelduitingen te uploaden, kopieert u deze opdracht in uw teksteditor:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT**_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[MacOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    _*_

1. Vervang de waarden die beginnen met `_*_YOUR-` door uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`_*_YOUR-AUTHORING-ENDPOINT_*_`| Het eindpunt van uw ontwerp-URL. Bijvoorbeeld https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/. U heeft uw resourcenaam ingesteld toen u de resource aanmaakte.|
    |`_*_YOUR-APP-ID_*_`| De id van uw LUIS-app. |
    |`_*_YOUR-APP-VERSION_*_`| De versie van uw LUIS-app. Voor de Pizza-app is het versienummer '0.1' zonder aanhalingstekens.|
    |`_*_YOUR-AUTHORING-KEY_*_`|Uw ontwerpsleutel van 32 tekens.|

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-portal in de sectie Beheren op de pagina *Azure-resources**. De App-ID is beschikbaar in hetzelfde gedeelte Beheren op de pagina **Toepassingsinstellingen**.

1. Start een Opdrachtprompt (Windows) of Terminal (macOS en Linux) en verander de mappen naar dezelfde map waarin u het `ExampleUtterances.JSON`-bestand hebt opgeslagen.

1. Kopieer de cURL-opdracht uit de editor en plak deze in een Opdrachtprompt (Windows) of Terminal (macOS en Linux). Druk op Enter om de opdracht uit te voeren.

    U ziet de volgende antwoorden:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Dit is de uitvoer, opgemaakt voor leesbaarheid:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Het model voor de Pizza-app trainen

1. Als u een trainingssessie voor de pizza-app wilt starten, kopieert u deze opdracht naar uw teksteditor:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "**_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[MacOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Vervang, zoals u zonet deed, de waarden die beginnen met `_*_YOUR-` door uw eigen waarden.

1. Kopieer de cURL-opdracht uit de editor en plak deze in een Opdrachtprompt (Windows) of Terminal (macOS en Linux). Druk op Enter om de opdracht uit te voeren.

    U ziet de volgende antwoorden:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Dit is de uitvoer, opgemaakt voor leesbaarheid:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>De status van een training ophalen

1. Als u de trainingsstatus voor de trainingssessie wilt ophalen, kopieert u deze opdracht naar uw teksteditor:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[MacOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Vervang, zoals u zonet deed, de waarden die beginnen met `_*_YOUR-` door uw eigen waarden.

1. Kopieer de cURL-opdracht uit de editor en plak deze in een Opdrachtprompt (Windows) of Terminal (macOS en Linux). Druk op Enter om de opdracht uit te voeren.

    U ziet de volgende antwoorden:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Dit is de uitvoer, opgemaakt voor leesbaarheid:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```


## <a name="get-intent-from-the-prediction-endpoint"></a>Intent ophalen vanaf het voorspellingseindpunt

Gebruik cURL om een query uit te voeren op het [voorspellingseindpunt](https://aka.ms/luis-apim-v3-prediction) en een voorspellingsresultaat op te halen.

> [!NOTE]
> Deze opdracht maakt gebruik van uw voorspellingseindpunt.

1. Kopieer deze opdracht naar uw teksteditor:

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[MacOS](#tab/unix)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    _*_

1. Vervang de waarden die beginnen met `_*_YOUR-` door uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`_*_YOUR-PREDICTION-ENDPOINT_*_`| Het eindpunt voor de voorspellings-URL. De Azure Resources-pagina voor uw app in het LUIS-portal.<br>Bijvoorbeeld `https://westus.api.cognitive.microsoft.com/`.|
    |`_*_YOUR-APP-ID_*_`|Uw app-ID. De pagina met toepassingsinstellingen voor uw app in het LUIS-portal.
    |`_*_YOUR-PREDICTION-KEY_**`|Uw voorspellingssleutel van 32 tekens. De Azure Resources-pagina voor uw app in het LUIS-portal.

1. Kopieer de tekst naar een consolevenster en druk op Enter om de opdracht uit te voeren:

1. Controleer het voorspellingsantwoord dat geretourneerd wordt als JSON:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    JSON-antwoord opgemaakt voor leesbaarheid:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ]
          }
        }
      }
    }
    ```
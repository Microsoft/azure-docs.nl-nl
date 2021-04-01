---
title: Model ophalen met REST-aanroep in Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd25ef6ff44f1d62d553db518f7389da0e5d5019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534572"
---
[Referentiedocumentatie](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [Voorbeeld](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-model-with-rest/model.js)

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/)-programmeertaal
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>Het Node.js-project maken

1. Maak een nieuwe map voor uw Node.js-project, zoals `node-model-with-rest`.

1. Open een nieuwe opdrachtprompt, navigeer naar de map die u hebt gemaakt en voer de volgende opdracht uit:

    ```console
    npm init
    ```

    Druk op Enter bij elke prompt om de standaardinstellingen te accepteren.

1. Installeer de module request-promise door de volgende opdracht in te voeren:

    ```console
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een nieuw bestand met de naam `model.js`. Voeg de volgende code toe:

    [!code-javascript[Code snippet](~/cognitive-services-quickstart-code/javascript/LUIS/node-model-with-rest/model.js)]

1. Vervang de waarden die beginnen met `YOUR-` door uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-APP-ID`| De id van uw LUIS-app. |
    |`YOUR-AUTHORING-KEY`|Uw ontwerpsleutel van 32 tekens.|
    |`YOUR-AUTHORING-ENDPOINT`| Het eindpunt van uw ontwerp-URL. Bijvoorbeeld `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. U heeft uw resourcenaam ingesteld toen u de resource aanmaakte.|

    Toegewezen sleutels en resources zijn zichtbaar in het LUIS-portaal in de sectie Beheren op de pagina **Azure-resources**. De App-ID is beschikbaar in hetzelfde gedeelte Beheren op de pagina **Toepassingsinstellingen**.

1. Voer bij de opdrachtprompt de volgende opdracht in om het project uit te voeren:

    ```console
    node model.js
    ```

1. Het creatieantwoord controleren:

    ```json
    addUtterance:
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    train POST:
    {
      "statusId": 9,
      "status": "Queued"
    }
    train GET:
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    done
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u de projectmap uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)
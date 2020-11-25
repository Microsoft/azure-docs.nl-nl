---
title: 'Quickstart: Een externe afbeelding analyseren met de REST API en Node.js'
titleSuffix: Azure Cognitive Services
description: In deze snelstart analyseert u een afbeelding met behulp van de Computer Vision-API met Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 38d377cc3731fe240c5450e9d902937e0ef9453c
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95746027"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Quickstart: Een externe afbeelding analyseren met behulp van de Computer Vision REST API met Node.js

In deze quickstart analyseert u een extern opgeslagen afbeelding om visuele kenmerken te extraheren met behulp van de Computer Vision REST API met Node.js. Met de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-methode kunt u visuele kenmerken verkrijgen op basis van de afbeeldingsinhoud.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4.x of hoger 
* [npm](https://www.npmjs.com/) 
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en eindpunt-URL, met respectievelijk de namen `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Installeer het npm-pakket [`request`](https://www.npmjs.com/package/request).
   1. Open een opdrachtpromptvenster als beheerder.
   1. Voer de volgende opdracht uit:

      ```console
      npm install request
      ```

   1. Sluit het opdrachtpromptvenster nadat het pakket is geïnstalleerd.

1. Kopieer de volgende code naar een teksteditor.
1. Vervang eventueel de waarde van `imageUrl` door de URL van een andere afbeelding die u wilt analyseren.
1. Vervang eventueel de waarde van de aanvraagparameter `language` door een andere taal.
1. Sla de code op als een bestand met de extensie `.js`. Bijvoorbeeld `analyze-image.js`.
1. Open een opdrachtpromptvenster.
1. Gebruik de opdracht `node` in de prompt om het bestand uit te voeren. Bijvoorbeeld `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.1/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. Het voorbeeld parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het bestand wanneer u het niet meer nodig hebt en verwijder vervolgens het npm-pakket `request`. Voer de volgende stappen uit om het pakket te verwijderen:

1. Open een opdrachtpromptvenster als beheerder.
2. Voer de volgende opdracht uit:

   ```console
   npm uninstall request
   ```

3. Sluit het opdrachtpromptvenster nadat het pakket is verwijderd.

## <a name="next-steps"></a>Volgende stappen

Bekijk vervolgens de Computer Vision-API's die worden gebruikt om een afbeelding te analyseren, beroemdheden en interessante plekken te detecteren, een miniatuur te maken en gedrukte en handgeschreven tekst te verkrijgen.

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d)

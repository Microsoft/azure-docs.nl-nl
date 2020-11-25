---
title: 'Quickstart: Een miniatuur genereren - REST, Node.js'
titleSuffix: Azure Cognitive Services
description: In deze snelstart maakt u een miniatuur van een afbeelding met behulp van de Computer Vision-API en Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: e18aac647c05b2ae78229d87b8d7c5fb2b90ba19
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95745959"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Quickstart: Een miniatuur genereren met de Computer Vision REST API en Node.js

In deze quickstart genereert u een miniatuur uit een afbeelding met behulp van de Computer Vision REST API. Met de methode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c) kunt u een miniatuur van een afbeelding genereren. U geeft de hoogte en breedte op. Deze waarden mogen afwijken van de hoogte-breedteverhouding van de invoerafbeelding. Computer Vision maakt gebruik van slim bijsnijden om op intelligente wijze het interessegebied te bepalen en coördinaten voor het bijsnijden te genereren op basis van dat gebied.

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
1. Sla de code op als een bestand met de extensie `.js`. Bijvoorbeeld `get-thumbnail.js`.
1. Open een opdrachtpromptvenster.
1. Gebruik de opdracht `node` in de prompt om het bestand uit te voeren. Bijvoorbeeld `node get-thumbnail.js`.

```javascript
'use strict';

const fs = require('fs');
const request = require('request').defaults({ encoding: null });

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']

var uriBase = endpoint + 'vision/v3.1/generateThumbnail';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

// Construct the request
const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
}

// Post the request and get the response (an image stream)
request.post(options, (error, response, body) => {
    // Write the stream to file
    var buf = Buffer.from(body, 'base64');
    fs.writeFile('thumbnail.png', buf, function (err) {
        if (err) throw err;
    });

    console.log('Image saved')
});
```

## <a name="examine-the-response"></a>Het antwoord bekijken

De miniatuurafbeelding wordt in een pop-upvenster weergegeven.
Een geslaagd antwoord wordt geretourneerd als binaire gegevens - deze staan voor de afbeeldingsgegevens van de miniatuur. Als de aanvraag mislukt, wordt het antwoord weergegeven in het consolevenster. De reactie op de mislukte aanvraag bevat een foutcode en een bericht om u te helpen bepalen wat er mis is gegaan.

## <a name="next-steps"></a>Volgende stappen

Bekijk vervolgens de Computer Vision-API die wordt gebruikt om een afbeelding te analyseren, beroemdheden en interessante plekken te detecteren, een miniatuur te maken en gedrukte en handgeschreven tekst te verkrijgen.

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d)

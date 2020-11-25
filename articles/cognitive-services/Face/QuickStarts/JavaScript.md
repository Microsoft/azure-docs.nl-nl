---
title: 'Quickstart: Gezichten in een afbeelding detecteren met de REST API en JavaScript'
titleSuffix: Azure Cognitive Services
description: In deze snelstart detecteert u gezichten in een afbeelding met behulp van de Face-API met JavaScript in Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.custom: devx-track-js
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f302000529e0dbf7ecce69ac9bebe77af59561ee
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020837"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Quickstart: Gezichten in een afbeelding detecteren met de REST API en JavaScript

In deze quickstart gebruikt u de Azure Face REST API met JavaScript om menselijke gezichten in een afbeelding te detecteren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Het HTML-bestand initialiseren

Maak een nieuw HTML-bestand, *detectFaces.html*, en voeg de volgende code toe.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

Voeg vervolgens de volgende code toe in het element `body` van het document. Met deze code wordt een eenvoudige gebruikersinterface ingesteld met een URL-veld, een knop **Gezicht analyseren**, een antwoordvenster en een venster voor de weergave van afbeeldingen.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="html_include":::

## <a name="write-the-javascript-script"></a>Het JavaScript-script schrijven

Voeg de volgende code rechtstreeks boven het element `h1` toe in uw document. Met deze code wordt de JavaScript-code ingesteld waarmee de Face-API wordt aangeroepen.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="script_include":::

U moet het veld `subscriptionKey` bijwerken met de waarde van uw abonnementssleutel en de tekenreeks `uriBase` zo wijzigen dat deze de juiste eindpunttekenreeks bevat. Het veld `returnFaceAttributes` geeft op welke gezichtskenmerken moeten worden opgehaald. U kunt deze queryreeks wijzigen afhankelijk van het beoogde gebruik.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Het script uitvoeren

Open *detectFaces.html* in uw browser. Wanneer u op de knop **Gezicht analyseren** klikt, zou de app de afbeelding van de opgegeven URL moeten weergeven en een JSON-tekenreeks van gezichtsgegevens afdrukken.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

De volgende tekst is een voorbeeld van een geslaagd JSON-antwoord.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]
```

## <a name="extract-face-attributes"></a>Gezichtskenmerken extraheren
 
Gebruik voor het extraheren van gezichtskenmerken detectiemodel 1 en voeg de queryparameter `returnFaceAttributes` toe.

```javascript
// Request parameters.
var params = {
    "detectionModel": "detection_01",
    "returnFaceAttributes": "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise",
    "returnFaceId": "true"
};
```

Het antwoord bevat nu gezichtskenmerken. Bijvoorbeeld:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een JavaScript-script geschreven waarmee de Azure Face-service wordt aangeroepen om gezichten in een afbeelding te detecteren en de gezichtskenmerken te retourneren. Lees het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

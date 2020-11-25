---
title: 'Snelstart: Gezichten in een afbeelding detecteren met de Azure REST API en cURL'
titleSuffix: Azure Cognitive Services
description: In deze snelstart gebruikt u de Azure Face REST API met cURL om gezichten in een afbeelding te detecteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 922b261ffeb6cb7a7170a50a3ba5e5ca91a10a11
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96011996"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Snelstart: Gezichten in een afbeelding detecteren met de Face REST API en cURL

In deze quickstart gebruikt u de Azure Face REST API met cURL om menselijke gezichten in een afbeelding te detecteren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="write-the-command"></a>De opdracht schrijven
 
U gebruikt een opdracht zoals de volgende om de Face-API aan te roepen en kenmerkgegevens voor gezichten op te halen uit een afbeelding. Kopieer eerst de code in een teksteditor&mdash;u moet wijzigingen aanbrengen in bepaalde gedeelten van de opdracht voordat u deze kunt uitvoeren.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_2":::

### <a name="subscription-key"></a>Abonnementssleutel
Vervang `<Subscription Key>` door een geldige Face-abonnementssleutel.

### <a name="face-endpoint-url"></a>Eindpunt-URL voor Face

De URL `https://<My Endpoint String>.com/face/v1.0/detect` geeft het Azure Face-eindpunt aan waarvoor een query moet worden uitgevoerd. U moet het eerste gedeelte van deze URL mogelijk wijzigen zodat deze overeenkomt met het eindpunt dat bij uw abonnementssleutel hoort.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="image-source-url"></a>Bron-URL van afbeelding
De bron-URL geeft aan welke afbeelding moet worden gebruikt als invoer. U kunt de URL wijzigen zodat deze verwijst naar de afbeelding die u wilt analyseren.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>De opdracht uitvoeren

Nadat u de wijzigingen hebt aangebracht, opent u een opdrachtprompt en voert u de nieuwe opdracht in. De gegevens over het gezicht worden nu in het consolevenster weergegeven als JSON-gegevens. Bijvoorbeeld:

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
 
Gebruik voor het extraheren van gezichtskenmerken detectiemodel 1 en voeg de queryparameter `returnFaceAttributes` toe. De opdracht zou er nu als volgt uit moeten zien. Voeg net als eerder uw Face-abonnementssleutel en eindpunt toe.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_1":::

De geretourneerde gezichtsgegevens bevatten nu gezichtskenmerken. Bijvoorbeeld:

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

In deze quickstart hebt u een cURL-opdracht geschreven waarmee de Azure Face-service wordt aangeroepen om gezichten in een afbeelding te detecteren en de gezichtskenmerken te retourneren. Lees het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

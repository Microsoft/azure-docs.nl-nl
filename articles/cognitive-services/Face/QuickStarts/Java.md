---
title: 'Quickstart: Gezichten in een afbeelding detecteren met de Azure REST API en Java'
titleSuffix: Azure Cognitive Services
description: In deze snelstart gebruikt u de Azure Face REST API met Java om gezichten in een afbeelding te detecteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: b8ea2908711497db5e86a7dd665548e33b9d9f25
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020854"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Quickstart: Gezichten in een afbeelding detecteren met de REST API en Java

In deze quickstart gebruikt u de Azure Face REST API met Java om menselijke gezichten in een afbeelding te detecteren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een Java IDE naar keuze.

## <a name="create-the-java-project"></a>Het Java-project maken

1. Maak een nieuwe Java-app met opdrachtregels in uw IDE en voeg een klasse **Main** toe met een methode **main**.
1. Importeer de volgende bibliotheken in uw Java-project. Als u Maven gebruikt, worden de Maven-coördinaten opgegeven voor elke bibliotheek.
   - [Apache HTTP-client](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Apache HTTP-core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [JSON-bibliotheek](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons logging](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Gezichtsdetectiecode toevoegen

Open de klasse Main van uw project. Hier voegt u de code toe voor het laden van afbeeldingen en detecteren van gezichten.

### <a name="import-packages"></a>Pakketten importeren

Voeg boven aan het bestand de volgende `import`-instructies toe.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Essentiële velden toevoegen

Vervang de **Hoofdklasse** door de volgende code. Met deze gegevens wordt aangegeven hoe de Face-service moet worden verbonden en waar de invoergegevens kunnen worden opgehaald. U moet het `subscriptionKey`-veld bijwerken met de waarde van uw abonnementssleutel en de `uriBase`-tekenreeks zo wijzigen dat deze de juiste eindpunttekenreeks bevat. U kunt ook de waarde `imageWithFaces` instellen voor een pad dat naar een ander afbeeldingsbestand verwijst.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Het veld `faceAttributes` is gewoon een lijst met bepaalde kenmerktypen. Dit veld geeft aan welke informatie over de gedetecteerde gezichten moet worden opgehaald.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>De REST API voor de gezichtsdetectie aanroepen

Voeg de **main**-methode toe met de volgende code. Hiermee wordt een REST-aanroep naar de Face-API gemaakt om informatie over het gezicht te detecteren in de externe afbeelding (de tekenreeks `faceAttributes` geeft aan welke gezichtskenmerken moeten worden opgehaald). Vervolgens worden de uitvoergegevens naar een JSON-tekenreeks geschreven.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Het JSON-antwoord parseren

Direct onder de vorige code voegt u het volgende blok toe. Hiermee worden de geretourneerde JSON-gegevens omgezet in een meer leesbare indeling voordat ze in de console worden weergegeven. Sluit als laatste het try-catch-blok, de **main**-methode en de **Hoofdklasse**.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>De app uitvoeren

Compileer de code en voer deze uit. Bij een geslaagd antwoord worden de Face-gegevens in de console weergegeven in een makkelijk leesbare JSON-indeling. Bijvoorbeeld:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Gezichtskenmerken extraheren
 
Gebruik voor het extraheren van gezichtskenmerken detectiemodel 1 en voeg de queryparameter `returnFaceAttributes` toe.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

Het antwoord bevat nu gezichtskenmerken. Bijvoorbeeld:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudige Java-consoletoepassing gemaakt die gebruikmaakt van REST-aanroepen naar de Azure Face-API voor het detecteren van gezichten in een afbeelding en het retourneren van de kenmerken. Lees het naslagmateriaal bij de Face-API voor meer informatie over de ondersteunde scenario's.

> [!div class="nextstepaction"]
> [Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

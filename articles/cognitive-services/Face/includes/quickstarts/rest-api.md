---
title: 'Face-REST API: quickstart'
description: Gebruik de Face-REST API met cURL om gezichten te detecteren, gelijksoortige gezichten te zoeken (gezichten zoeken op afbeelding), gezichten te identificeren (zoeken met gezichtsherkenning) en uw gezichtsgegevens te migreren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 9bdc153d88dceec37ae62bdcc6b38b32b4bc7787
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444319"
---
Aan de slag met gezichtsherkenning met behulp van de Face-REST API. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-REST API voor het volgende:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)

> [!NOTE]
> In deze quickstart wordt gebruik gemaakt van cURL-opdrachten om de REST API aan te roepen. U kunt de REST API ook aanroepen met behulp van een programmeertaal. Bekijk de GitHub-voorbeeldbestanden voor voorbeelden in [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) en [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Power shell versie 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1), of een vergelijk bare opdracht regel toepassing.


## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

U gebruikt een opdracht zoals de volgende om de Face-API aan te roepen en kenmerkgegevens voor gezichten op te halen uit een afbeelding. Kopieer eerst de code in een teksteditor&mdash;u moet wijzigingen aanbrengen in bepaalde gedeelten van de opdracht voordat u deze kunt uitvoeren.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Breng de volgende wijzigingen aan:
1. Wijs `Ocp-Apim-Subscription-Key` toe aan de geldige Face-abonnementssleutel.
1. Wijzig het eerste deel van deze URL, zodat deze overeenkomt met het eindpunt dat bij uw abonnementssleutel hoort.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Wijzig eventueel de URL in de hoofdtekst van de aanvraag om naar een andere afbeelding te verwijzen.

Nadat u de wijzigingen hebt aangebracht, opent u een opdrachtprompt en voert u de nieuwe opdracht in. 

### <a name="examine-the-results"></a>De resultaten bekijken

De gegevens over het gezicht worden nu in het consolevenster weergegeven als JSON-gegevens. Bijvoorbeeld:

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

### <a name="get-face-attributes"></a>Gezichtskenmerken ophalen
 
Als u gezichtskenmerken wilt extraheren, roept u de detectie-API opnieuw aan, maar stelt u `detectionModel` in op `detection_01`. Voeg queryparameter `returnFaceAttributes` ook toe. De opdracht zou er nu als volgt uit moeten zien. Voeg net als eerder uw Face-abonnementssleutel en eindpunt toe.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>De resultaten bekijken

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

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

Met deze bewerking wordt op basis van één gedetecteerd gezicht (bron) een reeks andere gezichten (doel) doorzocht om een overeenkomstig gezicht te vinden (gezichten zoeken op afbeelding). Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Eerst moet u gezichten in afbeeldingen detecteren voordat u ze kunt vergelijken. Voer deze opdracht uit zoals u dat hebt gedaan in de sectie [Gezichten detecteren](#detect-faces-in-an-image). Deze detectiemethode is geoptimaliseerd voor vergelijkingsbewerkingen. Er worden geen gedetailleerde gezichtskenmerken geëxtraheerd, zoals in de bovenstaande sectie, en er wordt een ander detectiemodel gebruikt.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Zoek de waarde `"faceId"` in het JSON-antwoord en sla deze op een tijdelijke locatie op. Roep vervolgens de bovenstaande opdracht opnieuw aan voor de andere afbeeldings-URL's en sla ook die gezichts-id's op. U gebruikt deze id's als de doelgroep van gezichten waarmee een vergelijkbaar gezicht moet worden gevonden.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Ten slotte detecteert u één brongezicht dat u gebruikt voor het vergelijken en slaat u de id ervan op. Bewaar deze id gescheiden van de andere.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Overeenkomsten zoeken

Kopieer de volgende opdracht naar een teksteditor.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Breng vervolgens de volgende wijzigingen aan:
1. Wijs `Ocp-Apim-Subscription-Key` toe aan de geldige Face-abonnementssleutel.
1. Wijzig het eerste deel van deze URL, zodat deze overeenkomt met het eindpunt dat bij uw abonnementssleutel hoort.

Gebruik de volgende JSON-inhoud voor de waarde `body`:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Gebruik de brongezicht-id voor `"faceId"`.
1. Plak de andere gezichts-id's als termen in de matrix `"faceIds"`.

### <a name="examine-the-results"></a>De resultaten bekijken

U ontvangt een JSON-antwoord met een lijst met id's van de gezichten die overeenkomen met uw het gezicht uit de query.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-REST API gebruikt om eenvoudige gezichtsherkenningstaken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Naslagmateriaal over de Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Engelstalig)

* [Wat is de Face-service?](../../overview.md)
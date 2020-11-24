---
title: 'Quickstart: Computer Vision 2.0 en 2.1 - Gedrukte en handgeschreven tekst extraheren - REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: In deze quickstart extraheert u gedrukte en handgeschreven tekst uit een afbeelding met behulp van de Computer Vision-API met JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: a0ec50f9f2666542d1f7ff7106b745461bb8731b
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95746214"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-javascript"></a>Quickstart: Gedrukte en handgeschreven tekst extraheren met behulp van de Computer Vision REST API en JavaScript

In deze snelstart extraheert u gedrukte en handgeschreven tekst uit een afbeelding met behulp van de Computer Vision REST API. Met de methoden [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) en [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) kunt u tekst in een afbeelding detecteren en de herkende tekens naar een machinaal leesbare tekenstroom extraheren. 

De methode [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) wordt asynchroon uitgevoerd. Deze methode retourneert geen gegevens in de hoofdtekst van een geslaagd antwoord. In plaats daarvan retourneert de methode Batch Read een URI in de waarde van het antwoordheader-veld `Operation-Location`. Deze URI vertegenwoordigt de [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750)-API en u kunt deze URI aanroepen om de status te controleren en de resultaten van de aanroep van de Read-methode te retourneren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.


## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende code naar een teksteditor.
1. Vervang eventueel de waarde van het kenmerk `value` voor het besturingselement `inputImage` door de URL van een andere afbeelding waaruit u tekst wilt extraheren.
1. Sla de code op als een bestand met de extensie `.html`. Bijvoorbeeld `get-text.html`.
1. Open een browservenster.
1. Wanneer de webpagina wordt weergegeven in de browser, vult u de vereiste parameters in en selecteert u de knop **Afbeelding lezen**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // **_ Update or verify the following values. _*_
        // _*********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Please enter your subscription key and endpoint.'); }
        
        var uriBase = endpoint + "/vision/v3.1/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;


        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>

Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
```



## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldwebpagina parseert en geeft een geslaagd antwoord weer in het browservenster dat vergelijkbaar is met het volgende voorbeeld:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              },
              {
                "boundingBox": [
                  540,
                  665,
                  926,
                  679,
                  926,
                  825,
                  541,
                  823
                ],
                "text": "quick",
                "confidence": 0.57
              },
              {
                "boundingBox": [
                  1125,
                  686,
                  1569,
                  700,
                  1569,
                  838,
                  1125,
                  828
                ],
                "text": "brown",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  1674,
                  703,
                  1966,
                  711,
                  1966,
                  851,
                  1674,
                  841
                ],
                "text": "fox",
                "confidence": 0.442
              },
              {
                "boundingBox": [
                  2083,
                  714,
                  2580,
                  725,
                  2579,
                  876,
                  2083,
                  855
                ],
                "text": "jumps",
                "confidence": 0.878
              }
            ]
          },
          {
            "boundingBox": [
              187,
              1062,
              485,
              1056,
              486,
              1120,
              189,
              1126
            ],
            "text": "over",
            "words": [
              {
                "boundingBox": [
                  190,
                  1064,
                  439,
                  1059,
                  441,
                  1122,
                  192,
                  1126
                ],
                "text": "over",
                "confidence": 0.37
              }
            ]
          },
          {
            "boundingBox": [
              664,
              1008,
              1973,
              1023,
              1969,
              1178,
              664,
              1154
            ],
            "text": "the lazy dog!",
            "words": [
              {
                "boundingBox": [
                  668,
                  1008,
                  923,
                  1015,
                  923,
                  1146,
                  669,
                  1117
                ],
                "text": "the",
                "confidence": 0.909
              },
              {
                "boundingBox": [
                  1107,
                  1018,
                  1447,
                  1023,
                  1445,
                  1178,
                  1107,
                  1162
                ],
                "text": "lazy",
                "confidence": 0.853
              },
              {
                "boundingBox": [
                  1639,
                  1024,
                  1974,
                  1023,
                  1971,
                  1170,
                  1636,
                  1178
                ],
                "text": "dog!",
                "confidence": 0.41
              }
            ]
          }
        ]
      }
    ]
  }
}
```


## <a name="next-steps"></a>Volgende stappen

Bekijk een JavaScript-toepassing die gebruikmaakt van Computer Vision om optische tekenherkenning (Optical Character Recognition of OCR) uit te voeren, maak slim bijgesneden miniaturen en detecteer, categoriseer, label en beschrijf visuele kenmerken in afbeeldingen. 

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met JavaScript](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial)

* Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

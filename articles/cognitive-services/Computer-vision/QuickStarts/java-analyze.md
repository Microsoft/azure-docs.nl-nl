---
title: 'Quickstart: Een externe afbeelding analyseren met de REST API en Java'
titleSuffix: Azure Cognitive Services
description: In deze snelstart analyseert u een afbeelding met behulp van de Computer Vision-API met Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 0341fa56bf119893ff94afccb8adf66c89c9a68a
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95746350"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>Quickstart: Een externe afbeelding analyseren met behulp van de Computer Vision REST API en Java

In deze snelstart analyseert u een extern opgeslagen afbeelding om visuele kenmerken te verkrijgen met behulp van Java en de REST-API van Computer Vision. Met de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-methode kunt u visuele kenmerken verkrijgen op basis van de afbeeldingsinhoud.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* [Java&trade; Platform, Standard Edition Development Kit 7 of 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK 7 of 8)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en eindpunt-URL, met respectievelijk de namen `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>De voorbeeldtoepassing maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Maak een nieuw Java-project in uw favoriete IDE of editor. Als de optie beschikbaar is, maakt u het Java-project vanuit een toepassingssjabloon met opdrachtregel.
1. Importeer de volgende bibliotheken in uw Java-project. Als u Maven gebruikt, worden de Maven-coördinaten opgegeven voor elke bibliotheek.
   - [Apache HTTP-client](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP-core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON-bibliotheek](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Voeg de volgende `import`-instructies toe aan het bestand met de openbare klasse `Main` voor uw project.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Vervang de openbare klasse `AnalyzeImage` door de volgende code.
1. Vervang eventueel de waarde van `imageToAnalyze` door de URL van een andere afbeelding die u wilt analyseren.

```java
public class AnalyzeImage {
    // **********************************************
    // **_ Update or verify the following values. _*_
    // _*********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + "vision/v3.1/analyze";
    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
            "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>Het programma compileren en uitvoeren

1. Sla vervolgens het Java-project op en bouw het.
1. Als u een IDE gebruikt, voert u `Main` uit.

U kunt ook de volgende opdrachten uitvoeren als u het programma uitvoert vanuit een opdrachtregelvenster. Bij deze opdrachten wordt aangenomen dat uw bibliotheken zich in een map met de naam `libs` bevinden die in dezelfde map staat als `Main.java`; als dat niet zo is, moet u `libs` vervangen door het pad naar uw bibliotheken.

1. Compileer het bestand `Main.java`.

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. Voer het programma uit. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de KB te maken en vervolgens worden elke 30 seconden de resultaten gecontroleerd. Elk antwoord wordt weergegeven in het opdrachtregelvenster.

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldtoepassing parseert en geeft een geslaagd antwoord weer in het consolevenster dat vergelijkbaar is met het volgende voorbeeld:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Volgende stappen

Een Java Swing-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren; slim bijgesneden miniaturen maken; plus visuele kenmerken, inclusief gezichten, in een afbeelding detecteren, categoriseren, labelen en beschrijven. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met Java](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial)
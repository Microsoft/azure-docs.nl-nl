---
title: 'Quickstart: Afbeeldingsinzichten krijgen met de REST API en Java - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: scottwhi
ms.openlocfilehash: 8974dbef47938460e5a1b32d4f6004a9fd35f9d2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499081"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Quickstart: Afbeeldingsinzichten krijgen met behulp van de Bing Visual Search REST-API en Java

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Visual Search-API te maken. Met deze Java-toepassing wordt er een afbeelding naar de API geüpload, waarna de geretourneerde gegevens worden weergegeven. Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit (JDK) 7 of 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* De [Gson Java-bibliotheek](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken:

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Maak variabelen voor uw API-eindpunt, abonnementssleutel en het pad naar uw afbeelding. Voor de `endpoint`-waarde kunt u het globale eindpunt in de volgende code gebruiken of het eindpunt voor het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat voor uw resource wordt weergegeven in de Azure-portal.

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
3. Wanneer u een lokale afbeelding uploadt, moeten de formuliergegevens de header `Content-Disposition` bevatten. Stel de `name`-parameter in op ‘afbeelding’ en stel de `filename`-parameter in op de bestandsnaam van de afbeelding. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u kunt uploaden is 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>De JSON-parser maken

Maak een methode om het JSON-antwoord van de API beter leesbaar te maken door `JsonParser` te gebruiken.

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>De zoekopdracht en query compileren

1. Maak in de Main-methode van uw toepassing een HTTP-client met behulp van `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Maak een `HttpEntity`-object om uw afbeelding te uploaden naar de API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Maak een `httpPost`-object met uw eindpunt en stel de header in voor gebruik van uw abonnementssleutel.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Het JSON-antwoord ontvangen en verwerken

1. Gebruik de `HttpClient.execute()`-methode om een aanvraag te verzenden naar de API en sla het antwoord op in een `InputStream`-object.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Sla de JSON-tekenreeks op en druk het antwoord af.

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Visual Search-web-app met één pagina bouwen](../tutorial-bing-visual-search-single-page-app.md)
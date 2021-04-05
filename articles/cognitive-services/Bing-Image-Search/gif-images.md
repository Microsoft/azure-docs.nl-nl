---
title: Zoeken naar GIF-afbeeldingen met behulp van de Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Met de Bing Afbeeldingen zoeken-API kunt u ook op het hele web zoeken naar de meest relevante GIF-afbeeldingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341900"
---
# <a name="search-for-gif-images"></a>Zoeken naar GIF-afbeeldingen 

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Afbeeldingen zoeken-API kunt u ook op het hele web zoeken naar de meest relevante GIF-afbeeldingen.  Ontwikkel aars kunnen aantrekkelijke gif's integreren in verschillende gespreks scenario's. 

De volgende URL is een query voor GIF-afbeeldingen met animatie.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
De [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) para meter bepaalt de zoek termen.  In de vorige query wordt ook `animatedGif` de [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) -filter parameter opgegeven.

Als u voor beelden van resultaten wilt weer geven, gebruikt u de volgende URL om bing.com te zoeken.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Queryparameters

Voor meer informatie over query parameters en-opties raadpleegt u de [afbeeldingen zoeken API-referentie](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Hieronder ziet u een voor beeld onder het [voor beeld van een geanimeerde GIF met Java zoeken](#gifExample).

## <a name="tips-and-suggestions"></a>Tips en suggesties

- U kunt [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) -en [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) -para meters opgeven. We raden u aan de instelling maxFileSize = 2000000 in te stellen als meren deel van de gif's in onze index onder 2 MB.  Zo kunt u de grootte van de gegevens beheren als de band breedte een probleem is, zoals in mobiele mobiel scenario's.
- Om de waargenomen prestaties te verbeteren, moet u eerst de miniatuur laden voordat u de bron-URL laadt.  
- Voor de ervaring van de eerste uitvoering of de landings pagina waar u nog geen gebruikers query hebt, kunt u proberen om met behulp van onze trending GIF-Zoek opdrachten te helpen van de [API voor trending-afbeeldingen](trending-images.md).
- Er zijn drie instellingen voor de para meter [SafeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) .  Met de `strict` optie wordt inhoud voor volwassenen geblokkeerd.
- Zie [MKT](./language-support.md) voor een volledige lijst met ondersteunde talen en locaties.
- *AnimatedGifHttps* retourneert alleen bewegende GIF-afbeeldingen uit een https-adres. Voor de beveiliging is voor veel toepassingen verbinding met externe webkoppelingen via HTTPS vereist. Voor de Apple App Store is bijvoorbeeld een verbinding met webservices via HTTPS vereist, waarmee gebruikers gegevens tijdens de overdracht veilig worden versleuteld.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Voor beeld van een animatie naar een geanimeerde GIF met Java

De volgende URL zoekt naar GIF-afbeeldingen met animatie: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Zoals in het volgende voor beeld wordt weer gegeven, vereist de URL-query [OCP-APIM-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) -header.

In het volgende Java-voor beeld wordt de aanvraag samengesteld en verzonden.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Resultaten
De code haalt de volgende resultaten op als JSON-objecten:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Volgende stappen
- [Snelstart voor C#](quickstarts/csharp.md)
- [Zelf studie Afbeeldingen zoeken toepassing met één pagina](tutorial-bing-image-search-single-page-app.md)
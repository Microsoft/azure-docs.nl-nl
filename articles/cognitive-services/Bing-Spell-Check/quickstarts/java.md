---
title: 'Quickstart: Spellingcontrole met de REST API en Java - Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST API en Java om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: cc6ad88b70a1addbbf7df6a53218810de683e5db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352708"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Quickstart: Spellingcontrole met de Bing Spellingcontrole-REST API en Java

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige Java-toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Hoewel deze toepassing in Java is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Vereisten

* De Java Development Kit (JDK) 7 of hoger.

* Importeer de [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) of de meest actuele [Gson](https://github.com/google/gson)-versie. Voor opdrachtregeluitvoering voegt u de `.jar` toe aan uw Java-map met de hoofdklasse.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor met een klassenaam van uw keuze, en importeer dan de volgende pakketten:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Maak variabelen voor het eindpunt, de host en het pad van de API, en uw abonnementssleutel. Maak vervolgens variabelen voor uw markt, de tekst waarop u spellingcontrole wilt uitvoeren en een tekenreeks voor de spellingcontrolemodus. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Een API-aanvraag maken en verzenden

1. Maak een functie met de naam `check()` om de API-aanvraag te maken en verzenden. In deze functie voegt u de code toe die in de volgende stappen is opgegeven. Maak een tekenreeks voor de aanvraagparameters:

   1. Wijs uw marktcode toe aan de parameter `mkt` met de operator `=`. 

   1. Voeg de parameter `mode` met de `&` operator toe en wijs vervolgens de spellingcontrolemodus toe. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Maak een URL door het eindpunt, de host, het pad en de parametertekenreeks te combineren. Maak een nieuw `HttpsURLConnection`-object.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Open een verbinding met de URL. Stel de aanvraagmethode in op `POST` en voeg uw aanvraagparameters toe. Vergeet niet om de abonnementssleutel toe te voegen aan de `Ocp-Apim-Subscription-Key`-header.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Maak een nieuw `DataOutputStream`-object en verzend de aanvraag naar de API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Het API-antwoord opmaken en lezen

1. Voeg de `prettify()`-methode aan uw klasse toe, zodat de JSON wordt opgemaakt om de uitvoer beter leesbaar te maken.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Maak een `BufferedReader` en lees het antwoord van de API. Druk het af naar de console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>De API aanroepen

Roep in de hoofdfunctie van uw toepassing uw eerder gemaakte `check()`-methode aan.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw uw project en voer het uit. Als u de opdrachtregel gebruikt, gebruikt u de volgende opdrachten om de toepassing te bouwen en uit te voeren:

1. Bouw de toepassing:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Voer de toepassing uit:

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Referentie voor de Bing Spellingcontrole-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
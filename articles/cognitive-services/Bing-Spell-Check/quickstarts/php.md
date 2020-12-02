---
title: 'Snelstart: Spellingcontrole met de REST API en PHP - Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: In deze quickstart ziet u hoe een eenvoudige PHP-toepassing een aanvraag verzendt naar de Bing Spellingcontrole-API en een lijst met voorgestelde correcties retourneert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 68c54fa9eab6021b50de96224729d0737973d624
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352674"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-php"></a>Snelstart: Spellingcontrole met de Bing Spellingcontrole-REST API en PHP

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige PHP-toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Hoewel deze toepassing in PHP is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

* [PHP 5.6.x](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="get-bing-spell-check-rest-api-results"></a>Resultaten van REST API voor Bing Spellingcontrole ophalen

1. Maak een nieuw PHP-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `subscriptionKey` door een geldige toegangssleutel voor uw abonnement.
4. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.
5. Voer het programma uit.
    
    ```php
    <?php
    
    // NOTE: Be sure to uncomment the following line in your php.ini file.
    // ;extension=php_openssl.dll
    
    // These properties are used for optional headers (see below).
    // define("CLIENT_ID", "<Client ID from Previous Response Goes Here>");
    // define("CLIENT_IP", "999.999.999.999");
    // define("CLIENT_LOCATION", "+90.0000000000000;long: 00.0000000000000;re:100.000000000000");
    
    $host = 'https://api.cognitive.microsoft.com';
    $path = '/bing/v7.0/spellcheck?';
    $params = 'mkt=en-us&mode=proof';
    
    $input = "Hollo, wrld!";
    
    $data = array (
        'text' => urlencode ($input)
    );
    
    // NOTE: Replace this example key with a valid subscription key.
    $key = 'ENTER KEY HERE';
    
    // The following headers are optional, but it is recommended
    // that they are treated as required. These headers will assist the service
    // with returning more accurate results.
    //'X-Search-Location' => CLIENT_LOCATION
    //'X-MSEdge-ClientID' => CLIENT_ID
    //'X-MSEdge-ClientIP' => CLIENT_IP
    
    $headers = "Content-type: application/x-www-form-urlencoded\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    
    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => http_build_query ($data)
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    
    if ($result === FALSE) {
        /* Handle error */
    }
    
    $json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
    echo $json;
    ?>
    ```


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw toepassing uit door een webserver te starten en naar uw bestand te navigeren.

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
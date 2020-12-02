---
title: 'Snelstart: Afbeeldingen zoeken met de REST API voor Bing Image Search en PHP'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen voor het zoeken van afbeeldingen naar de REST API voor Bing Image Search te verzenden met PHP en JSON-antwoorden te ontvangen.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf5478dfb15665047e39d51c71ec9e85a436fe91
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341849"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-php"></a>Snelstart: Afbeeldingen zoeken met de REST API voor Bing Image Search en PHP

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze snelstart om voor het eerst de Bing Afbeeldingen zoeken-API aan te roepen en een JSON-antwoord te ontvangen. De eenvoudige toepassing in dit artikel stuurt een zoekquery en toont de onbewerkte resultaten.

Hoewel deze toepassing in PHP is geschreven, is de API een RESTful-webservice die compatibel is met elke programmeertaal die HTTP-aanvragen kan doen en JSON kan parseren.

De broncode voor dit voorbeeld is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/php/Search/BingWebSearchv7.php).

## <a name="prerequisites"></a>Vereisten

* [PHP 5.6.x of later](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zie [Prijzen van Cognitive Services - Bing Search-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor meer informatie.

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

Volg deze stappen voor het uitvoeren van deze toepassing:

1. Zorg ervoor dat beveiligde HTTP-ondersteuning is ingeschakeld in uw `php.ini`-bestand. Voor Windows bevindt dit bestand zich in *C:\windows*.
2. Maak een nieuw PHP-project in uw favoriete IDE of editor.
3. Definieer het API-eindpunt, de abonnementssleutel en de zoekterm. Het eindpunt kan het globale eindpunt in de volgende code zijn of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md), dat voor uw resource wordt weergegeven in Azure Portal.

    ```php
    $endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';
    // Replace the accessKey string value with your valid access key.
    $accessKey = 'enter key here';
    $term = 'tropical ocean';
    ```

## <a name="construct-and-perform-an-http-request"></a>Een HTTP-aanvraag compileren en uitvoeren

1. Gebruik de variabelen in de vorige stap om een HTTP-aanvraag naar de Image Search-API voor te bereiden.

    ```php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                            'header' => $headers,
                            'method' => 'GET' ));
    ```

2. Voer de webaanvraag uit en haal het JSON-antwoord op.

    ```php
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);
    ```

## <a name="process-and-print-the-json"></a>De JSON verwerken en afdrukken

Verwerk en druk het geretourneerde JSON-antwoord af.

```php
$headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }
    return array($headers, $result);
```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Antwoorden die afkomstig zijn van de Bing Afbeeldingen zoeken-API, worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Image Search](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Wat is de Bing Image Search-API?](../overview.md)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Prijsinformatie voor Bing Search-API's](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentatie voor Azure Cognitive Services](../../index.yml)
* [Naslag voor Bing Afbeeldingen zoeken-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
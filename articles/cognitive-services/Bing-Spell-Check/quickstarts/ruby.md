---
title: 'Quickstart: Spellingcontrole met de REST API en Ruby - Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST API en Ruby om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 46b62b5721eca622fcf2795a395653071bb88c95
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352640"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Quickstart: Spellingcontrole met de Bing Spellingcontrole-REST API en Ruby

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole-REST API te maken met behulp van Ruby. Deze eenvoudige toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Ondanks dat deze toepassing in Ruby is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Vereisten

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) of later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Ruby-bestand in uw favoriete editor of IDE en voeg de volgende vereisten toe: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Maak variabelen voor uw abonnementssleutel, eindpunt-URI en pad. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource. Voeg uw aanvraagparameters toe:

   1. Wijs uw marktcode toe aan de parameter `mkt` met de operator `=`. De marktcode is de code van het land of de regio van waaruit u de aanvraag indient. 

   1. Voeg de parameter `mode` met de `&` operator toe en wijs vervolgens de spellingcontrolemodus toe. De modus kan de waarde `proof` (de meeste spelling- en grammaticafouten worden onderschept) of de waarde `spell` (de meeste spellingfouten worden onderschept, maar niet zo veel grammaticafouten) zijn. 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Een spellingcontroleaanvraag verzenden

1. Maak een URI op basis van uw host-URI, pad en parametertekenreeks. Voeg aan de query de tekst toe waarop u een spellingcontrole wilt uitvoeren.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Maak een aanvraag met behulp van de zojuist gemaakte URI. Voeg uw sleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Verzend de aanvraag.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Haal het JSON-antwoord op en druk het af naar de console. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw uw project en voer het uit. Als u gebruikmaakt van de opdrachtregel, gebruikt u de volgende opdracht om de toepassing uit te voeren:

   ```bash
   ruby <FILE_NAME>.rb
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
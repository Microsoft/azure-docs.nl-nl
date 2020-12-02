---
title: 'Quickstart: Spelling controleren met de REST API en Node.js - Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST API en Node.js om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 182ccc972fce40adc88ed66b01c92e778f6479a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352691"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Quickstart: spelling controleren met de Bing Spellingcontrole-REST API en Node.js

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Met deze eenvoudige JavaScript-toepassing verzendt u een aanvraag naar de API en wordt een lijst met voorgestelde correcties geretourneerd. 

Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js 6](https://nodejs.org/en/download/) of later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor. Stel het niveau voor strengheid in en vereis het gebruik van `https`. Maak vervolgens variabelen voor de host en het pad van het API-eindpunt en uw abonnementssleutel. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Maak variabelen voor uw zoekparameters en de tekst die u wilt controleren: 

   1. Wijs uw marktcode toe aan de parameter `mkt` met de operator `=`. De marktcode is de code van het land of de regio van waaruit u de aanvraag indient. 

   1. Voeg de parameter `mode` met de `&` operator toe en wijs vervolgens de spellingcontrolemodus toe. De modus kan de waarde `proof` (de meeste spelling- en grammaticafouten worden onderschept) of de waarde `spell` (de meeste spellingfouten worden onderschept, maar niet zo veel grammaticafouten) zijn.

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Parameters voor de aanvraag maken

Maak aanvraagparameters door een nieuw object met een methode van het type `POST` te maken. Voeg uw pad toe door uw eindpuntpad en de queryreeks toe te voegen. Voeg vervolgens uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Een antwoordhandler maken

Maak een functie met de naam `response_handler` om een JSON-antwoord van de API weer te geven. Maak een variabele voor de hoofdtekst van het antwoord. Voeg door middel van `response.on()` het antwoord toe als de vlag `data` wordt ontvangen. Nadat de vlag `end` is ontvangen, geeft u de JSON-hoofdtekst weer in de console.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>De aanvraag verzenden

Roep de API aan door middel van `https.request()` met uw aanvraagparameters en de antwoordhandler. Schrijf de tekst naar de API en beëindig vervolgens de aanvraag.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>De toepassing uitvoeren

1. Bouw uw project en voer het uit.

1. Als u gebruikmaakt van de opdrachtregel, gebruikt u de volgende opdracht om de toepassing te maken en uit te voeren:

   ```bash
   node <FILE_NAME>.js
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
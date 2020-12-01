---
title: Aanvragen verzenden naar de Automatische suggestie-API voor Bing
titleSuffix: Azure Cognitive Services
description: De Bing Automatische suggesties-API retourneert een lijst met voorgestelde query's op basis van de gedeeltelijke queryreeks in het zoekvak. Meer informatie over het verzenden van aanvragen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353388"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Aanvragen verzenden naar de Automatische suggestie-API voor Bing.

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Als uw toepassing query's verstuurt naar een van de API's van Bing Search, kunt u de Bing Automatische suggesties-API gebruiken om de ervaring van uw gebruikers te verbeteren. De Bing Automatische suggesties-API retourneert een lijst met voorgestelde query's op basis van de gedeeltelijke queryreeks in het zoekvak. Als tekens worden ingevoerd in een zoekvak in uw toepassing, kunt u suggesties weer geven in een vervolg keuzelijst. Gebruik dit artikel voor meer informatie over het verzenden van aanvragen naar deze API. 

## <a name="bing-autosuggest-api-endpoint"></a>Automatische suggestie-API voor Bing-eind punt

Het **Automatische suggestie-API voor Bing**  bevat één eind punt, waarmee een lijst met voorgestelde query's uit een gedeeltelijke zoek term wordt geretourneerd.

Als u aanbevolen query's wilt ontvangen met behulp van de Bing API, verzendt `GET` u een aanvraag naar het volgende eind punt. Gebruik de para meters headers en URL om verdere specificaties te definiëren.

**Eind punt:** Retourneert Zoek suggesties als JSON-resultaten die relevant zijn voor de invoer van de gebruiker die is gedefinieerd door `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Zie de naslag informatie over [Automatische suggestie-API voor Bing V7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) voor meer informatie over kopteksten, para meters, markt codes, reactie objecten, fouten, enzovoort.

De **Bing** api's ondersteunen zoek acties die resultaten retour neren op basis van hun type. Alle zoek eindpunten retour neren resultaten als JSON-antwoord objecten.
Alle eind punten ondersteunen query's die een specifieke taal en/of locatie retour neren met de lengte graad, breedte graad en de zoek RADIUS.

Zie de naslag pagina's voor elk type voor volledige informatie over de para meters die door elk eind punt worden ondersteund.
Voor voor beelden van basis aanvragen die gebruikmaken van de automatische suggestie-API, raadpleegt u automatisch [suggereren Quick](/azure/cognitive-services/Bing-Autosuggest)starts.

## <a name="bing-autosuggest-api-requests"></a>Automatische suggestie-API voor Bing aanvragen

> [!NOTE]
> * Aanvragen voor de Automatische suggestie-API voor Bing moeten gebruikmaken van het HTTPS-protocol.

Het is raadzaam dat alle aanvragen afkomstig zijn van een server. Het distribueren van de sleutel als onderdeel van een client toepassing biedt meer kans op kwaad aardige toegang van derden. Daarnaast biedt het maken van aanroepen vanaf een server één upgrade punt voor toekomstige updates.

De aanvraag moet de queryparameter [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) bevatten, met daarin de gedeeltelijke zoekterm van de gebruiker. Hoewel dit optioneel is, moet de aanvraag ook de queryparameter [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) bevatten. Hiermee geeft u de markt aan waarvan de resultaten afkomstig moeten zijn. Zie [Queryparameters](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) voor een lijst met optionele queryparameters. Alle waarden van queryparameter moeten als een URL zijn gecodeerd.

De aanvraag moet de header [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) bevatten. Hoewel dit optioneel is, wordt u aangeraden ook altijd deze headers op te geven:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

De headers ClientIP en Location zijn belangrijk voor het retourneren van locatiespecifieke inhoud.

Zie [Headers](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) voor een lijst met alle aanvraag- en antwoordheaders.

> [!NOTE]
> Wanneer u de Automatische suggestie-API voor Bing aanroept vanuit Java script, kan de ingebouwde beveiligings functies van uw browser ertoe leiden dat u de waarden van deze headers niet kunt openen.

U kunt dit oplossen door de Automatische suggestie-API voor Bing aanvraag via een CORS-proxy te maken. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers` koptekst die antwoord headers filtert en deze beschikbaar maakt voor Java script.

Het is eenvoudig om een CORS-proxy te installeren zodat onze [zelf studie-app](../tutorials/autosuggest.md) toegang kan krijgen tot de optionele client headers. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Automatische suggestie-API voor Bing-eind punt in het HTML-bestand in:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Start ten slotte de CORS-proxy met de volgende opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

Aanvragen moeten alle voorgestelde query parameters en kopteksten bevatten. 

In het volgende voorbeeld ziet u een aanvraag die de voorgestelde queryreeksen voor *sail* retourneert.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Als dit de eerste keer is van u een van de Bing-API's aanroept, moet u de header met de client-id weglaten. Voeg de header met de client-id alleen toe als u eerder een Bing-API hebt aangeroepen en Bing een client-id heeft geretourneerd voor de combinatie van gebruiker en apparaat.

De volgende groep websuggestie is een antwoord op de bovenstaande aanvraag. De groep bevat een lijst met suggesties voor zoek query's, met elke suggestie, waaronder een `displayText` , `query` en en `url` veld.

Het veld `displayText` bevat de voorgestelde query die u gebruikt voor het vullen van de vervolgkeuzelijst van het zoekvak. U moet alle suggesties uit het antwoord weergeven, en in de opgegeven volgorde.  

Als de gebruiker een query selecteert in de vervolg keuzelijst, kunt u deze gebruiken om een van de [Bing zoeken-API's](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) aan te roepen en de resultaten zelf weer te geven, of de gebruiker naar de pagina met zoek resultaten van de Bing verzenden met behulp van het geretourneerde `url` veld.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Wat is Bing Automatische suggesties?](../get-suggested-search-terms.md)
- [Naslaghandleiding Bing Automatische suggesties-API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Suggesties voor voorgestelde zoek termen ophalen uit de Automatische suggestie-API voor Bing](get-suggestions.md)
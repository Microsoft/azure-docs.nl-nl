---
title: API-query's en-antwoorden verzenden en gebruiken-zoeken in lokale bedrijfs-Bing
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel voor meer informatie over het verzenden en gebruiken van zoek query's met de Bing lokale Business Search-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96487164"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Query's en antwoorden van Bing Local Business Search-API verzenden en gebruiken

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

U kunt lokale resultaten ophalen van de Bing Local Business Search-API door een zoek opdracht naar het eind punt te verzenden en de header op te nemen `Ocp-Apim-Subscription-Key` , wat vereist is. Naast de beschik bare [kopteksten](local-search-reference.md#headers) en [para meters](local-search-reference.md#query-parameters)kunnen Zoek opdrachten worden aangepast door [geografische grenzen](specify-geographic-search.md) op te geven [voor het gebied](local-search-query-response.md) dat moet worden doorzocht, en de locatie van de geretourneerde locaties.

## <a name="creating-a-request"></a>Een aanvraag maken

Als u een aanvraag wilt verzenden naar de Bing lokale Business Search-API, voegt u een zoek term toe aan de `q=` para meter voordat u deze toevoegt aan het API-eind punt, inclusief de `Ocp-Apim-Subscription-Key` header. Bijvoorbeeld:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

De syntaxis van de volledige URL van de aanvraag wordt hieronder weer gegeven. Zie [Quick](quickstarts/local-quickstart.md)starts voor Bing Local Business Search API en referentie-inhoud voor [kopteksten](local-search-reference.md#headers) en [para meters](local-search-reference.md#query-parameters) voor meer informatie over het verzenden van aanvragen. 

Voor informatie over lokale zoek categorieën raadpleegt u [zoeken naar categorieën voor de Bing lokale Business Search-API](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Antwoorden gebruiken

JSON-antwoorden van de Bing lokale Business Search-API bevatten een `SearchResponse` object. De API retourneert relevante zoek resultaten in het `places` veld. Als er geen resultaten worden gevonden, `places` wordt het veld niet in het antwoord opgenomen.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Kenmerken van zoek resultaten

De JSON-resultaten die door de API worden geretourneerd, bevatten de volgende kenmerken:

* _type
* adres
* entityPresentationInfo
* geo
* id
* naam
* routeablePoint
* telefoon
* url

Zie voor algemene informatie over kopteksten, para meters, markt codes, antwoord objecten, fouten, enzovoort, de [Bing Local Search API V7](local-search-reference.md) -referentie

> [!NOTE]
> U, of derden namens u, mag geen gegevens uit de lokale zoek opdracht gebruiken, bewaren, opslaan, delen of distribueren met het oog op het testen, ontwikkelen, trainen, distribueren of maken van een service of functie die niet van micro soft is. 


## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

De volgende JSON-respons bevat Zoek resultaten die door de query zijn opgegeven `?q=restaurant+in+Bellevue` .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Volgende stappen
- [Quickstart over Business Search](quickstarts/local-quickstart.md)
- [Quickstart over Bing Local Business Search met Java](quickstarts/local-search-java-quickstart.md)
- [Quick start voor lokaal bedrijfs Zoek knooppunt](quickstarts/local-search-node-quickstart.md)
- [Quickstart over zoeken naar lokale bedrijven met Python](quickstarts/local-search-python-quickstart.md)
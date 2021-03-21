---
title: Bing Webzoekopdrachten-API-antwoord structuur en-antwoord typen
titleSuffix: Azure Cognitive Services
description: Wanneer u Bing Web Search een zoek opdracht verzendt, wordt een `SearchResponse` object geretourneerd in de hoofd tekst van het antwoord.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94380977"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Webzoekopdrachten-API-antwoord structuur en-antwoord typen  

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Wanneer u Bing Web Search een zoek opdracht verzendt, wordt een [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) object geretourneerd in de hoofd tekst van het antwoord. Het object bevat een veld voor elk antwoord dat door Bing is bepaald en dat van belang is voor de query. Dit voor beeld illustreert een reactie object als Bing alle antwoorden heeft geretourneerd:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Meestal retourneert Bing Web Search een subset van de antwoorden. Als de query term bijvoorbeeld *dinghies* is, kan de reactie het volgende omvatten `webPages` :, `images` en `rankingResponse` . Tenzij u [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) hebt gebruikt om webpagina's te filteren, bevat het antwoord altijd de `webpages` en `rankingResponse` antwoorden.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Antwoord op webpagina's

Het antwoord op [webpagina's](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) bevat een lijst met koppelingen naar webpagina's die Bing Web Search bepaald, zijn relevant voor de query. Elke [webpagina](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) in de lijst bevat: de naam van de pagina, URL, weer gave-URL, een korte beschrijving van de inhoud en de datum waarop Bing de inhoud heeft gevonden.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Gebruik `name` en `url` om een Hyper link te maken waarmee de gebruiker naar de webpagina gaat.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Antwoord op installatie kopieën

Het antwoord op [installatie kopieën](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) bevat een lijst met installatie kopieën die door Bing worden beschouwd als relevant voor de query. Elke [afbeelding](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) in de lijst bevat de URL van de afbeelding, de grootte, de afmetingen en de coderings indeling. Het Image-object bevat ook de URL van een miniatuur van de afbeelding en de afmetingen van de miniatuur.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Afhankelijk van het apparaat van de gebruiker, zou u normaal gesp roken een subset van de miniaturen weer geven, met een optie waarmee de gebruiker [door](./paging-search-results.md) de resterende installatie kopieën kan bladeren.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

U kunt de miniatuur ook uitbreiden op het moment dat de gebruiker er met de cursor naar wijst. Vergeet niet om een geschikt kenmerk toe wijzen aan de afbeelding als u dat wilt doen. Bijvoorbeeld door de host uit `hostPageDisplayUrl` te pakken en deze onder de installatie kopie weer te geven. Zie [Formaat van miniaturen wijzigen en miniaturen bijsnijden](./resize-and-crop-thumbnails.md) voor informatie over het aanpassen van het formaat van de miniatuur.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Als de gebruiker op de miniatuur klikt, kunt u gebruiken `webSearchUrl` om de gebruiker naar de zoek resultaten pagina van Bing te gaan voor installatie kopieën, die een collage van de afbeeldingen bevat.

Zie [afbeeldingen zoeken-API](../bing-image-search/overview.md)voor meer informatie over het antwoord en de installatie kopieën van de afbeelding.

## <a name="related-searches-answer"></a>Antwoord op verwante Zoek opdrachten

Het [relatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) -antwoord bevat een lijst met de populairste gerelateerde query's die door andere gebruikers zijn gemaakt. Elke [query](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) in de lijst bevat een query teken reeks ( `text` ), een query reeks met treffer markeringen ( `displayText` ) en een URL ( `webSearchUrl` ) naar de pagina Zoek resultaten van Bing voor die query.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Gebruik de `displayText` query teken reeks en de `webSearchUrl` URL voor het maken van een Hyper link waarmee de gebruiker naar de zoek resultaten pagina van Bing voor de gerelateerde query gaat. U kunt de `text` query reeks ook gebruiken in uw eigen WEBZOEKOPDRACHTEN API-query en de resultaten zelf weer geven.

`displayText`Zie [treffers markeren](../bing-web-search/hit-highlighting.md)voor informatie over het afhandelen van markeringen in.

Hieronder ziet u een voor beeld van het gebruik van gerelateerde query's in Bing.com.

![Verwante Zoek opdrachten voor beeld op Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Antwoord op Video's

Het antwoord [Video's](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) bevat een lijst met Video's die Bing dacht te zijn voor de query. Elke [video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) in de lijst bevat de URL van de video, de duur, de afmetingen en de coderings indeling. Het Video-object bevat ook de URL van een miniatuur van de video en de afmetingen van de miniatuur.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Afhankelijk van het apparaat van de gebruiker, geeft u normaal gesp roken een deel van de Video's weer met een optie voor de gebruiker om de resterende Video's weer te geven. U ziet een miniatuur van de video met de lengte, beschrijving (naam) en toewijzing (uitgever) van de video.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Wanneer de gebruiker op de miniatuur beweegt, kunt u gebruiken `motionThumbnailUrl` om een miniatuur versie van de video af te spelen. Vergeet niet om het kenmerk motionThumbnailUrl in te stellen wanneer u de miniatuur weergeeft.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Als de gebruiker op de miniatuur klikt, zijn dit de opties voor het bekijken van de video:

- Gebruiken `hostPageUrl` om de video op de host website weer te geven (bijvoorbeeld YouTube)
- Gebruik `webSearchUrl` om de video in de Bing Video browser weer te geven
- Gebruik `embedHtml` om de video in uw eigen ervaring in te sluiten

Zie [Video's zoeken-API](../bing-video-search/overview.md)voor meer informatie over het video antwoord en Video's.

## <a name="news-answer"></a>Antwoord op nieuws

Het [Nieuws](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) antwoord bevat een lijst met nieuws artikelen die door Bing worden beschouwd als relevant voor de query. Elk [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) in de lijst bevat de naam en beschrijving van het artikel, plus de URL naar het artikel op de website van de host. Als in het artikel een afbeelding staat, bevat het object een miniatuur van de afbeelding.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Afhankelijk van het apparaat van de gebruiker, kunt u een subset van de nieuws artikelen weer geven met een optie voor de gebruiker om de resterende artikelen weer te geven. Gebruik `name` en `url` om een hyperlink te maken waarmee de gebruiker naar het nieuwsartikel op de site van de host gaat. Als het artikel een afbeelding bevat, kunt u de afbeelding klikken met behulp van `url` . Vergeet niet om met `provider` bronvermelding toe te passen voor het artikel.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Zie [Nieuws zoeken-API](../bing-news-search/search-the-web.md)voor meer informatie over het antwoord en de nieuws artikelen van nieuws.

## <a name="computation-answer"></a>Antwoord op berekening

Als de gebruiker een wiskundige expressie of een query voor de conversie van een eenheid invoert, kan het antwoord een [reken](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) antwoord bevatten. Het `computation` antwoord bevat de genormaliseerde expressie en het resultaat.

Een query voor eenheids conversie is een query waarmee de ene eenheid naar een andere wordt geconverteerd. *Hoe groot bijvoorbeeld in 10 meters?* of *hoeveel tablespoons in een 1/4e Cup?*

Hieronder ziet u het `computation` antwoord op het *aantal poten in 10 meters?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Hieronder ziet u voor beelden van wiskundige query's en hun bijbehorende `computation` antwoorden.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Een wiskundige expressie kan de volgende symbolen bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|+|Optellen|
|-|Aftrekking|
|/|Afdeling|
|*|Vermenigvuldigen|
|^|Stroom|
|!|Faculteit|
|.|Decimaal|
|()|Rang groepering|
|[]|Functie|

Een wiskundige expressie kan de volgende constanten bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|Pi|3,14159...|
|Hoek|Hoek|
|i|Imaginair getal|
|e|e, 2,71828...|
|GoldenRatio|Golden-verhouding, 1,61803...|

Een wiskundige expressie kan de volgende functies bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|Sorteren|Vierkantswortel|
|Sin [x], COS [x], Tan [x]<br />CSC [x], SEC [x], Cot [x]|Trigonometrische functies (met argumenten in radialen)|
|ArcSin [x], ArcCos [x], ArcTan [x]<br />ArcCsc [x], ArcSec [x], ArcCot [x]|Inverse trigonometrische functies (resulteert in radialen)|
|EXP [x], E ^ x|Exponentiële functie|
|Logboek [x]|Natuurlijk logaritme|
|SINH [x], COSH [x], TANH [x]<br />CSCH [x], sech [x], Coth [x]|Hyperbolische functies|
|ArcSinh [x], ArcCosh [x], ArcTanh [x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Inverse hyperbolische functies|

Wiskundige expressies die variabelen bevatten (bijvoorbeeld 4x + 6 = 18, waarbij x de variabele is) worden niet ondersteund.

## <a name="timezone-answer"></a>Antwoord tijd zone

Als de gebruiker een tijd-of datum query invoert, kan het antwoord een tijd [zone](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) antwoord bevatten. Dit antwoord biedt ondersteuning voor impliciete of expliciete query's. Een impliciete query, zoals *Wat is de tijd?*, retourneert de lokale tijd op basis van de locatie van de gebruiker. Een expliciete query, zoals de *tijd in Seattle?*, retourneert de lokale tijd voor Seattle, WA.

Het `timeZone` antwoord bevat de naam van de locatie, de huidige UTC-datum en-tijd op de opgegeven locatie en de UTC-afwijking. Als de grens van de locatie zich binnen meerdere tijd zones bevindt, bevat het antwoord de huidige UTC-datum en-tijd van alle tijd zones binnen de grens. Omdat de provincie van Florida bijvoorbeeld binnen twee tijd zones valt, bevat het antwoord de lokale datum en tijd van beide tijd zones.  

Als de query de tijd van een staat of land/regio aanvraagt, bepaalt Bing de primaire plaats binnen de geografische grens van de locatie en retourneert deze in het `primaryCityTime` veld. Als de grens meerdere tijd zones bevat, worden de resterende tijd zones in het `otherCityTimes` veld geretourneerd.

Hieronder ziet u voor beelden van query's waarmee het antwoord wordt geretourneerd `timeZone` .

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion-antwoord

Als Bing bepaalt dat de gebruiker mogelijk is bedoeld om iets anders te zoeken, bevat het antwoord een [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) -object. Als de gebruiker bijvoorbeeld zoekt naar de *Carlos-pen*, kan Bing bepalen of de gebruiker die is bedoeld om te zoeken naar Carlos Pena in plaats daarvan (op basis van vroegere Zoek opdrachten door andere *Carlos-pen*). Hieronder ziet u een voor beeld van een spelling reactie.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Antwoordheaders

Antwoorden van de Bing Webzoekopdrachten-API kunnen de volgende headers bevatten:

| Header | Beschrijving |
|-|-|
|`X-MSEdge-ClientID`|De unieke ID die Bing aan de gebruiker heeft toegewezen|
|`BingAPIs-Market`|De markt die is gebruikt om aan de aanvraag te voldoen|
|`BingAPIs-TraceId`|De logboek vermelding op de Bing API-server voor deze aanvraag (voor ondersteuning)|

Het is vooral belang rijk dat u de client-ID persistent maakt en de volgende aanvragen retourneert. Wanneer u dit doet, wordt in de zoek opdracht in de context van de zoek resultaten gebruikgemaakt van de focus, en wordt ook een consistente gebruikers ervaring geboden.

Wanneer u echter de Bing Webzoekopdrachten-API aanroept vanuit Java script, kunnen de ingebouwde beveiligings functies (CORS) van uw browser ertoe leiden dat u geen toegang hebt tot de waarden van deze headers.

Als u toegang wilt krijgen tot de headers, kunt u de Bing Webzoekopdrachten-API aanvraag indienen via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers` koptekst die antwoord headers filtert en deze beschikbaar maakt voor Java script.

Het is eenvoudig om een CORS-proxy te installeren zodat onze [zelf studie-app](tutorial-bing-web-search-single-page-app.md) toegang kan krijgen tot de optionele client headers. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Bing Webzoekopdrachten-API-eind punt in het HTML-bestand in: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Start ten slotte de CORS-proxy met de volgende opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="response-headers-in-production"></a>Antwoord headers in productie

De in het vorige antwoord beschreven methode voor CORS-proxy is geschikt voor ontwikkeling, testen en leren.

In een productie omgeving moet u een script aan de server zijde hosten in hetzelfde domein als de webpagina die gebruikmaakt van de Bing Webzoekopdrachten-API. Dit script moet API-aanroepen uitvoeren op aanvraag van de webpagina java script en alle resultaten, inclusief headers, terugsturen naar de client. Omdat de twee resources (pagina en script) een oorsprong delen, wordt CORS niet gebruikt en zijn de speciale headers toegankelijk voor de Java script op de webpagina.

Deze benadering beschermt ook uw API-sleutel tegen bloot stelling aan het publiek, omdat alleen het script aan de server zijde dit vereist. Het script kan een andere methode gebruiken om ervoor te zorgen dat de aanvraag wordt geautoriseerd.

Hieronder ziet u hoe Bing de spelling suggestie gebruikt.

![Voor beeld van Bing spelling suggesties](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Volgende stappen  

* Raadpleeg de documentatie over het [beperken van aanvragen](throttling-requests.md) .  

## <a name="see-also"></a>Zie ook  

* [Bing Webzoekopdrachten-API referentie](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)